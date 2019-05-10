---
title: Páginas Razor com o EF Core no ASP.NET Core – Migrações – 4 de 8
author: rick-anderson
description: Neste tutorial, você começa a usar o recurso de migrações do EF Core para gerenciar alterações do modelo de dados em um aplicativo ASP.NET Core MVC.
ms.author: riande
ms.date: 6/31/2017
uid: data/ef-rp/migrations
ms.openlocfilehash: 406b94d74a99dcec3b2ce01a5eb3313c48819148
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65517008"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a><span data-ttu-id="d7ef6-103">Páginas Razor com o EF Core no ASP.NET Core – Migrações – 4 de 8</span><span class="sxs-lookup"><span data-stu-id="d7ef6-103">Razor Pages with EF Core in ASP.NET Core - Migrations - 4 of 8</span></span>

<span data-ttu-id="d7ef6-104">Por [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d7ef6-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="d7ef6-105">Neste tutorial, o recurso de migrações do EF Core para o gerenciamento de alterações do modelo de dados é usado.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-105">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="d7ef6-106">Caso tenha problemas que não consiga resolver, baixe o [aplicativo concluído](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="d7ef6-106">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="d7ef6-107">Quando um novo aplicativo é desenvolvido, o modelo de dados é alterado com frequência.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-107">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="d7ef6-108">Sempre que o modelo é alterado, ele fica fora de sincronia com o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-108">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="d7ef6-109">Este tutorial começa configurando o Entity Framework para criar o banco de dados, caso ele não exista.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-109">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="d7ef6-110">Sempre que o modelo de dados é alterado:</span><span class="sxs-lookup"><span data-stu-id="d7ef6-110">Each time the data model changes:</span></span>

* <span data-ttu-id="d7ef6-111">O BD é removido.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-111">The DB is dropped.</span></span>
* <span data-ttu-id="d7ef6-112">O EF cria um novo que corresponde ao modelo.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-112">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="d7ef6-113">O aplicativo propaga o BD com os dados de teste.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-113">The app seeds the DB with test data.</span></span>

<span data-ttu-id="d7ef6-114">Essa abordagem para manter o BD em sincronia com o modelo de dados funciona bem até que você implante o aplicativo em produção.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-114">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="d7ef6-115">Quando o aplicativo é executado em produção, normalmente, ele armazena dados que precisam ser mantidos.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-115">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="d7ef6-116">O aplicativo não pode começar com um BD de teste sempre que uma alteração é feita (como a adição de uma nova coluna).</span><span class="sxs-lookup"><span data-stu-id="d7ef6-116">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="d7ef6-117">O recurso Migrações do EF Core resolve esse problema, permitindo que o EF Core atualize o esquema de BD em vez de criar um novo BD.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-117">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="d7ef6-118">Em vez de remover e recriar o BD quando o modelo de dados é alterado, as migrações atualizam o esquema e retêm os dados existentes.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-118">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="d7ef6-119">Remover o banco de dados</span><span class="sxs-lookup"><span data-stu-id="d7ef6-119">Drop the database</span></span>

<span data-ttu-id="d7ef6-120">Use o **SSOX** (Pesquisador de Objetos do SQL Server) ou o comando `database drop`:</span><span class="sxs-lookup"><span data-stu-id="d7ef6-120">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7ef6-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7ef6-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d7ef6-122">No **PMC** (Console do Gerenciador de Pacotes), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d7ef6-122">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
```

<span data-ttu-id="d7ef6-123">Execute `Get-Help about_EntityFrameworkCore` no PMC para obter informações de ajuda.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-123">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d7ef6-124">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d7ef6-124">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d7ef6-125">Abra uma janela Comando e navegue para a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-125">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="d7ef6-126">A pasta do projeto contém o arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-126">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="d7ef6-127">Insira o seguinte na janela Comando:</span><span class="sxs-lookup"><span data-stu-id="d7ef6-127">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="d7ef6-128">Criar uma migração inicial e atualizar o BD</span><span class="sxs-lookup"><span data-stu-id="d7ef6-128">Create an initial migration and update the DB</span></span>

<span data-ttu-id="d7ef6-129">Crie o projeto e a primeira migração.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-129">Build the project and create the first migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7ef6-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7ef6-130">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d7ef6-131">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d7ef6-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="d7ef6-132">Examinar os métodos Up e Down</span><span class="sxs-lookup"><span data-stu-id="d7ef6-132">Examine the Up and Down methods</span></span>

<span data-ttu-id="d7ef6-133">O comando `migrations add` do EF Core gerou um código para criar o BD.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-133">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="d7ef6-134">Esse código de migrações está localizado no arquivo *Migrations\<timestamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-134">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="d7ef6-135">O método `Up` da classe `InitialCreate` cria as tabelas de BD que correspondem aos conjuntos de entidades do modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-135">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="d7ef6-136">O método `Down` exclui-os, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="d7ef6-136">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="d7ef6-137">As migrações chamam o método `Up` para implementar as alterações do modelo de dados para uma migração.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-137">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="d7ef6-138">Quando você insere um comando para reverter a atualização, as migrações chamam o método `Down`.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-138">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="d7ef6-139">O código anterior refere-se à migração inicial.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-139">The preceding code is for the initial migration.</span></span> <span data-ttu-id="d7ef6-140">Esse código foi criado quando o comando `migrations add InitialCreate` foi executado.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-140">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="d7ef6-141">O parâmetro de nome da migração ("InitialCreate" no exemplo) é usado para o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-141">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="d7ef6-142">O nome da migração pode ser qualquer nome de arquivo válido.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-142">The migration name can be any valid file name.</span></span> <span data-ttu-id="d7ef6-143">É melhor escolher uma palavra ou frase que resume o que está sendo feito na migração.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-143">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="d7ef6-144">Por exemplo, uma migração que adicionou uma tabela de departamento pode ser chamada "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="d7ef6-144">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="d7ef6-145">Se a migração inicial foi criada e o BD existe:</span><span class="sxs-lookup"><span data-stu-id="d7ef6-145">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="d7ef6-146">O código de criação do BD é gerado.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-146">The DB creation code is generated.</span></span>
* <span data-ttu-id="d7ef6-147">O código de criação do BD não precisa ser executado porque o BD já corresponde ao modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-147">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="d7ef6-148">Se o código de criação do BD for executado, ele não fará nenhuma alteração porque o BD já corresponde ao modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-148">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="d7ef6-149">Quando o aplicativo é implantado em um novo ambiente, o código de criação do BD precisa ser executado para criar o BD.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-149">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="d7ef6-150">Anteriormente, o BD foi removido, e não existe mais. Então, as migrações criam o novo BD.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-150">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="d7ef6-151">O instantâneo do modelo de dados</span><span class="sxs-lookup"><span data-stu-id="d7ef6-151">The data model snapshot</span></span>

<span data-ttu-id="d7ef6-152">As migrações criam um *instantâneo* do esquema de banco de dados atual em *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-152">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="d7ef6-153">Quando você adiciona uma migração, o EF determina o que foi alterado, comparando o modelo de dados com o arquivo de instantâneo.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-153">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="d7ef6-154">Para excluir uma migração, use o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="d7ef6-154">To delete a migration, use the following command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7ef6-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7ef6-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d7ef6-156">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="d7ef6-156">Remove-Migration</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d7ef6-157">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="d7ef6-157">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations remove
```

<span data-ttu-id="d7ef6-158">Para saber mais, confira [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="d7ef6-158">For more information, see  [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="d7ef6-159">O comando de exclusão de migrações exclui a migração e garante que o instantâneo seja redefinido corretamente.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-159">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="d7ef6-160">Remover EnsureCreated e testar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="d7ef6-160">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="d7ef6-161">Para o desenvolvimento inicial, `EnsureCreated` foi usado.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-161">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="d7ef6-162">Neste tutorial, as migrações são usadas.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-162">In this tutorial, migrations are used.</span></span> <span data-ttu-id="d7ef6-163">`EnsureCreated` tem as seguintes limitações:</span><span class="sxs-lookup"><span data-stu-id="d7ef6-163">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="d7ef6-164">Ignora as migrações e cria o BD e o esquema.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-164">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="d7ef6-165">Não cria uma tabela de migrações.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-165">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="d7ef6-166">*Não* pode ser usado com migrações.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-166">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="d7ef6-167">Foi projetado para teste ou criação rápida de protótipos em que o BD é removido e recriado com frequência.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-167">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="d7ef6-168">Remova `EnsureCreated`:</span><span class="sxs-lookup"><span data-stu-id="d7ef6-168">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="d7ef6-169">Execute o aplicativo e verifique se o BD é propagado.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-169">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="d7ef6-170">Inspecionar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="d7ef6-170">Inspect the database</span></span>

<span data-ttu-id="d7ef6-171">Use o **Pesquisador de Objetos do SQL Server** para inspecionar o BD.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-171">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="d7ef6-172">Observe a adição de uma tabela `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-172">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="d7ef6-173">A tabela `__EFMigrationsHistory` controla quais migrações foram aplicadas ao BD.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-173">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="d7ef6-174">Exiba os dados na `__EFMigrationsHistory` tabela; ela mostra uma linha para a primeira migração.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-174">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="d7ef6-175">O último log no exemplo de saída da CLI anterior mostra a instrução INSERT que cria essa linha.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-175">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="d7ef6-176">Execute o aplicativo e verifique se tudo funciona.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-176">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="d7ef6-177">Aplicando migrações na produção</span><span class="sxs-lookup"><span data-stu-id="d7ef6-177">Applying migrations in production</span></span>

<span data-ttu-id="d7ef6-178">Recomendamos que os aplicativos de produção **não** chamem [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-178">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="d7ef6-179">`Migrate` não deve ser chamado em um aplicativo no farm de servidores.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-179">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="d7ef6-180">Por exemplo, se o aplicativo foi implantado na nuvem com escalabilidade horizontal (várias instâncias do aplicativo estão sendo executadas).</span><span class="sxs-lookup"><span data-stu-id="d7ef6-180">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="d7ef6-181">A migração de banco de dados deve ser feita como parte da implantação e de maneira controlada.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-181">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="d7ef6-182">Abordagens de migração de banco de dados de produção incluem:</span><span class="sxs-lookup"><span data-stu-id="d7ef6-182">Production database migration approaches include:</span></span>

* <span data-ttu-id="d7ef6-183">Uso de migrações para criar scripts SQL e uso dos scripts SQL na implantação.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-183">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="d7ef6-184">Execução de `dotnet ef database update` em um ambiente controlado.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-184">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="d7ef6-185">O EF Core usa a tabela `__MigrationsHistory` para ver se uma migração precisa ser executada.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-185">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="d7ef6-186">Se o BD estiver atualizado, nenhuma migração será executada.</span><span class="sxs-lookup"><span data-stu-id="d7ef6-186">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="d7ef6-187">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="d7ef6-187">Troubleshooting</span></span>

<span data-ttu-id="d7ef6-188">Baixar o [aplicativo concluído](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).</span><span class="sxs-lookup"><span data-stu-id="d7ef6-188">Download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).</span></span>

<span data-ttu-id="d7ef6-189">O aplicativo gera a seguinte exceção:</span><span class="sxs-lookup"><span data-stu-id="d7ef6-189">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="d7ef6-190">Solução: Execute `dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="d7ef6-190">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="d7ef6-191">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d7ef6-191">Additional resources</span></span>

* [<span data-ttu-id="d7ef6-192">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="d7ef6-192">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="d7ef6-193">[CLI do .NET Core](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="d7ef6-193">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="d7ef6-194">Console do Gerenciador de Pacotes (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="d7ef6-194">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="d7ef6-195">[Anterior](xref:data/ef-rp/sort-filter-page)
> [Próximo](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="d7ef6-195">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>
