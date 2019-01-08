---
uid: mvc/overview/getting-started/introduction/adding-a-new-field
title: Adicionando um novo campo | Microsoft Docs
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 10/17/2013
ms.assetid: 4085de68-d243-4378-8a64-86236ea8d2da
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-a-new-field
msc.type: authoredcontent
ms.openlocfilehash: 950ae17ebd6b0f15520c2a4e9372703f5374dfbe
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098682"
---
<a name="adding-a-new-field"></a><span data-ttu-id="3d5a9-102">Adicionando um Novo Campo</span><span class="sxs-lookup"><span data-stu-id="3d5a9-102">Adding a New Field</span></span>
====================
<span data-ttu-id="3d5a9-103">por [Rick Anderson]((https://twitter.com/RickAndMSFT))</span><span class="sxs-lookup"><span data-stu-id="3d5a9-103">by [Rick Anderson]((https://twitter.com/RickAndMSFT))</span></span>

[!INCLUDE [Tutorial Note](sample/code-location.md)]

<span data-ttu-id="3d5a9-104">Nesta seção, você usará migrações do Entity Framework Code First para migrar algumas alterações para as classes de modelo para que a alteração será aplicada ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-104">In this section you'll use Entity Framework Code First Migrations to migrate some changes to the model classes so the change is applied to the database.</span></span>

<span data-ttu-id="3d5a9-105">Por padrão, quando você usa o Entity Framework Code First para criar automaticamente um banco de dados, como você fez neste tutorial, Code First adiciona uma tabela no banco de dados para ajudar a controlar se o esquema do banco de dados está em sincronizado com as classes de modelo, que ele foi gerado.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-105">By default, when you use Entity Framework Code First to automatically create a database, as you did earlier in this tutorial, Code First adds a table to the database to help track whether the schema of the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="3d5a9-106">Se não estiverem sincronizados, o Entity Framework gera um erro.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-106">If they aren't in sync, the Entity Framework throws an error.</span></span> <span data-ttu-id="3d5a9-107">Isso torna mais fácil rastrear problemas em tempo de desenvolvimento que caso contrário, apenas talvez (por erros obscuros) em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-107">This makes it easier to track down issues at development time that you might otherwise only find (by obscure errors) at run time.</span></span>

## <a name="setting-up-code-first-migrations-for-model-changes"></a><span data-ttu-id="3d5a9-108">Configuração de migrações do Code First para alterações do modelo</span><span class="sxs-lookup"><span data-stu-id="3d5a9-108">Setting up Code First Migrations for Model Changes</span></span>

<span data-ttu-id="3d5a9-109">Navegue até o Gerenciador de soluções.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-109">Navigate to Solution Explorer.</span></span> <span data-ttu-id="3d5a9-110">Clique com botão direito do *Movies.mdf* do arquivo e selecione **excluir** para remover o banco de dados de filmes.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-110">Right click on the *Movies.mdf* file and select **Delete** to remove the movies database.</span></span> <span data-ttu-id="3d5a9-111">Se você não vir as *Movies.mdf* do arquivo, clique no **Mostrar todos os arquivos** ícone mostrado abaixo no contorno vermelho.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-111">If you don't see the *Movies.mdf* file, click on the **Show All Files** icon shown below in the red outline.</span></span>

![](adding-a-new-field/_static/image1.png)

<span data-ttu-id="3d5a9-112">Compile o aplicativo para garantir que não existem erros.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-112">Build the application to make sure there are no errors.</span></span>

<span data-ttu-id="3d5a9-113">Dos **ferramentas** menu, clique em **Gerenciador de pacotes NuGet** e, em seguida, **Package Manager Console**.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-113">From the **Tools** menu, click **NuGet Package Manager** and then **Package Manager Console**.</span></span>

![Adicionar pacote Man](adding-a-new-field/_static/image2.png)

<span data-ttu-id="3d5a9-115">No **Package Manager Console** janela no `PM>` prompt insira</span><span class="sxs-lookup"><span data-stu-id="3d5a9-115">In the **Package Manager Console** window at the `PM>` prompt enter</span></span>

<span data-ttu-id="3d5a9-116">Enable-Migrations -ContextTypeName MvcMovie.Models.MovieDBContext</span><span class="sxs-lookup"><span data-stu-id="3d5a9-116">Enable-Migrations -ContextTypeName MvcMovie.Models.MovieDBContext</span></span>

![](adding-a-new-field/_static/image3.png)

<span data-ttu-id="3d5a9-117">O **Enable-Migrations** comando (mostrado acima) cria um *Configuration.cs* arquivo em uma nova *migrações* pasta.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-117">The **Enable-Migrations** command (shown above) creates a *Configuration.cs* file in a new *Migrations* folder.</span></span>

![](adding-a-new-field/_static/image4.png)

<span data-ttu-id="3d5a9-118">O Visual Studio abre o *Configuration.cs* arquivo.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-118">Visual Studio opens the *Configuration.cs* file.</span></span> <span data-ttu-id="3d5a9-119">Substitua os `Seed` método na *Configuration.cs* arquivo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="3d5a9-119">Replace the `Seed` method in the *Configuration.cs* file with the following code:</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample1.cs)]

<span data-ttu-id="3d5a9-120">Passe o mouse sobre a linha vermelha ondulada sob `Movie` e clique em `Show Potential Fixes` e, em seguida, clique em **usando** **mvcmovie. Models;**</span><span class="sxs-lookup"><span data-stu-id="3d5a9-120">Hover over the red squiggly line under `Movie` and click `Show Potential Fixes` and then click **using** **MvcMovie.Models;**</span></span>

![](adding-a-new-field/_static/image5.png)

<span data-ttu-id="3d5a9-121">Isso adiciona a seguinte instrução using:</span><span class="sxs-lookup"><span data-stu-id="3d5a9-121">Doing so adds the following using statement:</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample2.cs)]

