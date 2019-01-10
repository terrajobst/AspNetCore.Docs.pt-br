---
title: Páginas Razor com o EF Core no ASP.NET Core – Simultaneidade – 8 de 8
author: rick-anderson
description: Este tutorial mostra como lidar com conflitos quando os mesmos usuários atualizam a mesma entidade simultaneamente.
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2018
uid: data/ef-rp/concurrency
ms.openlocfilehash: 71d68a7ee249c31efa78d98247017e85c009ed8b
ms.sourcegitcommit: e1cc4c1ef6c9e07918a609d5ad7fadcb6abe3e12
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53997312"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a><span data-ttu-id="8fe3b-103">Páginas Razor com o EF Core no ASP.NET Core – Simultaneidade – 8 de 8</span><span class="sxs-lookup"><span data-stu-id="8fe3b-103">Razor Pages with EF Core in ASP.NET Core - Concurrency - 8 of 8</span></span>

<span data-ttu-id="8fe3b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) e [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="8fe3b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

<span data-ttu-id="8fe3b-105">Este tutorial mostra como lidar com conflitos quando os mesmos usuários atualizam uma entidade simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="8fe3b-106">Caso tenha problemas que não consiga resolver, [baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="8fe3b-106">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="8fe3b-107">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="8fe3b-107">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="8fe3b-108">Conflitos de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="8fe3b-108">Concurrency conflicts</span></span>

<span data-ttu-id="8fe3b-109">Um conflito de simultaneidade ocorre quando:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-109">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="8fe3b-110">Um usuário navega para a página de edição de uma entidade.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-110">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="8fe3b-111">Outro usuário atualiza a mesma entidade antes que a primeira alteração do usuário seja gravada no BD.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-111">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="8fe3b-112">Se a detecção de simultaneidade não estiver habilitada, quando ocorrerem atualizações simultâneas:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-112">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="8fe3b-113">A última atualização vencerá.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-113">The last update wins.</span></span> <span data-ttu-id="8fe3b-114">Ou seja, os últimos valores de atualização serão salvos no BD.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-114">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="8fe3b-115">A primeira das atualizações atuais será perdida.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-115">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="8fe3b-116">Simultaneidade otimista</span><span class="sxs-lookup"><span data-stu-id="8fe3b-116">Optimistic concurrency</span></span>

<span data-ttu-id="8fe3b-117">A simultaneidade otimista permite que conflitos de simultaneidade ocorram e, em seguida, responde adequadamente quando ocorrem.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-117">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="8fe3b-118">Por exemplo, Alice visita a página Editar Departamento e altera o orçamento para o departamento de inglês de US$ 350.000,00 para US$ 0,00.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-118">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Alterando o orçamento para 0](concurrency/_static/change-budget.png)

<span data-ttu-id="8fe3b-120">Antes que Alice clique em **Salvar**, Julio visita a mesma página e altera o campo Data de Início de 1/9/2007 para 1/9/2013.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-120">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Alterando a data de início para 2013](concurrency/_static/change-date.png)

<span data-ttu-id="8fe3b-122">Alice clica em **Salvar** primeiro e vê a alteração quando o navegador exibe a página Índice.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-122">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![Orçamento alterado para zero](concurrency/_static/budget-zero.png)

