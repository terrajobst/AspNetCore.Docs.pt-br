---
title: Adicionar um novo campo em uma página Razor no ASP.NET Core
author: rick-anderson
description: Mostra como adicionar um novo campo a uma página Razor com o Entity Framework Core
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/5/2018
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: e280bc9553113982a1f1a77eabab32575c905237
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52862285"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="1d93b-103">Adicionar um novo campo em uma página Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d93b-103">Add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="1d93b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1d93b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="1d93b-105">Nesta seção, as Migrações do [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First são usadas para:</span><span class="sxs-lookup"><span data-stu-id="1d93b-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="1d93b-106">Adicionar um novo campo ao modelo.</span><span class="sxs-lookup"><span data-stu-id="1d93b-106">Add a new field to the model.</span></span>
* <span data-ttu-id="1d93b-107">Migrar a nova alteração de esquema de campo para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="1d93b-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="1d93b-108">Ao usar o Code First do EF para criar automaticamente um banco de dados, o Code First:</span><span class="sxs-lookup"><span data-stu-id="1d93b-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="1d93b-109">Adiciona uma tabela ao banco de dados para acompanhar se o esquema do banco de dados está sincronizado com as classes de modelo das quais ele foi gerado.</span><span class="sxs-lookup"><span data-stu-id="1d93b-109">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="1d93b-110">Se as classes de modelo não estiverem em sincronia com o banco de dados, o EF gerará uma exceção.</span><span class="sxs-lookup"><span data-stu-id="1d93b-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="1d93b-111">Verificação automática de esquema/modelo em sincronia torna mais fácil encontrar problemas de código/banco de dados inconsistente.</span><span class="sxs-lookup"><span data-stu-id="1d93b-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="1d93b-112">Adicionando uma propriedade de classificação ao modelo de filme</span><span class="sxs-lookup"><span data-stu-id="1d93b-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="1d93b-113">Abra o arquivo *Models/Movie.cs* e adicione uma propriedade `Rating`:</span><span class="sxs-lookup"><span data-stu-id="1d93b-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="1d93b-114">Crie o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1d93b-114">Build the app.</span></span>

<span data-ttu-id="1d93b-115">Edite *Pages/Movies/Index.cshtml* e adicione um campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="1d93b-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml.?highlight=40-42,61-63)]

<span data-ttu-id="1d93b-116">Atualize as seguintes páginas:</span><span class="sxs-lookup"><span data-stu-id="1d93b-116">Update the following pages:</span></span>

