---
title: Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar classes de gerenciamento de filmes em um banco de dados usando o EF Core (Entity Framework Core).
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 95b6d3e016edcd2e13207c8e658cf0d2fb21f945
ms.sourcegitcommit: 4e3edff24ba6e43a103fee1b126c9826241bb37b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74959068"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="7c4e5-103">Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c4e5-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="7c4e5-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7c4e5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7c4e5-105">Nesta seção, as classes são adicionadas para gerenciar filmes em um banco de [dados SQLite](https://www.sqlite.org/index.html)de plataforma cruzada.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="7c4e5-106">Os aplicativos criados por meio de um modelo de ASP.NET Core usam um banco de dados SQLite.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="7c4e5-107">As classes de modelo do aplicativo são usadas com [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core provedor de banco de dados](/ef/core/providers/sqlite)) para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="7c4e5-108">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="7c4e5-109">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="7c4e5-110">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="7c4e5-111">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="7c4e5-111">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c4e5-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c4e5-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c4e5-113">Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="7c4e5-114">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-114">Name the folder *Models*.</span></span>

<span data-ttu-id="7c4e5-115">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-115">Right click the *Models* folder.</span></span> <span data-ttu-id="7c4e5-116">Selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="7c4e5-117">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7c4e5-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c4e5-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7c4e5-119">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="7c4e5-120">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7c4e5-121">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c4e5-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7c4e5-122">No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e então selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-122">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="7c4e5-123">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-123">Name the folder *Models*.</span></span>
* <span data-ttu-id="7c4e5-124">Clique com o botão direito do mouse na pasta *Models* e selecione **Adicionar** > **Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-124">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="7c4e5-125">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-125">In the **New File** dialog:</span></span>

  * <span data-ttu-id="7c4e5-126">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-126">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="7c4e5-127">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-127">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="7c4e5-128">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-128">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="7c4e5-129">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-129">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="7c4e5-130">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="7c4e5-130">Scaffold the movie model</span></span>

<span data-ttu-id="7c4e5-131">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-131">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="7c4e5-132">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-132">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c4e5-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c4e5-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c4e5-134">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-134">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="7c4e5-135">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-135">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="7c4e5-136">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="7c4e5-136">Name the folder *Movies*</span></span>

<span data-ttu-id="7c4e5-137">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-137">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="7c4e5-139">Na caixa de diálogo **Adicionar Scaffold**, selecione **Razor Pages usando o Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-139">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="7c4e5-141">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="7c4e5-141">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="7c4e5-142">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="7c4e5-142">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="7c4e5-143">Na linha **Classe de contexto de dados**, selecione o sinal de adição ( **+** ) e altere o nome gerado de RazorPagesMovie.**Models**.RazorPagesMovieContext para RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-143">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="7c4e5-144">[Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-144">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="7c4e5-145">Ela cria a classe de contexto do banco de dados com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-145">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="7c4e5-146">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-146">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/3/arp.png)

<span data-ttu-id="7c4e5-148">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-148">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7c4e5-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c4e5-149">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="7c4e5-150">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-150">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="7c4e5-151">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-151">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="7c4e5-152">**No Windows**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-152">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="7c4e5-153">**No macOS e Linux**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-153">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7c4e5-154">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c4e5-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7c4e5-155">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-155">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="7c4e5-156">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-156">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="7c4e5-157">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-157">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

---

### <a name="files-created"></a><span data-ttu-id="7c4e5-158">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="7c4e5-158">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c4e5-159">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c4e5-159">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c4e5-160">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-160">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="7c4e5-161">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-161">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="7c4e5-162">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="7c4e5-162">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="7c4e5-163">Atualizado</span><span class="sxs-lookup"><span data-stu-id="7c4e5-163">Updated</span></span>

* <span data-ttu-id="7c4e5-164">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="7c4e5-164">*Startup.cs*</span></span>

<span data-ttu-id="7c4e5-165">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-165">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="7c4e5-166">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c4e5-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7c4e5-167">O processo de scaffold cria os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-167">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="7c4e5-168">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-168">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="7c4e5-169">Os arquivos criados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-169">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="7c4e5-170">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="7c4e5-170">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c4e5-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c4e5-171">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c4e5-172">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-172">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="7c4e5-173">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-173">Add an initial migration.</span></span>
* <span data-ttu-id="7c4e5-174">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-174">Update the database with the initial migration.</span></span>