<span data-ttu-id="8fe3b-124">Julio clica em **Salvar** em uma página Editar que ainda mostra um orçamento de US$ 350.000,00.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-124">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="8fe3b-125">O que acontece em seguida é determinado pela forma como você lida com conflitos de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-125">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="8fe3b-126">A simultaneidade otimista inclui as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-126">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="8fe3b-127">Controle qual propriedade um usuário modificou e atualize apenas as colunas correspondentes no BD.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-127">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="8fe3b-128">No cenário, não haverá perda de dados.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-128">In the scenario, no data would be lost.</span></span> <span data-ttu-id="8fe3b-129">Propriedades diferentes foram atualizadas pelos dois usuários.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-129">Different properties were updated by the two users.</span></span> <span data-ttu-id="8fe3b-130">Na próxima vez que alguém navegar no departamento de inglês, verá as alterações de Alice e Julio.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-130">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="8fe3b-131">Esse método de atualização pode reduzir o número de conflitos que podem resultar em perda de dados.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-131">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="8fe3b-132">Essa abordagem:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-132">This approach:</span></span>
 
  * <span data-ttu-id="8fe3b-133">Não poderá evitar a perda de dados se forem feitas alterações concorrentes na mesma propriedade.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-133">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="8fe3b-134">Geralmente, não é prática em um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-134">Is generally not practical in a web app.</span></span> <span data-ttu-id="8fe3b-135">Ela exige um estado de manutenção significativo para controlar todos os valores buscados e novos valores.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-135">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="8fe3b-136">Manter grandes quantidades de estado pode afetar o desempenho do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-136">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="8fe3b-137">Pode aumentar a complexidade do aplicativo comparado à detecção de simultaneidade em uma entidade.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-137">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="8fe3b-138">Você não pode deixar a alteração de Julio substituir a alteração de Alice.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-138">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="8fe3b-139">Na próxima vez que alguém navegar pelo departamento de inglês, verá 1/9/2013 e o valor de US$ 350.000,00 buscado.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-139">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="8fe3b-140">Essa abordagem é chamada de um cenário *O cliente vence* ou *O último vence*.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-140">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="8fe3b-141">(Todos os valores do cliente têm precedência sobre o conteúdo do armazenamento de dados.) Se você não fizer nenhuma codificação para a manipulação de simultaneidade, o cenário O cliente vence ocorrerá automaticamente.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-141">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="8fe3b-142">Você pode impedir que as alterações de Julio sejam atualizadas no BD.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-142">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="8fe3b-143">Normalmente, o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-143">Typically, the app would:</span></span>

  * <span data-ttu-id="8fe3b-144">Exibe uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-144">Display an error message.</span></span>
  * <span data-ttu-id="8fe3b-145">Mostra o estado atual dos dados.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-145">Show the current state of the data.</span></span>
  * <span data-ttu-id="8fe3b-146">Permite ao usuário aplicar as alterações novamente.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-146">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="8fe3b-147">Isso é chamado de um cenário *O armazenamento vence*.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-147">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="8fe3b-148">(Os valores do armazenamento de dados têm precedência sobre os valores enviados pelo cliente.) Você implementará o cenário O armazenamento vence neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-148">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="8fe3b-149">Esse método garante que nenhuma alteração é substituída sem que um usuário seja alertado.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-149">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="8fe3b-150">Tratamento de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="8fe3b-150">Handling concurrency</span></span> 

<span data-ttu-id="8fe3b-151">Quando uma propriedade é configurada como um [token de simultaneidade](/ef/core/modeling/concurrency):</span><span class="sxs-lookup"><span data-stu-id="8fe3b-151">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="8fe3b-152">O EF Core verifica se a propriedade não foi modificada depois que foi buscada.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-152">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="8fe3b-153">A verificação ocorre quando [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) ou [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) é chamado.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-153">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="8fe3b-154">Se a propriedade tiver sido alterada depois que ela foi buscada, uma [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) será gerada.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-154">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) is thrown.</span></span> 

