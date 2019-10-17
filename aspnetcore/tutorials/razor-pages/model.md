---
title: Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar classes de gerenciamento de filmes em um banco de dados usando o EF Core (Entity Framework Core).
ms.author: riande
ms.date: 9/22/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 4f8b80cb51bd10eb3b136a780dc123c41d61c0a5
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519080"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="cce6d-103">Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cce6d-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="cce6d-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cce6d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cce6d-105">Nesta seção, classes são adicionadas para o gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cce6d-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="cce6d-106">Essas classes são usadas com o [EF Core](/ef/core) (Entity Framework Core) para trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cce6d-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="cce6d-107">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="cce6d-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="cce6d-108">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="cce6d-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="cce6d-109">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cce6d-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="cce6d-110">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="cce6d-110">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cce6d-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cce6d-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cce6d-112">Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="cce6d-113">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="cce6d-113">Name the folder *Models*.</span></span>

<span data-ttu-id="cce6d-114">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="cce6d-114">Right click the *Models* folder.</span></span> <span data-ttu-id="cce6d-115">Selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="cce6d-116">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cce6d-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cce6d-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cce6d-118">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="cce6d-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="cce6d-119">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="cce6d-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cce6d-120">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cce6d-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cce6d-121">No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e então selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-121">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="cce6d-122">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="cce6d-122">Name the folder *Models*.</span></span>
* <span data-ttu-id="cce6d-123">Clique com o botão direito do mouse na pasta *Models* e selecione **Adicionar** > **Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-123">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="cce6d-124">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="cce6d-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="cce6d-125">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="cce6d-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="cce6d-126">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="cce6d-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="cce6d-127">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="cce6d-128">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="cce6d-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="cce6d-129">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="cce6d-129">Scaffold the movie model</span></span>

<span data-ttu-id="cce6d-130">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="cce6d-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="cce6d-131">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="cce6d-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cce6d-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cce6d-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cce6d-133">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="cce6d-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="cce6d-134">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="cce6d-135">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="cce6d-135">Name the folder *Movies*</span></span>

<span data-ttu-id="cce6d-136">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="cce6d-138">Na caixa de diálogo **Adicionar Scaffold**, selecione **Razor Pages usando o Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="cce6d-140">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="cce6d-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="cce6d-141">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="cce6d-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="cce6d-142">Na linha **Classe de contexto de dados**, selecione o sinal de adição ( **+** ) e altere o nome gerado de RazorPagesMovie.**Models**.RazorPagesMovieContext para RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="cce6d-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="cce6d-143">[Esta alteração](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) não é obrigatória.</span><span class="sxs-lookup"><span data-stu-id="cce6d-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="cce6d-144">Ela cria a classe de contexto do banco de dados com o namespace correto.</span><span class="sxs-lookup"><span data-stu-id="cce6d-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="cce6d-145">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-145">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/3/arp.png)

<span data-ttu-id="cce6d-147">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="cce6d-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cce6d-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cce6d-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="cce6d-149">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="cce6d-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="cce6d-150">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="cce6d-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="cce6d-151">**No Windows**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="cce6d-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="cce6d-152">**No macOS e Linux**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="cce6d-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cce6d-153">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cce6d-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cce6d-154">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="cce6d-154">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="cce6d-155">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="cce6d-155">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="cce6d-156">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cce6d-156">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

### <a name="files-created"></a><span data-ttu-id="cce6d-157">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="cce6d-157">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cce6d-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cce6d-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cce6d-159">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="cce6d-159">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="cce6d-160">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="cce6d-160">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="cce6d-161">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="cce6d-161">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="cce6d-162">Atualizado</span><span class="sxs-lookup"><span data-stu-id="cce6d-162">Updated</span></span>

* <span data-ttu-id="cce6d-163">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="cce6d-163">*Startup.cs*</span></span>

<span data-ttu-id="cce6d-164">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="cce6d-164">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="cce6d-165">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cce6d-165">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="cce6d-166">O processo de scaffold cria os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="cce6d-166">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="cce6d-167">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="cce6d-167">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="cce6d-168">Os arquivos criados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="cce6d-168">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="cce6d-169">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="cce6d-169">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cce6d-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cce6d-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cce6d-171">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="cce6d-171">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="cce6d-172">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="cce6d-172">Add an initial migration.</span></span>
* <span data-ttu-id="cce6d-173">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="cce6d-173">Update the database with the initial migration.</span></span>

