---
title: Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar classes de gerenciamento de filmes em um banco de dados usando o EF Core (Entity Framework Core).
ms.author: riande
monikerRange: '>= aspnetcore-2.2'
ms.date: 12/3/2018
uid: tutorials/razor-pages/model
ms.openlocfilehash: 5693fb833d19375ef53e0f367bc6064e239b01d3
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444098"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="c42be-103">Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c42be-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="c42be-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c42be-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="c42be-105">Nesta seção, classes são adicionadas para o gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c42be-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="c42be-106">Essas classes são usadas com o [EF Core](/ef/core) (Entity Framework Core) para trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c42be-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="c42be-107">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o código de acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="c42be-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="c42be-108">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="c42be-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="c42be-109">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c42be-109">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="c42be-110">[Exiba ou baixe](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) a amostra.</span><span class="sxs-lookup"><span data-stu-id="c42be-110">[View or download](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) sample.</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="c42be-111">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="c42be-111">Add a data model</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c42be-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c42be-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c42be-113">Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="c42be-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="c42be-114">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="c42be-114">Name the folder *Models*.</span></span>

<span data-ttu-id="c42be-115">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="c42be-115">Right click the *Models* folder.</span></span> <span data-ttu-id="c42be-116">Selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="c42be-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="c42be-117">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="c42be-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c42be-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c42be-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="c42be-119">Adicione uma pasta chamada *Models*.</span><span class="sxs-lookup"><span data-stu-id="c42be-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="c42be-120">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="c42be-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c42be-121">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c42be-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c42be-122">No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e então selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="c42be-122">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="c42be-123">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="c42be-123">Name the folder *Models*.</span></span>
* <span data-ttu-id="c42be-124">Clique com o botão direito do mouse na pasta *Modelos* e, em seguida, selecione **Adicionar** > **Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="c42be-124">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="c42be-125">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="c42be-125">In the **New File** dialog:</span></span>

  * <span data-ttu-id="c42be-126">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="c42be-126">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="c42be-127">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="c42be-127">Select **Empty Class** in the center pain.</span></span>
  * <span data-ttu-id="c42be-128">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="c42be-128">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<!-- End of VS tabs -->

---

<span data-ttu-id="c42be-129">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="c42be-129">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="c42be-130">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="c42be-130">Scaffold the movie model</span></span>

<span data-ttu-id="c42be-131">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="c42be-131">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="c42be-132">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="c42be-132">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c42be-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c42be-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c42be-134">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="c42be-134">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="c42be-135">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="c42be-135">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="c42be-136">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="c42be-136">Name the folder *Movies*</span></span>

<span data-ttu-id="c42be-137">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo Item Gerado por Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="c42be-137">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="c42be-139">Na caixa de diálogo **Adicionar Scaffold**, selecione **Razor Pages usando o Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="c42be-139">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="c42be-141">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="c42be-141">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="c42be-142">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="c42be-142">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="c42be-143">Na linha **Classe de contexto de dados**, selecione o sinal **+** (+) e aceite o nome gerado **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="c42be-143">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="c42be-144">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="c42be-144">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arp.png)

