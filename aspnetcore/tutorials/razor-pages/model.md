---
title: Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar classes de gerenciamento de filmes em um banco de dados usando o EF Core (Entity Framework Core).
ms.author: riande
ms.date: 07/22/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 6132f7b907014b4f57bb9ae0300e00b6ecb23f1a
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68820075"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="3388f-103">Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3388f-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="3388f-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3388f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3388f-105">Nesta seção, classes são adicionadas para o gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3388f-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="3388f-106">Essas classes são usadas com o [EF Core](/ef/core) (Entity Framework Core) para trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3388f-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="3388f-107">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="3388f-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="3388f-108">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="3388f-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="3388f-109">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3388f-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="3388f-110">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="3388f-110">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3388f-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3388f-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3388f-112">Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="3388f-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="3388f-113">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="3388f-113">Name the folder *Models*.</span></span>

<span data-ttu-id="3388f-114">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="3388f-114">Right click the *Models* folder.</span></span> <span data-ttu-id="3388f-115">Selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="3388f-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="3388f-116">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="3388f-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3388f-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3388f-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3388f-118">Adicione uma pasta chamada *Models*.</span><span class="sxs-lookup"><span data-stu-id="3388f-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="3388f-119">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="3388f-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3388f-120">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3388f-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3388f-121">No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e então selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="3388f-121">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="3388f-122">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="3388f-122">Name the folder *Models*.</span></span>
* <span data-ttu-id="3388f-123">Clique com o botão direito do mouse na pasta *Models* e selecione **Adicionar** > **Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="3388f-123">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="3388f-124">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="3388f-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="3388f-125">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="3388f-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="3388f-126">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="3388f-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="3388f-127">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="3388f-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="3388f-128">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="3388f-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="3388f-129">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="3388f-129">Scaffold the movie model</span></span>

<span data-ttu-id="3388f-130">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="3388f-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="3388f-131">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="3388f-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3388f-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3388f-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3388f-133">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="3388f-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="3388f-134">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="3388f-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="3388f-135">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="3388f-135">Name the folder *Movies*</span></span>

<span data-ttu-id="3388f-136">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="3388f-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="3388f-138">Na caixa de diálogo **Adicionar Scaffold**, selecione **Razor Pages usando o Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="3388f-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="3388f-140">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="3388f-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="3388f-141">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="3388f-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="3388f-142">Na linha **Classe de contexto de dados**, selecione o sinal de adição ( **+** ) e altere o nome gerado de RazorPagesMovie.**Models**.RazorPagesMovieContext para RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="3388f-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="3388f-143">[Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="3388f-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="3388f-144">Ela cria a classe de contexto do banco de dados com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="3388f-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="3388f-145">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="3388f-145">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/3/arp.png)

<span data-ttu-id="3388f-147">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="3388f-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3388f-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3388f-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="3388f-149">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="3388f-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="3388f-150">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="3388f-150">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator --version 3.0.0-*
   ```

* <span data-ttu-id="3388f-151">**para Windows**: Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3388f-151">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="3388f-152">**para macOS e Linux**: Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3388f-152">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3388f-153">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3388f-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3388f-154">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="3388f-154">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="3388f-155">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="3388f-155">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator --version 3.0.0-*
   ```

* <span data-ttu-id="3388f-156">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3388f-156">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="3388f-157">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="3388f-157">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="3388f-158">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="3388f-158">Files created</span></span>

* <span data-ttu-id="3388f-159">*Pages/Movies*: Criar, Excluir, Detalhes, Editar e Índice.</span><span class="sxs-lookup"><span data-stu-id="3388f-159">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3388f-160">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="3388f-160">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="3388f-161">Arquivo atualizado</span><span class="sxs-lookup"><span data-stu-id="3388f-161">File updated</span></span>

* <span data-ttu-id="3388f-162">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3388f-162">*Startup.cs*</span></span>

<span data-ttu-id="3388f-163">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="3388f-163">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="3388f-164">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="3388f-164">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3388f-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3388f-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3388f-166">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="3388f-166">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="3388f-167">Adicione uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="3388f-167">Add an initial migration.</span></span>
* <span data-ttu-id="3388f-168">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="3388f-168">Update the database with the initial migration.</span></span>