> [!NOTE]
> 
> <span data-ttu-id="3d5a9-122">Code First Migrations chamadas a `Seed` método após cada migração (ou seja, chamando **Atualizar banco de dados** no Console do Gerenciador de pacotes), e esse método atualiza as linhas que já foram inseridas ou insere-os se eles ainda não existem.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-122">Code First Migrations calls the `Seed` method after every migration (that is, calling **update-database** in the Package Manager Console), and this method updates rows that have already been inserted, or inserts them if they don't exist yet.</span></span>
> 
> <span data-ttu-id="3d5a9-123">O [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) método no código a seguir executa uma operação "upsert":</span><span class="sxs-lookup"><span data-stu-id="3d5a9-123">The [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) method in the following code performs an "upsert" operation:</span></span>
> 
> [!code-csharp[Main](adding-a-new-field/samples/sample3.cs)]
> 
> <span data-ttu-id="3d5a9-124">Porque o [semente](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) método é executado com cada migração, você simplesmente não é possível inserir dados, porque as linhas que você está tentando adicionar já estará lá após a primeira migração que cria o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-124">Because the [Seed](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) method runs with every migration, you can't just insert data, because the rows you are trying to add will already be there after the first migration that creates the database.</span></span> <span data-ttu-id="3d5a9-125">O "[upsert](http://en.wikipedia.org/wiki/Upsert)" operação impede que os erros que acontecem se você tentar inserir uma linha que já existe, mas ela substitui as alterações nos dados feitas durante o teste o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-125">The "[upsert](http://en.wikipedia.org/wiki/Upsert)" operation prevents errors that would happen if you try to insert a row that already exists, but it overrides any changes to data that you may have made while testing the application.</span></span> <span data-ttu-id="3d5a9-126">Com os dados de teste em algumas tabelas você talvez não queira que aconteça: em alguns casos quando você altera os dados durante o teste você deseja as suas alterações para permanecer após as atualizações do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-126">With test data in some tables you might not want that to happen: in some cases when you change data while testing you want your changes to remain after database updates.</span></span> <span data-ttu-id="3d5a9-127">Nesse caso, você deseja fazer uma operação de inserção condicional: inserir uma linha apenas se ele ainda não existir.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-127">In that case you want to do a conditional insert operation: insert a row only if it doesn't already exist.</span></span>   
> 
> <span data-ttu-id="3d5a9-128">O primeiro parâmetro passado para o [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) método Especifica a propriedade a ser usada para verificar se já existe uma linha.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-128">The first parameter passed to the [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) method specifies the property to use to check if a row already exists.</span></span> <span data-ttu-id="3d5a9-129">Para os dados de filme de teste que você fornecer, o `Title` propriedade pode ser usada para essa finalidade, pois cada cargo da lista é exclusivo:</span><span class="sxs-lookup"><span data-stu-id="3d5a9-129">For the test movie data that you are providing, the `Title` property can be used for this purpose since each title in the list is unique:</span></span>
> 
> [!code-csharp[Main](adding-a-new-field/samples/sample4.cs)]
> 
> <span data-ttu-id="3d5a9-130">Esse código supõe que os títulos sejam exclusivos.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-130">This code assumes that titles are unique.</span></span> <span data-ttu-id="3d5a9-131">Se você adicionar um título duplicado manualmente, você obterá a seguinte exceção na próxima vez que você executar uma migração.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-131">If you manually add a duplicate title, you'll get the following exception the next time you perform a migration.</span></span>   
> 
>  <span data-ttu-id="3d5a9-132">*Sequência contém mais de um elemento*</span><span class="sxs-lookup"><span data-stu-id="3d5a9-132">*Sequence contains more than one element*</span></span>  
> 
> <span data-ttu-id="3d5a9-133">Para obter mais informações sobre o [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) método, consulte [tome cuidado com o EF 4.3 AddOrUpdate método](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/)...</span><span class="sxs-lookup"><span data-stu-id="3d5a9-133">For more information about the [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) method, see [Take care with EF 4.3 AddOrUpdate Method](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/)..</span></span>


<span data-ttu-id="3d5a9-134">**Pressione CTRL-SHIFT-B para compilar o projeto.** (As etapas a seguir falhará se você não criar neste momento).</span><span class="sxs-lookup"><span data-stu-id="3d5a9-134">**Press CTRL-SHIFT-B to build the project.**(The following steps will fail if you don't build at this point.)</span></span>

<span data-ttu-id="3d5a9-135">A próxima etapa é criar um `DbMigration` classe para a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-135">The next step is to create a `DbMigration` class for the initial migration.</span></span> <span data-ttu-id="3d5a9-136">Essa migração cria um novo banco de dados, é por isso que você excluiu o *movie.mdf* arquivo em uma etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-136">This migration creates a new database, that's why you deleted the *movie.mdf* file in a previous step.</span></span>

<span data-ttu-id="3d5a9-137">No **Package Manager Console** janela, digite o comando `add-migration Initial` para criar a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-137">In the **Package Manager Console** window, enter the command `add-migration Initial` to create the initial migration.</span></span> <span data-ttu-id="3d5a9-138">O nome "Inicial" é arbitrário e é usado para nomear o arquivo de migração criado.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-138">The name "Initial" is arbitrary and is used to name the migration file created.</span></span>

![](adding-a-new-field/_static/image6.png)

<span data-ttu-id="3d5a9-139">Migrações do Code First cria outro arquivo de classe na *migrações* pasta (com o nome *{carimbo de data}\_Initial.cs* ), e essa classe contém código que cria o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-139">Code First Migrations creates another class file in the *Migrations* folder (with the name *{DateStamp}\_Initial.cs* ), and this class contains code that creates the database schema.</span></span> <span data-ttu-id="3d5a9-140">O nome do arquivo de migração é previamente corrigido com um carimbo de hora para ajudar com a ordenação.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-140">The migration filename is pre-fixed with a timestamp to help with ordering.</span></span> <span data-ttu-id="3d5a9-141">Examine os *{carimbo de data}\_Initial.cs* arquivo, ele contém as instruções para criar o `Movies` tabela para o banco de dados do filme.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-141">Examine the *{DateStamp}\_Initial.cs* file, it contains the instructions to create the `Movies` table for the Movie DB.</span></span> <span data-ttu-id="3d5a9-142">Quando você atualiza o banco de dados nas instruções a seguir, isso *{carimbo de data}\_Initial.cs* arquivo será executado e criar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-142">When you update the database in the instructions below, this *{DateStamp}\_Initial.cs* file will run and create the DB schema.</span></span> <span data-ttu-id="3d5a9-143">Em seguida, a **semente** método será executado para preencher o banco de dados com dados de teste.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-143">Then the **Seed** method will run to populate the DB with test data.</span></span>

<span data-ttu-id="3d5a9-144">No **Package Manager Console**, digite o comando `update-database` para criar o banco de dados e executar o `Seed` método.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-144">In the **Package Manager Console**, enter the command `update-database` to create the database and run the `Seed` method.</span></span>

![](adding-a-new-field/_static/image7.png)

<span data-ttu-id="3d5a9-145">Se você receber um erro que indica uma tabela já existe e não pode ser criada, provavelmente é porque você executou o aplicativo depois que você excluiu o banco de dados e antes da execução `update-database`.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-145">If you get an error that indicates a table already exists and can't be created, it is probably because you ran the application after you deleted the database and before you executed `update-database`.</span></span> <span data-ttu-id="3d5a9-146">Nesse caso, exclua o *Movies.mdf* novamente e repita a `update-database` comando.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-146">In that case, delete the *Movies.mdf* file again and retry the `update-database` command.</span></span> <span data-ttu-id="3d5a9-147">Se você ainda receber um erro, exclua a pasta migrações e o conteúdo, comece com as instruções na parte superior desta página (que é exclusão a *Movies.mdf* de arquivo e em seguida, vá para Enable-Migrations).</span><span class="sxs-lookup"><span data-stu-id="3d5a9-147">If you still get an error, delete the migrations folder and contents then start with the instructions at the top of this page (that is delete the *Movies.mdf* file then proceed to Enable-Migrations).</span></span> <span data-ttu-id="3d5a9-148">Se você ainda receber um erro, abra o Pesquisador de objetos do SQL Server e remover o banco de dados da lista.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-148">If you still get an error, open SQL Server Object Explorer and remove the database from the list.</span></span>

<span data-ttu-id="3d5a9-149">Execute o aplicativo e navegue até a */Movies* URL.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-149">Run the application and navigate to the */Movies* URL.</span></span> <span data-ttu-id="3d5a9-150">Os dados de semente são exibidos.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-150">The seed data is displayed.</span></span>

![](adding-a-new-field/_static/image8.png)

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="3d5a9-151">Adicionando uma propriedade de classificação ao modelo de filme</span><span class="sxs-lookup"><span data-stu-id="3d5a9-151">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="3d5a9-152">Comece adicionando um novo `Rating` propriedade à existente `Movie` classe.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-152">Start by adding a new `Rating` property to the existing `Movie` class.</span></span> <span data-ttu-id="3d5a9-153">Abra o *Models\Movie.cs* arquivo e adicione o `Rating` propriedade parecida com esta:</span><span class="sxs-lookup"><span data-stu-id="3d5a9-153">Open the *Models\Movie.cs* file and add the `Rating` property like this one:</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample5.cs)]

<span data-ttu-id="3d5a9-154">Completo `Movie` classe agora parece o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="3d5a9-154">The complete `Movie` class now looks like the following code:</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample6.cs?highlight=12)]