<span data-ttu-id="cce6d-174">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-174">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="cce6d-176">No PMC, digite os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="cce6d-176">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cce6d-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cce6d-177">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cce6d-178">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cce6d-178">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="cce6d-179">Os comandos anteriores geram o seguinte aviso: "nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '.</span><span class="sxs-lookup"><span data-stu-id="cce6d-179">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="cce6d-180">Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão.</span><span class="sxs-lookup"><span data-stu-id="cce6d-180">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="cce6d-181">Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="cce6d-181">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="cce6d-182">Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="cce6d-182">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="cce6d-183">O comando migrações gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="cce6d-183">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="cce6d-184">O esquema é baseado no modelo especificado em `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="cce6d-184">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="cce6d-185">O argumento `InitialCreate` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="cce6d-185">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="cce6d-186">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.</span><span class="sxs-lookup"><span data-stu-id="cce6d-186">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="cce6d-187">O comando `update` executa o método `Up` em migrações que não foram aplicadas.</span><span class="sxs-lookup"><span data-stu-id="cce6d-187">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="cce6d-188">Nesse caso, `update` executa o método `Up` em *migrações/@no__t-stamp > arquivo _InitialCreate. cs* , que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cce6d-188">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cce6d-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cce6d-189">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="cce6d-190">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="cce6d-190">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="cce6d-191">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cce6d-191">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cce6d-192">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cce6d-192">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="cce6d-193">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="cce6d-193">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="cce6d-194">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="cce6d-194">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="cce6d-195">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="cce6d-195">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="cce6d-196">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cce6d-196">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cce6d-197">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="cce6d-197">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="cce6d-198">O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="cce6d-198">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="cce6d-199">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="cce6d-199">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="cce6d-200">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="cce6d-200">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="cce6d-201">O código anterior cria uma propriedade [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="cce6d-201">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="cce6d-202">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cce6d-202">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="cce6d-203">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="cce6d-203">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="cce6d-204">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="cce6d-204">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="cce6d-205">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="cce6d-205">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cce6d-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cce6d-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cce6d-207">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="cce6d-207">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cce6d-208">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cce6d-208">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cce6d-209">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="cce6d-209">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="cce6d-210">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="cce6d-210">Test the app</span></span>

* <span data-ttu-id="cce6d-211">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="cce6d-211">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="cce6d-212">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="cce6d-212">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="cce6d-213">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="cce6d-213">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="cce6d-214">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-214">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="cce6d-216">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="cce6d-216">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="cce6d-217">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="cce6d-217">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="cce6d-218">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="cce6d-218">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="cce6d-219">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-219">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="cce6d-220">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="cce6d-220">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cce6d-221">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cce6d-221">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cce6d-222">[Anterior: Introdução](xref:tutorials/razor-pages/razor-pages-start)
> [Próximo: Páginas Razor geradas por scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="cce6d-222">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cce6d-223">Nesta seção, classes são adicionadas para o gerenciamento de filmes em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cce6d-223">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="cce6d-224">Essas classes são usadas com o [EF Core](/ef/core) (Entity Framework Core) para trabalhar com um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cce6d-224">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="cce6d-225">O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o código de acesso a dados.</span><span class="sxs-lookup"><span data-stu-id="cce6d-225">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="cce6d-226">As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core.</span><span class="sxs-lookup"><span data-stu-id="cce6d-226">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="cce6d-227">Elas definem as propriedades dos dados que são armazenados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cce6d-227">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="cce6d-228">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="cce6d-228">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cce6d-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cce6d-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cce6d-230">Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-230">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="cce6d-231">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="cce6d-231">Name the folder *Models*.</span></span>

<span data-ttu-id="cce6d-232">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="cce6d-232">Right click the *Models* folder.</span></span> <span data-ttu-id="cce6d-233">Selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-233">Select **Add** > **Class**.</span></span> <span data-ttu-id="cce6d-234">Dê à classe o nome **Movie**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-234">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cce6d-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cce6d-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cce6d-236">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="cce6d-236">Add a folder named *Models*.</span></span>
* <span data-ttu-id="cce6d-237">Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="cce6d-237">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cce6d-238">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cce6d-238">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cce6d-239">No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e então selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-239">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="cce6d-240">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="cce6d-240">Name the folder *Models*.</span></span>
* <span data-ttu-id="cce6d-241">Clique com o botão direito do mouse na pasta *Models* e selecione **Adicionar** > **Novo Arquivo**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-241">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="cce6d-242">Na caixa de diálogo **Novo Arquivo**:</span><span class="sxs-lookup"><span data-stu-id="cce6d-242">In the **New File** dialog:</span></span>

  * <span data-ttu-id="cce6d-243">Selecione **Geral** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="cce6d-243">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="cce6d-244">Selecione **Classe Vazia** no painel central.</span><span class="sxs-lookup"><span data-stu-id="cce6d-244">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="cce6d-245">Nomeie a classe **Movie** e selecione **Novo**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-245">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="cce6d-246">Crie o projeto para verificar se não há erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="cce6d-246">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="cce6d-247">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="cce6d-247">Scaffold the movie model</span></span>

<span data-ttu-id="cce6d-248">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="cce6d-248">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="cce6d-249">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="cce6d-249">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cce6d-250">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cce6d-250">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cce6d-251">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="cce6d-251">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="cce6d-252">Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-252">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="cce6d-253">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="cce6d-253">Name the folder *Movies*</span></span>

<span data-ttu-id="cce6d-254">Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-254">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="cce6d-256">Na caixa de diálogo **Adicionar Scaffold**, selecione **Razor Pages usando o Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-256">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="cce6d-258">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="cce6d-258">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="cce6d-259">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="cce6d-259">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="cce6d-260">Na linha **Classe de contexto de dados**, selecione o sinal **+** (+) e aceite o nome gerado **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-260">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="cce6d-261">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-261">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arp.png)

<span data-ttu-id="cce6d-263">O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.</span><span class="sxs-lookup"><span data-stu-id="cce6d-263">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cce6d-264">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cce6d-264">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="cce6d-265">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="cce6d-265">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="cce6d-266">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="cce6d-266">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="cce6d-267">**No Windows**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="cce6d-267">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="cce6d-268">**No macOS e Linux**, execute o comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="cce6d-268">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cce6d-269">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cce6d-269">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cce6d-270">Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="cce6d-270">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="cce6d-271">Instale a ferramenta de scaffolding:</span><span class="sxs-lookup"><span data-stu-id="cce6d-271">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="cce6d-272">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cce6d-272">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="cce6d-273">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="cce6d-273">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="cce6d-274">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="cce6d-274">Files created</span></span>

* <span data-ttu-id="cce6d-275">*Pages/Movies*: Create, Delete, Details, Edit e Index.</span><span class="sxs-lookup"><span data-stu-id="cce6d-275">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="cce6d-276">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="cce6d-276">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="cce6d-277">Arquivo atualizado</span><span class="sxs-lookup"><span data-stu-id="cce6d-277">File updated</span></span>

* <span data-ttu-id="cce6d-278">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="cce6d-278">*Startup.cs*</span></span>

<span data-ttu-id="cce6d-279">Os arquivos criados e atualizados são explicados na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="cce6d-279">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="cce6d-280">Migração inicial</span><span class="sxs-lookup"><span data-stu-id="cce6d-280">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cce6d-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cce6d-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cce6d-282">Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:</span><span class="sxs-lookup"><span data-stu-id="cce6d-282">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="cce6d-283">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="cce6d-283">Add an initial migration.</span></span>
* <span data-ttu-id="cce6d-284">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="cce6d-284">Update the database with the initial migration.</span></span>

<span data-ttu-id="cce6d-285">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-285">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="cce6d-287">No PMC, digite os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="cce6d-287">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="cce6d-288">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="cce6d-288">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="cce6d-289">O esquema é baseado no modelo especificado no `DbContext` (no arquivo *RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="cce6d-289">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="cce6d-290">O argumento `InitialCreate` é usado para nomear a migração.</span><span class="sxs-lookup"><span data-stu-id="cce6d-290">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="cce6d-291">Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado.</span><span class="sxs-lookup"><span data-stu-id="cce6d-291">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="cce6d-292">Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="cce6d-292">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="cce6d-293">O comando `Update-Database` executa o método `Up` no arquivo *Migrations/\<time-stamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="cce6d-293">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="cce6d-294">O método `Up` cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cce6d-294">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cce6d-295">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cce6d-295">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cce6d-296">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cce6d-296">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="cce6d-297">Os comandos anteriores geram o seguinte aviso: "*nenhum tipo foi especificado para a coluna decimal ' Price ' no tipo de entidade ' Movie '. Isso fará com que os valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão. Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando ' HasColumnType () '.* " Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.</span><span class="sxs-lookup"><span data-stu-id="cce6d-297">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cce6d-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cce6d-298">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="cce6d-299">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="cce6d-299">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="cce6d-300">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cce6d-300">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cce6d-301">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cce6d-301">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="cce6d-302">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="cce6d-302">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="cce6d-303">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="cce6d-303">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="cce6d-304">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="cce6d-304">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="cce6d-305">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cce6d-305">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cce6d-306">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="cce6d-306">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="cce6d-307">O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="cce6d-307">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="cce6d-308">O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="cce6d-308">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="cce6d-309">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="cce6d-309">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="cce6d-310">O código anterior cria uma propriedade [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="cce6d-310">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="cce6d-311">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cce6d-311">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="cce6d-312">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="cce6d-312">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="cce6d-313">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="cce6d-313">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="cce6d-314">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="cce6d-314">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cce6d-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cce6d-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cce6d-316">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="cce6d-316">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cce6d-317">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="cce6d-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cce6d-318">Examine o método `Up`.</span><span class="sxs-lookup"><span data-stu-id="cce6d-318">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="cce6d-319">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="cce6d-319">Test the app</span></span>

* <span data-ttu-id="cce6d-320">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="cce6d-320">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="cce6d-321">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="cce6d-321">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="cce6d-322">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="cce6d-322">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="cce6d-323">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-323">Test the **Create** link.</span></span>

  ![Criar página](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="cce6d-325">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="cce6d-325">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="cce6d-326">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado.</span><span class="sxs-lookup"><span data-stu-id="cce6d-326">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="cce6d-327">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="cce6d-327">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="cce6d-328">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="cce6d-328">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="cce6d-329">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="cce6d-329">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cce6d-330">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cce6d-330">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cce6d-331">[Anterior: Introdução](xref:tutorials/razor-pages/razor-pages-start)
> [Próximo: Páginas Razor geradas por scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="cce6d-331">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
