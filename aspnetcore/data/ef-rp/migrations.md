---
title: Páginas Razor com o EF Core no ASP.NET Core – Migrações – 4 de 8
author: tdykstra
description: Neste tutorial, você começa a usar o recurso de migrações do EF Core para gerenciar alterações do modelo de dados em um aplicativo ASP.NET Core MVC.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/migrations
ms.openlocfilehash: efcf62d56a7b4cee4780d5f0475b4ef363fe1897
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187075"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a><span data-ttu-id="98a7a-103">Páginas Razor com o EF Core no ASP.NET Core – Migrações – 4 de 8</span><span class="sxs-lookup"><span data-stu-id="98a7a-103">Razor Pages with EF Core in ASP.NET Core - Migrations - 4 of 8</span></span>

<span data-ttu-id="98a7a-104">Por [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="98a7a-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="98a7a-105">Este tutorial apresenta o recurso de migrações do EF Core para gerenciar alterações do modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="98a7a-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="98a7a-106">Quando um novo aplicativo é desenvolvido, o modelo de dados é alterado com frequência.</span><span class="sxs-lookup"><span data-stu-id="98a7a-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="98a7a-107">Sempre que o modelo é alterado, ele fica fora de sincronia com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="98a7a-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="98a7a-108">Esta série de tutoriais começa configurando o Entity Framework para criar o banco de dados, caso ele não exista.</span><span class="sxs-lookup"><span data-stu-id="98a7a-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="98a7a-109">Cada vez que o modelo de dados é alterado, você precisa remover o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="98a7a-109">Each time the data model changes, you have to drop the database.</span></span> <span data-ttu-id="98a7a-110">Na próxima vez em que o aplicativo for executado, a chamada para `EnsureCreated` recriará o banco de dados para que corresponda ao novo modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="98a7a-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="98a7a-111">A classe `DbInitializer` então é executada para propagar o novo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="98a7a-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="98a7a-112">Essa abordagem para manter o banco de dados em sincronia com o modelo de dados funciona bem até que você implante o aplicativo em produção.</span><span class="sxs-lookup"><span data-stu-id="98a7a-112">This approach to keeping the database in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="98a7a-113">Quando o aplicativo é executado em produção, normalmente, ele armazena dados que precisam ser mantidos.</span><span class="sxs-lookup"><span data-stu-id="98a7a-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="98a7a-114">O aplicativo não pode começar com um banco de dados de teste sempre que uma alteração é feita (como a adição de uma nova coluna).</span><span class="sxs-lookup"><span data-stu-id="98a7a-114">The app can't start with a test database each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="98a7a-115">O recurso Migrações do EF Core resolve esse problema, permitindo que o EF Core atualize o esquema de banco de dados em vez de criar um novo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="98a7a-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the database schema instead of creating a new database.</span></span>

<span data-ttu-id="98a7a-116">Em vez de remover e recriar o banco de dados quando o modelo de dados é alterado, as migrações atualizam o esquema e retêm os dados existentes.</span><span class="sxs-lookup"><span data-stu-id="98a7a-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="98a7a-117">Remover o banco de dados</span><span class="sxs-lookup"><span data-stu-id="98a7a-117">Drop the database</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98a7a-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98a7a-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="98a7a-119">Use o SSOX **(Pesquisador de Objetos do SQL Server)** para excluir o banco de dados ou execute o seguinte comando no PMC **(Console do Gerenciador de Pacotes)** :</span><span class="sxs-lookup"><span data-stu-id="98a7a-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98a7a-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98a7a-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="98a7a-121">Execute o comando a seguir no prompt de comando para instalar as ferramentas da CLI do EF:</span><span class="sxs-lookup"><span data-stu-id="98a7a-121">Run the following command at a command prompt to install the EF CLI tools:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="98a7a-122">No prompt de comando, navegue até a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="98a7a-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="98a7a-123">A pasta do projeto contém o arquivo *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="98a7a-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="98a7a-124">Exclua o arquivo *CU.db* ou execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="98a7a-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="98a7a-125">Criar uma migração inicial</span><span class="sxs-lookup"><span data-stu-id="98a7a-125">Create an initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98a7a-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98a7a-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="98a7a-127">Executar os seguintes comandos no PMC:</span><span class="sxs-lookup"><span data-stu-id="98a7a-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98a7a-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98a7a-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="98a7a-129">Verifique se o prompt de comando está na pasta do projeto e execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="98a7a-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a><span data-ttu-id="98a7a-130">Métodos Para Cima e Para Baixo</span><span class="sxs-lookup"><span data-stu-id="98a7a-130">Up and Down methods</span></span>

<span data-ttu-id="98a7a-131">O comando `migrations add` do EF Core gerou um código para criar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="98a7a-131">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="98a7a-132">Esse código de migrações está localizado no arquivo *Migrations\<timestamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="98a7a-132">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="98a7a-133">O método `Up` da classe `InitialCreate` cria as tabelas de banco de dados que correspondem aos conjuntos de entidades do modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="98a7a-133">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="98a7a-134">O método `Down` exclui-os, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="98a7a-134">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="98a7a-135">O código anterior refere-se à migração inicial.</span><span class="sxs-lookup"><span data-stu-id="98a7a-135">The preceding code is for the initial migration.</span></span> <span data-ttu-id="98a7a-136">O código:</span><span class="sxs-lookup"><span data-stu-id="98a7a-136">The code:</span></span>

* <span data-ttu-id="98a7a-137">Foi gerado pelo comando `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="98a7a-137">Was generated by the `migrations add InitialCreate` command.</span></span> 
* <span data-ttu-id="98a7a-138">É executado pelo comando `database update`.</span><span class="sxs-lookup"><span data-stu-id="98a7a-138">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="98a7a-139">Cria um banco de dados para o modelo de dados especificado pela classe de contexto do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="98a7a-139">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="98a7a-140">O parâmetro de nome da migração ("InitialCreate" no exemplo) é usado para o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="98a7a-140">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="98a7a-141">O nome da migração pode ser qualquer nome de arquivo válido.</span><span class="sxs-lookup"><span data-stu-id="98a7a-141">The migration name can be any valid file name.</span></span> <span data-ttu-id="98a7a-142">É melhor escolher uma palavra ou frase que resume o que está sendo feito na migração.</span><span class="sxs-lookup"><span data-stu-id="98a7a-142">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="98a7a-143">Por exemplo, uma migração que adicionou uma tabela de departamento pode ser chamada "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="98a7a-143">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="98a7a-144">A tabela de histórico de migrações</span><span class="sxs-lookup"><span data-stu-id="98a7a-144">The migrations history table</span></span>

* <span data-ttu-id="98a7a-145">Use SSOX ou sua ferramenta SQLite para inspecionar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="98a7a-145">Use SSOX or your SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="98a7a-146">Observe a adição de uma tabela `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="98a7a-146">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="98a7a-147">A tabela `__EFMigrationsHistory` controla quais migrações foram aplicadas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="98a7a-147">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="98a7a-148">Exibir os dados na tabela `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="98a7a-148">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="98a7a-149">Mostra uma linha para a primeira migração.</span><span class="sxs-lookup"><span data-stu-id="98a7a-149">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="98a7a-150">O instantâneo do modelo de dados</span><span class="sxs-lookup"><span data-stu-id="98a7a-150">The data model snapshot</span></span>

<span data-ttu-id="98a7a-151">As migrações criam um *instantâneo* do modelo de dados atual em *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="98a7a-151">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="98a7a-152">Quando você adiciona uma migração, o EF determina o que foi alterado, comparando o modelo de dados atual com o arquivo de instantâneo.</span><span class="sxs-lookup"><span data-stu-id="98a7a-152">When you add a migration, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="98a7a-153">Como o arquivo de instantâneo rastreia o estado do modelo de dados, não é possível excluir uma migração excluindo o arquivo `<timestamp>_<migrationname>.cs`.</span><span class="sxs-lookup"><span data-stu-id="98a7a-153">Because the snapshot file tracks the state of the data model, you can't delete a migration by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="98a7a-154">Para fazer backup da migração mais recente, você precisa usar o comando `migrations remove`.</span><span class="sxs-lookup"><span data-stu-id="98a7a-154">To back out the most recent migration, you have to use the `migrations remove` command.</span></span> <span data-ttu-id="98a7a-155">Esse comando exclui a migração e garante que o instantâneo seja redefinido corretamente.</span><span class="sxs-lookup"><span data-stu-id="98a7a-155">That command deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="98a7a-156">Para saber mais, confira [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="98a7a-156">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

## <a name="remove-ensurecreated"></a><span data-ttu-id="98a7a-157">Remover EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="98a7a-157">Remove EnsureCreated</span></span>

<span data-ttu-id="98a7a-158">Esta série de tutoriais começou usando o `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="98a7a-158">This tutorial series started by using `EnsureCreated`.</span></span> <span data-ttu-id="98a7a-159">O `EnsureCreated` não cria uma tabela de histórico de migrações, portanto, não pode ser usada com migrações.</span><span class="sxs-lookup"><span data-stu-id="98a7a-159">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="98a7a-160">Ele foi projetado para teste ou criação rápida de protótipos em que o banco de dados é removido e recriado com frequência.</span><span class="sxs-lookup"><span data-stu-id="98a7a-160">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="98a7a-161">Deste ponto em diante, os tutoriais usarão as migrações.</span><span class="sxs-lookup"><span data-stu-id="98a7a-161">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="98a7a-162">Em *data/DBInitializer.cs*, comente a seguinte linha:</span><span class="sxs-lookup"><span data-stu-id="98a7a-162">In *Data/DBInitializer.cs*, comment out the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```
<span data-ttu-id="98a7a-163">Execute o aplicativo e verifique se o banco de dados é propagado.</span><span class="sxs-lookup"><span data-stu-id="98a7a-163">Run the app and verify that the database is seeded.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="98a7a-164">Aplicando migrações na produção</span><span class="sxs-lookup"><span data-stu-id="98a7a-164">Applying migrations in production</span></span>

<span data-ttu-id="98a7a-165">Recomendamos que os aplicativos de produção **não** chamem [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="98a7a-165">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="98a7a-166">`Migrate` não deve ser chamado de um aplicativo implantado em um farm de servidores.</span><span class="sxs-lookup"><span data-stu-id="98a7a-166">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="98a7a-167">Se o aplicativo for escalado horizontalmente para várias instâncias de servidor, será difícil garantir que as atualizações do esquema de banco de dados não ocorram de vários servidores ou que estejam em conflito com o acesso de leitura/gravação.</span><span class="sxs-lookup"><span data-stu-id="98a7a-167">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="98a7a-168">A migração de banco de dados deve ser feita como parte da implantação e de maneira controlada.</span><span class="sxs-lookup"><span data-stu-id="98a7a-168">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="98a7a-169">Abordagens de migração de banco de dados de produção incluem:</span><span class="sxs-lookup"><span data-stu-id="98a7a-169">Production database migration approaches include:</span></span>

* <span data-ttu-id="98a7a-170">Uso de migrações para criar scripts SQL e uso dos scripts SQL na implantação.</span><span class="sxs-lookup"><span data-stu-id="98a7a-170">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="98a7a-171">Execução de `dotnet ef database update` em um ambiente controlado.</span><span class="sxs-lookup"><span data-stu-id="98a7a-171">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="98a7a-172">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="98a7a-172">Troubleshooting</span></span>

<span data-ttu-id="98a7a-173">Se o aplicativo usar SQL Server LocalDB e exibir a seguinte exceção:</span><span class="sxs-lookup"><span data-stu-id="98a7a-173">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="98a7a-174">A solução pode ser executar `dotnet ef database update` em um prompt de comando.</span><span class="sxs-lookup"><span data-stu-id="98a7a-174">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="98a7a-175">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="98a7a-175">Additional resources</span></span>

* <span data-ttu-id="98a7a-176">[CLI do EF Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="98a7a-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="98a7a-177">Console do Gerenciador de Pacotes (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="98a7a-177">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="98a7a-178">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="98a7a-178">Next steps</span></span>

<span data-ttu-id="98a7a-179">O próximo tutorial cria o modelo de dados adicionando propriedades da entidade e novas entidades.</span><span class="sxs-lookup"><span data-stu-id="98a7a-179">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="98a7a-180">[Tutorial anterior](xref:data/ef-rp/sort-filter-page)
> [Próximo tutorial](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="98a7a-180">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="98a7a-181">Neste tutorial, o recurso de migrações do EF Core para o gerenciamento de alterações do modelo de dados é usado.</span><span class="sxs-lookup"><span data-stu-id="98a7a-181">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="98a7a-182">Caso tenha problemas que não consiga resolver, baixe o [aplicativo concluído](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="98a7a-182">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="98a7a-183">Quando um novo aplicativo é desenvolvido, o modelo de dados é alterado com frequência.</span><span class="sxs-lookup"><span data-stu-id="98a7a-183">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="98a7a-184">Sempre que o modelo é alterado, ele fica fora de sincronia com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="98a7a-184">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="98a7a-185">Este tutorial começa configurando o Entity Framework para criar o banco de dados, caso ele não exista.</span><span class="sxs-lookup"><span data-stu-id="98a7a-185">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="98a7a-186">Sempre que o modelo de dados é alterado:</span><span class="sxs-lookup"><span data-stu-id="98a7a-186">Each time the data model changes:</span></span>

* <span data-ttu-id="98a7a-187">O BD é removido.</span><span class="sxs-lookup"><span data-stu-id="98a7a-187">The DB is dropped.</span></span>
* <span data-ttu-id="98a7a-188">O EF cria um novo que corresponde ao modelo.</span><span class="sxs-lookup"><span data-stu-id="98a7a-188">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="98a7a-189">O aplicativo propaga o BD com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="98a7a-189">The app seeds the DB with test data.</span></span>

<span data-ttu-id="98a7a-190">Essa abordagem para manter o BD em sincronia com o modelo de dados funciona bem até que você implante o aplicativo em produção.</span><span class="sxs-lookup"><span data-stu-id="98a7a-190">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="98a7a-191">Quando o aplicativo é executado em produção, normalmente, ele armazena dados que precisam ser mantidos.</span><span class="sxs-lookup"><span data-stu-id="98a7a-191">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="98a7a-192">O aplicativo não pode começar com um BD de teste sempre que uma alteração é feita (como a adição de uma nova coluna).</span><span class="sxs-lookup"><span data-stu-id="98a7a-192">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="98a7a-193">O recurso Migrações do EF Core resolve esse problema, permitindo que o EF Core atualize o esquema de BD em vez de criar um novo BD.</span><span class="sxs-lookup"><span data-stu-id="98a7a-193">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="98a7a-194">Em vez de remover e recriar o BD quando o modelo de dados é alterado, as migrações atualizam o esquema e retêm os dados existentes.</span><span class="sxs-lookup"><span data-stu-id="98a7a-194">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="98a7a-195">Remover o banco de dados</span><span class="sxs-lookup"><span data-stu-id="98a7a-195">Drop the database</span></span>

<span data-ttu-id="98a7a-196">Use o **SSOX** (Pesquisador de Objetos do SQL Server) ou o comando `database drop`:</span><span class="sxs-lookup"><span data-stu-id="98a7a-196">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98a7a-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98a7a-197">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="98a7a-198">No **PMC** (Console do Gerenciador de Pacotes), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="98a7a-198">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
```

<span data-ttu-id="98a7a-199">Execute `Get-Help about_EntityFrameworkCore` no PMC para obter informações de ajuda.</span><span class="sxs-lookup"><span data-stu-id="98a7a-199">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98a7a-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98a7a-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="98a7a-201">Abra uma janela Comando e navegue para a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="98a7a-201">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="98a7a-202">A pasta do projeto contém o arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="98a7a-202">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="98a7a-203">Insira o seguinte na janela Comando:</span><span class="sxs-lookup"><span data-stu-id="98a7a-203">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="98a7a-204">Criar uma migração inicial e atualizar o BD</span><span class="sxs-lookup"><span data-stu-id="98a7a-204">Create an initial migration and update the DB</span></span>

<span data-ttu-id="98a7a-205">Crie o projeto e a primeira migração.</span><span class="sxs-lookup"><span data-stu-id="98a7a-205">Build the project and create the first migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98a7a-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98a7a-206">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98a7a-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98a7a-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="98a7a-208">Examinar os métodos Up e Down</span><span class="sxs-lookup"><span data-stu-id="98a7a-208">Examine the Up and Down methods</span></span>

<span data-ttu-id="98a7a-209">O comando `migrations add` do EF Core gerou um código para criar o BD.</span><span class="sxs-lookup"><span data-stu-id="98a7a-209">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="98a7a-210">Esse código de migrações está localizado no arquivo *Migrations\<timestamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="98a7a-210">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="98a7a-211">O método `Up` da classe `InitialCreate` cria as tabelas de BD que correspondem aos conjuntos de entidades do modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="98a7a-211">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="98a7a-212">O método `Down` exclui-os, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="98a7a-212">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="98a7a-213">As migrações chamam o método `Up` para implementar as alterações do modelo de dados para uma migração.</span><span class="sxs-lookup"><span data-stu-id="98a7a-213">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="98a7a-214">Quando você insere um comando para reverter a atualização, as migrações chamam o método `Down`.</span><span class="sxs-lookup"><span data-stu-id="98a7a-214">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="98a7a-215">O código anterior refere-se à migração inicial.</span><span class="sxs-lookup"><span data-stu-id="98a7a-215">The preceding code is for the initial migration.</span></span> <span data-ttu-id="98a7a-216">Esse código foi criado quando o comando `migrations add InitialCreate` foi executado.</span><span class="sxs-lookup"><span data-stu-id="98a7a-216">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="98a7a-217">O parâmetro de nome da migração ("InitialCreate" no exemplo) é usado para o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="98a7a-217">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="98a7a-218">O nome da migração pode ser qualquer nome de arquivo válido.</span><span class="sxs-lookup"><span data-stu-id="98a7a-218">The migration name can be any valid file name.</span></span> <span data-ttu-id="98a7a-219">É melhor escolher uma palavra ou frase que resume o que está sendo feito na migração.</span><span class="sxs-lookup"><span data-stu-id="98a7a-219">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="98a7a-220">Por exemplo, uma migração que adicionou uma tabela de departamento pode ser chamada "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="98a7a-220">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="98a7a-221">Se a migração inicial foi criada e o BD existe:</span><span class="sxs-lookup"><span data-stu-id="98a7a-221">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="98a7a-222">O código de criação do BD é gerado.</span><span class="sxs-lookup"><span data-stu-id="98a7a-222">The DB creation code is generated.</span></span>
* <span data-ttu-id="98a7a-223">O código de criação do BD não precisa ser executado porque o BD já corresponde ao modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="98a7a-223">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="98a7a-224">Se o código de criação do BD for executado, ele não fará nenhuma alteração porque o BD já corresponde ao modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="98a7a-224">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="98a7a-225">Quando o aplicativo é implantado em um novo ambiente, o código de criação do BD precisa ser executado para criar o BD.</span><span class="sxs-lookup"><span data-stu-id="98a7a-225">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="98a7a-226">Anteriormente, o BD foi removido, e não existe mais. Então, as migrações criam o novo BD.</span><span class="sxs-lookup"><span data-stu-id="98a7a-226">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="98a7a-227">O instantâneo do modelo de dados</span><span class="sxs-lookup"><span data-stu-id="98a7a-227">The data model snapshot</span></span>

<span data-ttu-id="98a7a-228">As migrações criam um *instantâneo* do esquema de banco de dados atual em *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="98a7a-228">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="98a7a-229">Quando você adiciona uma migração, o EF determina o que foi alterado, comparando o modelo de dados com o arquivo de instantâneo.</span><span class="sxs-lookup"><span data-stu-id="98a7a-229">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="98a7a-230">Para excluir uma migração, use o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="98a7a-230">To delete a migration, use the following command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="98a7a-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98a7a-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="98a7a-232">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="98a7a-232">Remove-Migration</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="98a7a-233">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="98a7a-233">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="98a7a-234">Para saber mais, confira [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="98a7a-234">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="98a7a-235">O comando de exclusão de migrações exclui a migração e garante que o instantâneo seja redefinido corretamente.</span><span class="sxs-lookup"><span data-stu-id="98a7a-235">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="98a7a-236">Remover EnsureCreated e testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="98a7a-236">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="98a7a-237">Para o desenvolvimento inicial, `EnsureCreated` foi usado.</span><span class="sxs-lookup"><span data-stu-id="98a7a-237">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="98a7a-238">Neste tutorial, as migrações são usadas.</span><span class="sxs-lookup"><span data-stu-id="98a7a-238">In this tutorial, migrations are used.</span></span> <span data-ttu-id="98a7a-239">`EnsureCreated` tem as seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="98a7a-239">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="98a7a-240">Ignora as migrações e cria o BD e o esquema.</span><span class="sxs-lookup"><span data-stu-id="98a7a-240">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="98a7a-241">Não cria uma tabela de migrações.</span><span class="sxs-lookup"><span data-stu-id="98a7a-241">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="98a7a-242">*Não* pode ser usado com migrações.</span><span class="sxs-lookup"><span data-stu-id="98a7a-242">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="98a7a-243">Foi projetado para teste ou criação rápida de protótipos em que o BD é removido e recriado com frequência.</span><span class="sxs-lookup"><span data-stu-id="98a7a-243">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="98a7a-244">Remova `EnsureCreated`:</span><span class="sxs-lookup"><span data-stu-id="98a7a-244">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="98a7a-245">Execute o aplicativo e verifique se o BD é propagado.</span><span class="sxs-lookup"><span data-stu-id="98a7a-245">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="98a7a-246">Inspecionar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="98a7a-246">Inspect the database</span></span>

<span data-ttu-id="98a7a-247">Use o **Pesquisador de Objetos do SQL Server** para inspecionar o BD.</span><span class="sxs-lookup"><span data-stu-id="98a7a-247">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="98a7a-248">Observe a adição de uma tabela `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="98a7a-248">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="98a7a-249">A tabela `__EFMigrationsHistory` controla quais migrações foram aplicadas ao BD.</span><span class="sxs-lookup"><span data-stu-id="98a7a-249">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="98a7a-250">Exiba os dados na `__EFMigrationsHistory` tabela; ela mostra uma linha para a primeira migração.</span><span class="sxs-lookup"><span data-stu-id="98a7a-250">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="98a7a-251">O último log no exemplo de saída da CLI anterior mostra a instrução INSERT que cria essa linha.</span><span class="sxs-lookup"><span data-stu-id="98a7a-251">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="98a7a-252">Execute o aplicativo e verifique se tudo funciona.</span><span class="sxs-lookup"><span data-stu-id="98a7a-252">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="98a7a-253">Aplicando migrações na produção</span><span class="sxs-lookup"><span data-stu-id="98a7a-253">Applying migrations in production</span></span>

<span data-ttu-id="98a7a-254">Recomendamos que os aplicativos de produção **não** chamem [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="98a7a-254">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="98a7a-255">`Migrate` não deve ser chamado em um aplicativo no farm de servidores.</span><span class="sxs-lookup"><span data-stu-id="98a7a-255">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="98a7a-256">Por exemplo, se o aplicativo foi implantado na nuvem com escalabilidade horizontal (várias instâncias do aplicativo estão sendo executadas).</span><span class="sxs-lookup"><span data-stu-id="98a7a-256">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="98a7a-257">A migração de banco de dados deve ser feita como parte da implantação e de maneira controlada.</span><span class="sxs-lookup"><span data-stu-id="98a7a-257">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="98a7a-258">Abordagens de migração de banco de dados de produção incluem:</span><span class="sxs-lookup"><span data-stu-id="98a7a-258">Production database migration approaches include:</span></span>

* <span data-ttu-id="98a7a-259">Uso de migrações para criar scripts SQL e uso dos scripts SQL na implantação.</span><span class="sxs-lookup"><span data-stu-id="98a7a-259">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="98a7a-260">Execução de `dotnet ef database update` em um ambiente controlado.</span><span class="sxs-lookup"><span data-stu-id="98a7a-260">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="98a7a-261">O EF Core usa a tabela `__MigrationsHistory` para ver se uma migração precisa ser executada.</span><span class="sxs-lookup"><span data-stu-id="98a7a-261">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="98a7a-262">Se o BD estiver atualizado, nenhuma migração será executada.</span><span class="sxs-lookup"><span data-stu-id="98a7a-262">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="98a7a-263">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="98a7a-263">Troubleshooting</span></span>

<span data-ttu-id="98a7a-264">Baixar o [aplicativo concluído](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span><span class="sxs-lookup"><span data-stu-id="98a7a-264">Download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="98a7a-265">O aplicativo gera a seguinte exceção:</span><span class="sxs-lookup"><span data-stu-id="98a7a-265">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="98a7a-266">Solução: Execute `dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="98a7a-266">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="98a7a-267">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="98a7a-267">Additional resources</span></span>

* [<span data-ttu-id="98a7a-268">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="98a7a-268">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="98a7a-269">[CLI do .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="98a7a-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="98a7a-270">Console do Gerenciador de Pacotes (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="98a7a-270">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="98a7a-271">[Anterior](xref:data/ef-rp/sort-filter-page)
> [Próximo](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="98a7a-271">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