<span data-ttu-id="3d5a9-155">Compile o aplicativo (Ctrl + Shift + B).</span><span class="sxs-lookup"><span data-stu-id="3d5a9-155">Build the application (Ctrl+Shift+B).</span></span>

<span data-ttu-id="3d5a9-156">Como você adicionou um novo campo para o `Movie` classe, você também precisará atualizar a associação *lista branca* para que essa nova propriedade seja incluída.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-156">Because you've added a new field to the `Movie` class, you also need to update the binding *white list* so this new property will be included.</span></span> <span data-ttu-id="3d5a9-157">Atualizar o `bind` de atributo para `Create` e `Edit` métodos de ação para incluir o `Rating` propriedade:</span><span class="sxs-lookup"><span data-stu-id="3d5a9-157">Update the `bind` attribute for `Create` and `Edit` action methods to include the `Rating` property:</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample7.cs?highlight=1)]

<span data-ttu-id="3d5a9-158">Você também precisa atualizar os modelos de exibição para exibir, criar e editar a nova propriedade `Rating` na exibição do navegador.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-158">You also need to update the view templates in order to display, create and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="3d5a9-159">Abra o *\Views\Movies\Index.cshtml* arquivo e adicione um `<th>Rating</th>` título de coluna logo após o **preço** coluna.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-159">Open the *\Views\Movies\Index.cshtml* file and add a `<th>Rating</th>` column heading just after the **Price** column.</span></span> <span data-ttu-id="3d5a9-160">Em seguida, adicione uma `<td>` coluna, próximo ao final do modelo para renderizar o `@item.Rating` valor.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-160">Then add a `<td>` column near the end of the template to render the `@item.Rating` value.</span></span> <span data-ttu-id="3d5a9-161">Abaixo está o que a atualização *index. cshtml* modelo de exibição se parece com:</span><span class="sxs-lookup"><span data-stu-id="3d5a9-161">Below is what the updated *Index.cshtml* view template looks like:</span></span>

