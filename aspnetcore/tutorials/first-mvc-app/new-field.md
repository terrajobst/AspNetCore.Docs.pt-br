---
title: Adicionar um novo campo a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Saiba como usar as Migrações do Entity Framework Code First para adicionar um novo campo a um modelo e migrar essa alteração para um banco de dados.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: f352a9c3573119028d1bc42fd622919ce0560e7c
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57841495"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="0a84b-103">Adicionar um novo campo a um aplicativo ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="0a84b-103">Add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="0a84b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0a84b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0a84b-105">Nesta seção, as Migrações do [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First são usadas para:</span><span class="sxs-lookup"><span data-stu-id="0a84b-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="0a84b-106">Adicionar um novo campo ao modelo.</span><span class="sxs-lookup"><span data-stu-id="0a84b-106">Add a new field to the model.</span></span>
* <span data-ttu-id="0a84b-107">Migrar o novo campo para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0a84b-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="0a84b-108">Ao usar o Code First do EF para criar automaticamente um banco de dados, o Code First:</span><span class="sxs-lookup"><span data-stu-id="0a84b-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="0a84b-109">Adiciona uma tabela no banco de dados para rastrear o esquema do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0a84b-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="0a84b-110">Verifica se o banco de dados está em sincronia com as classes de modelo das quais foi gerado.</span><span class="sxs-lookup"><span data-stu-id="0a84b-110">Verify the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="0a84b-111">Se ele não estiver sincronizado, o EF gerará uma exceção.</span><span class="sxs-lookup"><span data-stu-id="0a84b-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="0a84b-112">Isso facilita a descoberta de problemas de código/banco de dados inconsistente.</span><span class="sxs-lookup"><span data-stu-id="0a84b-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="0a84b-113">Adicionar uma Propriedade de Classificação ao Modelo de Filme</span><span class="sxs-lookup"><span data-stu-id="0a84b-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="0a84b-114">Adicionar uma propriedade `Rating` a *Models/Movie.cs*:</span><span class="sxs-lookup"><span data-stu-id="0a84b-114">Add a `Rating` property to *Models/Movie.cs*:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="0a84b-115">Compile o aplicativo (Ctrl+Shift+B).</span><span class="sxs-lookup"><span data-stu-id="0a84b-115">Build the app (Ctrl+Shift+B).</span></span>

<span data-ttu-id="0a84b-116">Como adicionou um novo campo à classe `Movie`, você também precisará atualizar a lista de permissões de associação para que essa nova propriedade seja incluída.</span><span class="sxs-lookup"><span data-stu-id="0a84b-116">Because you've added a new field to the `Movie` class, you need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="0a84b-117">Em *MoviesController.cs*, atualize o atributo `[Bind]` dos métodos de ação `Create` e `Edit` para incluir a propriedade `Rating`:</span><span class="sxs-lookup"><span data-stu-id="0a84b-117">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("ID,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="0a84b-118">Atualize os modelos de exibição para exibir, criar e editar a nova propriedade `Rating` na exibição do navegador.</span><span class="sxs-lookup"><span data-stu-id="0a84b-118">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="0a84b-119">Edite o arquivo */Views/Movies/Index.cshtml* e adicione um campo `Rating`:</span><span class="sxs-lookup"><span data-stu-id="0a84b-119">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="0a84b-120">Atualize */Views/Movies/Create.cshtml* com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="0a84b-120">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

<!-- VS -------------------------->
# <a name="visual-studio--visual-studio-for-mactabvisual-studiovisual-studio-mac"></a>[<span data-ttu-id="0a84b-121">Visual Studio/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a84b-121">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="0a84b-122">Copie/cole o “grupo de formulário” anterior e permita que o IntelliSense ajude você a atualizar os campos.</span><span class="sxs-lookup"><span data-stu-id="0a84b-122">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="0a84b-123">O IntelliSense funciona com os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="0a84b-123">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![O desenvolvedor digitou a letra R para o valor do atributo asp-for no segundo elemento de rótulo da exibição.](new-field/_static/cr.png)

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0a84b-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a84b-127">Visual Studio Code</span></span>](#tab/visual-studio-code)
<!-- This tab intentionally left blank. -->
---  
<!-- End of VS tabs -->

