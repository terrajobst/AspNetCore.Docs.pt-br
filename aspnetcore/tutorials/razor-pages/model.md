---
title: Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar classes de gerenciamento de filmes em um banco de dados usando o EF Core (Entity Framework Core).
ms.author: riande
ms.date: 9/22/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: dcbcf37dfd95d784ebe249ec6e9e4184a8853d3d
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187196"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="46011-103">Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="46011-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="46011-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="46011-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="46011-105">Nesta seção, classes são adicionadas para o gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="46011-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="46011-106">Essas classes são usadas com o [EF Core](/ef/core) (Entity Framework Core) para trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="46011-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="46011-107">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="46011-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="46011-108">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="46011-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="46011-109">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="46011-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="46011-110">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="46011-110">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="46011-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46011-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46011-112">Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="46011-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="46011-113">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="46011-113">Name the folder *Models*.</span></span>

<span data-ttu-id="46011-114">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="46011-114">Right click the *Models* folder.</span></span> <span data-ttu-id="46011-115">Selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="46011-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="46011-116">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="46011-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="46011-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46011-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="46011-118">Adicione uma pasta chamada *Models*.</span><span class="sxs-lookup"><span data-stu-id="46011-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="46011-119">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="46011-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="46011-120">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="46011-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="46011-121">No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e então selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="46011-121">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="46011-122">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="46011-122">Name the folder *Models*.</span></span>
* <span data-ttu-id="46011-123">Clique com o botão direito do mouse na pasta *Models* e selecione **Adicionar** > **Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="46011-123">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="46011-124">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="46011-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="46011-125">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="46011-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="46011-126">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="46011-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="46011-127">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="46011-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="46011-128">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="46011-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="46011-129">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="46011-129">Scaffold the movie model</span></span>

<span data-ttu-id="46011-130">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="46011-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="46011-131">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="46011-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="46011-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46011-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46011-133">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="46011-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="46011-134">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="46011-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="46011-135">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="46011-135">Name the folder *Movies*</span></span>

<span data-ttu-id="46011-136">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="46011-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="46011-138">Na caixa de diálogo **Adicionar Scaffold**, selecione **Razor Pages usando o Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="46011-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="46011-140">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="46011-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="46011-141">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="46011-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="46011-142">Na linha **Classe de contexto de dados**, selecione o sinal de adição ( **+** ) e altere o nome gerado de RazorPagesMovie.**Models**.RazorPagesMovieContext para RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="46011-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="46011-143">[Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="46011-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="46011-144">Ela cria a classe de contexto do banco de dados com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="46011-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="46011-145">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="46011-145">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/3/arp.png)

<span data-ttu-id="46011-147">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="46011-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="46011-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46011-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="46011-149">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="46011-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="46011-150">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="46011-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="46011-151">**para Windows**: Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="46011-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="46011-152">**para macOS e Linux**: Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="46011-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="46011-153">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="46011-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="46011-154">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="46011-154">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="46011-155">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="46011-155">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="46011-156">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="46011-156">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

### <a name="files-created"></a><span data-ttu-id="46011-157">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="46011-157">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="46011-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46011-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46011-159">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="46011-159">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="46011-160">*Pages/Movies*: Criar, Excluir, Detalhes, Editar e Índice.</span><span class="sxs-lookup"><span data-stu-id="46011-160">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="46011-161">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="46011-161">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="46011-162">Atualizado</span><span class="sxs-lookup"><span data-stu-id="46011-162">Updated</span></span>

* <span data-ttu-id="46011-163">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="46011-163">*Startup.cs*</span></span>

<span data-ttu-id="46011-164">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="46011-164">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="46011-165">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="46011-165">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="46011-166">O processo de scaffold cria os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="46011-166">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="46011-167">*Pages/Movies*: Criar, Excluir, Detalhes, Editar e Índice.</span><span class="sxs-lookup"><span data-stu-id="46011-167">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="46011-168">Os arquivos criados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="46011-168">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="46011-169">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="46011-169">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="46011-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46011-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46011-171">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="46011-171">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="46011-172">Adicione uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="46011-172">Add an initial migration.</span></span>
* <span data-ttu-id="46011-173">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="46011-173">Update the database with the initial migration.</span></span>