[!code-cshtml[Main](adding-a-new-field/samples/sample8.cshtml?highlight=31-33,52-54)]

<span data-ttu-id="3d5a9-162">Em seguida, abra o *\Views\Movies\Create.cshtml* arquivo e adicione o `Rating` campo pela seguinte marcação destacadas.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-162">Next, open the *\Views\Movies\Create.cshtml* file and add the `Rating` field with the following highlighed markup.</span></span> <span data-ttu-id="3d5a9-163">Isso renderiza uma caixa de texto para que você possa especificar uma classificação quando um novo filme é criado.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-163">This renders a text box so that you can specify a rating when a new movie is created.</span></span>

[!code-cshtml[Main](adding-a-new-field/samples/sample9.cshtml?highlight=9-15)]

<span data-ttu-id="3d5a9-164">Agora você atualizou o código do aplicativo para dar suporte a novos `Rating` propriedade.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-164">You've now updated the application code to support the new `Rating` property.</span></span>

<span data-ttu-id="3d5a9-165">Execute o aplicativo e navegue até a */Movies* URL.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-165">Run the application and navigate to the */Movies* URL.</span></span> <span data-ttu-id="3d5a9-166">Quando você fizer isso, no entanto, você verá um dos seguintes erros:</span><span class="sxs-lookup"><span data-stu-id="3d5a9-166">When you do this, though, you'll see one of the following errors:</span></span>

