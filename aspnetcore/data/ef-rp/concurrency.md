---
title: Páginas Razor com o EF Core no ASP.NET Core – Simultaneidade – 8 de 8
author: tdykstra
description: Este tutorial mostra como lidar com conflitos quando os mesmos usuários atualizam a mesma entidade simultaneamente.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/concurrency
ms.openlocfilehash: da57633c345ec087b1a4f24ddc7771e7a2d04720
ms.sourcegitcommit: 0774a61a3a6c1412a7da0e7d932dc60c506441fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70059085"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a><span data-ttu-id="066ce-103">Páginas Razor com o EF Core no ASP.NET Core – Simultaneidade – 8 de 8</span><span class="sxs-lookup"><span data-stu-id="066ce-103">Razor Pages with EF Core in ASP.NET Core - Concurrency - 8 of 8</span></span>

<span data-ttu-id="066ce-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) e [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="066ce-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="066ce-105">Este tutorial mostra como lidar com conflitos quando os mesmos usuários atualizam uma entidade simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="066ce-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="066ce-106">Conflitos de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="066ce-106">Concurrency conflicts</span></span>

<span data-ttu-id="066ce-107">Um conflito de simultaneidade ocorre quando:</span><span class="sxs-lookup"><span data-stu-id="066ce-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="066ce-108">Um usuário navega para a página de edição de uma entidade.</span><span class="sxs-lookup"><span data-stu-id="066ce-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="066ce-109">Outro usuário atualiza a mesma entidade antes que a primeira alteração do usuário seja gravada no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="066ce-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="066ce-110">Se a detecção de simultaneidade não estiver habilitada, quem atualizar o banco de dados por último substituirá as alterações do outro usuário.</span><span class="sxs-lookup"><span data-stu-id="066ce-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="066ce-111">Se esse risco for aceitável, o custo de programação para simultaneidade poderá superar o benefício.</span><span class="sxs-lookup"><span data-stu-id="066ce-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="066ce-112">Simultaneidade pessimista (bloqueio)</span><span class="sxs-lookup"><span data-stu-id="066ce-112">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="066ce-113">Uma maneira de evitar conflitos de simultaneidade é usar bloqueios de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="066ce-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="066ce-114">Isso é chamado de simultaneidade pessimista.</span><span class="sxs-lookup"><span data-stu-id="066ce-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="066ce-115">Antes que o aplicativo leia uma linha de banco de dados que ele pretende atualizar, ele solicita um bloqueio.</span><span class="sxs-lookup"><span data-stu-id="066ce-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="066ce-116">Depois que uma linha é bloqueada para acesso de atualização, nenhum outro usuário tem permissão para bloquear a linha até que o primeiro bloqueio seja liberado.</span><span class="sxs-lookup"><span data-stu-id="066ce-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="066ce-117">O gerenciamento de bloqueios traz desvantagens.</span><span class="sxs-lookup"><span data-stu-id="066ce-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="066ce-118">Pode ser complexo de programar e causar problemas de desempenho conforme o número de usuários aumenta.</span><span class="sxs-lookup"><span data-stu-id="066ce-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="066ce-119">O Entity Framework Core não fornece nenhum suporte interno para ele, e este tutorial não mostra como implementá-lo.</span><span class="sxs-lookup"><span data-stu-id="066ce-119">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="066ce-120">Simultaneidade otimista</span><span class="sxs-lookup"><span data-stu-id="066ce-120">Optimistic concurrency</span></span>

<span data-ttu-id="066ce-121">A simultaneidade otimista permite que conflitos de simultaneidade ocorram e, em seguida, responde adequadamente quando ocorrem.</span><span class="sxs-lookup"><span data-stu-id="066ce-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="066ce-122">Por exemplo, Alice visita a página Editar Departamento e altera o orçamento para o departamento de inglês de US$ 350.000,00 para US$ 0,00.</span><span class="sxs-lookup"><span data-stu-id="066ce-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Alterando o orçamento para 0](concurrency/_static/change-budget30.png)

<span data-ttu-id="066ce-124">Antes que Alice clique em **Salvar**, Julio visita a mesma página e altera o campo Data de Início de 1/9/2007 para 1/9/2013.</span><span class="sxs-lookup"><span data-stu-id="066ce-124">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Alterando a data de início para 2013](concurrency/_static/change-date30.png)

<span data-ttu-id="066ce-126">Jane clica em **Salvar** primeiro e vê que sua alteração entrará em vigor, já que o navegador exibe a página de Índice com zero como o valor do Orçamento.</span><span class="sxs-lookup"><span data-stu-id="066ce-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="066ce-127">Julio clica em **Salvar** em uma página Editar que ainda mostra um orçamento de US$ 350.000,00.</span><span class="sxs-lookup"><span data-stu-id="066ce-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="066ce-128">O que acontece em seguida é determinado pela forma como você lida com conflitos de simultaneidade:</span><span class="sxs-lookup"><span data-stu-id="066ce-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="066ce-129">Controle qual propriedade um usuário modificou e atualize apenas as colunas correspondentes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="066ce-129">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="066ce-130">No cenário, não haverá perda de dados.</span><span class="sxs-lookup"><span data-stu-id="066ce-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="066ce-131">Propriedades diferentes foram atualizadas pelos dois usuários.</span><span class="sxs-lookup"><span data-stu-id="066ce-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="066ce-132">Na próxima vez que alguém navegar no departamento de inglês, verá as alterações de Alice e Julio.</span><span class="sxs-lookup"><span data-stu-id="066ce-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="066ce-133">Esse método de atualização pode reduzir o número de conflitos que podem resultar em perda de dados.</span><span class="sxs-lookup"><span data-stu-id="066ce-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="066ce-134">Essa abordagem tem algumas desvantagens:</span><span class="sxs-lookup"><span data-stu-id="066ce-134">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="066ce-135">Não poderá evitar a perda de dados se forem feitas alterações concorrentes na mesma propriedade.</span><span class="sxs-lookup"><span data-stu-id="066ce-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="066ce-136">Geralmente, não é prática em um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="066ce-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="066ce-137">Ela exige um estado de manutenção significativo para controlar todos os valores buscados e novos valores.</span><span class="sxs-lookup"><span data-stu-id="066ce-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="066ce-138">Manter grandes quantidades de estado pode afetar o desempenho do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="066ce-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="066ce-139">Pode aumentar a complexidade do aplicativo comparado à detecção de simultaneidade em uma entidade.</span><span class="sxs-lookup"><span data-stu-id="066ce-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="066ce-140">Você não pode deixar a alteração de Julio substituir a alteração de Alice.</span><span class="sxs-lookup"><span data-stu-id="066ce-140">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="066ce-141">Na próxima vez que alguém navegar pelo departamento de inglês, verá 1/9/2013 e o valor de US$ 350.000,00 buscado.</span><span class="sxs-lookup"><span data-stu-id="066ce-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="066ce-142">Essa abordagem é chamada de um cenário *O cliente vence* ou *O último vence*.</span><span class="sxs-lookup"><span data-stu-id="066ce-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="066ce-143">(Todos os valores do cliente têm precedência sobre o conteúdo do armazenamento de dados.) Se você não fizer nenhuma codificação para a manipulação de simultaneidade, o cenário O cliente vence ocorrerá automaticamente.</span><span class="sxs-lookup"><span data-stu-id="066ce-143">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="066ce-144">Você pode impedir que as alterações de Julio sejam atualizadas no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="066ce-144">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="066ce-145">Normalmente, o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="066ce-145">Typically, the app would:</span></span>

  * <span data-ttu-id="066ce-146">Exibe uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="066ce-146">Display an error message.</span></span>
  * <span data-ttu-id="066ce-147">Mostra o estado atual dos dados.</span><span class="sxs-lookup"><span data-stu-id="066ce-147">Show the current state of the data.</span></span>
  * <span data-ttu-id="066ce-148">Permite ao usuário aplicar as alterações novamente.</span><span class="sxs-lookup"><span data-stu-id="066ce-148">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="066ce-149">Isso é chamado de um cenário *O armazenamento vence*.</span><span class="sxs-lookup"><span data-stu-id="066ce-149">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="066ce-150">(Os valores do armazenamento de dados têm precedência sobre os valores enviados pelo cliente.) Você implementará o cenário O armazenamento vence neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="066ce-150">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="066ce-151">Esse método garante que nenhuma alteração é substituída sem que um usuário seja alertado.</span><span class="sxs-lookup"><span data-stu-id="066ce-151">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="066ce-152">Detecção de conflitos no EF Core</span><span class="sxs-lookup"><span data-stu-id="066ce-152">Conflict detection in EF Core</span></span>

