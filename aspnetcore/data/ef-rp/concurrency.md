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
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a>Páginas Razor com o EF Core no ASP.NET Core – Simultaneidade – 8 de 8

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) e [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Este tutorial mostra como lidar com conflitos quando os mesmos usuários atualizam uma entidade simultaneamente.

## <a name="concurrency-conflicts"></a>Conflitos de simultaneidade

Um conflito de simultaneidade ocorre quando:

* Um usuário navega para a página de edição de uma entidade.
* Outro usuário atualiza a mesma entidade antes que a primeira alteração do usuário seja gravada no banco de dados.

Se a detecção de simultaneidade não estiver habilitada, quem atualizar o banco de dados por último substituirá as alterações do outro usuário. Se esse risco for aceitável, o custo de programação para simultaneidade poderá superar o benefício.

### <a name="pessimistic-concurrency-locking"></a>Simultaneidade pessimista (bloqueio)

Uma maneira de evitar conflitos de simultaneidade é usar bloqueios de banco de dados. Isso é chamado de simultaneidade pessimista. Antes que o aplicativo leia uma linha de banco de dados que ele pretende atualizar, ele solicita um bloqueio. Depois que uma linha é bloqueada para acesso de atualização, nenhum outro usuário tem permissão para bloquear a linha até que o primeiro bloqueio seja liberado.

O gerenciamento de bloqueios traz desvantagens. Pode ser complexo de programar e causar problemas de desempenho conforme o número de usuários aumenta. O Entity Framework Core não fornece nenhum suporte interno para ele, e este tutorial não mostra como implementá-lo.

### <a name="optimistic-concurrency"></a>Simultaneidade otimista

A simultaneidade otimista permite que conflitos de simultaneidade ocorram e, em seguida, responde adequadamente quando ocorrem. Por exemplo, Alice visita a página Editar Departamento e altera o orçamento para o departamento de inglês de US$ 350.000,00 para US$ 0,00.

![Alterando o orçamento para 0](concurrency/_static/change-budget30.png)

Antes que Alice clique em **Salvar**, Julio visita a mesma página e altera o campo Data de Início de 1/9/2007 para 1/9/2013.

![Alterando a data de início para 2013](concurrency/_static/change-date30.png)

Jane clica em **Salvar** primeiro e vê que sua alteração entrará em vigor, já que o navegador exibe a página de Índice com zero como o valor do Orçamento.

Julio clica em **Salvar** em uma página Editar que ainda mostra um orçamento de US$ 350.000,00. O que acontece em seguida é determinado pela forma como você lida com conflitos de simultaneidade:

* Controle qual propriedade um usuário modificou e atualize apenas as colunas correspondentes no banco de dados.

  No cenário, não haverá perda de dados. Propriedades diferentes foram atualizadas pelos dois usuários. Na próxima vez que alguém navegar no departamento de inglês, verá as alterações de Alice e Julio. Esse método de atualização pode reduzir o número de conflitos que podem resultar em perda de dados. Essa abordagem tem algumas desvantagens:
 
  * Não poderá evitar a perda de dados se forem feitas alterações concorrentes na mesma propriedade.
  * Geralmente, não é prática em um aplicativo Web. Ela exige um estado de manutenção significativo para controlar todos os valores buscados e novos valores. Manter grandes quantidades de estado pode afetar o desempenho do aplicativo.
  * Pode aumentar a complexidade do aplicativo comparado à detecção de simultaneidade em uma entidade.

* Você não pode deixar a alteração de Julio substituir a alteração de Alice.

  Na próxima vez que alguém navegar pelo departamento de inglês, verá 1/9/2013 e o valor de US$ 350.000,00 buscado. Essa abordagem é chamada de um cenário *O cliente vence* ou *O último vence*. (Todos os valores do cliente têm precedência sobre o conteúdo do armazenamento de dados.) Se você não fizer nenhuma codificação para a manipulação de simultaneidade, o cenário O cliente vence ocorrerá automaticamente.

* Você pode impedir que as alterações de Julio sejam atualizadas no banco de dados. Normalmente, o aplicativo:

  * Exibe uma mensagem de erro.
  * Mostra o estado atual dos dados.
  * Permite ao usuário aplicar as alterações novamente.

  Isso é chamado de um cenário *O armazenamento vence*. (Os valores do armazenamento de dados têm precedência sobre os valores enviados pelo cliente.) Você implementará o cenário O armazenamento vence neste tutorial. Esse método garante que nenhuma alteração é substituída sem que um usuário seja alertado.

## <a name="conflict-detection-in-ef-core"></a>Detecção de conflitos no EF Core

O EF Core gera exceções `DbConcurrencyException` quando detecta conflitos. O modelo de dados deve ser configurado para habilitar a detecção de conflitos. As opções para habilitar a detecção de conflitos incluem as seguintes:

* Configurar o EF Core para incluir os valores originais das colunas configuradas como [tokens de simultaneidade](/ef/core/modeling/concurrency) na cláusula Where dos comandos Update e Delete.

  Quando `SaveChanges` é chamado, a cláusula WHERE procura os valores originais de quaisquer propriedades anotadas com o atributo [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute). As declarações de atualização não encontrarão uma linha a ser atualizada se qualquer uma das propriedades do token de simultaneidade for alteradas desde a primeira leitura da linha. O EF Core interpreta isso como um conflito de simultaneidade. Para tabelas de banco de dados que têm muitas colunas, essa abordagem pode resultar em cláusulas Where muito grandes e pode exigir grandes quantidades de estado. Portanto, essa abordagem geralmente não é recomendada e não é o método usado neste tutorial.

* Na tabela de banco de dados, inclua uma coluna de acompanhamento que pode ser usada para determinar quando uma linha é alterada.

  Em um banco de dados do SQL Server, o tipo de dados da coluna de acompanhamento é `rowversion`. O valor `rowversion` é um número sequencial que é incrementado sempre que a linha é atualizada. Em um comando Update ou Delete, a cláusula Where inclui o valor original da coluna de acompanhamento (o número de versão da linha original). Se a linha que está sendo atualizada tiver sido alterada por outro usuário, o valor na coluna `rowversion` será diferente do valor original. Nesse caso, a instrução Update ou Delete não pode localizar a linha a ser atualizada devido à cláusula Where. O EF Core gera uma exceção de simultaneidade quando nenhuma linha é afetada por um comando Update ou Delete.

## <a name="add-a-tracking-property"></a>Adicionar uma propriedade de acompanhamento

Em *Models/Department.cs*, adicione uma propriedade de controle chamada RowVersion:

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

O atributo [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) é o que identifica a coluna como uma coluna de acompanhamento de simultaneidade. A API fluente é uma maneira alternativa de especificar a propriedade de acompanhamento:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Para um banco de dados do SQL Server, o atributo `[Timestamp]` em uma propriedade de entidade definida como matriz de bytes:

* Faz com que a coluna seja incluída nas cláusulas DELETE e UPDATE WHERE.
* Define o tipo de coluna no banco de dados como [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).

O banco de dados gera um número de versão de linha sequencial que é incrementado sempre que a linha é atualizada. Em um comando `Update` ou `Delete`, a cláusula `Where` inclui o valor da versão de linha buscado. Se a linha que está sendo atualizada foi alterada desde que foi buscada:

* O valor da versão da linha atual não corresponde ao valor obtido.
* O comando `Update` ou `Delete` não localiza uma linha porque a cláusula `Where` procura o valor da versão da linha buscado.
* Uma `DbUpdateConcurrencyException` é gerada.

O seguinte código mostra uma parte do T-SQL gerado pelo EF Core quando o nome do Departamento é atualizado:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

O código anterior realçado mostra a cláusula `WHERE` que contém `RowVersion`. Se o banco de dados `RowVersion` não for igual ao parâmetro `RowVersion` (`@p2`), nenhuma linha será atualizada.

O seguinte código realçado mostra o T-SQL que verifica exatamente se uma linha foi atualizada:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) retorna o número de linhas afetadas pela última instrução. Se nenhuma linha for atualizada, o EF Core gerará uma `DbUpdateConcurrencyException`.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Para um banco de dados SQLite, o atributo `[Timestamp]` em uma propriedade de entidade definida como matriz de bytes:

