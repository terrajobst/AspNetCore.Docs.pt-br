---
title: Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar classes de gerenciamento de filmes em um banco de dados usando o EF Core (Entity Framework Core).
ms.author: riande
ms.date: 05/30/2018
uid: tutorials/razor-pages/model
ms.openlocfilehash: 0d33901805d6728fb8006f14d41090b874ab28b1
ms.sourcegitcommit: e8d80ff566bfe505b43389d7bc4551edb1c0c872
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52549104"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="d48ca-103">Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d48ca-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.1"

[!INCLUDE [model1](~/includes/RP/model1.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="d48ca-104">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="d48ca-104">Add a data model</span></span>

<span data-ttu-id="d48ca-105">No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="d48ca-105">In Solution Explorer, right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="d48ca-106">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="d48ca-106">Name the folder *Models*.</span></span>

<span data-ttu-id="d48ca-107">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="d48ca-107">Right click the *Models* folder.</span></span> <span data-ttu-id="d48ca-108">Selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="d48ca-108">Select **Add** > **Class**.</span></span> <span data-ttu-id="d48ca-109">Nomeie a classe **Movie** e substitua o conteúdo da classe `Movie` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="d48ca-109">Name the class **Movie** and replace the contents of the `Movie` class with the following code:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie21/Models/Movie1.cs?name=snippet)]

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="d48ca-110">Fazer scaffold do modelo de filme</span><span class="sxs-lookup"><span data-stu-id="d48ca-110">Scaffold the movie model</span></span>

<span data-ttu-id="d48ca-111">Nesta seção, é feito o scaffold do modelo de filme.</span><span class="sxs-lookup"><span data-stu-id="d48ca-111">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="d48ca-112">Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.</span><span class="sxs-lookup"><span data-stu-id="d48ca-112">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

<span data-ttu-id="d48ca-113">Crie uma pasta *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="d48ca-113">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="d48ca-114">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Páginas* > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="d48ca-114">In **Solution Explorer**, right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="d48ca-115">Dê à pasta o nome *Movies*</span><span class="sxs-lookup"><span data-stu-id="d48ca-115">Name the folder *Movies*</span></span>

<span data-ttu-id="d48ca-116">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Páginas/Filmes* pasta > **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="d48ca-116">In **Solution Explorer**, right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Imagem das instruções anteriores.](model/_static/sca.png)

<span data-ttu-id="d48ca-118">Na caixa de diálogo **Adicionar Scaffold**, selecione **Razor Pages usando o Entity Framework (CRUD)** > **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d48ca-118">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

<span data-ttu-id="d48ca-120">Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:</span><span class="sxs-lookup"><span data-stu-id="d48ca-120">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="d48ca-121">Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="d48ca-121">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="d48ca-122">Na linha **Classe de contexto de dados**, selecione o sinal **+** (+) e aceite o nome gerado **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="d48ca-122">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="d48ca-123">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="d48ca-123">Select **Add**.</span></span>

![Imagem das instruções anteriores.](model/_static/arp.png)

<span data-ttu-id="d48ca-125">O processo de scaffold cria e atualiza os arquivos a seguir:</span><span class="sxs-lookup"><span data-stu-id="d48ca-125">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="d48ca-126">Arquivos criados</span><span class="sxs-lookup"><span data-stu-id="d48ca-126">Files created</span></span>

* <span data-ttu-id="d48ca-127">*Pages/Movies*: Criar, Excluir, Detalhes, Editar, Índice.</span><span class="sxs-lookup"><span data-stu-id="d48ca-127">*Pages/Movies*: Create, Delete, Details, Edit, Index.</span></span> <span data-ttu-id="d48ca-128">Essas páginas serão detalhadas no próximo tutorial.</span><span class="sxs-lookup"><span data-stu-id="d48ca-128">These pages are detailed in the next tutorial.</span></span>
* <span data-ttu-id="d48ca-129">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="d48ca-129">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="d48ca-130">Arquivo atualizado</span><span class="sxs-lookup"><span data-stu-id="d48ca-130">File updated</span></span>

