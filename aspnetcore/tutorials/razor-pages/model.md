---
title: Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar classes de gerenciamento de filmes em um banco de dados usando o EF Core (Entity Framework Core).
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 1988877a552ba58841140a00b61bdcf003afd87d
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881337"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="6e7f0-103">Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6e7f0-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="6e7f0-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6e7f0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6e7f0-105">Nesta seção, as classes são adicionadas para gerenciar filmes em um banco de [dados SQLite](https://www.sqlite.org/index.html)de plataforma cruzada.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="6e7f0-106">Os aplicativos criados por meio de um modelo de ASP.NET Core usam um banco de dados SQLite.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="6e7f0-107">As classes de modelo do aplicativo são usadas com [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core provedor de banco de dados](/ef/core/providers/sqlite)) para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="6e7f0-108">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="6e7f0-109">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="6e7f0-110">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="6e7f0-111">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="6e7f0-111">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6e7f0-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6e7f0-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6e7f0-113">Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="6e7f0-114">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-114">Name the folder *Models*.</span></span>

<span data-ttu-id="6e7f0-115">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-115">Right click the *Models* folder.</span></span> <span data-ttu-id="6e7f0-116">Selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="6e7f0-117">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6e7f0-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6e7f0-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6e7f0-119">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="6e7f0-120">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6e7f0-121">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6e7f0-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6e7f0-122">No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e então selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-122">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="6e7f0-123">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-123">Name the folder *Models*.</span></span>
* <span data-ttu-id="6e7f0-124">Clique com o botão direito do mouse na pasta *Models* e selecione **Adicionar** > **Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-124">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="6e7f0-125">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-125">In the **New File** dialog:</span></span>

  * <span data-ttu-id="6e7f0-126">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-126">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="6e7f0-127">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-127">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="6e7f0-128">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-128">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="6e7f0-129">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-129">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="6e7f0-130">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="6e7f0-130">Scaffold the movie model</span></span>

<span data-ttu-id="6e7f0-131">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-131">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="6e7f0-132">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-132">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6e7f0-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6e7f0-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6e7f0-134">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-134">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="6e7f0-135">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-135">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="6e7f0-136">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="6e7f0-136">Name the folder *Movies*</span></span>

<span data-ttu-id="6e7f0-137">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-137">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="6e7f0-139">Na caixa de diálogo **Adicionar Scaffold**, selecione **Razor Pages usando o Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-139">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="6e7f0-141">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="6e7f0-141">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="6e7f0-142">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="6e7f0-142">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="6e7f0-143">Na linha **Classe de contexto de dados**, selecione o sinal de adição ( **+** ) e altere o nome gerado de RazorPagesMovie.**Models**.RazorPagesMovieContext para RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-143">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="6e7f0-144">[Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-144">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="6e7f0-145">Ela cria a classe de contexto do banco de dados com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-145">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="6e7f0-146">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-146">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/3/arp.png)

<span data-ttu-id="6e7f0-148">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-148">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6e7f0-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6e7f0-149">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="6e7f0-150">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-150">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="6e7f0-151">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-151">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="6e7f0-152">**No Windows**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-152">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="6e7f0-153">**No macOS e Linux**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-153">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6e7f0-154">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6e7f0-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6e7f0-155">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-155">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="6e7f0-156">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-156">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="6e7f0-157">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-157">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

---

### <a name="files-created"></a><span data-ttu-id="6e7f0-158">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="6e7f0-158">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6e7f0-159">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6e7f0-159">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6e7f0-160">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-160">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="6e7f0-161">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-161">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="6e7f0-162">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="6e7f0-162">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="6e7f0-163">Atualizado</span><span class="sxs-lookup"><span data-stu-id="6e7f0-163">Updated</span></span>

* <span data-ttu-id="6e7f0-164">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="6e7f0-164">*Startup.cs*</span></span>

<span data-ttu-id="6e7f0-165">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-165">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6e7f0-166">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6e7f0-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="6e7f0-167">O processo de scaffold cria os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-167">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="6e7f0-168">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-168">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="6e7f0-169">Os arquivos criados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-169">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="6e7f0-170">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="6e7f0-170">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6e7f0-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6e7f0-171">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6e7f0-172">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-172">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="6e7f0-173">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-173">Add an initial migration.</span></span>
* <span data-ttu-id="6e7f0-174">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-174">Update the database with the initial migration.</span></span>