<span data-ttu-id="7c4e5-175">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-175">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="7c4e5-177">No PMC, digite os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-177">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7c4e5-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c4e5-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7c4e5-179">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c4e5-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="7c4e5-180">Os comandos anteriores geram o seguinte aviso: "nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-180">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="7c4e5-181">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-181">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="7c4e5-182">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="7c4e5-182">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="7c4e5-183">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-183">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="7c4e5-184">O comando migrações gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-184">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="7c4e5-185">O esquema é baseado no modelo especificado em `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-185">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="7c4e5-186">O argumento `InitialCreate` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-186">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="7c4e5-187">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-187">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="7c4e5-188">O comando `update` executa o método `Up` em migrações que não foram aplicadas.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-188">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="7c4e5-189">Nesse caso, `update` executa o método `Up` em *migrações/\<carimbo de data/hora > arquivo _InitialCreate. cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-189">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c4e5-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c4e5-190">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="7c4e5-191">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="7c4e5-191">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="7c4e5-192">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-192">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7c4e5-193">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-193">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7c4e5-194">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-194">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7c4e5-195">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-195">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7c4e5-196">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-196">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="7c4e5-197">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-197">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7c4e5-198">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-198">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="7c4e5-199">O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-199">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="7c4e5-200">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-200">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7c4e5-201">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-201">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="7c4e5-202">O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-202">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="7c4e5-203">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-203">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="7c4e5-204">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-204">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7c4e5-205">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-205">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7c4e5-206">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-206">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7c4e5-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c4e5-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7c4e5-208">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-208">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7c4e5-209">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c4e5-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7c4e5-210">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-210">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="7c4e5-211">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="7c4e5-211">Test the app</span></span>

* <span data-ttu-id="7c4e5-212">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-212">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="7c4e5-213">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-213">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="7c4e5-214">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-214">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="7c4e5-215">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-215">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="7c4e5-217">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-217">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="7c4e5-218">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-218">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="7c4e5-219">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-219">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="7c4e5-220">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-220">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="7c4e5-221">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-221">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c4e5-222">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7c4e5-222">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="7c4e5-223">[Anterior: Introdução](xref:tutorials/razor-pages/razor-pages-start)
> [Próximo: Páginas Razor geradas por scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="7c4e5-223">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7c4e5-224">Nesta seção, as classes são adicionadas para gerenciar filmes em um banco de [dados SQLite](https://www.sqlite.org/index.html)de plataforma cruzada.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-224">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="7c4e5-225">Os aplicativos criados por meio de um modelo de ASP.NET Core usam um banco de dados SQLite.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-225">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="7c4e5-226">As classes de modelo do aplicativo são usadas com [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core provedor de banco de dados](/ef/core/providers/sqlite)) para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-226">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="7c4e5-227">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-227">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="7c4e5-228">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-228">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="7c4e5-229">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-229">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="7c4e5-230">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="7c4e5-230">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c4e5-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c4e5-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c4e5-232">Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-232">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="7c4e5-233">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-233">Name the folder *Models*.</span></span>

<span data-ttu-id="7c4e5-234">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-234">Right click the *Models* folder.</span></span> <span data-ttu-id="7c4e5-235">Selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-235">Select **Add** > **Class**.</span></span> <span data-ttu-id="7c4e5-236">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-236">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7c4e5-237">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c4e5-237">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7c4e5-238">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-238">Add a folder named *Models*.</span></span>
* <span data-ttu-id="7c4e5-239">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-239">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7c4e5-240">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c4e5-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7c4e5-241">No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e então selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-241">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="7c4e5-242">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-242">Name the folder *Models*.</span></span>
* <span data-ttu-id="7c4e5-243">Clique com o botão direito do mouse na pasta *Models* e selecione **Adicionar** > **Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-243">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="7c4e5-244">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-244">In the **New File** dialog:</span></span>

  * <span data-ttu-id="7c4e5-245">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-245">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="7c4e5-246">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-246">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="7c4e5-247">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-247">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="7c4e5-248">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-248">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="7c4e5-249">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="7c4e5-249">Scaffold the movie model</span></span>

<span data-ttu-id="7c4e5-250">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-250">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="7c4e5-251">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-251">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c4e5-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c4e5-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c4e5-253">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-253">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="7c4e5-254">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-254">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="7c4e5-255">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="7c4e5-255">Name the folder *Movies*</span></span>

<span data-ttu-id="7c4e5-256">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-256">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="7c4e5-258">Na caixa de diálogo **Adicionar Scaffold**, selecione **Razor Pages usando o Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-258">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="7c4e5-260">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="7c4e5-260">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="7c4e5-261">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="7c4e5-261">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="7c4e5-262">Na linha **Classe de contexto de dados**, selecione o sinal **+** (+) e aceite o nome gerado **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-262">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="7c4e5-263">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-263">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arp.png)

<span data-ttu-id="7c4e5-265">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-265">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7c4e5-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c4e5-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="7c4e5-267">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-267">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="7c4e5-268">**No Windows**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-268">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="7c4e5-269">**No macOS e Linux**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-269">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7c4e5-270">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c4e5-270">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7c4e5-271">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-271">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="7c4e5-272">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-272">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="7c4e5-273">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-273">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="7c4e5-274">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="7c4e5-274">Files created</span></span>

* <span data-ttu-id="7c4e5-275">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-275">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="7c4e5-276">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="7c4e5-276">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="7c4e5-277">Arquivo atualizado</span><span class="sxs-lookup"><span data-stu-id="7c4e5-277">File updated</span></span>

* <span data-ttu-id="7c4e5-278">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="7c4e5-278">*Startup.cs*</span></span>

<span data-ttu-id="7c4e5-279">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-279">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="7c4e5-280">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="7c4e5-280">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c4e5-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c4e5-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c4e5-282">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-282">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="7c4e5-283">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-283">Add an initial migration.</span></span>
* <span data-ttu-id="7c4e5-284">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-284">Update the database with the initial migration.</span></span>

<span data-ttu-id="7c4e5-285">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-285">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="7c4e5-287">No PMC, digite os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-287">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="7c4e5-288">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-288">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="7c4e5-289">O esquema é baseado no modelo especificado no `DbContext` (no arquivo *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-289">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="7c4e5-290">O argumento `InitialCreate` é usado para nomear a migração.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-290">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="7c4e5-291">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-291">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="7c4e5-292">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-292">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="7c4e5-293">O comando `Update-Database` executa o método `Up` no arquivo *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-293">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="7c4e5-294">O método `Up` cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-294">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7c4e5-295">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c4e5-295">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7c4e5-296">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c4e5-296">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="7c4e5-297">Os comandos anteriores geram o seguinte aviso: "*nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '. Isso fará com que os valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão. Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando ' HasColumnType () '.* " Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-297">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7c4e5-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c4e5-298">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="7c4e5-299">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="7c4e5-299">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="7c4e5-300">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-300">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7c4e5-301">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-301">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7c4e5-302">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-302">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7c4e5-303">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-303">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7c4e5-304">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-304">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="7c4e5-305">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-305">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7c4e5-306">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-306">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="7c4e5-307">O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-307">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="7c4e5-308">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-308">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7c4e5-309">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-309">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="7c4e5-310">O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-310">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="7c4e5-311">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-311">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="7c4e5-312">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-312">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7c4e5-313">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-313">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7c4e5-314">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-314">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7c4e5-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c4e5-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7c4e5-316">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-316">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7c4e5-317">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c4e5-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7c4e5-318">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-318">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="7c4e5-319">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="7c4e5-319">Test the app</span></span>

* <span data-ttu-id="7c4e5-320">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-320">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="7c4e5-321">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="7c4e5-321">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="7c4e5-322">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-322">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="7c4e5-323">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-323">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="7c4e5-325">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-325">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="7c4e5-326">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-326">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="7c4e5-327">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="7c4e5-327">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="7c4e5-328">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-328">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="7c4e5-329">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="7c4e5-329">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c4e5-330">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7c4e5-330">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="7c4e5-331">[Anterior: Introdução](xref:tutorials/razor-pages/razor-pages-start)
> [Próximo: Páginas Razor geradas por scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="7c4e5-331">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