<span data-ttu-id="8fe3b-155">O BD e o modelo de dados precisam ser configurados para dar suporte à geração de `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-155">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="8fe3b-156">Detectando conflitos de simultaneidade em uma propriedade</span><span class="sxs-lookup"><span data-stu-id="8fe3b-156">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="8fe3b-157">Os conflitos de simultaneidade podem ser detectados no nível do propriedade com o atributo [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="8fe3b-157">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) attribute.</span></span> <span data-ttu-id="8fe3b-158">O atributo pode ser aplicado a várias propriedades no modelo.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-158">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="8fe3b-159">Para obter mais informações, consulte [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span><span class="sxs-lookup"><span data-stu-id="8fe3b-159">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="8fe3b-160">O atributo `[ConcurrencyCheck]` não é usado neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-160">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="8fe3b-161">Detectando conflitos de simultaneidade em uma linha</span><span class="sxs-lookup"><span data-stu-id="8fe3b-161">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="8fe3b-162">Para detectar conflitos de simultaneidade, uma coluna de controle de [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) é adicionada ao modelo.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-162">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="8fe3b-163">`rowversion`:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-163">`rowversion` :</span></span>

* <span data-ttu-id="8fe3b-164">É específico ao SQL Server.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-164">Is SQL Server specific.</span></span> <span data-ttu-id="8fe3b-165">Outros bancos de dados podem não fornecer um recurso semelhante.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-165">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="8fe3b-166">É usado para determinar se uma entidade não foi alterada desde que foi buscada no BD.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-166">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="8fe3b-167">O BD gera um número `rowversion` sequencial que é incrementado sempre que a linha é atualizada.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-167">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="8fe3b-168">Em um comando `Update` ou `Delete`, a cláusula `Where` inclui o valor buscado de `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-168">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="8fe3b-169">Se a linha que está sendo atualizada foi alterada:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-169">If the row being updated has changed:</span></span>

 * <span data-ttu-id="8fe3b-170">`rowversion` não corresponde ao valor buscado.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-170">`rowversion` doesn't match the fetched value.</span></span>
 * <span data-ttu-id="8fe3b-171">Os comandos `Update` ou `Delete` não encontram uma linha porque a cláusula `Where` inclui a `rowversion` buscada.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-171">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
 * <span data-ttu-id="8fe3b-172">Uma `DbUpdateConcurrencyException` é gerada.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-172">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="8fe3b-173">No EF Core, quando nenhuma linha é atualizada por um comando `Update` ou `Delete`, uma exceção de simultaneidade é gerada.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-173">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="8fe3b-174">Adicionar uma propriedade de controle à entidade Department</span><span class="sxs-lookup"><span data-stu-id="8fe3b-174">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="8fe3b-175">Em *Models/Department.cs*, adicione uma propriedade de controle chamada RowVersion:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-175">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="8fe3b-176">O atributo [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) especifica que essa coluna é incluída na cláusula `Where` dos comandos `Update` e `Delete`.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-176">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="8fe3b-177">O atributo é chamado `Timestamp` porque as versões anteriores do SQL Server usavam um tipo de dados `timestamp` do SQL antes de o tipo `rowversion` SQL substituí-lo.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-177">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="8fe3b-178">A API fluente também pode especificar a propriedade de controle:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-178">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="8fe3b-179">O seguinte código mostra uma parte do T-SQL gerado pelo EF Core quando o nome do Departamento é atualizado:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-179">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/sql.txt?highlight=2-3)]

<span data-ttu-id="8fe3b-180">O código anterior realçado mostra a cláusula `WHERE` que contém `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-180">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="8fe3b-181">Se o BD `RowVersion` não for igual ao parâmetro `RowVersion` (`@p2`), nenhuma linha será atualizada.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-181">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="8fe3b-182">O seguinte código realçado mostra o T-SQL que verifica exatamente se uma linha foi atualizada:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-182">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/sql.txt?highlight=4-6)]

<span data-ttu-id="8fe3b-183">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) retorna o número de linhas afetadas pela última instrução.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-183">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="8fe3b-184">Quando nenhuma linha é atualizada, o EF Core gera uma `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-184">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="8fe3b-185">Veja o T-SQL gerado pelo EF Core na janela de Saída do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-185">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="8fe3b-186">Atualizar o BD</span><span class="sxs-lookup"><span data-stu-id="8fe3b-186">Update the DB</span></span>

<span data-ttu-id="8fe3b-187">A adição da propriedade `RowVersion` altera o modelo de BD, o que exige uma migração.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-187">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="8fe3b-188">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-188">Build the project.</span></span> <span data-ttu-id="8fe3b-189">Insira o seguinte em uma janela Comando:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-189">Enter the following in a command window:</span></span>

