---
title: Adicione um modelo a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Adicione um modelo a um aplicativo ASP.NET Core simples.
ms.author: riande
ms.date: 01/13/2020
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 12429f1f381788569615a2a40e1ca13c4b3cfb6e
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727115"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="716e2-103">Adicione um modelo a um aplicativo ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="716e2-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="716e2-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="716e2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="716e2-105">Nesta seção, você adiciona classes para gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="716e2-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="716e2-106">Essas classes serão a parte “**M**odel” parte do aplicativo **M**VC.</span><span class="sxs-lookup"><span data-stu-id="716e2-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="716e2-107">Você usa essas classes com o [EF Core](/ef/core) (Entity Framework Core) para trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="716e2-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="716e2-108">O EF Core é uma estrutura ORM (de mapeamento relacional de objetos) que simplifica o código de acesso a dados que você precisa escrever.</span><span class="sxs-lookup"><span data-stu-id="716e2-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="716e2-109">As classes de modelo que você cria são conhecidas como classes de dados POCO (de **o**bjetos **C**L**R** **b**ásicos) porque elas não têm nenhuma dependência no EF Core.</span><span class="sxs-lookup"><span data-stu-id="716e2-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="716e2-110">Elas apenas definem as propriedades dos dados que serão armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="716e2-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="716e2-111">Neste tutorial, você escreve as classes de modelo primeiro e o EF Core cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="716e2-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="716e2-112">Adicionar uma classe de modelo de dados</span><span class="sxs-lookup"><span data-stu-id="716e2-112">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="716e2-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="716e2-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="716e2-114">Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="716e2-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="716e2-115">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="716e2-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="716e2-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="716e2-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="716e2-117">Adicione um arquivo chamado *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="716e2-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="716e2-118">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="716e2-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="716e2-119">Clique com o botão direito do mouse na pasta *modelos* > **Adicionar** > **nova classe** > **classe vazia**.</span><span class="sxs-lookup"><span data-stu-id="716e2-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="716e2-120">Dê ao arquivo o nome de *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="716e2-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="716e2-121">Atualize o arquivo *Movie.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="716e2-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="716e2-122">A classe `Movie` contém um campo `Id`, que é exigido pelo banco de dados para a chave primária.</span><span class="sxs-lookup"><span data-stu-id="716e2-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="716e2-123">O atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) em `ReleaseDate` especifica o tipo de dados (`Date`).</span><span class="sxs-lookup"><span data-stu-id="716e2-123">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="716e2-124">Com esse atributo:</span><span class="sxs-lookup"><span data-stu-id="716e2-124">With this attribute:</span></span>

* <span data-ttu-id="716e2-125">O usuário não precisa inserir informações de tempo no campo de data.</span><span class="sxs-lookup"><span data-stu-id="716e2-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="716e2-126">Somente a data é exibida, não as informações de tempo.</span><span class="sxs-lookup"><span data-stu-id="716e2-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="716e2-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) são abordados em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="716e2-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="716e2-128">Adicionar pacotes do NuGet</span><span class="sxs-lookup"><span data-stu-id="716e2-128">Add NuGet packages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="716e2-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="716e2-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="716e2-130">No menu **ferramentas** , selecione **Gerenciador de pacotes NuGet** > **Package Manager Console** (PMC).</span><span class="sxs-lookup"><span data-stu-id="716e2-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="716e2-132">No PMC, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="716e2-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="716e2-133">O comando anterior adiciona o provedor SQL Server do EF Core.</span><span class="sxs-lookup"><span data-stu-id="716e2-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="716e2-134">O pacote do provedor instala o pacote do EF Core como uma dependência.</span><span class="sxs-lookup"><span data-stu-id="716e2-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="716e2-135">Pacotes adicionais são instalados automaticamente na etapa de scaffolding posteriormente no tutorial.</span><span class="sxs-lookup"><span data-stu-id="716e2-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="716e2-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="716e2-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="716e2-137">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="716e2-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="716e2-138">No menu **projeto** , selecione **gerenciar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="716e2-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="716e2-139">No campo de **pesquisa** no canto superior direito, insira `Microsoft.EntityFrameworkCore.SQLite` e pressione a tecla de **retorno** para pesquisar.</span><span class="sxs-lookup"><span data-stu-id="716e2-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="716e2-140">Selecione o pacote NuGet correspondente e pressione o botão **Adicionar pacote** .</span><span class="sxs-lookup"><span data-stu-id="716e2-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Adicionar Entity Framework Core pacote NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="716e2-142">A caixa de diálogo **selecionar projetos** será exibida, com o projeto `MvcMovie` selecionado.</span><span class="sxs-lookup"><span data-stu-id="716e2-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="716e2-143">Pressione o botão **OK** .</span><span class="sxs-lookup"><span data-stu-id="716e2-143">Press the **Ok** button.</span></span>