<span data-ttu-id="066ce-153">O EF Core gera exceções `DbConcurrencyException` quando detecta conflitos.</span><span class="sxs-lookup"><span data-stu-id="066ce-153">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="066ce-154">O modelo de dados deve ser configurado para habilitar a detecção de conflitos.</span><span class="sxs-lookup"><span data-stu-id="066ce-154">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="066ce-155">As opções para habilitar a detecção de conflitos incluem as seguintes:</span><span class="sxs-lookup"><span data-stu-id="066ce-155">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="066ce-156">Configurar o EF Core para incluir os valores originais das colunas configuradas como [tokens de simultaneidade](/ef/core/modeling/concurrency) na cláusula Where dos comandos Update e Delete.</span><span class="sxs-lookup"><span data-stu-id="066ce-156">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="066ce-157">Quando `SaveChanges` é chamado, a cláusula WHERE procura os valores originais de quaisquer propriedades anotadas com o atributo [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute).</span><span class="sxs-lookup"><span data-stu-id="066ce-157">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="066ce-158">As declarações de atualização não encontrarão uma linha a ser atualizada se qualquer uma das propriedades do token de simultaneidade for alteradas desde a primeira leitura da linha.</span><span class="sxs-lookup"><span data-stu-id="066ce-158">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="066ce-159">O EF Core interpreta isso como um conflito de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="066ce-159">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="066ce-160">Para tabelas de banco de dados que têm muitas colunas, essa abordagem pode resultar em cláusulas Where muito grandes e pode exigir grandes quantidades de estado.</span><span class="sxs-lookup"><span data-stu-id="066ce-160">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="066ce-161">Portanto, essa abordagem geralmente não é recomendada e não é o método usado neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="066ce-161">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="066ce-162">Na tabela de banco de dados, inclua uma coluna de acompanhamento que pode ser usada para determinar quando uma linha é alterada.</span><span class="sxs-lookup"><span data-stu-id="066ce-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="066ce-163">Em um banco de dados do SQL Server, o tipo de dados da coluna de acompanhamento é `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="066ce-163">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="066ce-164">O valor `rowversion` é um número sequencial que é incrementado sempre que a linha é atualizada.</span><span class="sxs-lookup"><span data-stu-id="066ce-164">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="066ce-165">Em um comando Update ou Delete, a cláusula Where inclui o valor original da coluna de acompanhamento (o número de versão da linha original).</span><span class="sxs-lookup"><span data-stu-id="066ce-165">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="066ce-166">Se a linha que está sendo atualizada tiver sido alterada por outro usuário, o valor na coluna `rowversion` será diferente do valor original.</span><span class="sxs-lookup"><span data-stu-id="066ce-166">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="066ce-167">Nesse caso, a instrução Update ou Delete não pode localizar a linha a ser atualizada devido à cláusula Where.</span><span class="sxs-lookup"><span data-stu-id="066ce-167">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="066ce-168">O EF Core gera uma exceção de simultaneidade quando nenhuma linha é afetada por um comando Update ou Delete.</span><span class="sxs-lookup"><span data-stu-id="066ce-168">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="066ce-169">Adicionar uma propriedade de acompanhamento</span><span class="sxs-lookup"><span data-stu-id="066ce-169">Add a tracking property</span></span>