* Faz com que a coluna seja incluída nas cláusulas DELETE e UPDATE WHERE.
* Mapeia para um tipo de coluna de BLOB.

Os gatilhos de banco de dados atualizam a coluna RowVersion com uma nova matriz de bytes aleatórios sempre que uma linha é atualizada. Em um comando `Update` ou `Delete`, a cláusula `Where` inclui o valor buscado da coluna RowVersion. Se a linha que está sendo atualizada foi alterada desde que foi buscada:

* O valor da versão da linha atual não corresponde ao valor obtido.
* O comando `Update` ou `Delete` não localiza uma linha porque a cláusula `Where` procura o valor da versão da linha original.
* Uma `DbUpdateConcurrencyException` é gerada.

---

### <a name="update-the-database"></a>Atualizar o banco de dados

Adicionar a propriedade `RowVersion` muda o modelo de dados, o que exige uma migração.

Compile o projeto. 

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Execute o seguinte comando no PMC:

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Em um terminal, execute o seguinte comando:

  ```console
  dotnet ef migrations add RowVersion
  ```

---

Esse comando:

* Cria o arquivo de migração *Migrations/{time stamp}_RowVersion.cs*.
* Atualizam o arquivo *Migrations/SchoolContextModelSnapshot.cs*. A atualização adiciona o seguinte código realçado ao método `BuildModel`:

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Execute o seguinte comando no PMC:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra o arquivo `Migrations/<timestamp>_RowVersion.cs` e adicione o código realçado:

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  O código anterior:

  * Atualiza as linhas existentes com valores de blob aleatórios.
  * Adiciona gatilhos de banco de dados que definem a coluna RowVersion como um valor de blob aleatório sempre que uma linha é atualizada.