* <span data-ttu-id="d48ca-131">*Startup.cs*: alterações nesse arquivo serão detalhadas na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="d48ca-131">*Startup.cs*: Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="d48ca-132">*appsettings.json*: a cadeia de conexão usada para se conectar a um banco de dados local é adicionada.</span><span class="sxs-lookup"><span data-stu-id="d48ca-132">*appsettings.json*: The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="d48ca-133">Examinar o contexto registrado com a injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="d48ca-133">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="d48ca-134">O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d48ca-134">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d48ca-135">Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d48ca-135">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="d48ca-136">Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor.</span><span class="sxs-lookup"><span data-stu-id="d48ca-136">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="d48ca-137">O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="d48ca-137">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="d48ca-138">A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="d48ca-138">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="d48ca-139">Examine o método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d48ca-139">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d48ca-140">A linha destacada foi adicionada pelo scaffolder:</span><span class="sxs-lookup"><span data-stu-id="d48ca-140">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie21/Startup.cs?name=snippet_ConfigureServices&highlight=12-13)]

<span data-ttu-id="d48ca-141">A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de BD.</span><span class="sxs-lookup"><span data-stu-id="d48ca-141">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="d48ca-142">O contexto de dados deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="d48ca-142">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="d48ca-143">O contexto de dados especifica quais entidades são incluídas no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="d48ca-143">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="d48ca-144">Neste projeto, a classe é chamada `RazorPagesMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="d48ca-144">In this project, the class is named `RazorPagesMovieContext`.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie21/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="d48ca-145">O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="d48ca-145">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="d48ca-146">Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d48ca-146">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="d48ca-147">Uma entidade corresponde a uma linha da tabela.</span><span class="sxs-lookup"><span data-stu-id="d48ca-147">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="d48ca-148">O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions).</span><span class="sxs-lookup"><span data-stu-id="d48ca-148">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="d48ca-149">Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d48ca-149">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="pmc"></a>
## <a name="perform-initial-migration"></a><span data-ttu-id="d48ca-150">Executar a migração inicial</span><span class="sxs-lookup"><span data-stu-id="d48ca-150">Perform initial migration</span></span>

<span data-ttu-id="d48ca-151">Nesta seção, você deve usar o PMC (Console de Gerenciador de Pacotes) para:</span><span class="sxs-lookup"><span data-stu-id="d48ca-151">In this section, you use the Package Manager Console (PMC) to:</span></span>

* <span data-ttu-id="d48ca-152">Adicione uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="d48ca-152">Add an initial migration.</span></span>
* <span data-ttu-id="d48ca-153">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="d48ca-153">Update the database with the initial migration.</span></span>

<span data-ttu-id="d48ca-154">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="d48ca-154">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="d48ca-156">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d48ca-156">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

<span data-ttu-id="d48ca-157">Como alternativa, é possível usar os seguintes comandos do .NET Core CLI na pasta do projeto:</span><span class="sxs-lookup"><span data-stu-id="d48ca-157">Alternatively, the following .NET Core CLI commands can be used from the project folder:</span></span>

```console
dotnet ef migrations add Initial
dotnet ef database update
```

<span data-ttu-id="d48ca-158">Ignore a mensagem de aviso a seguir, que você corrigirá em um tutorial posterior:</span><span class="sxs-lookup"><span data-stu-id="d48ca-158">Ignore the following warning message, which you fix in a later tutorial:</span></span>

```console
Microsoft.EntityFrameworkCore.Model.Validation[30000]
      No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'ForHasColumnType()'.
