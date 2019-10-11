---
title: 'Tutorial: Lidar com a simultaneidade - ASP.NET MVC com EF Core'
description: Este tutorial mostra como lidar com conflitos quando os mesmos usuários atualizam a mesma entidade simultaneamente.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/concurrency
ms.openlocfilehash: 227128607460f9b5821bd0697fde3f393cf6daa9
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259433"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a><span data-ttu-id="da45e-103">Tutorial: Lidar com a simultaneidade - ASP.NET MVC com EF Core</span><span class="sxs-lookup"><span data-stu-id="da45e-103">Tutorial: Handle concurrency - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="da45e-104">Nos tutoriais anteriores, você aprendeu a atualizar dados.</span><span class="sxs-lookup"><span data-stu-id="da45e-104">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="da45e-105">Este tutorial mostra como lidar com conflitos quando os mesmos usuários atualizam a mesma entidade simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="da45e-105">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="da45e-106">Você criará páginas da Web que funcionam com a entidade Department e tratará erros de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="da45e-106">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="da45e-107">As ilustrações a seguir mostram as páginas Editar e Excluir, incluindo algumas mensagens exibidas se ocorre um conflito de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="da45e-107">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![Página Editar Departamento](concurrency/_static/edit-error.png)

![Página Excluir Departamento](concurrency/_static/delete-error.png)

<span data-ttu-id="da45e-110">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="da45e-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="da45e-111">Aprender sobre conflitos de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="da45e-111">Learn about concurrency conflicts</span></span>
> * <span data-ttu-id="da45e-112">Adicionar uma propriedade de acompanhamento</span><span class="sxs-lookup"><span data-stu-id="da45e-112">Add a tracking property</span></span>
> * <span data-ttu-id="da45e-113">Criar um controlador e exibições de Departamentos</span><span class="sxs-lookup"><span data-stu-id="da45e-113">Create Departments controller and views</span></span>
> * <span data-ttu-id="da45e-114">Atualizar a exibição do Índice</span><span class="sxs-lookup"><span data-stu-id="da45e-114">Update Index view</span></span>
> * <span data-ttu-id="da45e-115">Atualizar métodos de Edição</span><span class="sxs-lookup"><span data-stu-id="da45e-115">Update Edit methods</span></span>
> * <span data-ttu-id="da45e-116">Atualizar a exibição de Edição</span><span class="sxs-lookup"><span data-stu-id="da45e-116">Update Edit view</span></span>
> * <span data-ttu-id="da45e-117">Testar os conflitos de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="da45e-117">Test concurrency conflicts</span></span>
> * <span data-ttu-id="da45e-118">Atualizar a página Excluir</span><span class="sxs-lookup"><span data-stu-id="da45e-118">Update the Delete page</span></span>
> * <span data-ttu-id="da45e-119">Exibições Atualizar Detalhes e Criar</span><span class="sxs-lookup"><span data-stu-id="da45e-119">Update Details and Create views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="da45e-120">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="da45e-120">Prerequisites</span></span>

* [<span data-ttu-id="da45e-121">Atualizar dados relacionados</span><span class="sxs-lookup"><span data-stu-id="da45e-121">Update related data</span></span>](update-related-data.md)

## <a name="concurrency-conflicts"></a><span data-ttu-id="da45e-122">Conflitos de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="da45e-122">Concurrency conflicts</span></span>