<span data-ttu-id="066ce-170">Em *Models/Department.cs*, adicione uma propriedade de controle chamada RowVersion:</span><span class="sxs-lookup"><span data-stu-id="066ce-170">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="066ce-171">O atributo [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) é o que identifica a coluna como uma coluna de acompanhamento de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="066ce-171">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="066ce-172">A API fluente é uma maneira alternativa de especificar a propriedade de acompanhamento:</span><span class="sxs-lookup"><span data-stu-id="066ce-172">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="066ce-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="066ce-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="066ce-174">Para um banco de dados do SQL Server, o atributo `[Timestamp]` em uma propriedade de entidade definida como matriz de bytes:</span><span class="sxs-lookup"><span data-stu-id="066ce-174">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="066ce-175">Faz com que a coluna seja incluída nas cláusulas DELETE e UPDATE WHERE.</span><span class="sxs-lookup"><span data-stu-id="066ce-175">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="066ce-176">Define o tipo de coluna no banco de dados como [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="066ce-176">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="066ce-177">O banco de dados gera um número de versão de linha sequencial que é incrementado sempre que a linha é atualizada.</span><span class="sxs-lookup"><span data-stu-id="066ce-177">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="066ce-178">Em um comando `Update` ou `Delete`, a cláusula `Where` inclui o valor da versão de linha buscado.</span><span class="sxs-lookup"><span data-stu-id="066ce-178">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="066ce-179">Se a linha que está sendo atualizada foi alterada desde que foi buscada:</span><span class="sxs-lookup"><span data-stu-id="066ce-179">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="066ce-180">O valor da versão da linha atual não corresponde ao valor obtido.</span><span class="sxs-lookup"><span data-stu-id="066ce-180">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="066ce-181">O comando `Update` ou `Delete` não localiza uma linha porque a cláusula `Where` procura o valor da versão da linha buscado.</span><span class="sxs-lookup"><span data-stu-id="066ce-181">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="066ce-182">Uma `DbUpdateConcurrencyException` é gerada.</span><span class="sxs-lookup"><span data-stu-id="066ce-182">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="066ce-183">O seguinte código mostra uma parte do T-SQL gerado pelo EF Core quando o nome do Departamento é atualizado:</span><span class="sxs-lookup"><span data-stu-id="066ce-183">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="066ce-184">O código anterior realçado mostra a cláusula `WHERE` que contém `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="066ce-184">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="066ce-185">Se o banco de dados `RowVersion` não for igual ao parâmetro `RowVersion` (`@p2`), nenhuma linha será atualizada.</span><span class="sxs-lookup"><span data-stu-id="066ce-185">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="066ce-186">O seguinte código realçado mostra o T-SQL que verifica exatamente se uma linha foi atualizada:</span><span class="sxs-lookup"><span data-stu-id="066ce-186">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="066ce-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) retorna o número de linhas afetadas pela última instrução.</span><span class="sxs-lookup"><span data-stu-id="066ce-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="066ce-188">Se nenhuma linha for atualizada, o EF Core gerará uma `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="066ce-188">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="066ce-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="066ce-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="066ce-190">Para um banco de dados SQLite, o atributo `[Timestamp]` em uma propriedade de entidade definida como matriz de bytes:</span><span class="sxs-lookup"><span data-stu-id="066ce-190">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="066ce-191">Faz com que a coluna seja incluída nas cláusulas DELETE e UPDATE WHERE.</span><span class="sxs-lookup"><span data-stu-id="066ce-191">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="066ce-192">Mapeia para um tipo de coluna de BLOB.</span><span class="sxs-lookup"><span data-stu-id="066ce-192">Maps to a BLOB column type.</span></span>

<span data-ttu-id="066ce-193">Os gatilhos de banco de dados atualizam a coluna RowVersion com uma nova matriz de bytes aleatórios sempre que uma linha é atualizada.</span><span class="sxs-lookup"><span data-stu-id="066ce-193">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="066ce-194">Em um comando `Update` ou `Delete`, a cláusula `Where` inclui o valor buscado da coluna RowVersion.</span><span class="sxs-lookup"><span data-stu-id="066ce-194">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="066ce-195">Se a linha que está sendo atualizada foi alterada desde que foi buscada:</span><span class="sxs-lookup"><span data-stu-id="066ce-195">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="066ce-196">O valor da versão da linha atual não corresponde ao valor obtido.</span><span class="sxs-lookup"><span data-stu-id="066ce-196">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="066ce-197">O comando `Update` ou `Delete` não localiza uma linha porque a cláusula `Where` procura o valor da versão da linha original.</span><span class="sxs-lookup"><span data-stu-id="066ce-197">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="066ce-198">Uma `DbUpdateConcurrencyException` é gerada.</span><span class="sxs-lookup"><span data-stu-id="066ce-198">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="066ce-199">Atualizar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="066ce-199">Update the database</span></span>

<span data-ttu-id="066ce-200">Adicionar a propriedade `RowVersion` muda o modelo de dados, o que exige uma migração.</span><span class="sxs-lookup"><span data-stu-id="066ce-200">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="066ce-201">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="066ce-201">Build the project.</span></span> 

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="066ce-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="066ce-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="066ce-203">Execute o seguinte comando no PMC:</span><span class="sxs-lookup"><span data-stu-id="066ce-203">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="066ce-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="066ce-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="066ce-205">Em um terminal, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="066ce-205">Run the following command in a terminal:</span></span>

  ```console
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="066ce-206">Esse comando:</span><span class="sxs-lookup"><span data-stu-id="066ce-206">This command:</span></span>

* <span data-ttu-id="066ce-207">Cria o arquivo de migração *Migrations/{time stamp}_RowVersion.cs*.</span><span class="sxs-lookup"><span data-stu-id="066ce-207">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="066ce-208">Atualizam o arquivo *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="066ce-208">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="066ce-209">A atualização adiciona o seguinte código realçado ao método `BuildModel`:</span><span class="sxs-lookup"><span data-stu-id="066ce-209">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="066ce-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="066ce-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="066ce-211">Execute o seguinte comando no PMC:</span><span class="sxs-lookup"><span data-stu-id="066ce-211">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="066ce-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="066ce-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="066ce-213">Abra o arquivo `Migrations/<timestamp>_RowVersion.cs` e adicione o código realçado:</span><span class="sxs-lookup"><span data-stu-id="066ce-213">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="066ce-214">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="066ce-214">The preceding code:</span></span>

  * <span data-ttu-id="066ce-215">Atualiza as linhas existentes com valores de blob aleatórios.</span><span class="sxs-lookup"><span data-stu-id="066ce-215">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="066ce-216">Adiciona gatilhos de banco de dados que definem a coluna RowVersion como um valor de blob aleatório sempre que uma linha é atualizada.</span><span class="sxs-lookup"><span data-stu-id="066ce-216">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="066ce-217">Em um terminal, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="066ce-217">Run the following command in a terminal:</span></span>

  ```console
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="066ce-218">Aplicar scaffold a páginas de Departamento</span><span class="sxs-lookup"><span data-stu-id="066ce-218">Scaffold Department pages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="066ce-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="066ce-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="066ce-220">Siga as instruções em [páginas do aluno do Scaffold](xref:data/ef-rp/intro#scaffold-student-pages) com as seguintes exceções:</span><span class="sxs-lookup"><span data-stu-id="066ce-220">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="066ce-221">Crie uma pasta *Pages/Departments*.</span><span class="sxs-lookup"><span data-stu-id="066ce-221">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="066ce-222">Use `Department` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="066ce-222">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="066ce-223">Use a classe de contexto existente, em vez de criar uma nova.</span><span class="sxs-lookup"><span data-stu-id="066ce-223">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="066ce-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="066ce-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="066ce-225">Crie uma pasta *Pages/Departments*.</span><span class="sxs-lookup"><span data-stu-id="066ce-225">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="066ce-226">Execute o comando a seguir para aplicar scaffold das páginas do Departamento.</span><span class="sxs-lookup"><span data-stu-id="066ce-226">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="066ce-227">**No Windows:**</span><span class="sxs-lookup"><span data-stu-id="066ce-227">**On Windows:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="066ce-228">**No Linux ou macOS:**</span><span class="sxs-lookup"><span data-stu-id="066ce-228">**On Linux or macOS:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="066ce-229">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="066ce-229">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="066ce-230">Atualize a página Índice</span><span class="sxs-lookup"><span data-stu-id="066ce-230">Update the Index page</span></span>

<span data-ttu-id="066ce-231">A ferramenta de scaffolding criou uma coluna `RowVersion` para a página de índice, mas esse campo não seria exibido em um aplicativo de produção.</span><span class="sxs-lookup"><span data-stu-id="066ce-231">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="066ce-232">Neste tutorial, o último byte do `RowVersion` é exibido para ajudar a mostrar como funciona a manipulação de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="066ce-232">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="066ce-233">O último byte não tem garantia de ser exclusivo em si.</span><span class="sxs-lookup"><span data-stu-id="066ce-233">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="066ce-234">Atualize a página *Pages\Departments\Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="066ce-234">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="066ce-235">Substitua Índice por Departamentos.</span><span class="sxs-lookup"><span data-stu-id="066ce-235">Replace Index with Departments.</span></span>
* <span data-ttu-id="066ce-236">Altere o código que contém `RowVersion` para mostrar apenas o último byte da matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="066ce-236">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="066ce-237">Substitua FirstMidName por FullName.</span><span class="sxs-lookup"><span data-stu-id="066ce-237">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="066ce-238">O código a seguir mostra a página atualizada:</span><span class="sxs-lookup"><span data-stu-id="066ce-238">The following code shows the updated page:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="066ce-239">Atualizar o modelo da página Editar</span><span class="sxs-lookup"><span data-stu-id="066ce-239">Update the Edit page model</span></span>

<span data-ttu-id="066ce-240">Atualize *Pages\Departments\Edit.cshtml.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="066ce-240">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="066ce-241">O [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) é atualizado com o valor `rowVersion` da entidade quando ele foi buscado no método `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="066ce-241">The [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity when it was fetched in the `OnGet` method.</span></span> <span data-ttu-id="066ce-242">O EF Core gera um comando SQL UPDATE com uma cláusula WHERE que contém o valor `RowVersion` original.</span><span class="sxs-lookup"><span data-stu-id="066ce-242">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="066ce-243">Se nenhuma linha for afetada pelo comando UPDATE (nenhuma linha tem o valor `RowVersion` original), uma exceção `DbUpdateConcurrencyException` será gerada.</span><span class="sxs-lookup"><span data-stu-id="066ce-243">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="066ce-244">No código realçado anterior:</span><span class="sxs-lookup"><span data-stu-id="066ce-244">In the preceding highlighted code:</span></span>

* <span data-ttu-id="066ce-245">O valor em `Department.RowVersion` é o que estava na entidade quando ela foi buscada originalmente na solicitação Get para a página Editar.</span><span class="sxs-lookup"><span data-stu-id="066ce-245">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="066ce-246">O valor é fornecido ao método `OnPost` por um campo oculto na página do Razor que exibe a entidade a ser editada.</span><span class="sxs-lookup"><span data-stu-id="066ce-246">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="066ce-247">O valor do campo oculto é copiado para `Department.RowVersion` pelo associador de modelos.</span><span class="sxs-lookup"><span data-stu-id="066ce-247">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="066ce-248">`OriginalValue` é o que o EF Core usará na cláusula Where.</span><span class="sxs-lookup"><span data-stu-id="066ce-248">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="066ce-249">Antes que a linha de código realçada seja executada, o `OriginalValue` tem o valor que estava no banco de dados quando `FirstOrDefaultAsync` foi chamado nesse método, que pode ser diferente do que foi exibido na página de edição.</span><span class="sxs-lookup"><span data-stu-id="066ce-249">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="066ce-250">O código realçado garante que o EF Core use o valor `RowVersion` original da entidade `Department` exibida na cláusula Where da declaração SQL UPDATE.</span><span class="sxs-lookup"><span data-stu-id="066ce-250">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="066ce-251">Quando ocorre um erro de simultaneidade, o código realçado a seguir obtém os valores do cliente (os valores postados para esse método) e os valores do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="066ce-251">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="066ce-252">O seguinte código adiciona uma mensagem de erro personalizada a cada coluna que tem valores de banco de dados diferentes daqueles que foram postados em `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="066ce-252">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="066ce-253">O código realçado a seguir define o valor `RowVersion` com o novo valor recuperado do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="066ce-253">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="066ce-254">Na próxima vez que o usuário clicar em **Salvar**, somente os erros de simultaneidade que ocorrerem desde a última exibição da página Editar serão capturados.</span><span class="sxs-lookup"><span data-stu-id="066ce-254">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="066ce-255">A instrução `ModelState.Remove` é obrigatória porque `ModelState` tem o valor `RowVersion` antigo.</span><span class="sxs-lookup"><span data-stu-id="066ce-255">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="066ce-256">Na Página do Razor, o valor `ModelState` de um campo tem precedência sobre os valores de propriedade do modelo, quando ambos estão presentes.</span><span class="sxs-lookup"><span data-stu-id="066ce-256">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-razor-page"></a><span data-ttu-id="066ce-257">Atualizar a páginas do Razor</span><span class="sxs-lookup"><span data-stu-id="066ce-257">Update the Razor page</span></span>

<span data-ttu-id="066ce-258">Atualize *Pages/Departments/Edit.cshtml* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="066ce-258">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="066ce-259">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="066ce-259">The preceding code:</span></span>

* <span data-ttu-id="066ce-260">Atualiza a diretiva `page` de `@page` para `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="066ce-260">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="066ce-261">Adiciona uma versão de linha oculta.</span><span class="sxs-lookup"><span data-stu-id="066ce-261">Adds a hidden row version.</span></span> <span data-ttu-id="066ce-262">`RowVersion` deve ser adicionado para que o postback associe o valor.</span><span class="sxs-lookup"><span data-stu-id="066ce-262">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="066ce-263">Exibe o último byte de `RowVersion` para fins de depuração.</span><span class="sxs-lookup"><span data-stu-id="066ce-263">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="066ce-264">Substitui `ViewData` pelo `InstructorNameSL` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="066ce-264">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="066ce-265">Testar conflitos de simultaneidade com a página Editar</span><span class="sxs-lookup"><span data-stu-id="066ce-265">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="066ce-266">Abra duas instâncias de navegadores de Editar no departamento de inglês:</span><span class="sxs-lookup"><span data-stu-id="066ce-266">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="066ce-267">Execute o aplicativo e selecione Departamentos.</span><span class="sxs-lookup"><span data-stu-id="066ce-267">Run the app and select Departments.</span></span>
* <span data-ttu-id="066ce-268">Clique com o botão direito do mouse no hiperlink **Editar** do departamento de inglês e selecione **Abrir em uma nova guia**.</span><span class="sxs-lookup"><span data-stu-id="066ce-268">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="066ce-269">Na primeira guia, clique no hiperlink **Editar** do departamento de inglês.</span><span class="sxs-lookup"><span data-stu-id="066ce-269">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="066ce-270">As duas guias do navegador exibem as mesmas informações.</span><span class="sxs-lookup"><span data-stu-id="066ce-270">The two browser tabs display the same information.</span></span>

<span data-ttu-id="066ce-271">Altere o nome na primeira guia do navegador e clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="066ce-271">Change the name in the first browser tab and click **Save**.</span></span>

![Página 1 Editar Departamento após a alteração](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="066ce-273">O navegador mostra a página de Índice com o valor alterado e o indicador de rowVersion atualizado.</span><span class="sxs-lookup"><span data-stu-id="066ce-273">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="066ce-274">Observe o indicador de rowVersion atualizado: ele é exibido no segundo postback na outra guia.</span><span class="sxs-lookup"><span data-stu-id="066ce-274">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="066ce-275">Altere outro campo na segunda guia do navegador.</span><span class="sxs-lookup"><span data-stu-id="066ce-275">Change a different field in the second browser tab.</span></span>

![Página 2 Editar Departamento após a alteração](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="066ce-277">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="066ce-277">Click **Save**.</span></span> <span data-ttu-id="066ce-278">Você verá mensagens de erro em todos os campos que não correspondem aos valores do banco de dados:</span><span class="sxs-lookup"><span data-stu-id="066ce-278">You see error messages for all fields that don't match the database values:</span></span>

![Mensagem de erro da página Editar Departamento](concurrency/_static/edit-error30.png)

<span data-ttu-id="066ce-280">Essa janela do navegador não pretendia alterar o campo Name.</span><span class="sxs-lookup"><span data-stu-id="066ce-280">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="066ce-281">Copie e cole o valor atual (Languages) para o campo Name.</span><span class="sxs-lookup"><span data-stu-id="066ce-281">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="066ce-282">Saída da guia. A validação do lado do cliente remove a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="066ce-282">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="066ce-283">Clique em **Salvar** novamente.</span><span class="sxs-lookup"><span data-stu-id="066ce-283">Click **Save** again.</span></span> <span data-ttu-id="066ce-284">O valor inserido na segunda guia do navegador foi salvo.</span><span class="sxs-lookup"><span data-stu-id="066ce-284">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="066ce-285">Você verá os valores salvos na página Índice.</span><span class="sxs-lookup"><span data-stu-id="066ce-285">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="066ce-286">Atualizar a página Excluir</span><span class="sxs-lookup"><span data-stu-id="066ce-286">Update the Delete page</span></span>

<span data-ttu-id="066ce-287">Atualize *Pages/Departments/Delete.cshtml.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="066ce-287">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="066ce-288">A página Excluir detectou conflitos de simultaneidade quando a entidade foi alterada depois de ser buscada.</span><span class="sxs-lookup"><span data-stu-id="066ce-288">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="066ce-289">`Department.RowVersion` é a versão de linha quando a entidade foi buscada.</span><span class="sxs-lookup"><span data-stu-id="066ce-289">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="066ce-290">Quando o EF Core cria o comando SQL DELETE, ele inclui uma cláusula WHERE com `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="066ce-290">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="066ce-291">Se o comando SQL DELETE não resultar em nenhuma linha afetada:</span><span class="sxs-lookup"><span data-stu-id="066ce-291">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="066ce-292">A `RowVersion` no comando SQL DELETE não corresponderá a `RowVersion` no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="066ce-292">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="066ce-293">Uma exceção DbUpdateConcurrencyException é gerada.</span><span class="sxs-lookup"><span data-stu-id="066ce-293">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="066ce-294">`OnGetAsync` é chamado com o `concurrencyError`.</span><span class="sxs-lookup"><span data-stu-id="066ce-294">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-razor-page"></a><span data-ttu-id="066ce-295">Atualizar a página Excluir do Razor</span><span class="sxs-lookup"><span data-stu-id="066ce-295">Update the Delete Razor page</span></span>

<span data-ttu-id="066ce-296">Atualize *Pages/Departments/Delete.cshtml* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="066ce-296">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="066ce-297">O código anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="066ce-297">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="066ce-298">Atualiza a diretiva `page` de `@page` para `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="066ce-298">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="066ce-299">Adiciona uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="066ce-299">Adds an error message.</span></span>
* <span data-ttu-id="066ce-300">Substitua FirstMidName por FullName no campo **Administrador**.</span><span class="sxs-lookup"><span data-stu-id="066ce-300">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="066ce-301">Altere `RowVersion` para exibir o último byte.</span><span class="sxs-lookup"><span data-stu-id="066ce-301">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="066ce-302">Adiciona uma versão de linha oculta.</span><span class="sxs-lookup"><span data-stu-id="066ce-302">Adds a hidden row version.</span></span> <span data-ttu-id="066ce-303">O `RowVersion` deve ser adicionado para que o postgit add back associe o valor.</span><span class="sxs-lookup"><span data-stu-id="066ce-303">`RowVersion` must be added so postgit add back binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="066ce-304">Testar os conflitos de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="066ce-304">Test concurrency conflicts</span></span>

<span data-ttu-id="066ce-305">Crie um departamento de teste.</span><span class="sxs-lookup"><span data-stu-id="066ce-305">Create a test department.</span></span>

<span data-ttu-id="066ce-306">Abra duas instâncias dos navegadores de Excluir no departamento de teste:</span><span class="sxs-lookup"><span data-stu-id="066ce-306">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="066ce-307">Execute o aplicativo e selecione Departamentos.</span><span class="sxs-lookup"><span data-stu-id="066ce-307">Run the app and select Departments.</span></span>
* <span data-ttu-id="066ce-308">Clique com o botão direito do mouse no hiperlink **Excluir** do departamento de teste e selecione **Abrir em uma nova guia**.</span><span class="sxs-lookup"><span data-stu-id="066ce-308">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="066ce-309">Clique no hiperlink **Editar** do departamento de teste.</span><span class="sxs-lookup"><span data-stu-id="066ce-309">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="066ce-310">As duas guias do navegador exibem as mesmas informações.</span><span class="sxs-lookup"><span data-stu-id="066ce-310">The two browser tabs display the same information.</span></span>

<span data-ttu-id="066ce-311">Altere o orçamento na primeira guia do navegador e clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="066ce-311">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="066ce-312">O navegador mostra a página de Índice com o valor alterado e o indicador de rowVersion atualizado.</span><span class="sxs-lookup"><span data-stu-id="066ce-312">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="066ce-313">Observe o indicador de rowVersion atualizado: ele é exibido no segundo postback na outra guia.</span><span class="sxs-lookup"><span data-stu-id="066ce-313">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="066ce-314">Exclua o departamento de teste na segunda guia. Um erro de simultaneidade é exibido com os valores atuais do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="066ce-314">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="066ce-315">Clicar em **Excluir** exclui a entidade, a menos que `RowVersion` tenha sido atualizada e o departamento tenha sido excluído.</span><span class="sxs-lookup"><span data-stu-id="066ce-315">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="066ce-316">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="066ce-316">Additional resources</span></span>

* [<span data-ttu-id="066ce-317">Tokens de simultaneidade no EF Core</span><span class="sxs-lookup"><span data-stu-id="066ce-317">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="066ce-318">Lidar com a simultaneidade no EF Core</span><span class="sxs-lookup"><span data-stu-id="066ce-318">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="066ce-319">Depuração de origem do ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="066ce-319">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/aspnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="066ce-320">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="066ce-320">Next steps</span></span>

<span data-ttu-id="066ce-321">Este é o último tutorial da série.</span><span class="sxs-lookup"><span data-stu-id="066ce-321">This is the last tutorial in the series.</span></span> <span data-ttu-id="066ce-322">Tópicos adicionais são abordados na [versão MVC desta série de tutoriais](xref:data/ef-mvc/index).</span><span class="sxs-lookup"><span data-stu-id="066ce-322">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="066ce-323">Tutorial anterior</span><span class="sxs-lookup"><span data-stu-id="066ce-323">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="066ce-324">Este tutorial mostra como lidar com conflitos quando os mesmos usuários atualizam uma entidade simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="066ce-324">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="066ce-325">Caso tenha problemas que não consiga resolver, [baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="066ce-325">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="066ce-326">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="066ce-326">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="066ce-327">Conflitos de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="066ce-327">Concurrency conflicts</span></span>

<span data-ttu-id="066ce-328">Um conflito de simultaneidade ocorre quando:</span><span class="sxs-lookup"><span data-stu-id="066ce-328">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="066ce-329">Um usuário navega para a página de edição de uma entidade.</span><span class="sxs-lookup"><span data-stu-id="066ce-329">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="066ce-330">Outro usuário atualiza a mesma entidade antes que a primeira alteração do usuário seja gravada no BD.</span><span class="sxs-lookup"><span data-stu-id="066ce-330">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="066ce-331">Se a detecção de simultaneidade não estiver habilitada, quando ocorrerem atualizações simultâneas:</span><span class="sxs-lookup"><span data-stu-id="066ce-331">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="066ce-332">A última atualização vencerá.</span><span class="sxs-lookup"><span data-stu-id="066ce-332">The last update wins.</span></span> <span data-ttu-id="066ce-333">Ou seja, os últimos valores de atualização serão salvos no BD.</span><span class="sxs-lookup"><span data-stu-id="066ce-333">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="066ce-334">A primeira das atualizações atuais será perdida.</span><span class="sxs-lookup"><span data-stu-id="066ce-334">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="066ce-335">Simultaneidade otimista</span><span class="sxs-lookup"><span data-stu-id="066ce-335">Optimistic concurrency</span></span>

<span data-ttu-id="066ce-336">A simultaneidade otimista permite que conflitos de simultaneidade ocorram e, em seguida, responde adequadamente quando ocorrem.</span><span class="sxs-lookup"><span data-stu-id="066ce-336">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="066ce-337">Por exemplo, Alice visita a página Editar Departamento e altera o orçamento para o departamento de inglês de US$ 350.000,00 para US$ 0,00.</span><span class="sxs-lookup"><span data-stu-id="066ce-337">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Alterando o orçamento para 0](concurrency/_static/change-budget.png)

<span data-ttu-id="066ce-339">Antes que Alice clique em **Salvar**, Julio visita a mesma página e altera o campo Data de Início de 1/9/2007 para 1/9/2013.</span><span class="sxs-lookup"><span data-stu-id="066ce-339">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Alterando a data de início para 2013](concurrency/_static/change-date.png)

<span data-ttu-id="066ce-341">Alice clica em **Salvar** primeiro e vê a alteração quando o navegador exibe a página Índice.</span><span class="sxs-lookup"><span data-stu-id="066ce-341">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![Orçamento alterado para zero](concurrency/_static/budget-zero.png)

<span data-ttu-id="066ce-343">Julio clica em **Salvar** em uma página Editar que ainda mostra um orçamento de US$ 350.000,00.</span><span class="sxs-lookup"><span data-stu-id="066ce-343">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="066ce-344">O que acontece em seguida é determinado pela forma como você lida com conflitos de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="066ce-344">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="066ce-345">A simultaneidade otimista inclui as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="066ce-345">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="066ce-346">Controle qual propriedade um usuário modificou e atualize apenas as colunas correspondentes no BD.</span><span class="sxs-lookup"><span data-stu-id="066ce-346">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="066ce-347">No cenário, não haverá perda de dados.</span><span class="sxs-lookup"><span data-stu-id="066ce-347">In the scenario, no data would be lost.</span></span> <span data-ttu-id="066ce-348">Propriedades diferentes foram atualizadas pelos dois usuários.</span><span class="sxs-lookup"><span data-stu-id="066ce-348">Different properties were updated by the two users.</span></span> <span data-ttu-id="066ce-349">Na próxima vez que alguém navegar no departamento de inglês, verá as alterações de Alice e Julio.</span><span class="sxs-lookup"><span data-stu-id="066ce-349">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="066ce-350">Esse método de atualização pode reduzir o número de conflitos que podem resultar em perda de dados.</span><span class="sxs-lookup"><span data-stu-id="066ce-350">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="066ce-351">Essa abordagem:</span><span class="sxs-lookup"><span data-stu-id="066ce-351">This approach:</span></span>
 
  * <span data-ttu-id="066ce-352">Não poderá evitar a perda de dados se forem feitas alterações concorrentes na mesma propriedade.</span><span class="sxs-lookup"><span data-stu-id="066ce-352">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="066ce-353">Geralmente, não é prática em um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="066ce-353">Is generally not practical in a web app.</span></span> <span data-ttu-id="066ce-354">Ela exige um estado de manutenção significativo para controlar todos os valores buscados e novos valores.</span><span class="sxs-lookup"><span data-stu-id="066ce-354">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="066ce-355">Manter grandes quantidades de estado pode afetar o desempenho do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="066ce-355">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="066ce-356">Pode aumentar a complexidade do aplicativo comparado à detecção de simultaneidade em uma entidade.</span><span class="sxs-lookup"><span data-stu-id="066ce-356">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="066ce-357">Você não pode deixar a alteração de Julio substituir a alteração de Alice.</span><span class="sxs-lookup"><span data-stu-id="066ce-357">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="066ce-358">Na próxima vez que alguém navegar pelo departamento de inglês, verá 1/9/2013 e o valor de US$ 350.000,00 buscado.</span><span class="sxs-lookup"><span data-stu-id="066ce-358">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="066ce-359">Essa abordagem é chamada de um cenário *O cliente vence* ou *O último vence*.</span><span class="sxs-lookup"><span data-stu-id="066ce-359">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="066ce-360">(Todos os valores do cliente têm precedência sobre o conteúdo do armazenamento de dados.) Se você não fizer nenhuma codificação para a manipulação de simultaneidade, o cenário O cliente vence ocorrerá automaticamente.</span><span class="sxs-lookup"><span data-stu-id="066ce-360">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="066ce-361">Você pode impedir que as alterações de Julio sejam atualizadas no BD.</span><span class="sxs-lookup"><span data-stu-id="066ce-361">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="066ce-362">Normalmente, o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="066ce-362">Typically, the app would:</span></span>

  * <span data-ttu-id="066ce-363">Exibe uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="066ce-363">Display an error message.</span></span>
  * <span data-ttu-id="066ce-364">Mostra o estado atual dos dados.</span><span class="sxs-lookup"><span data-stu-id="066ce-364">Show the current state of the data.</span></span>
  * <span data-ttu-id="066ce-365">Permite ao usuário aplicar as alterações novamente.</span><span class="sxs-lookup"><span data-stu-id="066ce-365">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="066ce-366">Isso é chamado de um cenário *O armazenamento vence*.</span><span class="sxs-lookup"><span data-stu-id="066ce-366">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="066ce-367">(Os valores do armazenamento de dados têm precedência sobre os valores enviados pelo cliente.) Você implementará o cenário O armazenamento vence neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="066ce-367">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="066ce-368">Esse método garante que nenhuma alteração é substituída sem que um usuário seja alertado.</span><span class="sxs-lookup"><span data-stu-id="066ce-368">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="066ce-369">Tratamento de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="066ce-369">Handling concurrency</span></span> 

<span data-ttu-id="066ce-370">Quando uma propriedade é configurada como um [token de simultaneidade](/ef/core/modeling/concurrency):</span><span class="sxs-lookup"><span data-stu-id="066ce-370">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="066ce-371">O EF Core verifica se a propriedade não foi modificada depois que foi buscada.</span><span class="sxs-lookup"><span data-stu-id="066ce-371">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="066ce-372">A verificação ocorre quando [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) ou [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) é chamado.</span><span class="sxs-lookup"><span data-stu-id="066ce-372">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="066ce-373">Se a propriedade tiver sido alterada depois que ela foi buscada, uma [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) será gerada.</span><span class="sxs-lookup"><span data-stu-id="066ce-373">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) is thrown.</span></span> 

<span data-ttu-id="066ce-374">O BD e o modelo de dados precisam ser configurados para dar suporte à geração de `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="066ce-374">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="066ce-375">Detectando conflitos de simultaneidade em uma propriedade</span><span class="sxs-lookup"><span data-stu-id="066ce-375">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="066ce-376">Os conflitos de simultaneidade podem ser detectados no nível do propriedade com o atributo [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="066ce-376">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) attribute.</span></span> <span data-ttu-id="066ce-377">O atributo pode ser aplicado a várias propriedades no modelo.</span><span class="sxs-lookup"><span data-stu-id="066ce-377">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="066ce-378">Para obter mais informações, consulte [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span><span class="sxs-lookup"><span data-stu-id="066ce-378">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="066ce-379">O atributo `[ConcurrencyCheck]` não é usado neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="066ce-379">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="066ce-380">Detectando conflitos de simultaneidade em uma linha</span><span class="sxs-lookup"><span data-stu-id="066ce-380">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="066ce-381">Para detectar conflitos de simultaneidade, uma coluna de controle de [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) é adicionada ao modelo.</span><span class="sxs-lookup"><span data-stu-id="066ce-381">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="066ce-382">`rowversion`:</span><span class="sxs-lookup"><span data-stu-id="066ce-382">`rowversion` :</span></span>

* <span data-ttu-id="066ce-383">É específico ao SQL Server.</span><span class="sxs-lookup"><span data-stu-id="066ce-383">Is SQL Server specific.</span></span> <span data-ttu-id="066ce-384">Outros bancos de dados podem não fornecer um recurso semelhante.</span><span class="sxs-lookup"><span data-stu-id="066ce-384">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="066ce-385">É usado para determinar se uma entidade não foi alterada desde que foi buscada no BD.</span><span class="sxs-lookup"><span data-stu-id="066ce-385">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="066ce-386">O BD gera um número `rowversion` sequencial que é incrementado sempre que a linha é atualizada.</span><span class="sxs-lookup"><span data-stu-id="066ce-386">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="066ce-387">Em um comando `Update` ou `Delete`, a cláusula `Where` inclui o valor buscado de `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="066ce-387">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="066ce-388">Se a linha que está sendo atualizada foi alterada:</span><span class="sxs-lookup"><span data-stu-id="066ce-388">If the row being updated has changed:</span></span>

* <span data-ttu-id="066ce-389">`rowversion` não corresponde ao valor buscado.</span><span class="sxs-lookup"><span data-stu-id="066ce-389">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="066ce-390">Os comandos `Update` ou `Delete` não encontram uma linha porque a cláusula `Where` inclui a `rowversion` buscada.</span><span class="sxs-lookup"><span data-stu-id="066ce-390">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="066ce-391">Uma `DbUpdateConcurrencyException` é gerada.</span><span class="sxs-lookup"><span data-stu-id="066ce-391">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="066ce-392">No EF Core, quando nenhuma linha é atualizada por um comando `Update` ou `Delete`, uma exceção de simultaneidade é gerada.</span><span class="sxs-lookup"><span data-stu-id="066ce-392">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="066ce-393">Adicionar uma propriedade de controle à entidade Department</span><span class="sxs-lookup"><span data-stu-id="066ce-393">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="066ce-394">Em *Models/Department.cs*, adicione uma propriedade de controle chamada RowVersion:</span><span class="sxs-lookup"><span data-stu-id="066ce-394">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="066ce-395">O atributo [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) especifica que essa coluna é incluída na cláusula `Where` dos comandos `Update` e `Delete`.</span><span class="sxs-lookup"><span data-stu-id="066ce-395">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="066ce-396">O atributo é chamado `Timestamp` porque as versões anteriores do SQL Server usavam um tipo de dados `timestamp` do SQL antes de o tipo `rowversion` SQL substituí-lo.</span><span class="sxs-lookup"><span data-stu-id="066ce-396">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="066ce-397">A API fluente também pode especificar a propriedade de controle:</span><span class="sxs-lookup"><span data-stu-id="066ce-397">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="066ce-398">O seguinte código mostra uma parte do T-SQL gerado pelo EF Core quando o nome do Departamento é atualizado:</span><span class="sxs-lookup"><span data-stu-id="066ce-398">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="066ce-399">O código anterior realçado mostra a cláusula `WHERE` que contém `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="066ce-399">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="066ce-400">Se o BD `RowVersion` não for igual ao parâmetro `RowVersion` (`@p2`), nenhuma linha será atualizada.</span><span class="sxs-lookup"><span data-stu-id="066ce-400">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="066ce-401">O seguinte código realçado mostra o T-SQL que verifica exatamente se uma linha foi atualizada:</span><span class="sxs-lookup"><span data-stu-id="066ce-401">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="066ce-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) retorna o número de linhas afetadas pela última instrução.</span><span class="sxs-lookup"><span data-stu-id="066ce-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="066ce-403">Quando nenhuma linha é atualizada, o EF Core gera uma `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="066ce-403">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="066ce-404">Veja o T-SQL gerado pelo EF Core na janela de Saída do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="066ce-404">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="066ce-405">Atualizar o BD</span><span class="sxs-lookup"><span data-stu-id="066ce-405">Update the DB</span></span>

<span data-ttu-id="066ce-406">A adição da propriedade `RowVersion` altera o modelo de BD, o que exige uma migração.</span><span class="sxs-lookup"><span data-stu-id="066ce-406">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="066ce-407">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="066ce-407">Build the project.</span></span> <span data-ttu-id="066ce-408">Insira o seguinte em uma janela Comando:</span><span class="sxs-lookup"><span data-stu-id="066ce-408">Enter the following in a command window:</span></span>

```console
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="066ce-409">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="066ce-409">The preceding commands:</span></span>

* <span data-ttu-id="066ce-410">Adicionam o arquivo de migração *Migrations/{time stamp}_RowVersion.cs*.</span><span class="sxs-lookup"><span data-stu-id="066ce-410">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="066ce-411">Atualizam o arquivo *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="066ce-411">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="066ce-412">A atualização adiciona o seguinte código realçado ao método `BuildModel`:</span><span class="sxs-lookup"><span data-stu-id="066ce-412">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="066ce-413">Executam migrações para atualizar o BD.</span><span class="sxs-lookup"><span data-stu-id="066ce-413">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="066ce-414">Gerar o modelo Departamentos por scaffolding</span><span class="sxs-lookup"><span data-stu-id="066ce-414">Scaffold the Departments model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="066ce-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="066ce-415">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="066ce-416">Siga as instruções em [Gere um modelo de aluno por scaffold](xref:data/ef-rp/intro#scaffold-student-pages) e use `Department` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="066ce-416">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="066ce-417">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="066ce-417">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="066ce-418">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="066ce-418">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="066ce-419">O comando anterior gera o modelo `Department` por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="066ce-419">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="066ce-420">Abra o projeto no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="066ce-420">Open the project in Visual Studio.</span></span>

<span data-ttu-id="066ce-421">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="066ce-421">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="066ce-422">Atualizar a página Índice de Departamentos</span><span class="sxs-lookup"><span data-stu-id="066ce-422">Update the Departments Index page</span></span>

<span data-ttu-id="066ce-423">O mecanismo de scaffolding criou uma coluna `RowVersion` para a página Índice, mas esse campo não deve ser exibido.</span><span class="sxs-lookup"><span data-stu-id="066ce-423">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="066ce-424">Neste tutorial, o último byte da `RowVersion` é exibido para ajudar a entender a simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="066ce-424">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="066ce-425">O último byte não tem garantia de ser exclusivo.</span><span class="sxs-lookup"><span data-stu-id="066ce-425">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="066ce-426">Um aplicativo real não exibe `RowVersion` ou o último byte de `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="066ce-426">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="066ce-427">Atualize a página Índice:</span><span class="sxs-lookup"><span data-stu-id="066ce-427">Update the Index page:</span></span>

* <span data-ttu-id="066ce-428">Substitua Índice por Departamentos.</span><span class="sxs-lookup"><span data-stu-id="066ce-428">Replace Index with Departments.</span></span>
* <span data-ttu-id="066ce-429">Substitua a marcação que contém `RowVersion` pelo último byte de `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="066ce-429">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="066ce-430">Substitua FirstMidName por FullName.</span><span class="sxs-lookup"><span data-stu-id="066ce-430">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="066ce-431">A seguinte marcação mostra a página atualizada:</span><span class="sxs-lookup"><span data-stu-id="066ce-431">The following markup shows the updated page:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="066ce-432">Atualizar o modelo da página Editar</span><span class="sxs-lookup"><span data-stu-id="066ce-432">Update the Edit page model</span></span>

<span data-ttu-id="066ce-433">Atualize *Pages\Departments\Edit.cshtml.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="066ce-433">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="066ce-434">Para detectar um problema de simultaneidade, o [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) é atualizado com o valor `rowVersion` da entidade que foi buscada.</span><span class="sxs-lookup"><span data-stu-id="066ce-434">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="066ce-435">O EF Core gera um comando SQL UPDATE com uma cláusula WHERE que contém o valor `RowVersion` original.</span><span class="sxs-lookup"><span data-stu-id="066ce-435">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="066ce-436">Se nenhuma linha for afetada pelo comando UPDATE (nenhuma linha tem o valor `RowVersion` original), uma exceção `DbUpdateConcurrencyException` será gerada.</span><span class="sxs-lookup"><span data-stu-id="066ce-436">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="066ce-437">No código anterior, `Department.RowVersion` é o valor quando a entidade foi buscada.</span><span class="sxs-lookup"><span data-stu-id="066ce-437">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="066ce-438">`OriginalValue` é o valor no BD quando `FirstOrDefaultAsync` foi chamado nesse método.</span><span class="sxs-lookup"><span data-stu-id="066ce-438">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="066ce-439">O seguinte código obtém os valores de cliente (os valores postados nesse método) e os valores do BD:</span><span class="sxs-lookup"><span data-stu-id="066ce-439">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="066ce-440">O seguinte código adiciona uma mensagem de erro personalizada a cada coluna que tem valores de BD diferentes daqueles que foram postados em `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="066ce-440">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="066ce-441">O código realçado a seguir define o valor `RowVersion` com o novo valor recuperado do BD.</span><span class="sxs-lookup"><span data-stu-id="066ce-441">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="066ce-442">Na próxima vez que o usuário clicar em **Salvar**, somente os erros de simultaneidade que ocorrerem desde a última exibição da página Editar serão capturados.</span><span class="sxs-lookup"><span data-stu-id="066ce-442">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="066ce-443">A instrução `ModelState.Remove` é obrigatória porque `ModelState` tem o valor `RowVersion` antigo.</span><span class="sxs-lookup"><span data-stu-id="066ce-443">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="066ce-444">Na Página do Razor, o valor `ModelState` de um campo tem precedência sobre os valores de propriedade do modelo, quando ambos estão presentes.</span><span class="sxs-lookup"><span data-stu-id="066ce-444">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="066ce-445">Atualizar a página Editar</span><span class="sxs-lookup"><span data-stu-id="066ce-445">Update the Edit page</span></span>

<span data-ttu-id="066ce-446">Atualize *Pages/Departments/Edit.cshtml* com a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="066ce-446">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="066ce-447">A marcação anterior:</span><span class="sxs-lookup"><span data-stu-id="066ce-447">The preceding markup:</span></span>

* <span data-ttu-id="066ce-448">Atualiza a diretiva `page` de `@page` para `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="066ce-448">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="066ce-449">Adiciona uma versão de linha oculta.</span><span class="sxs-lookup"><span data-stu-id="066ce-449">Adds a hidden row version.</span></span> <span data-ttu-id="066ce-450">`RowVersion` deve ser adicionado para que o postback associe o valor.</span><span class="sxs-lookup"><span data-stu-id="066ce-450">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="066ce-451">Exibe o último byte de `RowVersion` para fins de depuração.</span><span class="sxs-lookup"><span data-stu-id="066ce-451">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="066ce-452">Substitui `ViewData` pelo `InstructorNameSL` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="066ce-452">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="066ce-453">Testar conflitos de simultaneidade com a página Editar</span><span class="sxs-lookup"><span data-stu-id="066ce-453">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="066ce-454">Abra duas instâncias de navegadores de Editar no departamento de inglês:</span><span class="sxs-lookup"><span data-stu-id="066ce-454">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="066ce-455">Execute o aplicativo e selecione Departamentos.</span><span class="sxs-lookup"><span data-stu-id="066ce-455">Run the app and select Departments.</span></span>
* <span data-ttu-id="066ce-456">Clique com o botão direito do mouse no hiperlink **Editar** do departamento de inglês e selecione **Abrir em uma nova guia**.</span><span class="sxs-lookup"><span data-stu-id="066ce-456">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="066ce-457">Na primeira guia, clique no hiperlink **Editar** do departamento de inglês.</span><span class="sxs-lookup"><span data-stu-id="066ce-457">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="066ce-458">As duas guias do navegador exibem as mesmas informações.</span><span class="sxs-lookup"><span data-stu-id="066ce-458">The two browser tabs display the same information.</span></span>

<span data-ttu-id="066ce-459">Altere o nome na primeira guia do navegador e clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="066ce-459">Change the name in the first browser tab and click **Save**.</span></span>

![Página 1 Editar Departamento após a alteração](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="066ce-461">O navegador mostra a página de Índice com o valor alterado e o indicador de rowVersion atualizado.</span><span class="sxs-lookup"><span data-stu-id="066ce-461">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="066ce-462">Observe o indicador de rowVersion atualizado: ele é exibido no segundo postback na outra guia.</span><span class="sxs-lookup"><span data-stu-id="066ce-462">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="066ce-463">Altere outro campo na segunda guia do navegador.</span><span class="sxs-lookup"><span data-stu-id="066ce-463">Change a different field in the second browser tab.</span></span>

![Página 2 Editar Departamento após a alteração](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="066ce-465">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="066ce-465">Click **Save**.</span></span> <span data-ttu-id="066ce-466">Você verá mensagens de erro em todos os campos que não correspondem aos valores do BD:</span><span class="sxs-lookup"><span data-stu-id="066ce-466">You see error messages for all fields that don't match the DB values:</span></span>

![Mensagem de erro da página Editar Departamento](concurrency/_static/edit-error.png)

<span data-ttu-id="066ce-468">Essa janela do navegador não pretendia alterar o campo Name.</span><span class="sxs-lookup"><span data-stu-id="066ce-468">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="066ce-469">Copie e cole o valor atual (Languages) para o campo Name.</span><span class="sxs-lookup"><span data-stu-id="066ce-469">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="066ce-470">Saída da guia. A validação do lado do cliente remove a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="066ce-470">Tab out. Client-side validation removes the error message.</span></span>

![Mensagem de erro da página Editar Departamento](concurrency/_static/cv.png)

<span data-ttu-id="066ce-472">Clique em **Salvar** novamente.</span><span class="sxs-lookup"><span data-stu-id="066ce-472">Click **Save** again.</span></span> <span data-ttu-id="066ce-473">O valor inserido na segunda guia do navegador foi salvo.</span><span class="sxs-lookup"><span data-stu-id="066ce-473">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="066ce-474">Você verá os valores salvos na página Índice.</span><span class="sxs-lookup"><span data-stu-id="066ce-474">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="066ce-475">Atualizar a página Excluir</span><span class="sxs-lookup"><span data-stu-id="066ce-475">Update the Delete page</span></span>

<span data-ttu-id="066ce-476">Atualize o modelo da página Excluir com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="066ce-476">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="066ce-477">A página Excluir detectou conflitos de simultaneidade quando a entidade foi alterada depois de ser buscada.</span><span class="sxs-lookup"><span data-stu-id="066ce-477">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="066ce-478">`Department.RowVersion` é a versão de linha quando a entidade foi buscada.</span><span class="sxs-lookup"><span data-stu-id="066ce-478">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="066ce-479">Quando o EF Core cria o comando SQL DELETE, ele inclui uma cláusula WHERE com `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="066ce-479">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="066ce-480">Se o comando SQL DELETE não resultar em nenhuma linha afetada:</span><span class="sxs-lookup"><span data-stu-id="066ce-480">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="066ce-481">A `RowVersion` no comando SQL DELETE não corresponderá a `RowVersion` no BD.</span><span class="sxs-lookup"><span data-stu-id="066ce-481">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="066ce-482">Uma exceção DbUpdateConcurrencyException é gerada.</span><span class="sxs-lookup"><span data-stu-id="066ce-482">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="066ce-483">`OnGetAsync` é chamado com o `concurrencyError`.</span><span class="sxs-lookup"><span data-stu-id="066ce-483">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="066ce-484">Atualizar a página Excluir</span><span class="sxs-lookup"><span data-stu-id="066ce-484">Update the Delete page</span></span>

<span data-ttu-id="066ce-485">Atualize *Pages/Departments/Delete.cshtml* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="066ce-485">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="066ce-486">O código anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="066ce-486">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="066ce-487">Atualiza a diretiva `page` de `@page` para `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="066ce-487">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="066ce-488">Adiciona uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="066ce-488">Adds an error message.</span></span>
* <span data-ttu-id="066ce-489">Substitua FirstMidName por FullName no campo **Administrador**.</span><span class="sxs-lookup"><span data-stu-id="066ce-489">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="066ce-490">Altere `RowVersion` para exibir o último byte.</span><span class="sxs-lookup"><span data-stu-id="066ce-490">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="066ce-491">Adiciona uma versão de linha oculta.</span><span class="sxs-lookup"><span data-stu-id="066ce-491">Adds a hidden row version.</span></span> <span data-ttu-id="066ce-492">`RowVersion` deve ser adicionado para que o postback associe o valor.</span><span class="sxs-lookup"><span data-stu-id="066ce-492">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="066ce-493">Testar conflitos de simultaneidade com a página Excluir</span><span class="sxs-lookup"><span data-stu-id="066ce-493">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="066ce-494">Crie um departamento de teste.</span><span class="sxs-lookup"><span data-stu-id="066ce-494">Create a test department.</span></span>

<span data-ttu-id="066ce-495">Abra duas instâncias dos navegadores de Excluir no departamento de teste:</span><span class="sxs-lookup"><span data-stu-id="066ce-495">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="066ce-496">Execute o aplicativo e selecione Departamentos.</span><span class="sxs-lookup"><span data-stu-id="066ce-496">Run the app and select Departments.</span></span>
* <span data-ttu-id="066ce-497">Clique com o botão direito do mouse no hiperlink **Excluir** do departamento de teste e selecione **Abrir em uma nova guia**.</span><span class="sxs-lookup"><span data-stu-id="066ce-497">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="066ce-498">Clique no hiperlink **Editar** do departamento de teste.</span><span class="sxs-lookup"><span data-stu-id="066ce-498">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="066ce-499">As duas guias do navegador exibem as mesmas informações.</span><span class="sxs-lookup"><span data-stu-id="066ce-499">The two browser tabs display the same information.</span></span>

<span data-ttu-id="066ce-500">Altere o orçamento na primeira guia do navegador e clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="066ce-500">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="066ce-501">O navegador mostra a página de Índice com o valor alterado e o indicador de rowVersion atualizado.</span><span class="sxs-lookup"><span data-stu-id="066ce-501">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="066ce-502">Observe o indicador de rowVersion atualizado: ele é exibido no segundo postback na outra guia.</span><span class="sxs-lookup"><span data-stu-id="066ce-502">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="066ce-503">Exclua o departamento de teste na segunda guia. Um erro de simultaneidade é exibido com os valores atuais do BD.</span><span class="sxs-lookup"><span data-stu-id="066ce-503">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="066ce-504">Clicar em **Excluir** exclui a entidade, a menos que `RowVersion` tenha sido atualizada e o departamento tenha sido excluído.</span><span class="sxs-lookup"><span data-stu-id="066ce-504">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

<span data-ttu-id="066ce-505">Consulte [Herança](xref:data/ef-mvc/inheritance) para saber como herdar um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="066ce-505">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="066ce-506">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="066ce-506">Additional resources</span></span>

* [<span data-ttu-id="066ce-507">Tokens de simultaneidade no EF Core</span><span class="sxs-lookup"><span data-stu-id="066ce-507">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="066ce-508">Lidar com a simultaneidade no EF Core</span><span class="sxs-lookup"><span data-stu-id="066ce-508">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="066ce-509">Versão do YouTube deste tutorial (Como tratar conflitos de simultaneidade)</span><span class="sxs-lookup"><span data-stu-id="066ce-509">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="066ce-510">Versão do YouTube deste tutorial (Parte 2)</span><span class="sxs-lookup"><span data-stu-id="066ce-510">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="066ce-511">Versão do YouTube deste tutorial (Parte 3)</span><span class="sxs-lookup"><span data-stu-id="066ce-511">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="066ce-512">Anterior</span><span class="sxs-lookup"><span data-stu-id="066ce-512">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