<span data-ttu-id="3388f-169">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="3388f-169">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="3388f-171">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="3388f-171">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3388f-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3388f-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3388f-173">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3388f-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="3388f-174">Os comandos anteriores geram o seguinte aviso: “Nenhum tipo foi especificado para a coluna decimal "Preço" no tipo de entidade "Filme".</span><span class="sxs-lookup"><span data-stu-id="3388f-174">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="3388f-175">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="3388f-175">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="3388f-176">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="3388f-176">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="3388f-177">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="3388f-177">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="3388f-178">O comando `ef migrations add InitialCreate` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="3388f-178">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="3388f-179">O esquema é baseado no modelo especificado no `DbContext` (no arquivo *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="3388f-179">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="3388f-180">O argumento `InitialCreate` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="3388f-180">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="3388f-181">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="3388f-181">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="3388f-182">O comando `ef database update` executa o método `Up` no arquivo *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="3388f-182">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="3388f-183">O método `Up` cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3388f-183">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3388f-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3388f-184">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="3388f-185">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="3388f-185">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="3388f-186">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3388f-186">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3388f-187">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3388f-187">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="3388f-188">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="3388f-188">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="3388f-189">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="3388f-189">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3388f-190">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="3388f-190">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="3388f-191">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3388f-191">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="3388f-192">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="3388f-192">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="3388f-193">O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="3388f-193">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="3388f-194">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="3388f-194">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="3388f-195">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="3388f-195">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3388f-196">O código anterior cria uma propriedade [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="3388f-196">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="3388f-197">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3388f-197">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="3388f-198">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="3388f-198">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3388f-199">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="3388f-199">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="3388f-200">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3388f-200">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3388f-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3388f-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3388f-202">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="3388f-202">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3388f-203">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3388f-203">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3388f-204">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="3388f-204">Examine the `Up` method.</span></span>

---