![](adding-a-new-field/_static/image9.png)  
  
<span data-ttu-id="3d5a9-167">O modelo fazendo o contexto de 'MovieDBContext' foi alterado desde que o banco de dados foi criado.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-167">The model backing the 'MovieDBContext' context has changed since the database was created.</span></span> <span data-ttu-id="3d5a9-168">Considere o uso de migrações do Code First para atualizar o banco de dados (https://go.microsoft.com/fwlink/?LinkId=238269).</span><span class="sxs-lookup"><span data-stu-id="3d5a9-168">Consider using Code First Migrations to update the database (https://go.microsoft.com/fwlink/?LinkId=238269).</span></span>

![](adding-a-new-field/_static/image10.png)

<span data-ttu-id="3d5a9-169">Você está vendo esse erro porque atualizada `Movie` classe de modelo no aplicativo agora é diferente do esquema do `Movie` tabela do banco de dados existente.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-169">You're seeing this error because the updated `Movie` model class in the application is now different than the schema of the `Movie` table of the existing database.</span></span> <span data-ttu-id="3d5a9-170">(Não há nenhuma coluna `Rating` na tabela de banco de dados.)</span><span class="sxs-lookup"><span data-stu-id="3d5a9-170">(There's no `Rating` column in the database table.)</span></span>