<span data-ttu-id="c42be-146">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="c42be-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c42be-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c42be-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="c42be-148">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="c42be-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="c42be-149">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="c42be-149">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="c42be-150">**para Windows**: Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="c42be-150">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="c42be-151">**para macOS e Linux**: Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="c42be-151">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c42be-152">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c42be-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c42be-153">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="c42be-153">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="c42be-154">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="c42be-154">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```
* <span data-ttu-id="c42be-155">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="c42be-155">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="c42be-156">Os comandos anteriores geram o seguinte aviso: “Nenhum tipo foi especificado para a coluna decimal "Preço" no tipo de entidade "Filme".</span><span class="sxs-lookup"><span data-stu-id="c42be-156">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="c42be-157">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="c42be-157">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="c42be-158">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="c42be-158">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="c42be-159">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="c42be-159">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="c42be-160">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="c42be-160">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="c42be-161">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="c42be-161">Files created</span></span>

* <span data-ttu-id="c42be-162">*Pages/Movies*: Criar, Excluir, Detalhes, Editar e Índice.</span><span class="sxs-lookup"><span data-stu-id="c42be-162">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="c42be-163">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="c42be-163">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="c42be-164">Arquivo atualizado</span><span class="sxs-lookup"><span data-stu-id="c42be-164">File updated</span></span>

* <span data-ttu-id="c42be-165">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="c42be-165">*Startup.cs*</span></span>

<span data-ttu-id="c42be-166">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="c42be-166">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="c42be-167">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="c42be-167">Initial migration</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c42be-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c42be-168">Visual Studio</span></span>](#tab/visual-studio)

<!-- VS -------------------------->

<span data-ttu-id="c42be-169">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="c42be-169">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="c42be-170">Adicione uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="c42be-170">Add an initial migration.</span></span>
* <span data-ttu-id="c42be-171">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="c42be-171">Update the database with the initial migration.</span></span>

<span data-ttu-id="c42be-172">No menu **Ferramentas**, selecione **Gerenciador de pacotes NuGet** > **Console do Gerenciador de pacotes**.</span><span class="sxs-lookup"><span data-stu-id="c42be-172">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="c42be-174">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="c42be-174">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c42be-175">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c42be-175">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- Mac -------------------------->

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c42be-176">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c42be-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---  
<!-- End of VS tabs -->

<span data-ttu-id="c42be-177">O comando `ef migrations add InitialCreate` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="c42be-177">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="c42be-178">O esquema é baseado no modelo especificado no `DbContext` (no arquivo *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="c42be-178">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="c42be-179">O argumento `InitialCreate` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="c42be-179">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="c42be-180">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="c42be-180">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="c42be-181">O comando `ef database update` executa o método `Up` no arquivo *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="c42be-181">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="c42be-182">O método `Up` cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c42be-182">The `Up` method creates the database.</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c42be-183">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c42be-183">Visual Studio</span></span>](#tab/visual-studio)

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="c42be-184">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="c42be-184">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="c42be-185">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c42be-185">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c42be-186">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c42be-186">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="c42be-187">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="c42be-187">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="c42be-188">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="c42be-188">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="c42be-189">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="c42be-189">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="c42be-190">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c42be-190">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c42be-191">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="c42be-191">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="c42be-192">O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="c42be-192">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="c42be-193">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="c42be-193">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="c42be-194">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="c42be-194">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="c42be-195">O código anterior cria uma propriedade [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="c42be-195">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="c42be-196">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c42be-196">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="c42be-197">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="c42be-197">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="c42be-198">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="c42be-198">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="c42be-199">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c42be-199">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>
<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c42be-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c42be-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c42be-201">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="c42be-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<!-- End of VS tabs -->

---

<span data-ttu-id="c42be-202">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="c42be-202">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="c42be-203">O esquema é baseado no modelo especificado no `RazorPagesMovieContext` (no arquivo *Data/RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="c42be-203">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="c42be-204">O argumento `Initial` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="c42be-204">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="c42be-205">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="c42be-205">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="c42be-206">Consulte [Introdução às migrações](xref:data/ef-mvc/migrations#introduction-to-migrations) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="c42be-206">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="c42be-207">O comando `Update-Database` executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="c42be-207">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="c42be-208">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="c42be-208">Test the app</span></span>

* <span data-ttu-id="c42be-209">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="c42be-209">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="c42be-210">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="c42be-210">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="c42be-211">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="c42be-211">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="c42be-212">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="c42be-212">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)
  
  > [!NOTE]
  > <span data-ttu-id="c42be-214">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="c42be-214">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="c42be-215">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="c42be-215">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="c42be-216">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="c42be-216">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="c42be-217">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="c42be-217">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="c42be-218">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="c42be-218">The next tutorial explains the files created by scaffolding.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="c42be-219">[Anterior: introdução](xref:tutorials/razor-pages/razor-pages-start)
> [Próximo: Razor Pages geradas por scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="c42be-219">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>