<span data-ttu-id="46011-174">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="46011-174">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="46011-176">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="46011-176">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="46011-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46011-177">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="46011-178">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="46011-178">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="46011-179">Os comandos anteriores geram o seguinte aviso: “Nenhum tipo foi especificado para a coluna decimal "Preço" no tipo de entidade "Filme".</span><span class="sxs-lookup"><span data-stu-id="46011-179">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="46011-180">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="46011-180">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="46011-181">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="46011-181">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="46011-182">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="46011-182">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="46011-183">O comando `ef migrations add InitialCreate` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="46011-183">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="46011-184">O esquema é baseado no modelo especificado no `DbContext` (no arquivo *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="46011-184">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="46011-185">O argumento `InitialCreate` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="46011-185">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="46011-186">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="46011-186">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="46011-187">O comando `ef database update` executa o método `Up` no arquivo *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="46011-187">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="46011-188">O método `Up` cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="46011-188">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="46011-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46011-189">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="46011-190">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="46011-190">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="46011-191">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="46011-191">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="46011-192">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="46011-192">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="46011-193">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="46011-193">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="46011-194">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="46011-194">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="46011-195">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="46011-195">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="46011-196">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="46011-196">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="46011-197">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="46011-197">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="46011-198">O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="46011-198">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="46011-199">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="46011-199">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="46011-200">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="46011-200">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="46011-201">O código anterior cria uma propriedade [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="46011-201">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="46011-202">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="46011-202">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="46011-203">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="46011-203">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="46011-204">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="46011-204">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="46011-205">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="46011-205">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="46011-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46011-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="46011-207">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="46011-207">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="46011-208">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="46011-208">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="46011-209">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="46011-209">Examine the `Up` method.</span></span>

---

<span data-ttu-id="46011-210">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="46011-210">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="46011-211">O esquema é baseado no modelo especificado no `RazorPagesMovieContext` (no arquivo *Data/RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="46011-211">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="46011-212">O argumento `Initial` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="46011-212">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="46011-213">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="46011-213">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="46011-214">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="46011-214">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="46011-215">O comando `Update-Database` executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="46011-215">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="46011-216">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="46011-216">Test the app</span></span>

* <span data-ttu-id="46011-217">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="46011-217">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="46011-218">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="46011-218">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="46011-219">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="46011-219">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="46011-220">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="46011-220">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="46011-222">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="46011-222">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="46011-223">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="46011-223">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="46011-224">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="46011-224">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="46011-225">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="46011-225">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="46011-226">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="46011-226">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="46011-227">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="46011-227">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="46011-228">[Anterior: introdução](xref:tutorials/razor-pages/razor-pages-start)
> [Próximo: Razor Pages geradas por scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="46011-228">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="46011-229">Nesta seção, classes são adicionadas para o gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="46011-229">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="46011-230">Essas classes são usadas com o [EF Core](/ef/core) (Entity Framework Core) para trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="46011-230">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="46011-231">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o código de acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="46011-231">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="46011-232">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="46011-232">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="46011-233">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="46011-233">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="46011-234">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="46011-234">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="46011-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46011-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46011-236">Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="46011-236">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="46011-237">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="46011-237">Name the folder *Models*.</span></span>

<span data-ttu-id="46011-238">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="46011-238">Right click the *Models* folder.</span></span> <span data-ttu-id="46011-239">Selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="46011-239">Select **Add** > **Class**.</span></span> <span data-ttu-id="46011-240">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="46011-240">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="46011-241">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46011-241">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="46011-242">Adicione uma pasta chamada *Models*.</span><span class="sxs-lookup"><span data-stu-id="46011-242">Add a folder named *Models*.</span></span>
* <span data-ttu-id="46011-243">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="46011-243">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="46011-244">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="46011-244">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="46011-245">No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e então selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="46011-245">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="46011-246">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="46011-246">Name the folder *Models*.</span></span>
* <span data-ttu-id="46011-247">Clique com o botão direito do mouse na pasta *Models* e selecione **Adicionar** > **Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="46011-247">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="46011-248">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="46011-248">In the **New File** dialog:</span></span>

  * <span data-ttu-id="46011-249">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="46011-249">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="46011-250">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="46011-250">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="46011-251">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="46011-251">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="46011-252">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="46011-252">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="46011-253">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="46011-253">Scaffold the movie model</span></span>

<span data-ttu-id="46011-254">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="46011-254">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="46011-255">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="46011-255">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="46011-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46011-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46011-257">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="46011-257">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="46011-258">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="46011-258">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="46011-259">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="46011-259">Name the folder *Movies*</span></span>

<span data-ttu-id="46011-260">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="46011-260">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="46011-262">Na caixa de diálogo **Adicionar Scaffold**, selecione **Razor Pages usando o Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="46011-262">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="46011-264">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="46011-264">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="46011-265">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="46011-265">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="46011-266">Na linha **Classe de contexto de dados**, selecione o sinal **+** (+) e aceite o nome gerado **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="46011-266">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="46011-267">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="46011-267">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arp.png)

<span data-ttu-id="46011-269">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="46011-269">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="46011-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46011-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="46011-271">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="46011-271">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="46011-272">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="46011-272">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="46011-273">**para Windows**: Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="46011-273">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="46011-274">**para macOS e Linux**: Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="46011-274">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="46011-275">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="46011-275">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="46011-276">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="46011-276">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="46011-277">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="46011-277">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="46011-278">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="46011-278">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="46011-279">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="46011-279">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="46011-280">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="46011-280">Files created</span></span>

* <span data-ttu-id="46011-281">*Pages/Movies*: Criar, Excluir, Detalhes, Editar e Índice.</span><span class="sxs-lookup"><span data-stu-id="46011-281">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="46011-282">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="46011-282">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="46011-283">Arquivo atualizado</span><span class="sxs-lookup"><span data-stu-id="46011-283">File updated</span></span>

* <span data-ttu-id="46011-284">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="46011-284">*Startup.cs*</span></span>

<span data-ttu-id="46011-285">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="46011-285">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="46011-286">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="46011-286">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="46011-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46011-287">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="46011-288">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="46011-288">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="46011-289">Adicione uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="46011-289">Add an initial migration.</span></span>
* <span data-ttu-id="46011-290">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="46011-290">Update the database with the initial migration.</span></span>

<span data-ttu-id="46011-291">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="46011-291">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="46011-293">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="46011-293">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="46011-294">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46011-294">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="46011-295">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="46011-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="46011-296">Os comandos anteriores geram o seguinte aviso: “Nenhum tipo foi especificado para a coluna decimal "Preço" no tipo de entidade "Filme".</span><span class="sxs-lookup"><span data-stu-id="46011-296">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="46011-297">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="46011-297">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="46011-298">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="46011-298">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="46011-299">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="46011-299">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="46011-300">O comando `ef migrations add InitialCreate` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="46011-300">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="46011-301">O esquema é baseado no modelo especificado no `DbContext` (no arquivo *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="46011-301">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="46011-302">O argumento `InitialCreate` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="46011-302">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="46011-303">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="46011-303">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="46011-304">O comando `ef database update` executa o método `Up` no arquivo *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="46011-304">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="46011-305">O método `Up` cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="46011-305">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="46011-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46011-306">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="46011-307">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="46011-307">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="46011-308">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="46011-308">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="46011-309">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="46011-309">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="46011-310">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="46011-310">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="46011-311">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="46011-311">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="46011-312">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="46011-312">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="46011-313">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="46011-313">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="46011-314">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="46011-314">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="46011-315">O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="46011-315">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="46011-316">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="46011-316">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="46011-317">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="46011-317">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="46011-318">O código anterior cria uma propriedade [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="46011-318">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="46011-319">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="46011-319">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="46011-320">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="46011-320">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="46011-321">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="46011-321">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="46011-322">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="46011-322">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="46011-323">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="46011-323">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="46011-324">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="46011-324">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="46011-325">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="46011-325">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="46011-326">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="46011-326">Examine the `Up` method.</span></span>

---

<span data-ttu-id="46011-327">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="46011-327">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="46011-328">O esquema é baseado no modelo especificado no `RazorPagesMovieContext` (no arquivo *Data/RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="46011-328">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="46011-329">O argumento `Initial` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="46011-329">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="46011-330">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="46011-330">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="46011-331">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="46011-331">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="46011-332">O comando `Update-Database` executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="46011-332">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="46011-333">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="46011-333">Test the app</span></span>

* <span data-ttu-id="46011-334">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="46011-334">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="46011-335">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="46011-335">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="46011-336">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="46011-336">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="46011-337">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="46011-337">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="46011-339">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="46011-339">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="46011-340">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="46011-340">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="46011-341">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="46011-341">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="46011-342">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="46011-342">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="46011-343">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="46011-343">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="46011-344">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="46011-344">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="46011-345">[Anterior: introdução](xref:tutorials/razor-pages/razor-pages-start)
> [Próximo: Razor Pages geradas por scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="46011-345">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