<span data-ttu-id="0a84b-128">Atualize a classe `SeedData` para que ela forneça um valor para a nova coluna.</span><span class="sxs-lookup"><span data-stu-id="0a84b-128">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="0a84b-129">Uma alteração de amostra é mostrada abaixo, mas é recomendável fazer essa alteração em cada `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="0a84b-129">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="0a84b-130">O aplicativo não funcionará até que o BD seja atualizado para incluir o novo campo.</span><span class="sxs-lookup"><span data-stu-id="0a84b-130">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="0a84b-131">Se ele tiver sido executado agora, o seguinte `SqlException` será gerado:</span><span class="sxs-lookup"><span data-stu-id="0a84b-131">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="0a84b-132">Este erro ocorre porque a classe de modelo Movie atualizada é diferente do esquema da tabela Movie do banco de dados existente.</span><span class="sxs-lookup"><span data-stu-id="0a84b-132">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="0a84b-133">(Não há nenhuma coluna `Rating` na tabela de banco de dados.)</span><span class="sxs-lookup"><span data-stu-id="0a84b-133">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="0a84b-134">Existem algumas abordagens para resolver o erro:</span><span class="sxs-lookup"><span data-stu-id="0a84b-134">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="0a84b-135">Faça com que o Entity Framework remova automaticamente e recrie o banco de dados com base no novo esquema de classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="0a84b-135">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="0a84b-136">Essa abordagem é muito conveniente no início do ciclo de desenvolvimento, quando você está fazendo o desenvolvimento ativo em um banco de dados de teste. Ela permite que você desenvolva rapidamente o modelo e o esquema de banco de dados juntos.</span><span class="sxs-lookup"><span data-stu-id="0a84b-136">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="0a84b-137">No entanto, a desvantagem é que você perde os dados existentes no banco de dados – portanto, você não deseja usar essa abordagem em um banco de dados de produção!</span><span class="sxs-lookup"><span data-stu-id="0a84b-137">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="0a84b-138">Muitas vezes, o uso de um inicializador para propagar um banco de dados com os dados de teste automaticamente é uma maneira produtiva de desenvolver um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0a84b-138">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="0a84b-139">Isso é uma boa abordagem para o desenvolvimento inicial e ao usar o SQLite.</span><span class="sxs-lookup"><span data-stu-id="0a84b-139">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="0a84b-140">Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo.</span><span class="sxs-lookup"><span data-stu-id="0a84b-140">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="0a84b-141">A vantagem dessa abordagem é que você mantém os dados.</span><span class="sxs-lookup"><span data-stu-id="0a84b-141">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="0a84b-142">Faça essa alteração manualmente ou criando um script de alteração de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0a84b-142">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="0a84b-143">Use as Migrações do Code First para atualizar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0a84b-143">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="0a84b-144">Para este tutorial, as Migrações do Code First são usadas.</span><span class="sxs-lookup"><span data-stu-id="0a84b-144">For this tutorial, Code First Migrations is used.</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a84b-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a84b-145">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a84b-146">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="0a84b-146">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![Menu do PMC](adding-model/_static/pmc.png)

<span data-ttu-id="0a84b-148">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="0a84b-148">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="0a84b-149">O comando `Add-Migration` informa a estrutura de migração para examinar o atual modelo `Movie` com o atual esquema de BD `Movie` e criar o código necessário para migrar o BD para o novo modelo.</span><span class="sxs-lookup"><span data-stu-id="0a84b-149">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="0a84b-150">O nome “Classificação” é arbitrário e é usado para nomear o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="0a84b-150">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="0a84b-151">É útil usar um nome significativo para o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="0a84b-151">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="0a84b-152">Se você excluir todos os registros do BD, o método de inicialização propagará o BD e incluirá o campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="0a84b-152">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0a84b-153">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="0a84b-153">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="0a84b-154">Excluir o banco de dados e usar as migrações para recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="0a84b-154">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="0a84b-155">Para excluir o banco de dados, exclua o arquivo de banco de dados (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="0a84b-155">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="0a84b-156">Depois, execute o comando `ef database update`:</span><span class="sxs-lookup"><span data-stu-id="0a84b-156">Then run the `ef database update` command:</span></span> 

```console
dotnet ef database update
```

---  
<!-- End of VS tabs -->

<span data-ttu-id="0a84b-157">Execute o aplicativo e verifique se você pode criar/editar/exibir filmes com um campo `Rating`.</span><span class="sxs-lookup"><span data-stu-id="0a84b-157">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="0a84b-158">Você deve adicionar o campo `Rating` aos modelos de exibição `Edit`, `Details` e `Delete`.</span><span class="sxs-lookup"><span data-stu-id="0a84b-158">You should add the `Rating` field to the `Edit`, `Details`, and `Delete` view templates.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="0a84b-159">[Anterior](search.md)
> [Próximo](validation.md)</span><span class="sxs-lookup"><span data-stu-id="0a84b-159">[Previous](search.md)
[Next](validation.md)</span></span>  