<span data-ttu-id="da45e-123">Um conflito de simultaneidade ocorre quando um usuário exibe dados de uma entidade para editá-los e, em seguida, outro usuário atualiza os mesmos dados da entidade antes que a primeira alteração do usuário seja gravada no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="da45e-123">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="da45e-124">Se você não habilitar a detecção desses conflitos, a última pessoa que atualizar o banco de dados substituirá as outras alterações do usuário.</span><span class="sxs-lookup"><span data-stu-id="da45e-124">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="da45e-125">Em muitos aplicativos, esse risco é aceitável: se houver poucos usuários ou poucas atualizações ou se não for realmente crítico se algumas alterações forem substituídas, o custo de programação para simultaneidade poderá superar o benefício.</span><span class="sxs-lookup"><span data-stu-id="da45e-125">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="da45e-126">Nesse caso, você não precisa configurar o aplicativo para lidar com conflitos de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="da45e-126">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="da45e-127">Simultaneidade pessimista (bloqueio)</span><span class="sxs-lookup"><span data-stu-id="da45e-127">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="da45e-128">Se o aplicativo precisar evitar a perda acidental de dados em cenários de simultaneidade, uma maneira de fazer isso será usar bloqueios de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="da45e-128">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="da45e-129">Isso é chamado de simultaneidade pessimista.</span><span class="sxs-lookup"><span data-stu-id="da45e-129">This is called pessimistic concurrency.</span></span> <span data-ttu-id="da45e-130">Por exemplo, antes de ler uma linha de um banco de dados, você solicita um bloqueio para o acesso somente leitura ou de atualização.</span><span class="sxs-lookup"><span data-stu-id="da45e-130">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="da45e-131">Se você bloquear uma linha para o acesso de atualização, nenhum outro usuário terá permissão para bloquear a linha para o acesso somente leitura ou de atualização, porque ele obterá uma cópia dos dados que estão sendo alterados.</span><span class="sxs-lookup"><span data-stu-id="da45e-131">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="da45e-132">Se você bloquear uma linha para o acesso somente leitura, outros também poderão bloqueá-la para o acesso somente leitura, mas não para atualização.</span><span class="sxs-lookup"><span data-stu-id="da45e-132">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="da45e-133">O gerenciamento de bloqueios traz desvantagens.</span><span class="sxs-lookup"><span data-stu-id="da45e-133">Managing locks has disadvantages.</span></span> <span data-ttu-id="da45e-134">Ele pode ser complexo de ser programado.</span><span class="sxs-lookup"><span data-stu-id="da45e-134">It can be complex to program.</span></span> <span data-ttu-id="da45e-135">Exige recursos de gerenciamento de banco de dados significativos e pode causar problemas de desempenho, conforme o número de usuários de um aplicativo aumenta.</span><span class="sxs-lookup"><span data-stu-id="da45e-135">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="da45e-136">Por esses motivos, nem todos os sistemas de gerenciamento de banco de dados dão suporte à simultaneidade pessimista.</span><span class="sxs-lookup"><span data-stu-id="da45e-136">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="da45e-137">O Entity Framework Core não fornece nenhum suporte interno para ele, e este tutorial não mostra como implementá-lo.</span><span class="sxs-lookup"><span data-stu-id="da45e-137">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="da45e-138">Simultaneidade otimista</span><span class="sxs-lookup"><span data-stu-id="da45e-138">Optimistic Concurrency</span></span>

<span data-ttu-id="da45e-139">A alternativa à simultaneidade pessimista é a simultaneidade otimista.</span><span class="sxs-lookup"><span data-stu-id="da45e-139">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="da45e-140">Simultaneidade otimista significa permitir que conflitos de simultaneidade ocorram e responder adequadamente se eles ocorrerem.</span><span class="sxs-lookup"><span data-stu-id="da45e-140">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="da45e-141">Por exemplo, Alice visita a página Editar Departamento e altera o valor do Orçamento para o departamento de inglês de US$ 350.000,00 para US$ 0,00.</span><span class="sxs-lookup"><span data-stu-id="da45e-141">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![Alterando o orçamento para 0](concurrency/_static/change-budget.png)

<span data-ttu-id="da45e-143">Antes que Alice clique em **Salvar**, Julio visita a mesma página e altera o campo Data de Início de 1/9/2007 para 1/9/2013.</span><span class="sxs-lookup"><span data-stu-id="da45e-143">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Alterando a data de início para 2013](concurrency/_static/change-date.png)

<span data-ttu-id="da45e-145">Alice clica em **Salvar** primeiro e vê a alteração quando o navegador retorna à página Índice.</span><span class="sxs-lookup"><span data-stu-id="da45e-145">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![Orçamento alterado para zero](concurrency/_static/budget-zero.png)

<span data-ttu-id="da45e-147">Em seguida, Julio clica em **Salvar** em uma página Editar que ainda mostra um orçamento de US$ 350.000,00.</span><span class="sxs-lookup"><span data-stu-id="da45e-147">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="da45e-148">O que acontece em seguida é determinado pela forma como você lida com conflitos de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="da45e-148">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="da45e-149">Algumas das opções incluem o seguinte:</span><span class="sxs-lookup"><span data-stu-id="da45e-149">Some of the options include the following:</span></span>