```console
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="8fe3b-190">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-190">The preceding commands:</span></span>

* <span data-ttu-id="8fe3b-191">Adicionam o arquivo de migração *Migrations/{time stamp}_RowVersion.cs*.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-191">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="8fe3b-192">Atualizam o arquivo *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-192">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="8fe3b-193">A atualização adiciona o seguinte código realçado ao método `BuildModel`:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-193">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot2.cs?name=snippet&highlight=14-16)]

* <span data-ttu-id="8fe3b-194">Executam migrações para atualizar o BD.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-194">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>
## <a name="scaffold-the-departments-model"></a><span data-ttu-id="8fe3b-195">Gerar o modelo Departamentos por scaffolding</span><span class="sxs-lookup"><span data-stu-id="8fe3b-195">Scaffold the Departments model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8fe3b-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8fe3b-196">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="8fe3b-197">Siga as instruções em [Gere um modelo de aluno por scaffold](xref:data/ef-rp/intro#scaffold-the-student-model) e use `Department` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-197">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Department` for the model class.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="8fe3b-198">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="8fe3b-198">.NET Core CLI</span></span>](#tab/netcore-cli)

 <span data-ttu-id="8fe3b-199">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-199">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

------

<span data-ttu-id="8fe3b-200">O comando anterior gera o modelo `Department` por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-200">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="8fe3b-201">Abra o projeto no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-201">Open the project in Visual Studio.</span></span>

<span data-ttu-id="8fe3b-202">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-202">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="8fe3b-203">Atualizar a página Índice de Departamentos</span><span class="sxs-lookup"><span data-stu-id="8fe3b-203">Update the Departments Index page</span></span>

<span data-ttu-id="8fe3b-204">O mecanismo de scaffolding criou uma coluna `RowVersion` para a página Índice, mas esse campo não deve ser exibido.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-204">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="8fe3b-205">Neste tutorial, o último byte da `RowVersion` é exibido para ajudar a entender a simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-205">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="8fe3b-206">O último byte não tem garantia de ser exclusivo.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-206">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="8fe3b-207">Um aplicativo real não exibe `RowVersion` ou o último byte de `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-207">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="8fe3b-208">Atualize a página Índice:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-208">Update the Index page:</span></span>

* <span data-ttu-id="8fe3b-209">Substitua Índice por Departamentos.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-209">Replace Index with Departments.</span></span>
* <span data-ttu-id="8fe3b-210">Substitua a marcação que contém `RowVersion` pelo último byte de `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-210">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="8fe3b-211">Substitua FirstMidName por FullName.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-211">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="8fe3b-212">A seguinte marcação mostra a página atualizada:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-212">The following markup shows the updated page:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="8fe3b-213">Atualizar o modelo da página Editar</span><span class="sxs-lookup"><span data-stu-id="8fe3b-213">Update the Edit page model</span></span>

<span data-ttu-id="8fe3b-214">Atualize *pages\departments\edit.cshtml.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-214">Update *pages\departments\edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="8fe3b-215">Para detectar um problema de simultaneidade, o [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) é atualizado com o valor `rowVersion` da entidade que foi buscada.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-215">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="8fe3b-216">O EF Core gera um comando SQL UPDATE com uma cláusula WHERE que contém o valor `RowVersion` original.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-216">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="8fe3b-217">Se nenhuma linha for afetada pelo comando UPDATE (nenhuma linha tem o valor `RowVersion` original), uma exceção `DbUpdateConcurrencyException` será gerada.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-217">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="8fe3b-218">No código anterior, `Department.RowVersion` é o valor quando a entidade foi buscada.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-218">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="8fe3b-219">`OriginalValue` é o valor no BD quando `FirstOrDefaultAsync` foi chamado nesse método.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-219">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="8fe3b-220">O seguinte código obtém os valores de cliente (os valores postados nesse método) e os valores do BD:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-220">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="8fe3b-221">O seguinte código adiciona uma mensagem de erro personalizada a cada coluna que tem valores de BD diferentes daqueles que foram postados em `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-221">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="8fe3b-222">O código realçado a seguir define o valor `RowVersion` com o novo valor recuperado do BD.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-222">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="8fe3b-223">Na próxima vez que o usuário clicar em **Salvar**, somente os erros de simultaneidade que ocorrerem desde a última exibição da página Editar serão capturados.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-223">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="8fe3b-224">A instrução `ModelState.Remove` é obrigatória porque `ModelState` tem o valor `RowVersion` antigo.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-224">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="8fe3b-225">Na Página do Razor, o valor `ModelState` de um campo tem precedência sobre os valores de propriedade do modelo, quando ambos estão presentes.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-225">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="8fe3b-226">Atualizar a página Editar</span><span class="sxs-lookup"><span data-stu-id="8fe3b-226">Update the Edit page</span></span>

<span data-ttu-id="8fe3b-227">Atualize *Pages/Departments/Edit.cshtml* com a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-227">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="8fe3b-228">A marcação anterior:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-228">The preceding markup:</span></span>

* <span data-ttu-id="8fe3b-229">Atualiza a diretiva `page` de `@page` para `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-229">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="8fe3b-230">Adiciona uma versão de linha oculta.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-230">Adds a hidden row version.</span></span> <span data-ttu-id="8fe3b-231">`RowVersion` deve ser adicionado para que o postback associe o valor.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-231">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="8fe3b-232">Exibe o último byte de `RowVersion` para fins de depuração.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-232">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="8fe3b-233">Substitui `ViewData` pelo `InstructorNameSL` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-233">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="8fe3b-234">Testar conflitos de simultaneidade com a página Editar</span><span class="sxs-lookup"><span data-stu-id="8fe3b-234">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="8fe3b-235">Abra duas instâncias de navegadores de Editar no departamento de inglês:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-235">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="8fe3b-236">Execute o aplicativo e selecione Departamentos.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-236">Run the app and select Departments.</span></span>
* <span data-ttu-id="8fe3b-237">Clique com o botão direito do mouse no hiperlink **Editar** do departamento de inglês e selecione **Abrir em uma nova guia**.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-237">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="8fe3b-238">Na primeira guia, clique no hiperlink **Editar** do departamento de inglês.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-238">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="8fe3b-239">As duas guias do navegador exibem as mesmas informações.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-239">The two browser tabs display the same information.</span></span>

<span data-ttu-id="8fe3b-240">Altere o nome na primeira guia do navegador e clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-240">Change the name in the first browser tab and click **Save**.</span></span>

![Página 1 Editar Departamento após a alteração](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="8fe3b-242">O navegador mostra a página de Índice com o valor alterado e o indicador de rowVersion atualizado.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-242">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="8fe3b-243">Observe o indicador de rowVersion atualizado: ele é exibido no segundo postback na outra guia.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-243">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="8fe3b-244">Altere outro campo na segunda guia do navegador.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-244">Change a different field in the second browser tab.</span></span>

![Página 2 Editar Departamento após a alteração](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="8fe3b-246">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-246">Click **Save**.</span></span> <span data-ttu-id="8fe3b-247">Você verá mensagens de erro em todos os campos que não correspondem aos valores do BD:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-247">You see error messages for all fields that don't match the DB values:</span></span>

![Mensagem de erro da página Editar Departamento](concurrency/_static/edit-error.png)

<span data-ttu-id="8fe3b-249">Essa janela do navegador não pretendia alterar o campo Name.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-249">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="8fe3b-250">Copie e cole o valor atual (Languages) para o campo Name.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-250">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="8fe3b-251">Saída da guia. A validação do lado do cliente remove a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-251">Tab out. Client-side validation removes the error message.</span></span>

![Mensagem de erro da página Editar Departamento](concurrency/_static/cv.png)

<span data-ttu-id="8fe3b-253">Clique em **Salvar** novamente.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-253">Click **Save** again.</span></span> <span data-ttu-id="8fe3b-254">O valor inserido na segunda guia do navegador foi salvo.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-254">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="8fe3b-255">Você verá os valores salvos na página Índice.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-255">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="8fe3b-256">Atualizar a página Excluir</span><span class="sxs-lookup"><span data-stu-id="8fe3b-256">Update the Delete page</span></span>

<span data-ttu-id="8fe3b-257">Atualize o modelo da página Excluir com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-257">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="8fe3b-258">A página Excluir detectou conflitos de simultaneidade quando a entidade foi alterada depois de ser buscada.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-258">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="8fe3b-259">`Department.RowVersion` é a versão de linha quando a entidade foi buscada.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-259">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="8fe3b-260">Quando o EF Core cria o comando SQL DELETE, ele inclui uma cláusula WHERE com `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-260">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="8fe3b-261">Se o comando SQL DELETE não resultar em nenhuma linha afetada:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-261">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="8fe3b-262">A `RowVersion` no comando SQL DELETE não corresponderá a `RowVersion` no BD.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-262">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="8fe3b-263">Uma exceção DbUpdateConcurrencyException é gerada.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-263">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="8fe3b-264">`OnGetAsync` é chamado com o `concurrencyError`.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-264">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="8fe3b-265">Atualizar a página Excluir</span><span class="sxs-lookup"><span data-stu-id="8fe3b-265">Update the Delete page</span></span>

<span data-ttu-id="8fe3b-266">Atualize *Pages/Departments/Delete.cshtml* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-266">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]


<span data-ttu-id="8fe3b-267">A marcação anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-267">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="8fe3b-268">Atualiza a diretiva `page` de `@page` para `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-268">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="8fe3b-269">Adiciona uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-269">Adds an error message.</span></span>
* <span data-ttu-id="8fe3b-270">Substitua FirstMidName por FullName no campo **Administrador**.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-270">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="8fe3b-271">Altere `RowVersion` para exibir o último byte.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-271">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="8fe3b-272">Adiciona uma versão de linha oculta.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-272">Adds a hidden row version.</span></span> <span data-ttu-id="8fe3b-273">`RowVersion` deve ser adicionado para que o postback associe o valor.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-273">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="8fe3b-274">Testar conflitos de simultaneidade com a página Excluir</span><span class="sxs-lookup"><span data-stu-id="8fe3b-274">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="8fe3b-275">Crie um departamento de teste.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-275">Create a test department.</span></span>

<span data-ttu-id="8fe3b-276">Abra duas instâncias dos navegadores de Excluir no departamento de teste:</span><span class="sxs-lookup"><span data-stu-id="8fe3b-276">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="8fe3b-277">Execute o aplicativo e selecione Departamentos.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-277">Run the app and select Departments.</span></span>
* <span data-ttu-id="8fe3b-278">Clique com o botão direito do mouse no hiperlink **Excluir** do departamento de teste e selecione **Abrir em uma nova guia**.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-278">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="8fe3b-279">Clique no hiperlink **Editar** do departamento de teste.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-279">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="8fe3b-280">As duas guias do navegador exibem as mesmas informações.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-280">The two browser tabs display the same information.</span></span>

<span data-ttu-id="8fe3b-281">Altere o orçamento na primeira guia do navegador e clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-281">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="8fe3b-282">O navegador mostra a página de Índice com o valor alterado e o indicador de rowVersion atualizado.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-282">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="8fe3b-283">Observe o indicador de rowVersion atualizado: ele é exibido no segundo postback na outra guia.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-283">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="8fe3b-284">Exclua o departamento de teste na segunda guia. Um erro de simultaneidade é exibido com os valores atuais do BD.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-284">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="8fe3b-285">Clicar em **Excluir** exclui a entidade, a menos que `RowVersion` tenha sido atualizada e o departamento tenha sido excluído.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-285">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.department has been deleted.</span></span>

<span data-ttu-id="8fe3b-286">Consulte [Herança](xref:data/ef-mvc/inheritance) para saber como herdar um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="8fe3b-286">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="8fe3b-287">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8fe3b-287">Additional resources</span></span>

* [<span data-ttu-id="8fe3b-288">Tokens de simultaneidade no EF Core</span><span class="sxs-lookup"><span data-stu-id="8fe3b-288">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="8fe3b-289">Lidar com a simultaneidade no EF Core</span><span class="sxs-lookup"><span data-stu-id="8fe3b-289">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)

> [!div class="step-by-step"]
> [<span data-ttu-id="8fe3b-290">Anterior</span><span class="sxs-lookup"><span data-stu-id="8fe3b-290">Previous</span></span>](xref:data/ef-rp/update-related-data)
