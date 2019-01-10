---
title: Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar classes de gerenciamento de filmes em um banco de dados usando o EF Core (Entity Framework Core).
ms.author: riande
monikerRange: '>= aspnetcore-2.2'
ms.date: 12/3/2018
uid: tutorials/razor-pages/model
ms.openlocfilehash: 0915c525d5fb96a3d32f91fbd65a4e1f62ee28b8
ms.sourcegitcommit: 68a3081dd175d6518d1bfa31b4712bd8a2dd3864
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53577858"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="826cc-103">Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="826cc-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="826cc-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="826cc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="826cc-105">Nesta seção, classes são adicionadas para o gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="826cc-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="826cc-106">Essas classes são usadas com o [EF Core](/ef/core) (Entity Framework Core) para trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="826cc-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="826cc-107">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o código de acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="826cc-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="826cc-108">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="826cc-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="826cc-109">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="826cc-109">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="826cc-110">[Exiba ou baixe](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages-start/sample/) a amostra.</span><span class="sxs-lookup"><span data-stu-id="826cc-110">[View or download](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages-start/sample/) sample.</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="826cc-111">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="826cc-111">Add a data model</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="826cc-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="826cc-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="826cc-113">Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="826cc-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="826cc-114">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="826cc-114">Name the folder *Models*.</span></span>

<span data-ttu-id="826cc-115">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="826cc-115">Right click the *Models* folder.</span></span> <span data-ttu-id="826cc-116">Selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="826cc-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="826cc-117">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="826cc-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="826cc-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="826cc-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="826cc-119">Adicione uma pasta chamada *Models*.</span><span class="sxs-lookup"><span data-stu-id="826cc-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="826cc-120">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="826cc-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="826cc-121">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="826cc-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="826cc-122">No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e então selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="826cc-122">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="826cc-123">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="826cc-123">Name the folder *Models*.</span></span>
* <span data-ttu-id="826cc-124">Clique com o botão direito do mouse na pasta *Modelos* e, em seguida, selecione **Adicionar** > **Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="826cc-124">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="826cc-125">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="826cc-125">In the **New File** dialog:</span></span>

  * <span data-ttu-id="826cc-126">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="826cc-126">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="826cc-127">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="826cc-127">Select **Empty Class** in the center pain.</span></span>
  * <span data-ttu-id="826cc-128">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="826cc-128">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<!-- End of VS tabs -->

---

<span data-ttu-id="826cc-129">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="826cc-129">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="826cc-130">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="826cc-130">Scaffold the movie model</span></span>

<span data-ttu-id="826cc-131">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="826cc-131">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="826cc-132">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="826cc-132">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="826cc-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="826cc-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="826cc-134">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="826cc-134">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="826cc-135">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="826cc-135">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="826cc-136">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="826cc-136">Name the folder *Movies*</span></span>

<span data-ttu-id="826cc-137">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo Item Gerado por Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="826cc-137">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="826cc-139">Na caixa de diálogo **Adicionar Scaffold**, selecione **Razor Pages usando o Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="826cc-139">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="826cc-141">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="826cc-141">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="826cc-142">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="826cc-142">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="826cc-143">Na linha **Classe de contexto de dados**, selecione o sinal **+** (+) e aceite o nome gerado **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="826cc-143">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="826cc-144">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="826cc-144">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arp.png)

<span data-ttu-id="826cc-146">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="826cc-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="826cc-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="826cc-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="826cc-148">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="826cc-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="826cc-149">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="826cc-149">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="826cc-150">**para Windows**: Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="826cc-150">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="826cc-151">**para macOS e Linux**: Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="826cc-151">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="826cc-152">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="826cc-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="826cc-153">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="826cc-153">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="826cc-154">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="826cc-154">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```
* <span data-ttu-id="826cc-155">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="826cc-155">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="826cc-156">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="826cc-156">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="826cc-157">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="826cc-157">Files created</span></span>