* <span data-ttu-id="da45e-150">Controle qual propriedade um usuário modificou e atualize apenas as colunas correspondentes no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="da45e-150">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="da45e-151">No cenário de exemplo, nenhum dado é perdido, porque propriedades diferentes foram atualizadas pelos dois usuários.</span><span class="sxs-lookup"><span data-stu-id="da45e-151">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="da45e-152">Na próxima vez que alguém navegar pelo departamento de inglês, verá as alterações de Alice e de Julio – a data de início de 1/9/2013 e um orçamento de zero dólar.</span><span class="sxs-lookup"><span data-stu-id="da45e-152">The next time someone browses the English department, they will see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="da45e-153">Esse método de atualização pode reduzir a quantidade de conflitos que podem resultar em perda de dados, mas ele não poderá evitar a perda de dados se forem feitas alterações concorrentes à mesma propriedade de uma entidade.</span><span class="sxs-lookup"><span data-stu-id="da45e-153">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="da45e-154">Se o Entity Framework funciona dessa maneira depende de como o código de atualização é implementado.</span><span class="sxs-lookup"><span data-stu-id="da45e-154">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="da45e-155">Geralmente, isso não é prático em um aplicativo Web, porque pode exigir que você mantenha grandes quantidades de estado para manter o controle de todos os valores de propriedade originais de uma entidade, bem como novos valores.</span><span class="sxs-lookup"><span data-stu-id="da45e-155">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="da45e-156">A manutenção de grandes quantidades de estado pode afetar o desempenho do aplicativo, pois exige recursos do servidor ou deve ser incluída na própria página da Web (por exemplo, em campos ocultos) ou em um cookie.</span><span class="sxs-lookup"><span data-stu-id="da45e-156">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a cookie.</span></span>

* <span data-ttu-id="da45e-157">Você não pode deixar a alteração de Julio substituir a alteração de Alice.</span><span class="sxs-lookup"><span data-stu-id="da45e-157">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="da45e-158">Na próxima vez que alguém navegar pelo departamento de inglês, verá 1/9/2013 e o valor de US$ 350.000,00 restaurado.</span><span class="sxs-lookup"><span data-stu-id="da45e-158">The next time someone browses the English department, they will see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="da45e-159">Isso é chamado de um cenário *O cliente vence* ou *O último vence*.</span><span class="sxs-lookup"><span data-stu-id="da45e-159">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="da45e-160">(Todos os valores do cliente têm precedência sobre o conteúdo do armazenamento de dados.) Conforme observado na introdução a esta seção, se você não fizer nenhuma codificação para a manipulação de simultaneidade, isso ocorrerá automaticamente.</span><span class="sxs-lookup"><span data-stu-id="da45e-160">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="da45e-161">Você pode impedir que as alterações de Julio sejam atualizadas no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="da45e-161">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="da45e-162">Normalmente, você exibirá uma mensagem de erro, mostrará a ele o estado atual dos dados e permitirá a ele aplicar as alterações novamente se ele ainda desejar fazê-las.</span><span class="sxs-lookup"><span data-stu-id="da45e-162">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="da45e-163">Isso é chamado de um cenário *O armazenamento vence*.</span><span class="sxs-lookup"><span data-stu-id="da45e-163">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="da45e-164">(Os valores do armazenamento de dados têm precedência sobre os valores enviados pelo cliente.) Você implementará o cenário O armazenamento vence neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="da45e-164">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="da45e-165">Esse método garante que nenhuma alteração é substituída sem que um usuário seja alertado sobre o que está acontecendo.</span><span class="sxs-lookup"><span data-stu-id="da45e-165">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="da45e-166">Detectando conflitos de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="da45e-166">Detecting concurrency conflicts</span></span>