* <span data-ttu-id="1d93b-117">Adicione o campo `Rating` às páginas Excluir e Detalhes.</span><span class="sxs-lookup"><span data-stu-id="1d93b-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="1d93b-118">Atualize [Create.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="1d93b-118">Update [Create.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="1d93b-119">Adicione o campo `Rating` à página Editar.</span><span class="sxs-lookup"><span data-stu-id="1d93b-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="1d93b-120">O aplicativo não funcionará até que o BD seja atualizado para incluir o novo campo.</span><span class="sxs-lookup"><span data-stu-id="1d93b-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="1d93b-121">Se for executado agora, o aplicativo gerará uma `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="1d93b-121">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="1d93b-122">Esse erro é causado devido à classe de modelo Movie atualizada ser diferente do esquema da tabela Movie do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="1d93b-122">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="1d93b-123">(Não há nenhuma coluna `Rating` na tabela de banco de dados.)</span><span class="sxs-lookup"><span data-stu-id="1d93b-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="1d93b-124">Existem algumas abordagens para resolver o erro:</span><span class="sxs-lookup"><span data-stu-id="1d93b-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="1d93b-125">Faça com que o Entity Framework remova automaticamente e recrie o banco de dados usando o novo esquema de classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="1d93b-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="1d93b-126">Essa abordagem é conveniente no início do ciclo de desenvolvimento; ela permite que você desenvolva rapidamente o modelo e o esquema de banco de dados juntos.</span><span class="sxs-lookup"><span data-stu-id="1d93b-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="1d93b-127">A desvantagem é que você perde os dados existentes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="1d93b-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="1d93b-128">Não use essa abordagem em um banco de dados de produção.</span><span class="sxs-lookup"><span data-stu-id="1d93b-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="1d93b-129">A remoção do BD em alterações de esquema e o uso de um inicializador para propagar automaticamente o banco de dados com os dados de teste é muitas vezes uma maneira produtiva de desenvolver um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1d93b-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="1d93b-130">Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo.</span><span class="sxs-lookup"><span data-stu-id="1d93b-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="1d93b-131">A vantagem dessa abordagem é que você mantém os dados.</span><span class="sxs-lookup"><span data-stu-id="1d93b-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="1d93b-132">Faça essa alteração manualmente ou criando um script de alteração de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="1d93b-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="1d93b-133">Use as Migrações do Code First para atualizar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="1d93b-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="1d93b-134">Para este tutorial, use as Migrações do Code First.</span><span class="sxs-lookup"><span data-stu-id="1d93b-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="1d93b-135">Atualize a classe `SeedData` para que ela forneça um valor para a nova coluna.</span><span class="sxs-lookup"><span data-stu-id="1d93b-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="1d93b-136">Uma alteração de amostra é mostrada abaixo, mas é recomendável fazer essa alteração em cada bloco `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="1d93b-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="1d93b-137">Consulte o [arquivo SeedData.cs concluído](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="1d93b-137">See the [completed SeedData.cs file](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="1d93b-138">Compile a solução.</span><span class="sxs-lookup"><span data-stu-id="1d93b-138">Build the solution.</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1d93b-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1d93b-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="1d93b-140">Adicionar uma migração para o campo de classificação</span><span class="sxs-lookup"><span data-stu-id="1d93b-140">Add a migration for the rating field</span></span>

<span data-ttu-id="1d93b-141">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="1d93b-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="1d93b-142">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="1d93b-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="1d93b-143">O comando `Add-Migration` informa à estrutura:</span><span class="sxs-lookup"><span data-stu-id="1d93b-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="1d93b-144">Compare o modelo `Movie` com o esquema de BD `Movie`.</span><span class="sxs-lookup"><span data-stu-id="1d93b-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="1d93b-145">Crie um código para migrar o esquema de BD para o novo modelo.</span><span class="sxs-lookup"><span data-stu-id="1d93b-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="1d93b-146">O nome “Classificação” é arbitrário e é usado para nomear o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="1d93b-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="1d93b-147">É útil usar um nome significativo para o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="1d93b-147">It's helpful to use a meaningful name for the migration file.</span></span>

<a name="ssox"></a>

<span data-ttu-id="1d93b-148">Se você excluir todos os registros no BD, o inicializador propagará o BD e incluirá o campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="1d93b-148">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="1d93b-149">Faça isso com os links Excluir no navegador ou no [SSOX](xref:tutorials/razor-pages/sql#ssox) (Pesquisador de Objetos do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="1d93b-149">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span> <span data-ttu-id="1d93b-150">Para excluir o banco de dados do SSOX:</span><span class="sxs-lookup"><span data-stu-id="1d93b-150">To delete the database from SSOX:</span></span>

* <span data-ttu-id="1d93b-151">Selecione o banco de dados no SSOX.</span><span class="sxs-lookup"><span data-stu-id="1d93b-151">Select the database in SSOX.</span></span>
* <span data-ttu-id="1d93b-152">Clique com o botão direito do mouse no banco de dados e selecione *Excluir*.</span><span class="sxs-lookup"><span data-stu-id="1d93b-152">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="1d93b-153">Marque **Fechar conexões existentes**.</span><span class="sxs-lookup"><span data-stu-id="1d93b-153">Check **Close existing connections**.</span></span>
* <span data-ttu-id="1d93b-154">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="1d93b-154">Select **OK**.</span></span>
* <span data-ttu-id="1d93b-155">No [PMC](xref:tutorials/razor-pages/new-field#pmc), atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="1d93b-155">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1d93b-156">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1d93b-156">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1d93b-157"><!-- copy/paste this tab to the next. Not worth an include  --> O SQLite não dá suporte a migrações.</span><span class="sxs-lookup"><span data-stu-id="1d93b-157"><!-- copy/paste this tab to the next. Not worth an include  --> SQLite doesn't support migrations.</span></span>

* <span data-ttu-id="1d93b-158">Exclua o banco de dados ou altere o nome do banco de dados no arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1d93b-158">Delete the database or change the database name in the *appsettings.json* file.</span></span>
* <span data-ttu-id="1d93b-159">Exclua a pasta *Migrations* (e todos os arquivos dela).</span><span class="sxs-lookup"><span data-stu-id="1d93b-159">Delete the *Migrations* folder (and all the files in the folder).</span></span>

<span data-ttu-id="1d93b-160">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="1d93b-160">Run the following .NET Core CLI commands:</span></span>

```console
dotnet ef migrations add Rating
dotnet ef database update
```

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1d93b-161">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1d93b-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1d93b-162">O SQLite não dá suporte a migrações.</span><span class="sxs-lookup"><span data-stu-id="1d93b-162">SQLite doesn't support migrations.</span></span>

* <span data-ttu-id="1d93b-163">Exclua o banco de dados ou altere o nome do banco de dados no arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="1d93b-163">Delete the database or change the database name in the *appsettings.json* file.</span></span>
* <span data-ttu-id="1d93b-164">Exclua a pasta *Migrations* (e todos os arquivos dela).</span><span class="sxs-lookup"><span data-stu-id="1d93b-164">Delete the *Migrations* folder (and all the files in the folder).</span></span>

<span data-ttu-id="1d93b-165">Execute os seguintes comandos da CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="1d93b-165">Run the following .NET Core CLI commands:</span></span>

```console
dotnet ef migrations add Rating
dotnet ef database update
```

---  
<!-- End of VS tabs -->

<span data-ttu-id="1d93b-166">Execute o aplicativo e verifique se você pode criar/editar/exibir filmes com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="1d93b-166">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="1d93b-167">Se o banco de dados não for propagado, defina um ponto de interrupção no método `SeedData.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="1d93b-167">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1d93b-168">[Anterior: Adicionando uma pesquisa](xref:tutorials/razor-pages/search)
> [Próximo: Adicionando Validação](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="1d93b-168">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>