<span data-ttu-id="6e7f0-175">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-175">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="6e7f0-177">No PMC, digite os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-177">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6e7f0-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6e7f0-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6e7f0-179">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6e7f0-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="6e7f0-180">Os comandos anteriores geram o seguinte aviso: "nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-180">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="6e7f0-181">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-181">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="6e7f0-182">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="6e7f0-182">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="6e7f0-183">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-183">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="6e7f0-184">O comando migrações gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-184">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="6e7f0-185">O esquema é baseado no modelo especificado em `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-185">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="6e7f0-186">O argumento `InitialCreate` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-186">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="6e7f0-187">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-187">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="6e7f0-188">O comando `update` executa o método `Up` em migrações que não foram aplicadas.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-188">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="6e7f0-189">Nesse caso, `update` executa o método `Up` em *migrações/\<carimbo de data/hora > arquivo _InitialCreate. cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-189">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6e7f0-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6e7f0-190">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="6e7f0-191">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="6e7f0-191">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="6e7f0-192">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-192">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6e7f0-193">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-193">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="6e7f0-194">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-194">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6e7f0-195">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-195">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="6e7f0-196">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-196">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="6e7f0-197">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-197">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6e7f0-198">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-198">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="6e7f0-199">O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-199">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="6e7f0-200">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-200">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="6e7f0-201">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-201">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="6e7f0-202">O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-202">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="6e7f0-203">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-203">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="6e7f0-204">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-204">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="6e7f0-205">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-205">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="6e7f0-206">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-206">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6e7f0-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6e7f0-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6e7f0-208">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-208">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6e7f0-209">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6e7f0-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6e7f0-210">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-210">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="6e7f0-211">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="6e7f0-211">Test the app</span></span>

* <span data-ttu-id="6e7f0-212">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-212">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="6e7f0-213">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-213">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="6e7f0-214">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-214">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="6e7f0-215">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-215">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="6e7f0-217">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-217">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="6e7f0-218">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-218">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="6e7f0-219">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-219">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="6e7f0-220">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-220">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="6e7f0-221">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-221">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6e7f0-222">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6e7f0-222">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="6e7f0-223">[Anterior: Introdução](xref:tutorials/razor-pages/razor-pages-start)
> [Próximo: Páginas Razor geradas por scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="6e7f0-223">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6e7f0-224">Nesta seção, as classes são adicionadas para gerenciar filmes em um banco de [dados SQLite](https://www.sqlite.org/index.html)de plataforma cruzada.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-224">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="6e7f0-225">Os aplicativos criados por meio de um modelo de ASP.NET Core usam um banco de dados SQLite.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-225">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="6e7f0-226">As classes de modelo do aplicativo são usadas com [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core provedor de banco de dados](/ef/core/providers/sqlite)) para trabalhar com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-226">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="6e7f0-227">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-227">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="6e7f0-228">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-228">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="6e7f0-229">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-229">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="6e7f0-230">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="6e7f0-230">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6e7f0-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6e7f0-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6e7f0-232">Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-232">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="6e7f0-233">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-233">Name the folder *Models*.</span></span>

<span data-ttu-id="6e7f0-234">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-234">Right click the *Models* folder.</span></span> <span data-ttu-id="6e7f0-235">Selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-235">Select **Add** > **Class**.</span></span> <span data-ttu-id="6e7f0-236">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-236">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6e7f0-237">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6e7f0-237">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6e7f0-238">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-238">Add a folder named *Models*.</span></span>
* <span data-ttu-id="6e7f0-239">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-239">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6e7f0-240">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6e7f0-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6e7f0-241">No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e então selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-241">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="6e7f0-242">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-242">Name the folder *Models*.</span></span>
* <span data-ttu-id="6e7f0-243">Clique com o botão direito do mouse na pasta *Models* e selecione **Adicionar** > **Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-243">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="6e7f0-244">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-244">In the **New File** dialog:</span></span>

  * <span data-ttu-id="6e7f0-245">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-245">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="6e7f0-246">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-246">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="6e7f0-247">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-247">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="6e7f0-248">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-248">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="6e7f0-249">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="6e7f0-249">Scaffold the movie model</span></span>

<span data-ttu-id="6e7f0-250">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-250">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="6e7f0-251">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-251">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6e7f0-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6e7f0-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6e7f0-253">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-253">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="6e7f0-254">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-254">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="6e7f0-255">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="6e7f0-255">Name the folder *Movies*</span></span>

<span data-ttu-id="6e7f0-256">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-256">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="6e7f0-258">Na caixa de diálogo **Adicionar Scaffold**, selecione **Razor Pages usando o Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-258">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="6e7f0-260">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="6e7f0-260">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="6e7f0-261">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="6e7f0-261">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="6e7f0-262">Na linha **Classe de contexto de dados**, selecione o sinal **+** (+) e aceite o nome gerado **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-262">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="6e7f0-263">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-263">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arp.png)

<span data-ttu-id="6e7f0-265">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-265">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6e7f0-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6e7f0-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="6e7f0-267">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-267">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="6e7f0-268">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-268">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="6e7f0-269">**No Windows**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-269">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="6e7f0-270">**No macOS e Linux**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-270">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6e7f0-271">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6e7f0-271">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6e7f0-272">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-272">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="6e7f0-273">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-273">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="6e7f0-274">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-274">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="6e7f0-275">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-275">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="6e7f0-276">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="6e7f0-276">Files created</span></span>

* <span data-ttu-id="6e7f0-277">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-277">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="6e7f0-278">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="6e7f0-278">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="6e7f0-279">Arquivo atualizado</span><span class="sxs-lookup"><span data-stu-id="6e7f0-279">File updated</span></span>

* <span data-ttu-id="6e7f0-280">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="6e7f0-280">*Startup.cs*</span></span>

<span data-ttu-id="6e7f0-281">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-281">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="6e7f0-282">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="6e7f0-282">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6e7f0-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6e7f0-283">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6e7f0-284">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-284">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="6e7f0-285">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-285">Add an initial migration.</span></span>
* <span data-ttu-id="6e7f0-286">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-286">Update the database with the initial migration.</span></span>

<span data-ttu-id="6e7f0-287">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-287">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="6e7f0-289">No PMC, digite os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-289">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="6e7f0-290">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-290">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="6e7f0-291">O esquema é baseado no modelo especificado no `DbContext` (no arquivo *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-291">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="6e7f0-292">O argumento `InitialCreate` é usado para nomear a migração.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-292">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="6e7f0-293">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-293">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="6e7f0-294">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-294">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="6e7f0-295">O comando `Update-Database` executa o método `Up` no arquivo *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-295">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="6e7f0-296">O método `Up` cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-296">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6e7f0-297">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6e7f0-297">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6e7f0-298">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6e7f0-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="6e7f0-299">Os comandos anteriores geram o seguinte aviso: "*nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '. Isso fará com que os valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão. Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando ' HasColumnType () '.* " Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-299">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6e7f0-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6e7f0-300">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="6e7f0-301">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="6e7f0-301">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="6e7f0-302">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-302">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6e7f0-303">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-303">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="6e7f0-304">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-304">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6e7f0-305">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-305">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="6e7f0-306">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-306">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="6e7f0-307">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-307">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6e7f0-308">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-308">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="6e7f0-309">O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-309">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="6e7f0-310">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-310">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="6e7f0-311">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-311">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="6e7f0-312">O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-312">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="6e7f0-313">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-313">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="6e7f0-314">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-314">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="6e7f0-315">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-315">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="6e7f0-316">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-316">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6e7f0-317">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6e7f0-317">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6e7f0-318">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-318">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6e7f0-319">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6e7f0-319">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6e7f0-320">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-320">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="6e7f0-321">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="6e7f0-321">Test the app</span></span>

* <span data-ttu-id="6e7f0-322">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-322">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="6e7f0-323">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="6e7f0-323">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="6e7f0-324">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-324">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="6e7f0-325">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-325">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="6e7f0-327">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-327">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="6e7f0-328">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-328">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="6e7f0-329">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="6e7f0-329">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="6e7f0-330">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-330">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="6e7f0-331">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="6e7f0-331">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6e7f0-332">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6e7f0-332">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="6e7f0-333">[Anterior: Introdução](xref:tutorials/razor-pages/razor-pages-start)
> [Próximo: Páginas Razor geradas por scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="6e7f0-333">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