<span data-ttu-id="da45e-167">Resolva conflitos manipulando exceções `DbConcurrencyException` geradas pelo Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="da45e-167">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="da45e-168">Para saber quando gerar essas exceções, o Entity Framework precisa poder detectar conflitos.</span><span class="sxs-lookup"><span data-stu-id="da45e-168">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="da45e-169">Portanto, é necessário configurar o banco de dados e o modelo de dados de forma adequada.</span><span class="sxs-lookup"><span data-stu-id="da45e-169">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="da45e-170">Algumas opções para habilitar a detecção de conflitos incluem as seguintes:</span><span class="sxs-lookup"><span data-stu-id="da45e-170">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="da45e-171">Na tabela de banco de dados, inclua uma coluna de acompanhamento que pode ser usada para determinar quando uma linha é alterada.</span><span class="sxs-lookup"><span data-stu-id="da45e-171">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="da45e-172">Em seguida, configure o Entity Framework para incluir essa coluna na cláusula Where de comandos SQL Update ou Delete.</span><span class="sxs-lookup"><span data-stu-id="da45e-172">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="da45e-173">O tipo de dados da coluna de acompanhamento é normalmente `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="da45e-173">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="da45e-174">O valor `rowversion` é um número sequencial que é incrementado sempre que a linha é atualizada.</span><span class="sxs-lookup"><span data-stu-id="da45e-174">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="da45e-175">Em um comando Update ou Delete, a cláusula Where inclui o valor original da coluna de acompanhamento (a versão de linha original).</span><span class="sxs-lookup"><span data-stu-id="da45e-175">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="da45e-176">Se a linha que está sendo atualizada tiver sido alterada por outro usuário, o valor da coluna `rowversion` será diferente do valor original; portanto, a instrução Update ou Delete não poderá encontrar a linha a ser atualizada devido à cláusula Where.</span><span class="sxs-lookup"><span data-stu-id="da45e-176">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="da45e-177">Quando o Entity Framework descobre que nenhuma linha foi atualizada pelo comando Update ou Delete (ou seja, quando o número de linhas afetadas é zero), ele interpreta isso como um conflito de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="da45e-177">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="da45e-178">Configure o Entity Framework para incluir os valores originais de cada coluna na tabela na cláusula Where de comandos Update e Delete.</span><span class="sxs-lookup"><span data-stu-id="da45e-178">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="da45e-179">Como a primeira opção, se nada na linha for alterado desde que a linha tiver sido lida pela primeira vez, a cláusula Where não retornará uma linha a ser atualizada, o que o Entity Framework interpretará como um conflito de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="da45e-179">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="da45e-180">Para tabelas de banco de dados que têm muitas colunas, essa abordagem pode resultar em cláusulas Where muito grandes e pode exigir que você mantenha grandes quantidades de estado.</span><span class="sxs-lookup"><span data-stu-id="da45e-180">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="da45e-181">Conforme observado anteriormente, manter grandes quantidades de estado pode afetar o desempenho do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="da45e-181">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="da45e-182">Portanto, essa abordagem geralmente não é recomendada e não é o método usado neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="da45e-182">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="da45e-183">Caso deseje implementar essa abordagem, precisará marcar todas as propriedades de chave não primária na entidade para as quais você deseja controlar a simultaneidade adicionando o atributo `ConcurrencyCheck` a elas.</span><span class="sxs-lookup"><span data-stu-id="da45e-183">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="da45e-184">Essa alteração permite que o Entity Framework inclua todas as colunas na cláusula Where do SQL de instruções Update e Delete.</span><span class="sxs-lookup"><span data-stu-id="da45e-184">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="da45e-185">No restante deste tutorial, você adicionará uma propriedade de acompanhamento `rowversion` à entidade Department, criará um controlador e exibições e testará para verificar se tudo está funcionando corretamente.</span><span class="sxs-lookup"><span data-stu-id="da45e-185">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="da45e-186">Adicionar uma propriedade de acompanhamento</span><span class="sxs-lookup"><span data-stu-id="da45e-186">Add a tracking property</span></span>

<span data-ttu-id="da45e-187">Em *Models/Department.cs*, adicione uma propriedade de controle chamada RowVersion:</span><span class="sxs-lookup"><span data-stu-id="da45e-187">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="da45e-188">O atributo `Timestamp` especifica que essa coluna será incluída na cláusula Where de comandos Update e Delete enviados ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="da45e-188">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="da45e-189">O atributo é chamado `Timestamp` porque as versões anteriores do SQL Server usavam um tipo de dados `timestamp` do SQL antes de o `rowversion` do SQL substituí-lo.</span><span class="sxs-lookup"><span data-stu-id="da45e-189">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="da45e-190">O tipo do .NET para `rowversion` é uma matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="da45e-190">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="da45e-191">Se preferir usar a API fluente, use o método `IsConcurrencyToken` (em *Data/SchoolContext.cs*) para especificar a propriedade de acompanhamento, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="da45e-191">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs*) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="da45e-192">Adicionando uma propriedade, você alterou o modelo de banco de dados e, portanto, precisa fazer outra migração.</span><span class="sxs-lookup"><span data-stu-id="da45e-192">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="da45e-193">Salve as alterações e compile o projeto e, em seguida, insira os seguintes comandos na janela Comando:</span><span class="sxs-lookup"><span data-stu-id="da45e-193">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a><span data-ttu-id="da45e-194">Criar um controlador e exibições de Departamentos</span><span class="sxs-lookup"><span data-stu-id="da45e-194">Create Departments controller and views</span></span>

<span data-ttu-id="da45e-195">Gere por scaffolding um controlador e exibições Departamentos, como você fez anteriormente para Alunos, Cursos e Instrutores.</span><span class="sxs-lookup"><span data-stu-id="da45e-195">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![Gerar o departamento por scaffolding](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="da45e-197">No arquivo *DepartmentsController.cs*, altere todas as quatro ocorrências de "FirstMidName" para "FullName", de modo que as listas suspensas do administrador do departamento contenham o nome completo do instrutor em vez de apenas o sobrenome.</span><span class="sxs-lookup"><span data-stu-id="da45e-197">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a><span data-ttu-id="da45e-198">Atualizar a exibição do Índice</span><span class="sxs-lookup"><span data-stu-id="da45e-198">Update Index view</span></span>

<span data-ttu-id="da45e-199">O mecanismo de scaffolding criou uma coluna RowVersion na exibição Índice, mas esse campo não deve ser exibido.</span><span class="sxs-lookup"><span data-stu-id="da45e-199">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="da45e-200">Substitua o código em *Views/Departments/Index.cshtml* pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="da45e-200">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="da45e-201">Isso altera o título "Departamentos", exclui a coluna RowVersion e mostra o nome completo em vez de o nome do administrador.</span><span class="sxs-lookup"><span data-stu-id="da45e-201">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-edit-methods"></a><span data-ttu-id="da45e-202">Atualizar métodos de Edição</span><span class="sxs-lookup"><span data-stu-id="da45e-202">Update Edit methods</span></span>

<span data-ttu-id="da45e-203">Nos métodos HttpGet `Edit` e `Details`, adicione `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="da45e-203">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="da45e-204">No método HttpGet `Edit`, adicione o carregamento adiantado ao Administrador.</span><span class="sxs-lookup"><span data-stu-id="da45e-204">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="da45e-205">Substitua o código existente do método HttpPost `Edit` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="da45e-205">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="da45e-206">O código começa com a tentativa de ler o departamento a ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="da45e-206">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="da45e-207">Se o método `FirstOrDefaultAsync` retornar nulo, isso indicará que o departamento foi excluído por outro usuário.</span><span class="sxs-lookup"><span data-stu-id="da45e-207">If the `FirstOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="da45e-208">Nesse caso, o código usa os valores de formulário postados para criar uma entidade de departamento, de modo que a página Editar possa ser exibida novamente com uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="da45e-208">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="da45e-209">Como alternativa, você não precisará recriar a entidade de departamento se exibir apenas uma mensagem de erro sem exibir novamente os campos de departamento.</span><span class="sxs-lookup"><span data-stu-id="da45e-209">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="da45e-210">A exibição armazena o valor `RowVersion` original em um campo oculto e esse método recebe esse valor no parâmetro `rowVersion`.</span><span class="sxs-lookup"><span data-stu-id="da45e-210">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="da45e-211">Antes de chamar `SaveChanges`, você precisa colocar isso no valor da propriedade `RowVersion` original na coleção `OriginalValues` da entidade.</span><span class="sxs-lookup"><span data-stu-id="da45e-211">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="da45e-212">Em seguida, quando o Entity Framework criar um comando SQL UPDATE, esse comando incluirá uma cláusula WHERE que procura uma linha que tem o valor `RowVersion` original.</span><span class="sxs-lookup"><span data-stu-id="da45e-212">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="da45e-213">Se nenhuma linha for afetada pelo comando UPDATE (nenhuma linha tem o valor `RowVersion` original), o Entity Framework gerará uma exceção `DbUpdateConcurrencyException`.</span><span class="sxs-lookup"><span data-stu-id="da45e-213">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="da45e-214">O código no bloco catch dessa exceção obtém a entidade Department afetada que tem os valores atualizados da propriedade `Entries` no objeto de exceção.</span><span class="sxs-lookup"><span data-stu-id="da45e-214">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="da45e-215">A coleção `Entries` terá apenas um objeto `EntityEntry`.</span><span class="sxs-lookup"><span data-stu-id="da45e-215">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="da45e-216">Use esse objeto para obter os novos valores inseridos pelo usuário e os valores de banco de dados atuais.</span><span class="sxs-lookup"><span data-stu-id="da45e-216">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="da45e-217">O código adiciona uma mensagem de erro personalizada a cada coluna que tem valores de banco de dados diferentes do que o usuário inseriu na página Editar (apenas um campo é mostrado aqui para fins de brevidade).</span><span class="sxs-lookup"><span data-stu-id="da45e-217">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="da45e-218">Por fim, o código define o valor `RowVersion` do `departmentToUpdate` com o novo valor recuperado do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="da45e-218">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="da45e-219">Esse novo valor `RowVersion` será armazenado no campo oculto quando a página Editar for exibida novamente, e na próxima vez que o usuário clicar em **Salvar**, somente os erros de simultaneidade que ocorrem desde a nova exibição da página Editar serão capturados.</span><span class="sxs-lookup"><span data-stu-id="da45e-219">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save**, only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="da45e-220">A instrução `ModelState.Remove` é obrigatória porque `ModelState` tem o valor `RowVersion` antigo.</span><span class="sxs-lookup"><span data-stu-id="da45e-220">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="da45e-221">Na exibição, o valor `ModelState` de um campo tem precedência sobre os valores de propriedade do modelo, quando ambos estão presentes.</span><span class="sxs-lookup"><span data-stu-id="da45e-221">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-edit-view"></a><span data-ttu-id="da45e-222">Atualizar a exibição de Edição</span><span class="sxs-lookup"><span data-stu-id="da45e-222">Update Edit view</span></span>

<span data-ttu-id="da45e-223">Em *Views/Departments/Edit.cshtml*, faça as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="da45e-223">In *Views/Departments/Edit.cshtml*, make the following changes:</span></span>

* <span data-ttu-id="da45e-224">Adicione um campo oculto para salvar o valor da propriedade `RowVersion`, imediatamente após o campo oculto da propriedade `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="da45e-224">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="da45e-225">Adicione uma opção "Selecionar Administrador" à lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="da45e-225">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a><span data-ttu-id="da45e-226">Testar os conflitos de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="da45e-226">Test concurrency conflicts</span></span>