* <span data-ttu-id="826cc-158">*Pages/Movies*: Criar, Excluir, Detalhes, Editar e Índice.</span><span class="sxs-lookup"><span data-stu-id="826cc-158">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="826cc-159">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="826cc-159">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="826cc-160">Arquivo atualizado</span><span class="sxs-lookup"><span data-stu-id="826cc-160">File updated</span></span>

* <span data-ttu-id="826cc-161">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="826cc-161">*Startup.cs*</span></span>

<span data-ttu-id="826cc-162">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="826cc-162">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="826cc-163">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="826cc-163">Initial migration</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="826cc-164">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="826cc-164">Visual Studio</span></span>](#tab/visual-studio)

<!-- VS -------------------------->

<span data-ttu-id="826cc-165">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="826cc-165">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="826cc-166">Adicione uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="826cc-166">Add an initial migration.</span></span>
* <span data-ttu-id="826cc-167">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="826cc-167">Update the database with the initial migration.</span></span>

<span data-ttu-id="826cc-168">No menu **Ferramentas**, selecione **Gerenciador de pacotes NuGet** > **Console do Gerenciador de pacotes**.</span><span class="sxs-lookup"><span data-stu-id="826cc-168">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="826cc-170">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="826cc-170">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="826cc-171">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="826cc-171">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- Mac -------------------------->

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="826cc-172">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="826cc-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---  
<!-- End of VS tabs -->

<span data-ttu-id="826cc-173">O comando `ef migrations add InitialCreate` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="826cc-173">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="826cc-174">O esquema é baseado no modelo especificado no `DbContext` (no arquivo *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="826cc-174">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="826cc-175">O argumento `InitialCreate` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="826cc-175">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="826cc-176">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="826cc-176">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="826cc-177">O comando `ef database update` executa o método `Up` no arquivo *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="826cc-177">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="826cc-178">O método `Up` cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="826cc-178">The `Up` method creates the database.</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="826cc-179">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="826cc-179">Visual Studio</span></span>](#tab/visual-studio)

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="826cc-180">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="826cc-180">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="826cc-181">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="826cc-181">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="826cc-182">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="826cc-182">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="826cc-183">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="826cc-183">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="826cc-184">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="826cc-184">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="826cc-185">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="826cc-185">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="826cc-186">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="826cc-186">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="826cc-187">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="826cc-187">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="826cc-188">O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="826cc-188">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="826cc-189">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="826cc-189">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="826cc-190">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="826cc-190">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="826cc-191">O código anterior cria uma propriedade [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="826cc-191">The preceding code creates a [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="826cc-192">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="826cc-192">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="826cc-193">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="826cc-193">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="826cc-194">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="826cc-194">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="826cc-195">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="826cc-195">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>
<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="826cc-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="826cc-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="826cc-197">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="826cc-197">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<!-- End of VS tabs -->

---

<span data-ttu-id="826cc-198">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="826cc-198">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="826cc-199">O esquema é baseado no modelo especificado no `RazorPagesMovieContext` (no arquivo *Data/RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="826cc-199">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="826cc-200">O argumento `Initial` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="826cc-200">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="826cc-201">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="826cc-201">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="826cc-202">Consulte [Introdução às migrações](xref:data/ef-mvc/migrations#introduction-to-migrations) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="826cc-202">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="826cc-203">O comando `Update-Database` executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="826cc-203">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="826cc-204">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="826cc-204">Test the app</span></span>

* <span data-ttu-id="826cc-205">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="826cc-205">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="826cc-206">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="826cc-206">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="826cc-207">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="826cc-207">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="826cc-208">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="826cc-208">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)
  
  > [!NOTE]
  > <span data-ttu-id="826cc-210">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="826cc-210">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="826cc-211">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="826cc-211">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="826cc-212">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="826cc-212">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="826cc-213">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="826cc-213">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="826cc-214">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="826cc-214">The next tutorial explains the files created by scaffolding.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="826cc-215">[Anterior: introdução](xref:tutorials/razor-pages/razor-pages-start)
> [Próximo: Razor Pages geradas por scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="826cc-215">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>