<span data-ttu-id="3d5a9-171">Existem algumas abordagens para resolver o erro:</span><span class="sxs-lookup"><span data-stu-id="3d5a9-171">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="3d5a9-172">Faça com que o Entity Framework remova automaticamente e recrie o banco de dados com base no novo esquema de classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-172">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="3d5a9-173">Essa abordagem é muito conveniente no início do ciclo de desenvolvimento, quando você está fazendo o desenvolvimento ativo em um banco de dados de teste; ela permite que você desenvolva rapidamente o modelo e o esquema de banco de dados juntos.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-173">This approach is very convenient early in the development cycle when you are doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="3d5a9-174">A desvantagem, no entanto, é que você perde os dados existentes no banco de dados — para que você *não* para usar essa abordagem em um banco de dados de produção!</span><span class="sxs-lookup"><span data-stu-id="3d5a9-174">The downside, though, is that you lose existing data in the database — so you *don't* want to use this approach on a production database!</span></span> <span data-ttu-id="3d5a9-175">Muitas vezes, o uso de um inicializador para propagar um banco de dados com os dados de teste automaticamente é uma maneira produtiva de desenvolver um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-175">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="3d5a9-176">Para obter mais informações sobre inicializadores de banco de dados do Entity Framework, consulte [tutorial do ASP.NET MVC/Entity Framework](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span><span class="sxs-lookup"><span data-stu-id="3d5a9-176">For more information on Entity Framework database initializers, see [ASP.NET MVC/Entity Framework tutorial](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span></span>
2. <span data-ttu-id="3d5a9-177">Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-177">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="3d5a9-178">A vantagem dessa abordagem é que você mantém os dados.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-178">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="3d5a9-179">Faça essa alteração manualmente ou criando um script de alteração de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-179">You can make this change either manually or by creating a database change script.</span></span>
3. <span data-ttu-id="3d5a9-180">Use as Migrações do Code First para atualizar o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-180">Use Code First Migrations to update the database schema.</span></span>


<span data-ttu-id="3d5a9-181">Para este tutorial, usaremos as Migrações do Code First.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-181">For this tutorial, we'll use Code First Migrations.</span></span>

<span data-ttu-id="3d5a9-182">Atualize o método de propagação para que ele forneça um valor para a nova coluna.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-182">Update the Seed method so that it provides a value for the new column.</span></span> <span data-ttu-id="3d5a9-183">Abra o arquivo Migrations\Configuration.cs e adicionar um campo de classificação para cada objeto de filme.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-183">Open Migrations\Configuration.cs file and add a Rating field to each Movie object.</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample10.cs?highlight=6)]

<span data-ttu-id="3d5a9-184">Compile a solução e, em seguida, abra o **Package Manager Console** janela e digite o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3d5a9-184">Build the solution, and then open the **Package Manager Console** window and enter the following command:</span></span>

`add-migration Rating`

<span data-ttu-id="3d5a9-185">O `add-migration` comando informa a estrutura de migração para examinar o modelo de filme atual com o esquema de banco de dados do filme atual e crie o código necessário para migrar o banco de dados para o novo modelo.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-185">The `add-migration` command tells the migration framework to examine the current movie model with the current movie DB schema and create the necessary code to migrate the DB to the new model.</span></span> <span data-ttu-id="3d5a9-186">O nome *classificação* é arbitrário e é usado para nomear o arquivo de migração.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-186">The name *Rating* is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="3d5a9-187">É útil usar um nome significativo para a etapa de migração.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-187">It's helpful to use a meaningful name for the migration step.</span></span>

<span data-ttu-id="3d5a9-188">Quando esse comando for concluído, o Visual Studio abre o arquivo de classe que define o novo `DbMigration` classe, derivada e, no `Up` método, você pode ver o código que cria a nova coluna.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-188">When this command finishes, Visual Studio opens the class file that defines the new `DbMigration` derived class, and in the `Up` method you can see the code that creates the new column.</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample11.cs)]

<span data-ttu-id="3d5a9-189">Compile a solução e, em seguida, insira o `update-database` comando os **Package Manager Console** janela.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-189">Build the solution, and then enter the `update-database` command in the **Package Manager Console** window.</span></span>

<span data-ttu-id="3d5a9-190">A imagem a seguir mostra a saída na **Package Manager Console** janela (o carimbo de data acrescentando *classificação* será diferente.)</span><span class="sxs-lookup"><span data-stu-id="3d5a9-190">The following image shows the output in the **Package Manager Console** window (The date stamp prepending *Rating* will be different.)</span></span>

![](adding-a-new-field/_static/image11.png)

<span data-ttu-id="3d5a9-191">Execute novamente o aplicativo e navegue até a URL /Movies.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-191">Re-run the application and navigate to the /Movies URL.</span></span> <span data-ttu-id="3d5a9-192">Você pode ver o novo campo de classificação.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-192">You can see the new Rating field.</span></span>

![](adding-a-new-field/_static/image12.png)

