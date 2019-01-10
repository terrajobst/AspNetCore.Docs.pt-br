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
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a>Páginas Razor com o EF Core no ASP.NET Core – Simultaneidade – 8 de 8

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) e [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

Este tutorial mostra como lidar com conflitos quando os mesmos usuários atualizam uma entidade simultaneamente. Caso tenha problemas que não consiga resolver, [baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Instruções de download](xref:index#how-to-download-a-sample).

## <a name="concurrency-conflicts"></a>Conflitos de simultaneidade

Um conflito de simultaneidade ocorre quando:

* Um usuário navega para a página de edição de uma entidade.
* Outro usuário atualiza a mesma entidade antes que a primeira alteração do usuário seja gravada no BD.

Se a detecção de simultaneidade não estiver habilitada, quando ocorrerem atualizações simultâneas:

* A última atualização vencerá. Ou seja, os últimos valores de atualização serão salvos no BD.
* A primeira das atualizações atuais será perdida.

### <a name="optimistic-concurrency"></a>Simultaneidade otimista

A simultaneidade otimista permite que conflitos de simultaneidade ocorram e, em seguida, responde adequadamente quando ocorrem. Por exemplo, Alice visita a página Editar Departamento e altera o orçamento para o departamento de inglês de US$ 350.000,00 para US$ 0,00.

![Alterando o orçamento para 0](concurrency/_static/change-budget.png)

Antes que Alice clique em **Salvar**, Julio visita a mesma página e altera o campo Data de Início de 1/9/2007 para 1/9/2013.

![Alterando a data de início para 2013](concurrency/_static/change-date.png)

Alice clica em **Salvar** primeiro e vê a alteração quando o navegador exibe a página Índice.

![Orçamento alterado para zero](concurrency/_static/budget-zero.png)

Julio clica em **Salvar** em uma página Editar que ainda mostra um orçamento de US$ 350.000,00. O que acontece em seguida é determinado pela forma como você lida com conflitos de simultaneidade.

A simultaneidade otimista inclui as seguintes opções:

* Controle qual propriedade um usuário modificou e atualize apenas as colunas correspondentes no BD.

  No cenário, não haverá perda de dados. Propriedades diferentes foram atualizadas pelos dois usuários. Na próxima vez que alguém navegar no departamento de inglês, verá as alterações de Alice e Julio. Esse método de atualização pode reduzir o número de conflitos que podem resultar em perda de dados. Essa abordagem:
 
  * Não poderá evitar a perda de dados se forem feitas alterações concorrentes na mesma propriedade.
  * Geralmente, não é prática em um aplicativo Web. Ela exige um estado de manutenção significativo para controlar todos os valores buscados e novos valores. Manter grandes quantidades de estado pode afetar o desempenho do aplicativo.
  * Pode aumentar a complexidade do aplicativo comparado à detecção de simultaneidade em uma entidade.

* Você não pode deixar a alteração de Julio substituir a alteração de Alice.

  Na próxima vez que alguém navegar pelo departamento de inglês, verá 1/9/2013 e o valor de US$ 350.000,00 buscado. Essa abordagem é chamada de um cenário *O cliente vence* ou *O último vence*. (Todos os valores do cliente têm precedência sobre o conteúdo do armazenamento de dados.) Se você não fizer nenhuma codificação para a manipulação de simultaneidade, o cenário O cliente vence ocorrerá automaticamente.

* Você pode impedir que as alterações de Julio sejam atualizadas no BD. Normalmente, o aplicativo:

  * Exibe uma mensagem de erro.
  * Mostra o estado atual dos dados.
  * Permite ao usuário aplicar as alterações novamente.

  Isso é chamado de um cenário *O armazenamento vence*. (Os valores do armazenamento de dados têm precedência sobre os valores enviados pelo cliente.) Você implementará o cenário O armazenamento vence neste tutorial. Esse método garante que nenhuma alteração é substituída sem que um usuário seja alertado.

## <a name="handling-concurrency"></a>Tratamento de simultaneidade 

Quando uma propriedade é configurada como um [token de simultaneidade](/ef/core/modeling/concurrency):

* O EF Core verifica se a propriedade não foi modificada depois que foi buscada. A verificação ocorre quando [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) ou [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) é chamado.
* Se a propriedade tiver sido alterada depois que ela foi buscada, uma [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) será gerada. 

O BD e o modelo de dados precisam ser configurados para dar suporte à geração de `DbUpdateConcurrencyException`.

### <a name="detecting-concurrency-conflicts-on-a-property"></a>Detectando conflitos de simultaneidade em uma propriedade

Os conflitos de simultaneidade podem ser detectados no nível do propriedade com o atributo [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0). O atributo pode ser aplicado a várias propriedades no modelo. Para obter mais informações, consulte [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).

O atributo `[ConcurrencyCheck]` não é usado neste tutorial.

### <a name="detecting-concurrency-conflicts-on-a-row"></a>Detectando conflitos de simultaneidade em uma linha

Para detectar conflitos de simultaneidade, uma coluna de controle de [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) é adicionada ao modelo.  `rowversion`:

* É específico ao SQL Server. Outros bancos de dados podem não fornecer um recurso semelhante.
* É usado para determinar se uma entidade não foi alterada desde que foi buscada no BD. 

O BD gera um número `rowversion` sequencial que é incrementado sempre que a linha é atualizada. Em um comando `Update` ou `Delete`, a cláusula `Where` inclui o valor buscado de `rowversion`. Se a linha que está sendo atualizada foi alterada:

 * `rowversion` não corresponde ao valor buscado.
 * Os comandos `Update` ou `Delete` não encontram uma linha porque a cláusula `Where` inclui a `rowversion` buscada.
 * Uma `DbUpdateConcurrencyException` é gerada.

No EF Core, quando nenhuma linha é atualizada por um comando `Update` ou `Delete`, uma exceção de simultaneidade é gerada.

### <a name="add-a-tracking-property-to-the-department-entity"></a>Adicionar uma propriedade de controle à entidade Department

Em *Models/Department.cs*, adicione uma propriedade de controle chamada RowVersion:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

O atributo [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) especifica que essa coluna é incluída na cláusula `Where` dos comandos `Update` e `Delete`. O atributo é chamado `Timestamp` porque as versões anteriores do SQL Server usavam um tipo de dados `timestamp` do SQL antes de o tipo `rowversion` SQL substituí-lo.

A API fluente também pode especificar a propriedade de controle:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

O seguinte código mostra uma parte do T-SQL gerado pelo EF Core quando o nome do Departamento é atualizado:

[!code-sql[](intro/samples/sql.txt?highlight=2-3)]

O código anterior realçado mostra a cláusula `WHERE` que contém `RowVersion`. Se o BD `RowVersion` não for igual ao parâmetro `RowVersion` (`@p2`), nenhuma linha será atualizada.

O seguinte código realçado mostra o T-SQL que verifica exatamente se uma linha foi atualizada:

[!code-sql[](intro/samples/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) retorna o número de linhas afetadas pela última instrução. Quando nenhuma linha é atualizada, o EF Core gera uma `DbUpdateConcurrencyException`.

Veja o T-SQL gerado pelo EF Core na janela de Saída do Visual Studio.

### <a name="update-the-db"></a>Atualizar o BD

A adição da propriedade `RowVersion` altera o modelo de BD, o que exige uma migração.

Compile o projeto. Insira o seguinte em uma janela Comando:

```console
dotnet ef migrations add RowVersion
dotnet ef database update
```

Os comandos anteriores:

* Adicionam o arquivo de migração *Migrations/{time stamp}_RowVersion.cs*.
* Atualizam o arquivo *Migrations/SchoolContextModelSnapshot.cs*. A atualização adiciona o seguinte código realçado ao método `BuildModel`:

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot2.cs?name=snippet&highlight=14-16)]

* Executam migrações para atualizar o BD.

<a name="scaffold"></a>
## <a name="scaffold-the-departments-model"></a>Gerar o modelo Departamentos por scaffolding

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Siga as instruções em [Gere um modelo de aluno por scaffold](xref:data/ef-rp/intro#scaffold-the-student-model) e use `Department` para a classe de modelo.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

 Execute o seguinte comando:

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

------

O comando anterior gera o modelo `Department` por scaffolding. Abra o projeto no Visual Studio.

Compile o projeto.

### <a name="update-the-departments-index-page"></a>Atualizar a página Índice de Departamentos

O mecanismo de scaffolding criou uma coluna `RowVersion` para a página Índice, mas esse campo não deve ser exibido. Neste tutorial, o último byte da `RowVersion` é exibido para ajudar a entender a simultaneidade. O último byte não tem garantia de ser exclusivo. Um aplicativo real não exibe `RowVersion` ou o último byte de `RowVersion`.

Atualize a página Índice:

* Substitua Índice por Departamentos.
* Substitua a marcação que contém `RowVersion` pelo último byte de `RowVersion`.
* Substitua FirstMidName por FullName.

A seguinte marcação mostra a página atualizada:

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>Atualizar o modelo da página Editar

Atualize *pages\departments\edit.cshtml.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

Para detectar um problema de simultaneidade, o [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) é atualizado com o valor `rowVersion` da entidade que foi buscada. O EF Core gera um comando SQL UPDATE com uma cláusula WHERE que contém o valor `RowVersion` original. Se nenhuma linha for afetada pelo comando UPDATE (nenhuma linha tem o valor `RowVersion` original), uma exceção `DbUpdateConcurrencyException` será gerada.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

No código anterior, `Department.RowVersion` é o valor quando a entidade foi buscada. `OriginalValue` é o valor no BD quando `FirstOrDefaultAsync` foi chamado nesse método.

O seguinte código obtém os valores de cliente (os valores postados nesse método) e os valores do BD:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

O seguinte código adiciona uma mensagem de erro personalizada a cada coluna que tem valores de BD diferentes daqueles que foram postados em `OnPostAsync`:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

O código realçado a seguir define o valor `RowVersion` com o novo valor recuperado do BD. Na próxima vez que o usuário clicar em **Salvar**, somente os erros de simultaneidade que ocorrerem desde a última exibição da página Editar serão capturados.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

A instrução `ModelState.Remove` é obrigatória porque `ModelState` tem o valor `RowVersion` antigo. Na Página do Razor, o valor `ModelState` de um campo tem precedência sobre os valores de propriedade do modelo, quando ambos estão presentes.

## <a name="update-the-edit-page"></a>Atualizar a página Editar

Atualize *Pages/Departments/Edit.cshtml* com a seguinte marcação:

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

A marcação anterior:

* Atualiza a diretiva `page` de `@page` para `@page "{id:int}"`.
* Adiciona uma versão de linha oculta. `RowVersion` deve ser adicionado para que o postback associe o valor.
* Exibe o último byte de `RowVersion` para fins de depuração.
* Substitui `ViewData` pelo `InstructorNameSL` fortemente tipado.

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>Testar conflitos de simultaneidade com a página Editar

Abra duas instâncias de navegadores de Editar no departamento de inglês:

* Execute o aplicativo e selecione Departamentos.
* Clique com o botão direito do mouse no hiperlink **Editar** do departamento de inglês e selecione **Abrir em uma nova guia**.
* Na primeira guia, clique no hiperlink **Editar** do departamento de inglês.

As duas guias do navegador exibem as mesmas informações.

Altere o nome na primeira guia do navegador e clique em **Salvar**.

![Página 1 Editar Departamento após a alteração](concurrency/_static/edit-after-change-1.png)

O navegador mostra a página de Índice com o valor alterado e o indicador de rowVersion atualizado. Observe o indicador de rowVersion atualizado: ele é exibido no segundo postback na outra guia.

Altere outro campo na segunda guia do navegador.

![Página 2 Editar Departamento após a alteração](concurrency/_static/edit-after-change-2.png)

Clique em **Salvar**. Você verá mensagens de erro em todos os campos que não correspondem aos valores do BD:

![Mensagem de erro da página Editar Departamento](concurrency/_static/edit-error.png)

Essa janela do navegador não pretendia alterar o campo Name. Copie e cole o valor atual (Languages) para o campo Name. Saída da guia. A validação do lado do cliente remove a mensagem de erro.

![Mensagem de erro da página Editar Departamento](concurrency/_static/cv.png)

Clique em **Salvar** novamente. O valor inserido na segunda guia do navegador foi salvo. Você verá os valores salvos na página Índice.

## <a name="update-the-delete-page"></a>Atualizar a página Excluir

Atualize o modelo da página Excluir com o seguinte código:

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

A página Excluir detectou conflitos de simultaneidade quando a entidade foi alterada depois de ser buscada. `Department.RowVersion` é a versão de linha quando a entidade foi buscada. Quando o EF Core cria o comando SQL DELETE, ele inclui uma cláusula WHERE com `RowVersion`. Se o comando SQL DELETE não resultar em nenhuma linha afetada:

* A `RowVersion` no comando SQL DELETE não corresponderá a `RowVersion` no BD.
* Uma exceção DbUpdateConcurrencyException é gerada.
* `OnGetAsync` é chamado com o `concurrencyError`.

### <a name="update-the-delete-page"></a>Atualizar a página Excluir

Atualize *Pages/Departments/Delete.cshtml* com o seguinte código:

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]


A marcação anterior faz as seguintes alterações:

* Atualiza a diretiva `page` de `@page` para `@page "{id:int}"`.
* Adiciona uma mensagem de erro.
* Substitua FirstMidName por FullName no campo **Administrador**.
* Altere `RowVersion` para exibir o último byte.
* Adiciona uma versão de linha oculta. `RowVersion` deve ser adicionado para que o postback associe o valor.

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>Testar conflitos de simultaneidade com a página Excluir

Crie um departamento de teste.

Abra duas instâncias dos navegadores de Excluir no departamento de teste:

* Execute o aplicativo e selecione Departamentos.
* Clique com o botão direito do mouse no hiperlink **Excluir** do departamento de teste e selecione **Abrir em uma nova guia**.
* Clique no hiperlink **Editar** do departamento de teste.

As duas guias do navegador exibem as mesmas informações.

Altere o orçamento na primeira guia do navegador e clique em **Salvar**.

O navegador mostra a página de Índice com o valor alterado e o indicador de rowVersion atualizado. Observe o indicador de rowVersion atualizado: ele é exibido no segundo postback na outra guia.

Exclua o departamento de teste na segunda guia. Um erro de simultaneidade é exibido com os valores atuais do BD. Clicar em **Excluir** exclui a entidade, a menos que `RowVersion` tenha sido atualizada e o departamento tenha sido excluído.

Consulte [Herança](xref:data/ef-mvc/inheritance) para saber como herdar um modelo de dados.

### <a name="additional-resources"></a>Recursos adicionais

* [Tokens de simultaneidade no EF Core](/ef/core/modeling/concurrency)
* [Lidar com a simultaneidade no EF Core](/ef/core/saving/concurrency)

> [!div class="step-by-step"]
> [Anterior](xref:data/ef-rp/update-related-data)