<span data-ttu-id="3388f-205">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="3388f-205">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="3388f-206">O esquema é baseado no modelo especificado no `RazorPagesMovieContext` (no arquivo *Data/RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="3388f-206">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="3388f-207">O argumento `Initial` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="3388f-207">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="3388f-208">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="3388f-208">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="3388f-209">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="3388f-209">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="3388f-210">O comando `Update-Database` executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3388f-210">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="3388f-211">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="3388f-211">Test the app</span></span>

* <span data-ttu-id="3388f-212">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="3388f-212">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="3388f-213">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="3388f-213">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="3388f-214">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="3388f-214">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="3388f-215">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3388f-215">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="3388f-217">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="3388f-217">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="3388f-218">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="3388f-218">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="3388f-219">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="3388f-219">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="3388f-220">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="3388f-220">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="3388f-221">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="3388f-221">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3388f-222">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3388f-222">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3388f-223">[Anterior: introdução](xref:tutorials/razor-pages/razor-pages-start)
> [Próximo: Razor Pages geradas por scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="3388f-223">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3388f-224">Nesta seção, classes são adicionadas para o gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3388f-224">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="3388f-225">Essas classes são usadas com o [EF Core](/ef/core) (Entity Framework Core) para trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3388f-225">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="3388f-226">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o código de acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="3388f-226">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="3388f-227">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="3388f-227">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="3388f-228">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3388f-228">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="3388f-229">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="3388f-229">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3388f-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3388f-230">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3388f-231">Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="3388f-231">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="3388f-232">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="3388f-232">Name the folder *Models*.</span></span>

<span data-ttu-id="3388f-233">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="3388f-233">Right click the *Models* folder.</span></span> <span data-ttu-id="3388f-234">Selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="3388f-234">Select **Add** > **Class**.</span></span> <span data-ttu-id="3388f-235">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="3388f-235">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3388f-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3388f-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3388f-237">Adicione uma pasta chamada *Models*.</span><span class="sxs-lookup"><span data-stu-id="3388f-237">Add a folder named *Models*.</span></span>
* <span data-ttu-id="3388f-238">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="3388f-238">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3388f-239">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3388f-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3388f-240">No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e então selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="3388f-240">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="3388f-241">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="3388f-241">Name the folder *Models*.</span></span>
* <span data-ttu-id="3388f-242">Clique com o botão direito do mouse na pasta *Models* e selecione **Adicionar** > **Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="3388f-242">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="3388f-243">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="3388f-243">In the **New File** dialog:</span></span>

  * <span data-ttu-id="3388f-244">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="3388f-244">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="3388f-245">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="3388f-245">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="3388f-246">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="3388f-246">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="3388f-247">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="3388f-247">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="3388f-248">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="3388f-248">Scaffold the movie model</span></span>

<span data-ttu-id="3388f-249">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="3388f-249">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="3388f-250">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="3388f-250">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3388f-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3388f-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3388f-252">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="3388f-252">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="3388f-253">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="3388f-253">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="3388f-254">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="3388f-254">Name the folder *Movies*</span></span>

<span data-ttu-id="3388f-255">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="3388f-255">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="3388f-257">Na caixa de diálogo **Adicionar Scaffold**, selecione **Razor Pages usando o Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="3388f-257">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="3388f-259">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="3388f-259">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="3388f-260">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="3388f-260">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="3388f-261">Na linha **Classe de contexto de dados**, selecione o sinal **+** (+) e aceite o nome gerado **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="3388f-261">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="3388f-262">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="3388f-262">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arp.png)

<span data-ttu-id="3388f-264">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="3388f-264">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3388f-265">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3388f-265">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="3388f-266">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="3388f-266">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="3388f-267">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="3388f-267">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="3388f-268">**para Windows**: Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3388f-268">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="3388f-269">**para macOS e Linux**: Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3388f-269">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3388f-270">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3388f-270">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3388f-271">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="3388f-271">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="3388f-272">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="3388f-272">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="3388f-273">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3388f-273">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="3388f-274">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="3388f-274">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="3388f-275">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="3388f-275">Files created</span></span>

* <span data-ttu-id="3388f-276">*Pages/Movies*: Criar, Excluir, Detalhes, Editar e Índice.</span><span class="sxs-lookup"><span data-stu-id="3388f-276">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="3388f-277">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="3388f-277">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="3388f-278">Arquivo atualizado</span><span class="sxs-lookup"><span data-stu-id="3388f-278">File updated</span></span>

* <span data-ttu-id="3388f-279">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="3388f-279">*Startup.cs*</span></span>

<span data-ttu-id="3388f-280">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="3388f-280">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="3388f-281">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="3388f-281">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3388f-282">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3388f-282">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3388f-283">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="3388f-283">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="3388f-284">Adicione uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="3388f-284">Add an initial migration.</span></span>
* <span data-ttu-id="3388f-285">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="3388f-285">Update the database with the initial migration.</span></span>

<span data-ttu-id="3388f-286">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="3388f-286">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="3388f-288">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="3388f-288">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3388f-289">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3388f-289">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3388f-290">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3388f-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="3388f-291">Os comandos anteriores geram o seguinte aviso: “Nenhum tipo foi especificado para a coluna decimal "Preço" no tipo de entidade "Filme".</span><span class="sxs-lookup"><span data-stu-id="3388f-291">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="3388f-292">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="3388f-292">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="3388f-293">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="3388f-293">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="3388f-294">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="3388f-294">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="3388f-295">O comando `ef migrations add InitialCreate` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="3388f-295">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="3388f-296">O esquema é baseado no modelo especificado no `DbContext` (no arquivo *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="3388f-296">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="3388f-297">O argumento `InitialCreate` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="3388f-297">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="3388f-298">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="3388f-298">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="3388f-299">O comando `ef database update` executa o método `Up` no arquivo *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="3388f-299">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="3388f-300">O método `Up` cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3388f-300">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3388f-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3388f-301">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="3388f-302">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="3388f-302">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="3388f-303">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3388f-303">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3388f-304">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3388f-304">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="3388f-305">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="3388f-305">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="3388f-306">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="3388f-306">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3388f-307">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="3388f-307">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="3388f-308">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3388f-308">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="3388f-309">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="3388f-309">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="3388f-310">O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="3388f-310">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="3388f-311">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="3388f-311">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="3388f-312">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="3388f-312">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="3388f-313">O código anterior cria uma propriedade [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="3388f-313">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="3388f-314">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3388f-314">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="3388f-315">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="3388f-315">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3388f-316">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="3388f-316">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="3388f-317">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3388f-317">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3388f-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3388f-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3388f-319">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="3388f-319">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3388f-320">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3388f-320">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3388f-321">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="3388f-321">Examine the `Up` method.</span></span>

---

<span data-ttu-id="3388f-322">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="3388f-322">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="3388f-323">O esquema é baseado no modelo especificado no `RazorPagesMovieContext` (no arquivo *Data/RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="3388f-323">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="3388f-324">O argumento `Initial` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="3388f-324">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="3388f-325">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="3388f-325">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="3388f-326">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="3388f-326">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="3388f-327">O comando `Update-Database` executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3388f-327">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="3388f-328">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="3388f-328">Test the app</span></span>

* <span data-ttu-id="3388f-329">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="3388f-329">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="3388f-330">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="3388f-330">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="3388f-331">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="3388f-331">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="3388f-332">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3388f-332">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="3388f-334">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="3388f-334">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="3388f-335">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="3388f-335">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="3388f-336">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="3388f-336">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="3388f-337">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="3388f-337">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="3388f-338">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="3388f-338">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3388f-339">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3388f-339">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3388f-340">[Anterior: introdução](xref:tutorials/razor-pages/razor-pages-start)
> [Próximo: Razor Pages geradas por scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="3388f-340">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