<span data-ttu-id="3d5a9-193">Clique o **criar novo** link para adicionar um novo filme.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-193">Click the **Create New** link to add a new movie.</span></span> <span data-ttu-id="3d5a9-194">Observe que você pode adicionar uma classificação.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-194">Note that you can add a rating.</span></span>

![7_CreateRioII](adding-a-new-field/_static/image13.png)

<span data-ttu-id="3d5a9-196">Clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-196">Click **Create**.</span></span> <span data-ttu-id="3d5a9-197">O novo filme, incluindo a classificação é exibido nos listagem de filmes:</span><span class="sxs-lookup"><span data-stu-id="3d5a9-197">The new movie, including the rating, now shows up in the movies listing:</span></span>

![7_ourNewMovie_SM](adding-a-new-field/_static/image14.png)

<span data-ttu-id="3d5a9-199">Agora que o projeto está usando as migrações, você não precisará remover o banco de dados quando você adiciona um novo campo ou caso contrário, atualize o esquema.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-199">Now that the project is using migrations, you won't need to drop the database when you add a new field or otherwise update the schema.</span></span> <span data-ttu-id="3d5a9-200">Na próxima seção, vamos fazer mais alterações de esquema e usar as migrações para atualizar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-200">In the next section, we'll make more schema changes and use migrations to update the database.</span></span>

<span data-ttu-id="3d5a9-201">Você também deve adicionar o `Rating` campo para os modelos de exibição de edição, detalhes e exclusão.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-201">You should also add the `Rating` field to the Edit, Details, and Delete view templates.</span></span>

<span data-ttu-id="3d5a9-202">Você poderia digitar o comando "update-database" a **Package Manager Console** janela novamente e nenhum código de migração seriam executado, porque o esquema coincide com o modelo.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-202">You could enter the "update-database" command in the **Package Manager Console** window again and no migration code would run, because the schema matches the model.</span></span> <span data-ttu-id="3d5a9-203">No entanto, em execução "update-database" será executado o `Seed` método novamente, e se você alterou os dados de semente, as alterações serão perdidas porque o `Seed` dados upserts de método.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-203">However, running "update-database" will run the `Seed` method again, and if you changed any of the Seed data, the changes will be lost because the `Seed` method upserts data.</span></span> <span data-ttu-id="3d5a9-204">Você pode ler mais sobre o `Seed` método no de Tom Dykstra popular [tutorial do ASP.NET MVC/Entity Framework](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span><span class="sxs-lookup"><span data-stu-id="3d5a9-204">You can read more about the `Seed` method in Tom Dykstra's popular [ASP.NET MVC/Entity Framework tutorial](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span></span>

<span data-ttu-id="3d5a9-205">Nesta seção, você viu como você pode modificar objetos de modelo e manter o banco de dados em sincronia com as alterações.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-205">In this section you saw how you can modify model objects and keep the database in sync with the changes.</span></span> <span data-ttu-id="3d5a9-206">Você também aprendeu uma maneira de preencher um banco de dados recém-criado com dados de exemplo para que você pode experimentar os cenários.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-206">You also learned a way to populate a newly created database with sample data so you can try out scenarios.</span></span> <span data-ttu-id="3d5a9-207">Isso era apenas uma rápida introdução ao Code First, consulte [criando um modelo de dados do Entity Framework para um aplicativo ASP.NET MVC](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) para obter um tutorial mais completo sobre o assunto.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-207">This was just a quick introduction to Code First, see [Creating an Entity Framework Data Model for an ASP.NET MVC Application](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) for a more complete tutorial on the subject.</span></span> <span data-ttu-id="3d5a9-208">Em seguida, vamos dar uma olhada em como você pode adicionar lógica de validação mais avançada para as classes de modelo e habilitar algumas regras de negócio a serem impostos.</span><span class="sxs-lookup"><span data-stu-id="3d5a9-208">Next, let's look at how you can add richer validation logic to the model classes and enable some business rules to be enforced.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="3d5a9-209">[Anterior](adding-search.md)
> [Próximo](adding-validation.md)</span><span class="sxs-lookup"><span data-stu-id="3d5a9-209">[Previous](adding-search.md)
[Next](adding-validation.md)</span></span>