* Em um terminal, execute o seguinte comando:

  ```console
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a>Aplicar scaffold a páginas de Departamento

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Siga as instruções em [páginas do aluno do Scaffold](xref:data/ef-rp/intro#scaffold-student-pages) com as seguintes exceções:

* Crie uma pasta *Pages/Departments*.  
* Use `Department` para a classe de modelo.
  * Use a classe de contexto existente, em vez de criar uma nova.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Crie uma pasta *Pages/Departments*.

* Execute o comando a seguir para aplicar scaffold das páginas do Departamento.

  **No Windows:**

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  **No Linux ou macOS:**

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

Compile o projeto.

## <a name="update-the-index-page"></a>Atualize a página Índice

A ferramenta de scaffolding criou uma coluna `RowVersion` para a página de índice, mas esse campo não seria exibido em um aplicativo de produção. Neste tutorial, o último byte do `RowVersion` é exibido para ajudar a mostrar como funciona a manipulação de simultaneidade. O último byte não tem garantia de ser exclusivo em si.

Atualize a página *Pages\Departments\Index.cshtml*:

* Substitua Índice por Departamentos.
* Altere o código que contém `RowVersion` para mostrar apenas o último byte da matriz de bytes.
* Substitua FirstMidName por FullName.

O código a seguir mostra a página atualizada:

[!code-html[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a>Atualizar o modelo da página Editar

Atualize *Pages\Departments\Edit.cshtml.cs* com o código a seguir:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

O [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) é atualizado com o valor `rowVersion` da entidade quando ele foi buscado no método `OnGet`. O EF Core gera um comando SQL UPDATE com uma cláusula WHERE que contém o valor `RowVersion` original. Se nenhuma linha for afetada pelo comando UPDATE (nenhuma linha tem o valor `RowVersion` original), uma exceção `DbUpdateConcurrencyException` será gerada.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

No código realçado anterior:

* O valor em `Department.RowVersion` é o que estava na entidade quando ela foi buscada originalmente na solicitação Get para a página Editar. O valor é fornecido ao método `OnPost` por um campo oculto na página do Razor que exibe a entidade a ser editada. O valor do campo oculto é copiado para `Department.RowVersion` pelo associador de modelos.
* `OriginalValue` é o que o EF Core usará na cláusula Where. Antes que a linha de código realçada seja executada, o `OriginalValue` tem o valor que estava no banco de dados quando `FirstOrDefaultAsync` foi chamado nesse método, que pode ser diferente do que foi exibido na página de edição.
* O código realçado garante que o EF Core use o valor `RowVersion` original da entidade `Department` exibida na cláusula Where da declaração SQL UPDATE.

Quando ocorre um erro de simultaneidade, o código realçado a seguir obtém os valores do cliente (os valores postados para esse método) e os valores do banco de dados.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

O seguinte código adiciona uma mensagem de erro personalizada a cada coluna que tem valores de banco de dados diferentes daqueles que foram postados em `OnPostAsync`:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

O código realçado a seguir define o valor `RowVersion` com o novo valor recuperado do banco de dados. Na próxima vez que o usuário clicar em **Salvar**, somente os erros de simultaneidade que ocorrerem desde a última exibição da página Editar serão capturados.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

A instrução `ModelState.Remove` é obrigatória porque `ModelState` tem o valor `RowVersion` antigo. Na Página do Razor, o valor `ModelState` de um campo tem precedência sobre os valores de propriedade do modelo, quando ambos estão presentes.

### <a name="update-the-razor-page"></a>Atualizar a páginas do Razor

Atualize *Pages/Departments/Edit.cshtml* com o seguinte código:

[!code-html[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

O código anterior:

* Atualiza a diretiva `page` de `@page` para `@page "{id:int}"`.
* Adiciona uma versão de linha oculta. `RowVersion` deve ser adicionado para que o postback associe o valor.
* Exibe o último byte de `RowVersion` para fins de depuração.
* Substitui `ViewData` pelo `InstructorNameSL` fortemente tipado.

### <a name="test-concurrency-conflicts-with-the-edit-page"></a>Testar conflitos de simultaneidade com a página Editar

Abra duas instâncias de navegadores de Editar no departamento de inglês:

* Execute o aplicativo e selecione Departamentos.
* Clique com o botão direito do mouse no hiperlink **Editar** do departamento de inglês e selecione **Abrir em uma nova guia**.
* Na primeira guia, clique no hiperlink **Editar** do departamento de inglês.

As duas guias do navegador exibem as mesmas informações.

Altere o nome na primeira guia do navegador e clique em **Salvar**.

![Página 1 Editar Departamento após a alteração](concurrency/_static/edit-after-change-130.png)

O navegador mostra a página de Índice com o valor alterado e o indicador de rowVersion atualizado. Observe o indicador de rowVersion atualizado: ele é exibido no segundo postback na outra guia.

Altere outro campo na segunda guia do navegador.

![Página 2 Editar Departamento após a alteração](concurrency/_static/edit-after-change-230.png)

Clique em **Salvar**. Você verá mensagens de erro em todos os campos que não correspondem aos valores do banco de dados:

![Mensagem de erro da página Editar Departamento](concurrency/_static/edit-error30.png)

Essa janela do navegador não pretendia alterar o campo Name. Copie e cole o valor atual (Languages) para o campo Name. Saída da guia. A validação do lado do cliente remove a mensagem de erro.

Clique em **Salvar** novamente. O valor inserido na segunda guia do navegador foi salvo. Você verá os valores salvos na página Índice.

## <a name="update-the-delete-page"></a>Atualizar a página Excluir

Atualize *Pages/Departments/Delete.cshtml.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

A página Excluir detectou conflitos de simultaneidade quando a entidade foi alterada depois de ser buscada. `Department.RowVersion` é a versão de linha quando a entidade foi buscada. Quando o EF Core cria o comando SQL DELETE, ele inclui uma cláusula WHERE com `RowVersion`. Se o comando SQL DELETE não resultar em nenhuma linha afetada:

* A `RowVersion` no comando SQL DELETE não corresponderá a `RowVersion` no banco de dados.
* Uma exceção DbUpdateConcurrencyException é gerada.
* `OnGetAsync` é chamado com o `concurrencyError`.

### <a name="update-the-delete-razor-page"></a>Atualizar a página Excluir do Razor

Atualize *Pages/Departments/Delete.cshtml* com o seguinte código:

[!code-html[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

O código anterior faz as seguintes alterações:

* Atualiza a diretiva `page` de `@page` para `@page "{id:int}"`.
* Adiciona uma mensagem de erro.
* Substitua FirstMidName por FullName no campo **Administrador**.
* Altere `RowVersion` para exibir o último byte.
* Adiciona uma versão de linha oculta. O `RowVersion` deve ser adicionado para que o postgit add back associe o valor.

### <a name="test-concurrency-conflicts"></a>Testar os conflitos de simultaneidade

Crie um departamento de teste.

Abra duas instâncias dos navegadores de Excluir no departamento de teste:

* Execute o aplicativo e selecione Departamentos.
* Clique com o botão direito do mouse no hiperlink **Excluir** do departamento de teste e selecione **Abrir em uma nova guia**.
* Clique no hiperlink **Editar** do departamento de teste.

As duas guias do navegador exibem as mesmas informações.

Altere o orçamento na primeira guia do navegador e clique em **Salvar**.

O navegador mostra a página de Índice com o valor alterado e o indicador de rowVersion atualizado. Observe o indicador de rowVersion atualizado: ele é exibido no segundo postback na outra guia.

Exclua o departamento de teste na segunda guia. Um erro de simultaneidade é exibido com os valores atuais do banco de dados. Clicar em **Excluir** exclui a entidade, a menos que `RowVersion` tenha sido atualizada e o departamento tenha sido excluído.

## <a name="additional-resources"></a>Recursos adicionais

* [Tokens de simultaneidade no EF Core](/ef/core/modeling/concurrency)
* [Lidar com a simultaneidade no EF Core](/ef/core/saving/concurrency)
* [Depuração de origem do ASP.NET Core 2.x](https://github.com/aspnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a>Próximas etapas

Este é o último tutorial da série. Tópicos adicionais são abordados na [versão MVC desta série de tutoriais](xref:data/ef-mvc/index).

> [!div class="step-by-step"]
> [Tutorial anterior](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Este tutorial mostra como lidar com conflitos quando os mesmos usuários atualizam uma entidade simultaneamente. Caso tenha problemas que não consiga resolver, [baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Instruções de download](xref:index#how-to-download-a-sample).

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

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

O código anterior realçado mostra a cláusula `WHERE` que contém `RowVersion`. Se o BD `RowVersion` não for igual ao parâmetro `RowVersion` (`@p2`), nenhuma linha será atualizada.

O seguinte código realçado mostra o T-SQL que verifica exatamente se uma linha foi atualizada:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

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

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* Executam migrações para atualizar o BD.

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a>Gerar o modelo Departamentos por scaffolding

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Siga as instruções em [Gere um modelo de aluno por scaffold](xref:data/ef-rp/intro#scaffold-student-pages) e use `Department` para a classe de modelo.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Execute o seguinte comando:

  ```console
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

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

Atualize *Pages\Departments\Edit.cshtml.cs* com o código a seguir:

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

O código anterior faz as seguintes alterações:

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
* [Versão do YouTube deste tutorial (Como tratar conflitos de simultaneidade)](https://youtu.be/EosxHTFgYps)
* [Versão do YouTube deste tutorial (Parte 2)](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [Versão do YouTube deste tutorial (Parte 3)](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [Anterior](xref:data/ef-rp/update-related-data)

::: moniker-end