<span data-ttu-id="716e2-144">Uma caixa de diálogo de **aceitação de licença** será exibida.</span><span class="sxs-lookup"><span data-stu-id="716e2-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="716e2-145">Examine as licenças conforme desejado e clique no botão **aceitar** .</span><span class="sxs-lookup"><span data-stu-id="716e2-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="716e2-146">Repita as etapas acima para instalar os seguintes pacotes NuGet:</span><span class="sxs-lookup"><span data-stu-id="716e2-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="716e2-147">Criar uma classe de contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="716e2-147">Create a database context class</span></span>

<span data-ttu-id="716e2-148">Uma classe de contexto de banco de dados é necessária para coordenar a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="716e2-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="716e2-149">O contexto de banco de dados é derivado de [Microsoft. EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e especifica as entidades a serem incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="716e2-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="716e2-150">Crie uma pasta de *Dados*.</span><span class="sxs-lookup"><span data-stu-id="716e2-150">Create a *Data* folder.</span></span>

<span data-ttu-id="716e2-151">Adicione um arquivo *Data/MvcMovieContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="716e2-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="716e2-152">O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="716e2-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="716e2-153">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="716e2-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="716e2-154">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="716e2-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="716e2-155">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="716e2-155">Register the database context</span></span>

<span data-ttu-id="716e2-156">O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="716e2-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="716e2-157">Os serviços (como o contexto de BD do EF Core) devem ser registrados com a DI durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="716e2-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="716e2-158">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="716e2-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="716e2-159">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="716e2-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="716e2-160">Nesta seção, você registra o contexto do banco de dados com o contêiner DI.</span><span class="sxs-lookup"><span data-stu-id="716e2-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="716e2-161">Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="716e2-161">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="716e2-162">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="716e2-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="716e2-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="716e2-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="716e2-164">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="716e2-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="716e2-165">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="716e2-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="716e2-166">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="716e2-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="716e2-167">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="716e2-167">Add a database connection string</span></span>

<span data-ttu-id="716e2-168">Adicione uma cadeia de conexão ao arquivo *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="716e2-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="716e2-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="716e2-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="716e2-170">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="716e2-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="716e2-171">Compile o projeto como uma verificação de erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="716e2-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="716e2-172">Aplicar scaffold a páginas de filme</span><span class="sxs-lookup"><span data-stu-id="716e2-172">Scaffold movie pages</span></span>

<span data-ttu-id="716e2-173">Use a ferramenta scaffolding para produzir páginas CRUD (criar, ler, atualizar e excluir) para o modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="716e2-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="716e2-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="716e2-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="716e2-175">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Controladores* **> Adicionar > Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="716e2-175">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![exibição da etapa acima](adding-model/_static/add_controller21.png)

<span data-ttu-id="716e2-177">Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="716e2-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="716e2-179">Preencha a caixa de diálogo **Adicionar Controlador**:</span><span class="sxs-lookup"><span data-stu-id="716e2-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="716e2-180">**Classe de modelo:** *filme (MvcMovie. Models)*</span><span class="sxs-lookup"><span data-stu-id="716e2-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="716e2-181">**Classe de contexto de dados:** *MvcMovieContext (MvcMovie. Data)*</span><span class="sxs-lookup"><span data-stu-id="716e2-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Adicionar contexto de dados](adding-model/_static/dc3.png)