```

<span data-ttu-id="d48ca-159">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="d48ca-159">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="d48ca-160">O esquema é baseado no modelo especificado no `RazorPagesMovieContext` (no arquivo *Data/RazorPagesMovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="d48ca-160">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="d48ca-161">O argumento `Initial` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="d48ca-161">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="d48ca-162">Você pode usar qualquer nome, mas, por convenção, escolha um nome que descreve a migração.</span><span class="sxs-lookup"><span data-stu-id="d48ca-162">You can use any name, but by convention you choose a name that describes the migration.</span></span> <span data-ttu-id="d48ca-163">Consulte [Introdução às migrações](xref:data/ef-mvc/migrations#introduction-to-migrations) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d48ca-163">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="d48ca-164">O comando `Update-Database` executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d48ca-164">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<span data-ttu-id="d48ca-165">Se você obtiver o erro:</span><span class="sxs-lookup"><span data-stu-id="d48ca-165">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="d48ca-166">Você perdeu a [etapa de migrações](#pmc).</span><span class="sxs-lookup"><span data-stu-id="d48ca-166">You missed the [migrations step](#pmc).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!INCLUDE [model1](~/includes/RP/model1.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="d48ca-167">Adicionar um modelo de dados</span><span class="sxs-lookup"><span data-stu-id="d48ca-167">Add a data model</span></span>

<span data-ttu-id="d48ca-168">No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="d48ca-168">In Solution Explorer, right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="d48ca-169">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="d48ca-169">Name the folder *Models*.</span></span>

<span data-ttu-id="d48ca-170">Clique com o botão direito do mouse na pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="d48ca-170">Right click the *Models* folder.</span></span> <span data-ttu-id="d48ca-171">Selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="d48ca-171">Select **Add** > **Class**.</span></span> <span data-ttu-id="d48ca-172">Nomeie a classe **Movie** e adicione as seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="d48ca-172">Name the class **Movie** and add the following properties:</span></span>

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<a name="cs"></a>
### <a name="add-a-database-connection-string"></a><span data-ttu-id="d48ca-173">Adicionar uma cadeia de conexão de banco de dados</span><span class="sxs-lookup"><span data-stu-id="d48ca-173">Add a database connection string</span></span>

<span data-ttu-id="d48ca-174">Adicione uma cadeia de conexão ao arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="d48ca-174">Add a connection string to the *appsettings.json* file.</span></span>

[!code-json[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=8-10)]

<a name="reg"></a>
###  <a name="register-the-database-context"></a><span data-ttu-id="d48ca-175">Registrar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="d48ca-175">Register the database context</span></span>

<span data-ttu-id="d48ca-176">Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) no [método ConfigureServices da classe Startup](xref:fundamentals/startup#the-startup-class) (*Startup.cs*):</span><span class="sxs-lookup"><span data-stu-id="d48ca-176">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in the [ConfigureServices method of the Startup class](xref:fundamentals/startup#the-startup-class) (*Startup.cs*):</span></span>

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=3-5,7-9)]

<span data-ttu-id="d48ca-177">Compile o projeto para verificar se não há erros.</span><span class="sxs-lookup"><span data-stu-id="d48ca-177">Build the project to verify you don't have any errors.</span></span>

<a name="pmc"></a>
## <a name="add-scaffold-tooling-and-perform-initial-migration"></a><span data-ttu-id="d48ca-178">Adicionar ferramentas de scaffold e executar a migração inicial</span><span class="sxs-lookup"><span data-stu-id="d48ca-178">Add scaffold tooling and perform initial migration</span></span>

<span data-ttu-id="d48ca-179">Nesta seção, você deve usar o PMC (Console de Gerenciador de Pacotes) para:</span><span class="sxs-lookup"><span data-stu-id="d48ca-179">In this section, you use the Package Manager Console (PMC) to:</span></span>

* <span data-ttu-id="d48ca-180">Adicione o pacote de geração de código da Web do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d48ca-180">Add the Visual Studio web code generation package.</span></span> <span data-ttu-id="d48ca-181">Esse pacote é necessário para executar o mecanismo de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="d48ca-181">This package is required to run the scaffolding engine.</span></span>
* <span data-ttu-id="d48ca-182">Adicionar uma migração inicial.</span><span class="sxs-lookup"><span data-stu-id="d48ca-182">Add an initial migration.</span></span>
* <span data-ttu-id="d48ca-183">Atualize o banco de dados com a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="d48ca-183">Update the database with the initial migration.</span></span>

<span data-ttu-id="d48ca-184">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="d48ca-184">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="d48ca-186">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="d48ca-186">In the PMC, enter the following commands:</span></span>

```powershell
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design -Version 2.0.3
Add-Migration Initial
Update-Database
```

<span data-ttu-id="d48ca-187">Como alternativa, os seguintes comandos da CLI do .NET Core podem ser usados:</span><span class="sxs-lookup"><span data-stu-id="d48ca-187">Alternatively, the following .NET Core CLI commands can be used:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet ef migrations add Initial
dotnet ef database update
```