<span data-ttu-id="da45e-227">Execute o aplicativo e acesse a página Índice de Departamentos.</span><span class="sxs-lookup"><span data-stu-id="da45e-227">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="da45e-228">Clique com o botão direito do mouse na hiperlink **Editar** do departamento de inglês e selecione **Abrir em uma nova guia** e, em seguida, clique no hiperlink **Editar** no departamento de inglês.</span><span class="sxs-lookup"><span data-stu-id="da45e-228">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**, then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="da45e-229">As duas guias do navegador agora exibem as mesmas informações.</span><span class="sxs-lookup"><span data-stu-id="da45e-229">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="da45e-230">Altere um campo na primeira guia do navegador e clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="da45e-230">Change a field in the first browser tab and click **Save**.</span></span>

![Página 1 Editar Departamento após a alteração](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="da45e-232">O navegador mostra a página Índice com o valor alterado.</span><span class="sxs-lookup"><span data-stu-id="da45e-232">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="da45e-233">Altere um campo na segunda guia do navegador.</span><span class="sxs-lookup"><span data-stu-id="da45e-233">Change a field in the second browser tab.</span></span>

![Página 2 Editar Departamento após a alteração](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="da45e-235">Clique em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="da45e-235">Click **Save**.</span></span> <span data-ttu-id="da45e-236">Você verá uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="da45e-236">You see an error message:</span></span>

![Mensagem de erro da página Editar Departamento](concurrency/_static/edit-error.png)

<span data-ttu-id="da45e-238">Clique em **Salvar** novamente.</span><span class="sxs-lookup"><span data-stu-id="da45e-238">Click **Save** again.</span></span> <span data-ttu-id="da45e-239">O valor inserido na segunda guia do navegador foi salvo.</span><span class="sxs-lookup"><span data-stu-id="da45e-239">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="da45e-240">Você verá os valores salvos quando a página Índice for exibida.</span><span class="sxs-lookup"><span data-stu-id="da45e-240">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="da45e-241">Atualizar a página Excluir</span><span class="sxs-lookup"><span data-stu-id="da45e-241">Update the Delete page</span></span>

<span data-ttu-id="da45e-242">Para a página Excluir, o Entity Framework detecta conflitos de simultaneidade causados pela edição por outra pessoa do departamento de maneira semelhante.</span><span class="sxs-lookup"><span data-stu-id="da45e-242">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="da45e-243">Quando o método HttpGet `Delete` exibe a exibição de confirmação, a exibição inclui o valor `RowVersion` original em um campo oculto.</span><span class="sxs-lookup"><span data-stu-id="da45e-243">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="da45e-244">Em seguida, esse valor estará disponível para o método HttpPost `Delete` chamado quando o usuário confirmar a exclusão.</span><span class="sxs-lookup"><span data-stu-id="da45e-244">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="da45e-245">Quando o Entity Framework cria o comando SQL DELETE, ele inclui uma cláusula WHERE com o valor `RowVersion` original.</span><span class="sxs-lookup"><span data-stu-id="da45e-245">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="da45e-246">Se o comando não resultar em nenhuma linha afetada (o que significa que a linha foi alterada após a exibição da página Confirmação de exclusão), uma exceção de simultaneidade será gerada e o método HttpGet `Delete` será chamado com um sinalizador de erro definido como verdadeiro para exibir novamente a página de confirmação com uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="da45e-246">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="da45e-247">Também é possível que nenhuma linha tenha sido afetada porque a linha foi excluída por outro usuário; portanto, nesse caso, nenhuma mensagem de erro é exibida.</span><span class="sxs-lookup"><span data-stu-id="da45e-247">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="da45e-248">Atualizar os métodos Excluir no controlador Departamentos</span><span class="sxs-lookup"><span data-stu-id="da45e-248">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="da45e-249">Em *DepartmentsController.cs*, substitua o método HttpGet `Delete` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="da45e-249">In *DepartmentsController.cs*, replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="da45e-250">O método aceita um parâmetro opcional que indica se a página está sendo exibida novamente após um erro de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="da45e-250">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="da45e-251">Se esse sinalizador é verdadeiro e o departamento especificado não existe mais, isso indica que ele foi excluído por outro usuário.</span><span class="sxs-lookup"><span data-stu-id="da45e-251">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="da45e-252">Nesse caso, o código redireciona para a página Índice.</span><span class="sxs-lookup"><span data-stu-id="da45e-252">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="da45e-253">Se esse sinalizador é verdadeiro e o departamento existe, isso indica que ele foi alterado por outro usuário.</span><span class="sxs-lookup"><span data-stu-id="da45e-253">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="da45e-254">Nesse caso, o código envia uma mensagem de erro para a exibição usando `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="da45e-254">In that case, the code sends an error message to the view using `ViewData`.</span></span>

<span data-ttu-id="da45e-255">Substitua o código no método HttpPost `Delete` (chamado `DeleteConfirmed`) pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="da45e-255">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="da45e-256">No código gerado por scaffolding que acabou de ser substituído, esse método aceitou apenas uma ID de registro:</span><span class="sxs-lookup"><span data-stu-id="da45e-256">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="da45e-257">Você alterou esse parâmetro para uma instância da entidade Departamento criada pelo associador de modelos.</span><span class="sxs-lookup"><span data-stu-id="da45e-257">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="da45e-258">Isso concede o acesso ao EF ao valor da propriedade RowVersion, além da chave de registro.</span><span class="sxs-lookup"><span data-stu-id="da45e-258">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="da45e-259">Você também alterou o nome do método de ação de `DeleteConfirmed` para `Delete`.</span><span class="sxs-lookup"><span data-stu-id="da45e-259">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="da45e-260">O código gerado por scaffolding usou o nome `DeleteConfirmed` para fornecer ao método HttpPost uma assinatura exclusiva.</span><span class="sxs-lookup"><span data-stu-id="da45e-260">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="da45e-261">(O CLR exige que métodos sobrecarregados tenham diferentes parâmetros de método.) Agora que as assinaturas são exclusivas, você pode continuar com a convenção MVC e usar o mesmo nome para os métodos de exclusão HttpPost e HttpGet.</span><span class="sxs-lookup"><span data-stu-id="da45e-261">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="da45e-262">Se o departamento já foi excluído, o método `AnyAsync` retorna falso e o aplicativo apenas volta para o método de Índice.</span><span class="sxs-lookup"><span data-stu-id="da45e-262">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="da45e-263">Se um erro de simultaneidade é capturado, o código exibe novamente a página Confirmação de exclusão e fornece um sinalizador que indica que ela deve exibir uma mensagem de erro de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="da45e-263">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="da45e-264">Atualizar a exibição Excluir</span><span class="sxs-lookup"><span data-stu-id="da45e-264">Update the Delete view</span></span>

<span data-ttu-id="da45e-265">Em *Views/Departments/Delete.cshtml*, substitua o código gerado por scaffolding pelo seguinte código que adiciona um campo de mensagem de erro e campos ocultos às propriedades DepartmentID e RowVersion.</span><span class="sxs-lookup"><span data-stu-id="da45e-265">In *Views/Departments/Delete.cshtml*, replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="da45e-266">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="da45e-266">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="da45e-267">Isso faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="da45e-267">This makes the following changes:</span></span>

* <span data-ttu-id="da45e-268">Adiciona uma mensagem de erro entre os cabeçalhos `h2` e `h3`.</span><span class="sxs-lookup"><span data-stu-id="da45e-268">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="da45e-269">Substitua FirstMidName por FullName no campo **Administrador**.</span><span class="sxs-lookup"><span data-stu-id="da45e-269">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="da45e-270">Remove o campo RowVersion.</span><span class="sxs-lookup"><span data-stu-id="da45e-270">Removes the RowVersion field.</span></span>

* <span data-ttu-id="da45e-271">Adiciona um campo oculto à propriedade `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="da45e-271">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="da45e-272">Execute o aplicativo e acesse a página Índice de Departamentos.</span><span class="sxs-lookup"><span data-stu-id="da45e-272">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="da45e-273">Clique com o botão direito do mouse na hiperlink **Excluir** do departamento de inglês e selecione **Abrir em uma nova guia** e, em seguida, na primeira guia, clique no hiperlink **Editar** no departamento de inglês.</span><span class="sxs-lookup"><span data-stu-id="da45e-273">Right-click the **Delete** hyperlink for the English department and select **Open in new tab**, then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="da45e-274">Na primeira janela, altere um dos valores e clique em **Salvar**:</span><span class="sxs-lookup"><span data-stu-id="da45e-274">In the first window, change one of the values, and click **Save**:</span></span>

![Página Editar Departamento após a alteração antes da exclusão](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="da45e-276">Na segunda guia, clique em **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="da45e-276">In the second tab, click **Delete**.</span></span> <span data-ttu-id="da45e-277">Você verá a mensagem de erro de simultaneidade e os valores de Departamento serão atualizados com o que está atualmente no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="da45e-277">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![Página Confirmação de Exclusão de Departamento com erro de simultaneidade](concurrency/_static/delete-error.png)

<span data-ttu-id="da45e-279">Se você clicar em **Excluir** novamente, será redirecionado para a página Índice, que mostra que o departamento foi excluído.</span><span class="sxs-lookup"><span data-stu-id="da45e-279">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="da45e-280">Exibições Atualizar Detalhes e Criar</span><span class="sxs-lookup"><span data-stu-id="da45e-280">Update Details and Create views</span></span>

<span data-ttu-id="da45e-281">Opcionalmente, você pode limpar o código gerado por scaffolding nas exibições Detalhes e Criar.</span><span class="sxs-lookup"><span data-stu-id="da45e-281">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="da45e-282">Substitua o código em *Views/Departments/Details.cshtml* para excluir a coluna RowVersion e mostrar o nome completo do Administrador.</span><span class="sxs-lookup"><span data-stu-id="da45e-282">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="da45e-283">Substitua o código em *Views/Departments/Create.cshtml* para adicionar uma opção Selecionar à lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="da45e-283">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a><span data-ttu-id="da45e-284">Obter o código</span><span class="sxs-lookup"><span data-stu-id="da45e-284">Get the code</span></span>

[<span data-ttu-id="da45e-285">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="da45e-285">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="da45e-286">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="da45e-286">Additional resources</span></span>

 <span data-ttu-id="da45e-287">Para obter mais informações sobre como lidar com a simultaneidade no EF Core, consulte [Conflitos de simultaneidade](/ef/core/saving/concurrency).</span><span class="sxs-lookup"><span data-stu-id="da45e-287">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](/ef/core/saving/concurrency).</span></span>

## <a name="next-steps"></a><span data-ttu-id="da45e-288">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="da45e-288">Next steps</span></span>

<span data-ttu-id="da45e-289">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="da45e-289">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="da45e-290">Aprendeu sobre conflitos de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="da45e-290">Learned about concurrency conflicts</span></span>
> * <span data-ttu-id="da45e-291">Adicionou uma propriedade de acompanhamento</span><span class="sxs-lookup"><span data-stu-id="da45e-291">Added a tracking property</span></span>
> * <span data-ttu-id="da45e-292">Criou um controlador e exibições de Departamentos</span><span class="sxs-lookup"><span data-stu-id="da45e-292">Created Departments controller and views</span></span>
> * <span data-ttu-id="da45e-293">Atualizou a exibição do Índice</span><span class="sxs-lookup"><span data-stu-id="da45e-293">Updated Index view</span></span>
> * <span data-ttu-id="da45e-294">Atualizou métodos de Edição</span><span class="sxs-lookup"><span data-stu-id="da45e-294">Updated Edit methods</span></span>
> * <span data-ttu-id="da45e-295">Atualizou a exibição de Edição</span><span class="sxs-lookup"><span data-stu-id="da45e-295">Updated Edit view</span></span>
> * <span data-ttu-id="da45e-296">Testou conflitos de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="da45e-296">Tested concurrency conflicts</span></span>
> * <span data-ttu-id="da45e-297">Atualizou a página Excluir</span><span class="sxs-lookup"><span data-stu-id="da45e-297">Updated the Delete page</span></span>
> * <span data-ttu-id="da45e-298">Atualizou as exibições de Detalhes e Criar</span><span class="sxs-lookup"><span data-stu-id="da45e-298">Updated Details and Create views</span></span>

<span data-ttu-id="da45e-299">Vá para o próximo tutorial para aprender a implementar a herança de tabela por hierarquia para as entidades Instructor e Student.</span><span class="sxs-lookup"><span data-stu-id="da45e-299">Advance to the next tutorial to learn how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="da45e-300">Avançar: implementar a herança de tabela por hierarquia</span><span class="sxs-lookup"><span data-stu-id="da45e-300">Next: Implement table-per-hierarchy inheritance</span></span>](inheritance.md)