* <span data-ttu-id="716e2-183">**Exibições:** mantenha o padrão de cada opção marcado</span><span class="sxs-lookup"><span data-stu-id="716e2-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="716e2-184">**Nome do controlador:** mantenha o *MoviesController* padrão</span><span class="sxs-lookup"><span data-stu-id="716e2-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="716e2-185">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="716e2-185">Select **Add**</span></span>

<span data-ttu-id="716e2-186">O Visual Studio cria:</span><span class="sxs-lookup"><span data-stu-id="716e2-186">Visual Studio creates:</span></span>

* <span data-ttu-id="716e2-187">Um controlador de filmes (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="716e2-187">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="716e2-188">Arquivos de exibição do Razor para as páginas Criar, Excluir, Detalhes, Editar e Índice (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="716e2-188">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="716e2-189">A criação automática desses arquivos é conhecida como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="716e2-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="716e2-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="716e2-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="716e2-191">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="716e2-191">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="716e2-192">No Linux, exporte o caminho da ferramenta Scaffold:</span><span class="sxs-lookup"><span data-stu-id="716e2-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="716e2-193">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="716e2-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="716e2-194">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="716e2-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="716e2-195">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="716e2-195">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="716e2-196">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="716e2-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="716e2-197">Você não pode usar as páginas com scaffold ainda porque o banco de dados não existe.</span><span class="sxs-lookup"><span data-stu-id="716e2-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="716e2-198">Se você executar o aplicativo e clicar no link do **aplicativo de filme** , receberá uma mensagem de erro *não é possível abrir o banco de dados* ou *nenhuma tabela: filme* .</span><span class="sxs-lookup"><span data-stu-id="716e2-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="716e2-199">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="716e2-199">Initial migration</span></span>

<span data-ttu-id="716e2-200">Use o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="716e2-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="716e2-201">As migrações são um conjunto de ferramentas que permitem criar e atualizar um banco de dados para corresponder ao seu modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="716e2-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="716e2-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="716e2-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="716e2-203">No menu **ferramentas** , selecione **Gerenciador de pacotes NuGet** > **Package Manager Console** (PMC).</span><span class="sxs-lookup"><span data-stu-id="716e2-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="716e2-204">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="716e2-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="716e2-205">`Add-Migration InitialCreate`: gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="716e2-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="716e2-206">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="716e2-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="716e2-207">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="716e2-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="716e2-208">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="716e2-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="716e2-209">O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="716e2-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="716e2-210">`Update-Database`: atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="716e2-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="716e2-211">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="716e2-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="716e2-212">O comando de atualização de banco de dados gera o seguinte aviso:</span><span class="sxs-lookup"><span data-stu-id="716e2-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="716e2-213">Nenhum tipo foi especificado para a coluna decimal 'Preço' no tipo de entidade 'Filme'.</span><span class="sxs-lookup"><span data-stu-id="716e2-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="716e2-214">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="716e2-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="716e2-215">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.</span><span class="sxs-lookup"><span data-stu-id="716e2-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="716e2-216">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="716e2-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="716e2-217">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="716e2-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="716e2-218">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="716e2-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="716e2-219">`ef migrations add InitialCreate`: gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="716e2-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="716e2-220">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="716e2-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="716e2-221">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="716e2-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="716e2-222">Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="716e2-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="716e2-223">O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="716e2-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="716e2-224">`ef database update`: atualiza o banco de dados para a migração mais recente, que o comando anterior criou.</span><span class="sxs-lookup"><span data-stu-id="716e2-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="716e2-225">Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="716e2-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [more information on the CLI tools for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="716e2-226">A classe InitialCreate</span><span class="sxs-lookup"><span data-stu-id="716e2-226">The InitialCreate class</span></span>

<span data-ttu-id="716e2-227">Examine o arquivo de migração *Migrations/{timestamp}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="716e2-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="716e2-228">O método `Up` cria a tabela de filmes e configura `Id` como a chave primária.</span><span class="sxs-lookup"><span data-stu-id="716e2-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="716e2-229">O método `Down` reverte as alterações de esquema feitas pela migração `Up`.</span><span class="sxs-lookup"><span data-stu-id="716e2-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="716e2-230">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="716e2-230">Test the app</span></span>

* <span data-ttu-id="716e2-231">Execute o aplicativo e clique no link **Aplicativo de Filme**.</span><span class="sxs-lookup"><span data-stu-id="716e2-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="716e2-232">Se você receber uma exceção semelhante a uma das seguintes:</span><span class="sxs-lookup"><span data-stu-id="716e2-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="716e2-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="716e2-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="716e2-234">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="716e2-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="716e2-235">Você provavelmente perdeu a [etapa de migrações](#migration).</span><span class="sxs-lookup"><span data-stu-id="716e2-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="716e2-236">Teste a página **Criar**.</span><span class="sxs-lookup"><span data-stu-id="716e2-236">Test the **Create** page.</span></span> <span data-ttu-id="716e2-237">Inserir e enviar dados.</span><span class="sxs-lookup"><span data-stu-id="716e2-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="716e2-238">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="716e2-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="716e2-239">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="716e2-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="716e2-240">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="716e2-240">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="716e2-241">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="716e2-241">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="716e2-242">Injeção de dependência no controlador</span><span class="sxs-lookup"><span data-stu-id="716e2-242">Dependency injection in the controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="716e2-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="716e2-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="716e2-244">Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:</span><span class="sxs-lookup"><span data-stu-id="716e2-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="716e2-245">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="716e2-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="716e2-246">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="716e2-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="716e2-247">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="716e2-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="716e2-248">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="716e2-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="716e2-249">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="716e2-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="716e2-250">Usar o SQLite para desenvolvimento, SQL Server para produção</span><span class="sxs-lookup"><span data-stu-id="716e2-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="716e2-251">Quando o SQLite é selecionado, o código gerado pelo modelo está pronto para desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="716e2-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="716e2-252">O código a seguir mostra como injetar <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> na inicialização.</span><span class="sxs-lookup"><span data-stu-id="716e2-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="716e2-253">`IWebHostEnvironment` é injetado para que `ConfigureServices` possa usar o SQLite no desenvolvimento e SQL Server em produção.</span><span class="sxs-lookup"><span data-stu-id="716e2-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="716e2-254">Modelos fortemente tipados e a palavra-chave @model</span><span class="sxs-lookup"><span data-stu-id="716e2-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="716e2-255">Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="716e2-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="716e2-256">O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="716e2-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="716e2-257">O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="716e2-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="716e2-258">Essa abordagem fortemente tipada permite a verificação de código em tempo de compilação.</span><span class="sxs-lookup"><span data-stu-id="716e2-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="716e2-259">O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições.</span><span class="sxs-lookup"><span data-stu-id="716e2-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="716e2-260">Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="716e2-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="716e2-261">O parâmetro `id` geralmente é passado como dados de rota.</span><span class="sxs-lookup"><span data-stu-id="716e2-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="716e2-262">Por exemplo, `https://localhost:5001/movies/details/1` define:</span><span class="sxs-lookup"><span data-stu-id="716e2-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="716e2-263">O controlador para o controlador `movies` (o primeiro segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="716e2-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="716e2-264">A ação para `details` (o segundo segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="716e2-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="716e2-265">A ID como 1 (o último segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="716e2-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="716e2-266">Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="716e2-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="716e2-267">O parâmetro `id` é definido como um [tipo que permite valor nulo](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), caso um valor de ID não seja fornecido.</span><span class="sxs-lookup"><span data-stu-id="716e2-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="716e2-268">Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.</span><span class="sxs-lookup"><span data-stu-id="716e2-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="716e2-269">Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:</span><span class="sxs-lookup"><span data-stu-id="716e2-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="716e2-270">Examine o conteúdo do arquivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="716e2-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="716e2-271">A instrução `@model` na parte superior do arquivo de exibição especifica o tipo de objeto que a exibição espera.</span><span class="sxs-lookup"><span data-stu-id="716e2-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="716e2-272">Quando o controlador de filme foi criado, a seguinte instrução `@model` foi incluída:</span><span class="sxs-lookup"><span data-stu-id="716e2-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="716e2-273">Essa diretiva `@model` permite o acesso ao filme que o controlador passou para a exibição.</span><span class="sxs-lookup"><span data-stu-id="716e2-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="716e2-274">O objeto `Model` é fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="716e2-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="716e2-275">Por exemplo, na exibição *Details.cshtml*, o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="716e2-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="716e2-276">Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="716e2-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="716e2-277">Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="716e2-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="716e2-278">Observe como o código cria um objeto `List` quando ele chama o método `View`.</span><span class="sxs-lookup"><span data-stu-id="716e2-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="716e2-279">O código passa esta lista `Movies` do método de ação `Index` para a exibição:</span><span class="sxs-lookup"><span data-stu-id="716e2-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="716e2-280">Quando o controlador de filmes foi criado, o scaffolding incluiu a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="716e2-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="716e2-281">A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="716e2-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="716e2-282">Por exemplo, na exibição *Index.cshtml*, o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:</span><span class="sxs-lookup"><span data-stu-id="716e2-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="716e2-283">Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="716e2-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="716e2-284">Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código.</span><span class="sxs-lookup"><span data-stu-id="716e2-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="716e2-285">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="716e2-285">Additional resources</span></span>

* [<span data-ttu-id="716e2-286">Auxiliares de marcação</span><span class="sxs-lookup"><span data-stu-id="716e2-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="716e2-287">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="716e2-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="716e2-288">[Anterior – Adicionando uma exibição](adding-view.md)
> [Próximo – Trabalhando com o SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="716e2-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="716e2-289">Adicionar uma classe de modelo de dados</span><span class="sxs-lookup"><span data-stu-id="716e2-289">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="716e2-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="716e2-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="716e2-291">Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="716e2-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="716e2-292">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="716e2-292">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="716e2-293">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="716e2-293">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="716e2-294">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="716e2-294">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="716e2-295">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="716e2-295">Scaffold the movie model</span></span>

<span data-ttu-id="716e2-296">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="716e2-296">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="716e2-297">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="716e2-297">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="716e2-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="716e2-298">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="716e2-299">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Controladores* **> Adicionar > Novo Item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="716e2-299">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![exibição da etapa acima](adding-model/_static/add_controller21.png)

<span data-ttu-id="716e2-301">Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="716e2-301">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="716e2-303">Preencha a caixa de diálogo **Adicionar Controlador**:</span><span class="sxs-lookup"><span data-stu-id="716e2-303">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="716e2-304">**Classe de modelo:** *filme (MvcMovie. Models)*</span><span class="sxs-lookup"><span data-stu-id="716e2-304">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="716e2-305">**Classe de contexto de dados:** selecione o ícone **+** e adicione o **MvcMovie.Models.MvcMovieContext** padrão</span><span class="sxs-lookup"><span data-stu-id="716e2-305">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Adicionar contexto de dados](adding-model/_static/dc.png)

* <span data-ttu-id="716e2-307">**Exibições:** mantenha o padrão de cada opção marcado</span><span class="sxs-lookup"><span data-stu-id="716e2-307">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="716e2-308">**Nome do controlador:** mantenha o *MoviesController* padrão</span><span class="sxs-lookup"><span data-stu-id="716e2-308">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="716e2-309">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="716e2-309">Select **Add**</span></span>

![Caixa de diálogo Adicionar Controlador](adding-model/_static/add_controller2.png)

<span data-ttu-id="716e2-311">O Visual Studio cria:</span><span class="sxs-lookup"><span data-stu-id="716e2-311">Visual Studio creates:</span></span>

* <span data-ttu-id="716e2-312">Uma [classe de contexto de banco de dados](xref:data/ef-mvc/intro#create-the-database-context) do Entity Framework Core (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="716e2-312">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="716e2-313">Um controlador de filmes (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="716e2-313">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="716e2-314">Arquivos de exibição do Razor para as páginas Criar, Excluir, Detalhes, Editar e Índice (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="716e2-314">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="716e2-315">A criação automática do contexto de banco de dados e das exibições e métodos de ação [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (criar, ler, atualizar e excluir) é conhecida como *scaffolding*.</span><span class="sxs-lookup"><span data-stu-id="716e2-315">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="716e2-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="716e2-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="716e2-317">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="716e2-317">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="716e2-318">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="716e2-318">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="716e2-319">No Linux, exporte o caminho da ferramenta Scaffold:</span><span class="sxs-lookup"><span data-stu-id="716e2-319">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="716e2-320">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="716e2-320">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="716e2-321">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="716e2-321">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="716e2-322">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="716e2-322">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="716e2-323">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="716e2-323">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="716e2-324">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="716e2-324">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="716e2-325">Se você executar o aplicativo e clicar no link **Filme do MVC**, receberá um erro semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="716e2-325">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="716e2-326">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="716e2-326">Visual Studio</span></span>](#tab/visual-studio)

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="716e2-327">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="716e2-327">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

``` error
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="716e2-328">Você precisa criar o banco de dados e usará o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para fazer isso.</span><span class="sxs-lookup"><span data-stu-id="716e2-328">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="716e2-329">As Migrações permitem criar um banco de dados que corresponde ao seu modelo de dados e atualizar o esquema de banco de dados quando o modelo de dados é alterado.</span><span class="sxs-lookup"><span data-stu-id="716e2-329">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="716e2-330">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="716e2-330">Initial migration</span></span>

<span data-ttu-id="716e2-331">Nesta seção, há estas tarefas:</span><span class="sxs-lookup"><span data-stu-id="716e2-331">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="716e2-332">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="716e2-332">Add an initial migration.</span></span>
* <span data-ttu-id="716e2-333">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="716e2-333">Update the database with the initial migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="716e2-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="716e2-334">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="716e2-335">No menu **ferramentas** , selecione **Gerenciador de pacotes NuGet** > **Package Manager Console** (PMC).</span><span class="sxs-lookup"><span data-stu-id="716e2-335">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="716e2-337">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="716e2-337">In the PMC, enter the following commands:</span></span>

   ```PMC
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="716e2-338">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="716e2-338">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="716e2-339">O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="716e2-339">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="716e2-340">O argumento `Initial` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="716e2-340">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="716e2-341">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="716e2-341">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="716e2-342">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="716e2-342">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="716e2-343">O comando `Update-Database` executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="716e2-343">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="716e2-344">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="716e2-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="716e2-345">O comando `ef migrations add InitialCreate` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="716e2-345">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="716e2-346">O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="716e2-346">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="716e2-347">O argumento `InitialCreate` é o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="716e2-347">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="716e2-348">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="716e2-348">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="716e2-349">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="716e2-349">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="716e2-350">O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="716e2-350">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="716e2-351">Os serviços (como o contexto de BD do EF Core) são registrados com a DI durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="716e2-351">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="716e2-352">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="716e2-352">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="716e2-353">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="716e2-353">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="716e2-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="716e2-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="716e2-355">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da DI.</span><span class="sxs-lookup"><span data-stu-id="716e2-355">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="716e2-356">Examine o seguinte método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="716e2-356">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="716e2-357">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="716e2-357">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="716e2-358">O `MvcMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="716e2-358">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="716e2-359">O contexto de dados (`MvcMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="716e2-359">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="716e2-360">O contexto de dados especifica quais entidades são incluídas no modelo de dados:</span><span class="sxs-lookup"><span data-stu-id="716e2-360">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="716e2-361">O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="716e2-361">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="716e2-362">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="716e2-362">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="716e2-363">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="716e2-363">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="716e2-364">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="716e2-364">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="716e2-365">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="716e2-365">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="716e2-366">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="716e2-366">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="716e2-367">Você criou um contexto de BD e o registrou no contêiner da DI.</span><span class="sxs-lookup"><span data-stu-id="716e2-367">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="716e2-368">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="716e2-368">Test the app</span></span>

* <span data-ttu-id="716e2-369">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="716e2-369">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="716e2-370">Se você receber uma exceção de banco de dados semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="716e2-370">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="716e2-371">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="716e2-371">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="716e2-372">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="716e2-372">Test the **Create** link.</span></span> <span data-ttu-id="716e2-373">Inserir e enviar dados.</span><span class="sxs-lookup"><span data-stu-id="716e2-373">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="716e2-374">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="716e2-374">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="716e2-375">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="716e2-375">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="716e2-376">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="716e2-376">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="716e2-377">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="716e2-377">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="716e2-378">Examine a classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="716e2-378">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="716e2-379">o código realçado acima mostra o contexto de banco de dados do filme que está sendo adicionado ao contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="716e2-379">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="716e2-380">`services.AddDbContext<MvcMovieContext>(options =>` especifica o banco de dados a ser usado e a cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="716e2-380">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="716e2-381">`=>` é um [operador lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="716e2-381">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="716e2-382">Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:</span><span class="sxs-lookup"><span data-stu-id="716e2-382">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="716e2-383">O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="716e2-383">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="716e2-384">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="716e2-384">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="716e2-385">Modelos fortemente tipados e a palavra-chave @model</span><span class="sxs-lookup"><span data-stu-id="716e2-385">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="716e2-386">Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="716e2-386">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="716e2-387">O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="716e2-387">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="716e2-388">O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="716e2-388">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="716e2-389">Essa abordagem fortemente tipada habilita uma melhor verificação do tempo de compilação do código.</span><span class="sxs-lookup"><span data-stu-id="716e2-389">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="716e2-390">O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições quando ele criou os métodos e as exibições.</span><span class="sxs-lookup"><span data-stu-id="716e2-390">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="716e2-391">Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="716e2-391">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="716e2-392">O parâmetro `id` geralmente é passado como dados de rota.</span><span class="sxs-lookup"><span data-stu-id="716e2-392">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="716e2-393">Por exemplo, `https://localhost:5001/movies/details/1` define:</span><span class="sxs-lookup"><span data-stu-id="716e2-393">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="716e2-394">O controlador para o controlador `movies` (o primeiro segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="716e2-394">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="716e2-395">A ação para `details` (o segundo segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="716e2-395">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="716e2-396">A ID como 1 (o último segmento de URL).</span><span class="sxs-lookup"><span data-stu-id="716e2-396">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="716e2-397">Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="716e2-397">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="716e2-398">O parâmetro `id` é definido como um [tipo que permite valor nulo](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), caso um valor de ID não seja fornecido.</span><span class="sxs-lookup"><span data-stu-id="716e2-398">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="716e2-399">Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.</span><span class="sxs-lookup"><span data-stu-id="716e2-399">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="716e2-400">Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:</span><span class="sxs-lookup"><span data-stu-id="716e2-400">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="716e2-401">Examine o conteúdo do arquivo *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="716e2-401">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="716e2-402">Incluindo uma instrução `@model` na parte superior do arquivo de exibição, você pode especificar o tipo de objeto esperado pela exibição.</span><span class="sxs-lookup"><span data-stu-id="716e2-402">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="716e2-403">Quando você criou o controlador de filmes, a seguinte instrução `@model` foi automaticamente incluída na parte superior do arquivo *Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="716e2-403">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="716e2-404">Esta diretiva `@model` permite acessar o filme que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="716e2-404">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="716e2-405">Por exemplo, na exibição *Details.cshtml*, o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="716e2-405">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="716e2-406">Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="716e2-406">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="716e2-407">Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="716e2-407">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="716e2-408">Observe como o código cria um objeto `List` quando ele chama o método `View`.</span><span class="sxs-lookup"><span data-stu-id="716e2-408">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="716e2-409">O código passa esta lista `Movies` do método de ação `Index` para a exibição:</span><span class="sxs-lookup"><span data-stu-id="716e2-409">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="716e2-410">Quando você criou o controlador de filmes, o scaffolding incluiu automaticamente a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="716e2-410">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="716e2-411">A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="716e2-411">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="716e2-412">Por exemplo, na exibição *Index.cshtml*, o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:</span><span class="sxs-lookup"><span data-stu-id="716e2-412">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="716e2-413">Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`.</span><span class="sxs-lookup"><span data-stu-id="716e2-413">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="716e2-414">Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código:</span><span class="sxs-lookup"><span data-stu-id="716e2-414">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="716e2-415">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="716e2-415">Additional resources</span></span>

* [<span data-ttu-id="716e2-416">Auxiliares de marcação</span><span class="sxs-lookup"><span data-stu-id="716e2-416">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="716e2-417">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="716e2-417">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="716e2-418">[Anterior, adicionando uma exibição](adding-view.md)
> [próxima trabalhando com um banco de dados](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="716e2-418">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