<span data-ttu-id="d48ca-188">Ignore a mensagem a seguir:</span><span class="sxs-lookup"><span data-stu-id="d48ca-188">Ignore the following message:</span></span>

```console
Microsoft.EntityFrameworkCore.Model.Validation[30000]
      No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'ForHasColumnType()'
```

<span data-ttu-id="d48ca-189">você corrigirá isso no próximo tutorial.</span><span class="sxs-lookup"><span data-stu-id="d48ca-189">You fix that in the next tutorial.</span></span>

<span data-ttu-id="d48ca-190">O comando `Install-Package` instala as ferramentas necessárias para executar o mecanismo de scaffolding.</span><span class="sxs-lookup"><span data-stu-id="d48ca-190">The `Install-Package` command installs the tooling required to run the scaffolding engine.</span></span>

<span data-ttu-id="d48ca-191">O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.</span><span class="sxs-lookup"><span data-stu-id="d48ca-191">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="d48ca-192">O esquema é baseado no modelo especificado no `DbContext` (no arquivo *Models/MovieContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="d48ca-192">The schema is based on the model specified in the `DbContext` (In the *Models/MovieContext.cs* file).</span></span> <span data-ttu-id="d48ca-193">O argumento `Initial` é usado para nomear as migrações.</span><span class="sxs-lookup"><span data-stu-id="d48ca-193">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="d48ca-194">Você pode usar qualquer nome, mas, por convenção, escolha um nome que descreve a migração.</span><span class="sxs-lookup"><span data-stu-id="d48ca-194">You can use any name, but by convention you choose a name that describes the migration.</span></span> <span data-ttu-id="d48ca-195">Consulte [Introdução às migrações](xref:data/ef-mvc/migrations#introduction-to-migrations) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d48ca-195">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="d48ca-196">O comando `Update-Database` executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d48ca-196">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [model 4windows](~/includes/RP/model4Win.md)]

[!INCLUDE [model 4](~/includes/RP/model4tbl.md)]

::: moniker-end

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="d48ca-197">Testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="d48ca-197">Test the app</span></span>

* <span data-ttu-id="d48ca-198">Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="d48ca-198">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>
* <span data-ttu-id="d48ca-199">Teste o link **Criar**.</span><span class="sxs-lookup"><span data-stu-id="d48ca-199">Test the **Create** link.</span></span>

  > [!NOTE]
  > <span data-ttu-id="d48ca-200">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="d48ca-200">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d48ca-201">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades de idiomas diferentes do inglês que usam uma vírgula (“,”) para um ponto decimal e formatos de data diferentes do inglês dos EUA, você deve globalizar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d48ca-201">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, you must globalize your app.</span></span> <span data-ttu-id="d48ca-202">Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="d48ca-202">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span></span>

  ![Criar página](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* <span data-ttu-id="d48ca-204">Teste os links **Editar**, **Detalhes** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="d48ca-204">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="d48ca-205">Se você receber uma exceção SQL, verifique se executou migrações e atualizou o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d48ca-205">If you get a SQL exception, verify you have run migrations and updated the database.</span></span>

<span data-ttu-id="d48ca-206">O tutorial a seguir explica os arquivos criados por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="d48ca-206">The next tutorial explains the files created by scaffolding.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d48ca-207">[Anterior: Introdução](xref:tutorials/razor-pages/razor-pages-start)
> [Próximo: Páginas Razor geradas por scaffolding](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="d48ca-207">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>
