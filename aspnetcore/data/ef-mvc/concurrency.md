---
title: 'Tutorial: Lidar com a simultaneidade - ASP.NET MVC com EF Core'
description: Este tutorial mostra como lidar com conflitos quando os mesmos usuários atualizam a mesma entidade simultaneamente.
author: tdykstra
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/concurrency
ms.openlocfilehash: e8c88ed2811ad221d94c963c6e14fea9bc1607ea
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080451"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a>Tutorial: Lidar com a simultaneidade - ASP.NET MVC com EF Core

Nos tutoriais anteriores, você aprendeu a atualizar dados. Este tutorial mostra como lidar com conflitos quando os mesmos usuários atualizam a mesma entidade simultaneamente.

Você criará páginas da Web que funcionam com a entidade Department e tratará erros de simultaneidade. As ilustrações a seguir mostram as páginas Editar e Excluir, incluindo algumas mensagens exibidas se ocorre um conflito de simultaneidade.

![Página Editar Departamento](concurrency/_static/edit-error.png)

![Página Excluir Departamento](concurrency/_static/delete-error.png)

Neste tutorial, você:

> [!div class="checklist"]
> * Aprender sobre conflitos de simultaneidade
> * Adicionar uma propriedade de acompanhamento
> * Criar um controlador e exibições de Departamentos
> * Atualizar a exibição do Índice
> * Atualizar métodos de Edição
> * Atualizar a exibição de Edição
> * Testar os conflitos de simultaneidade
> * Atualizar a página Excluir
> * Exibições Atualizar Detalhes e Criar

## <a name="prerequisites"></a>Pré-requisitos

* [Atualizar dados relacionados](update-related-data.md)

## <a name="concurrency-conflicts"></a>Conflitos de simultaneidade

Um conflito de simultaneidade ocorre quando um usuário exibe dados de uma entidade para editá-los e, em seguida, outro usuário atualiza os mesmos dados da entidade antes que a primeira alteração do usuário seja gravada no banco de dados. Se você não habilitar a detecção desses conflitos, a última pessoa que atualizar o banco de dados substituirá as outras alterações do usuário. Em muitos aplicativos, esse risco é aceitável: se houver poucos usuários ou poucas atualizações ou se não for realmente crítico se algumas alterações forem substituídas, o custo de programação para simultaneidade poderá superar o benefício. Nesse caso, você não precisa configurar o aplicativo para lidar com conflitos de simultaneidade.

### <a name="pessimistic-concurrency-locking"></a>Simultaneidade pessimista (bloqueio)

Se o aplicativo precisar evitar a perda acidental de dados em cenários de simultaneidade, uma maneira de fazer isso será usar bloqueios de banco de dados. Isso é chamado de simultaneidade pessimista. Por exemplo, antes de ler uma linha de um banco de dados, você solicita um bloqueio para o acesso somente leitura ou de atualização. Se você bloquear uma linha para o acesso de atualização, nenhum outro usuário terá permissão para bloquear a linha para o acesso somente leitura ou de atualização, porque ele obterá uma cópia dos dados que estão sendo alterados. Se você bloquear uma linha para o acesso somente leitura, outros também poderão bloqueá-la para o acesso somente leitura, mas não para atualização.

O gerenciamento de bloqueios traz desvantagens. Ele pode ser complexo de ser programado. Exige recursos de gerenciamento de banco de dados significativos e pode causar problemas de desempenho, conforme o número de usuários de um aplicativo aumenta. Por esses motivos, nem todos os sistemas de gerenciamento de banco de dados dão suporte à simultaneidade pessimista. O Entity Framework Core não fornece nenhum suporte interno para ele, e este tutorial não mostra como implementá-lo.

### <a name="optimistic-concurrency"></a>Simultaneidade otimista

A alternativa à simultaneidade pessimista é a simultaneidade otimista. Simultaneidade otimista significa permitir que conflitos de simultaneidade ocorram e responder adequadamente se eles ocorrerem. Por exemplo, Alice visita a página Editar Departamento e altera o valor do Orçamento para o departamento de inglês de US$ 350.000,00 para US$ 0,00.

![Alterando o orçamento para 0](concurrency/_static/change-budget.png)

Antes que Alice clique em **Salvar**, Julio visita a mesma página e altera o campo Data de Início de 1/9/2007 para 1/9/2013.

![Alterando a data de início para 2013](concurrency/_static/change-date.png)

Alice clica em **Salvar** primeiro e vê a alteração quando o navegador retorna à página Índice.

![Orçamento alterado para zero](concurrency/_static/budget-zero.png)

Em seguida, Julio clica em **Salvar** em uma página Editar que ainda mostra um orçamento de US$ 350.000,00. O que acontece em seguida é determinado pela forma como você lida com conflitos de simultaneidade.

Algumas das opções incluem o seguinte:

* Controle qual propriedade um usuário modificou e atualize apenas as colunas correspondentes no banco de dados.

     No cenário de exemplo, nenhum dado é perdido, porque propriedades diferentes foram atualizadas pelos dois usuários. Na próxima vez que alguém navegar pelo departamento de inglês, verá as alterações de Alice e de Julio – a data de início de 1/9/2013 e um orçamento de zero dólar. Esse método de atualização pode reduzir a quantidade de conflitos que podem resultar em perda de dados, mas ele não poderá evitar a perda de dados se forem feitas alterações concorrentes à mesma propriedade de uma entidade. Se o Entity Framework funciona dessa maneira depende de como o código de atualização é implementado. Geralmente, isso não é prático em um aplicativo Web, porque pode exigir que você mantenha grandes quantidades de estado para manter o controle de todos os valores de propriedade originais de uma entidade, bem como novos valores. A manutenção de grandes quantidades de estado pode afetar o desempenho do aplicativo, pois exige recursos do servidor ou deve ser incluída na própria página da Web (por exemplo, em campos ocultos) ou em um cookie.

* Você não pode deixar a alteração de Julio substituir a alteração de Alice.

     Na próxima vez que alguém navegar pelo departamento de inglês, verá 1/9/2013 e o valor de US$ 350.000,00 restaurado. Isso é chamado de um cenário *O cliente vence* ou *O último vence*. (Todos os valores do cliente têm precedência sobre o conteúdo do armazenamento de dados.) Conforme observado na introdução a esta seção, se você não fizer nenhuma codificação para a manipulação de simultaneidade, isso ocorrerá automaticamente.

* Você pode impedir que as alterações de Julio sejam atualizadas no banco de dados.

     Normalmente, você exibirá uma mensagem de erro, mostrará a ele o estado atual dos dados e permitirá a ele aplicar as alterações novamente se ele ainda desejar fazê-las. Isso é chamado de um cenário *O armazenamento vence*. (Os valores do armazenamento de dados têm precedência sobre os valores enviados pelo cliente.) Você implementará o cenário O armazenamento vence neste tutorial. Esse método garante que nenhuma alteração é substituída sem que um usuário seja alertado sobre o que está acontecendo.

### <a name="detecting-concurrency-conflicts"></a>Detectando conflitos de simultaneidade

Resolva conflitos manipulando exceções `DbConcurrencyException` geradas pelo Entity Framework. Para saber quando gerar essas exceções, o Entity Framework precisa poder detectar conflitos. Portanto, é necessário configurar o banco de dados e o modelo de dados de forma adequada. Algumas opções para habilitar a detecção de conflitos incluem as seguintes:

* Na tabela de banco de dados, inclua uma coluna de acompanhamento que pode ser usada para determinar quando uma linha é alterada. Em seguida, configure o Entity Framework para incluir essa coluna na cláusula Where de comandos SQL Update ou Delete.

     O tipo de dados da coluna de acompanhamento é normalmente `rowversion`. O valor `rowversion` é um número sequencial que é incrementado sempre que a linha é atualizada. Em um comando Update ou Delete, a cláusula Where inclui o valor original da coluna de acompanhamento (a versão de linha original). Se a linha que está sendo atualizada tiver sido alterada por outro usuário, o valor da coluna `rowversion` será diferente do valor original; portanto, a instrução Update ou Delete não poderá encontrar a linha a ser atualizada devido à cláusula Where. Quando o Entity Framework descobre que nenhuma linha foi atualizada pelo comando Update ou Delete (ou seja, quando o número de linhas afetadas é zero), ele interpreta isso como um conflito de simultaneidade.

* Configure o Entity Framework para incluir os valores originais de cada coluna na tabela na cláusula Where de comandos Update e Delete.

     Como a primeira opção, se nada na linha for alterado desde que a linha tiver sido lida pela primeira vez, a cláusula Where não retornará uma linha a ser atualizada, o que o Entity Framework interpretará como um conflito de simultaneidade. Para tabelas de banco de dados que têm muitas colunas, essa abordagem pode resultar em cláusulas Where muito grandes e pode exigir que você mantenha grandes quantidades de estado. Conforme observado anteriormente, manter grandes quantidades de estado pode afetar o desempenho do aplicativo. Portanto, essa abordagem geralmente não é recomendada e não é o método usado neste tutorial.

     Caso deseje implementar essa abordagem, precisará marcar todas as propriedades de chave não primária na entidade para as quais você deseja controlar a simultaneidade adicionando o atributo `ConcurrencyCheck` a elas. Essa alteração permite que o Entity Framework inclua todas as colunas na cláusula Where do SQL de instruções Update e Delete.

No restante deste tutorial, você adicionará uma propriedade de acompanhamento `rowversion` à entidade Department, criará um controlador e exibições e testará para verificar se tudo está funcionando corretamente.

## <a name="add-a-tracking-property"></a>Adicionar uma propriedade de acompanhamento

Em *Models/Department.cs*, adicione uma propriedade de controle chamada RowVersion:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

O atributo `Timestamp` especifica que essa coluna será incluída na cláusula Where de comandos Update e Delete enviados ao banco de dados. O atributo é chamado `Timestamp` porque as versões anteriores do SQL Server usavam um tipo de dados `timestamp` do SQL antes de o `rowversion` do SQL substituí-lo. O tipo do .NET para `rowversion` é uma matriz de bytes.

Se preferir usar a API fluente, use o método `IsConcurrencyToken` (em *Data/SchoolContext.cs*) para especificar a propriedade de acompanhamento, conforme mostrado no seguinte exemplo:

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

Adicionando uma propriedade, você alterou o modelo de banco de dados e, portanto, precisa fazer outra migração.

Salve as alterações e compile o projeto e, em seguida, insira os seguintes comandos na janela Comando:

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a>Criar um controlador e exibições de Departamentos

Gere por scaffolding um controlador e exibições Departamentos, como você fez anteriormente para Alunos, Cursos e Instrutores.

![Gerar o departamento por scaffolding](concurrency/_static/add-departments-controller.png)

No arquivo *DepartmentsController.cs*, altere todas as quatro ocorrências de "FirstMidName" para "FullName", de modo que as listas suspensas do administrador do departamento contenham o nome completo do instrutor em vez de apenas o sobrenome.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a>Atualizar a exibição do Índice

O mecanismo de scaffolding criou uma coluna RowVersion na exibição Índice, mas esse campo não deve ser exibido.

Substitua o código em *Views/Departments/Index.cshtml* pelo código a seguir.

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

Isso altera o título "Departamentos", exclui a coluna RowVersion e mostra o nome completo em vez de o nome do administrador.

## <a name="update-edit-methods"></a>Atualizar métodos de Edição

Nos métodos HttpGet `Edit` e `Details`, adicione `AsNoTracking`. No método HttpGet `Edit`, adicione o carregamento adiantado ao Administrador.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

Substitua o código existente do método HttpPost `Edit` pelo seguinte código:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

O código começa com a tentativa de ler o departamento a ser atualizado. Se o método `FirstOrDefaultAsync` retornar nulo, isso indicará que o departamento foi excluído por outro usuário. Nesse caso, o código usa os valores de formulário postados para criar uma entidade de departamento, de modo que a página Editar possa ser exibida novamente com uma mensagem de erro. Como alternativa, você não precisará recriar a entidade de departamento se exibir apenas uma mensagem de erro sem exibir novamente os campos de departamento.

A exibição armazena o valor `RowVersion` original em um campo oculto e esse método recebe esse valor no parâmetro `rowVersion`. Antes de chamar `SaveChanges`, você precisa colocar isso no valor da propriedade `RowVersion` original na coleção `OriginalValues` da entidade.

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

Em seguida, quando o Entity Framework criar um comando SQL UPDATE, esse comando incluirá uma cláusula WHERE que procura uma linha que tem o valor `RowVersion` original. Se nenhuma linha for afetada pelo comando UPDATE (nenhuma linha tem o valor `RowVersion` original), o Entity Framework gerará uma exceção `DbUpdateConcurrencyException`.

O código no bloco catch dessa exceção obtém a entidade Department afetada que tem os valores atualizados da propriedade `Entries` no objeto de exceção.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

A coleção `Entries` terá apenas um objeto `EntityEntry`.  Use esse objeto para obter os novos valores inseridos pelo usuário e os valores de banco de dados atuais.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

O código adiciona uma mensagem de erro personalizada a cada coluna que tem valores de banco de dados diferentes do que o usuário inseriu na página Editar (apenas um campo é mostrado aqui para fins de brevidade).

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

Por fim, o código define o valor `RowVersion` do `departmentToUpdate` com o novo valor recuperado do banco de dados. Esse novo valor `RowVersion` será armazenado no campo oculto quando a página Editar for exibida novamente, e na próxima vez que o usuário clicar em **Salvar**, somente os erros de simultaneidade que ocorrem desde a nova exibição da página Editar serão capturados.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

A instrução `ModelState.Remove` é obrigatória porque `ModelState` tem o valor `RowVersion` antigo. Na exibição, o valor `ModelState` de um campo tem precedência sobre os valores de propriedade do modelo, quando ambos estão presentes.

## <a name="update-edit-view"></a>Atualizar a exibição de Edição

Em *Views/Departments/Edit.cshtml*, faça as seguintes alterações:

* Adicione um campo oculto para salvar o valor da propriedade `RowVersion`, imediatamente após o campo oculto da propriedade `DepartmentID`.

* Adicione uma opção "Selecionar Administrador" à lista suspensa.

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a>Testar os conflitos de simultaneidade

Execute o aplicativo e acesse a página Índice de Departamentos. Clique com o botão direito do mouse na hiperlink **Editar** do departamento de inglês e selecione **Abrir em uma nova guia** e, em seguida, clique no hiperlink **Editar** no departamento de inglês. As duas guias do navegador agora exibem as mesmas informações.

Altere um campo na primeira guia do navegador e clique em **Salvar**.

![Página 1 Editar Departamento após a alteração](concurrency/_static/edit-after-change-1.png)

O navegador mostra a página Índice com o valor alterado.

Altere um campo na segunda guia do navegador.

![Página 2 Editar Departamento após a alteração](concurrency/_static/edit-after-change-2.png)

Clique em **Salvar**. Você verá uma mensagem de erro:

![Mensagem de erro da página Editar Departamento](concurrency/_static/edit-error.png)

Clique em **Salvar** novamente. O valor inserido na segunda guia do navegador foi salvo. Você verá os valores salvos quando a página Índice for exibida.

## <a name="update-the-delete-page"></a>Atualizar a página Excluir

Para a página Excluir, o Entity Framework detecta conflitos de simultaneidade causados pela edição por outra pessoa do departamento de maneira semelhante. Quando o método HttpGet `Delete` exibe a exibição de confirmação, a exibição inclui o valor `RowVersion` original em um campo oculto. Em seguida, esse valor estará disponível para o método HttpPost `Delete` chamado quando o usuário confirmar a exclusão. Quando o Entity Framework cria o comando SQL DELETE, ele inclui uma cláusula WHERE com o valor `RowVersion` original. Se o comando não resultar em nenhuma linha afetada (o que significa que a linha foi alterada após a exibição da página Confirmação de exclusão), uma exceção de simultaneidade será gerada e o método HttpGet `Delete` será chamado com um sinalizador de erro definido como verdadeiro para exibir novamente a página de confirmação com uma mensagem de erro. Também é possível que nenhuma linha tenha sido afetada porque a linha foi excluída por outro usuário; portanto, nesse caso, nenhuma mensagem de erro é exibida.

### <a name="update-the-delete-methods-in-the-departments-controller"></a>Atualizar os métodos Excluir no controlador Departamentos

Em *DepartmentsController.cs*, substitua o método HttpGet `Delete` pelo seguinte código:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

O método aceita um parâmetro opcional que indica se a página está sendo exibida novamente após um erro de simultaneidade. Se esse sinalizador é verdadeiro e o departamento especificado não existe mais, isso indica que ele foi excluído por outro usuário. Nesse caso, o código redireciona para a página Índice.  Se esse sinalizador é verdadeiro e o departamento existe, isso indica que ele foi alterado por outro usuário. Nesse caso, o código envia uma mensagem de erro para a exibição usando `ViewData`.

Substitua o código no método HttpPost `Delete` (chamado `DeleteConfirmed`) pelo seguinte código:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

No código gerado por scaffolding que acabou de ser substituído, esse método aceitou apenas uma ID de registro:

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

Você alterou esse parâmetro para uma instância da entidade Departamento criada pelo associador de modelos. Isso concede o acesso ao EF ao valor da propriedade RowVersion, além da chave de registro.

```csharp
public async Task<IActionResult> Delete(Department department)
```

Você também alterou o nome do método de ação de `DeleteConfirmed` para `Delete`. O código gerado por scaffolding usou o nome `DeleteConfirmed` para fornecer ao método HttpPost uma assinatura exclusiva. (O CLR exige que métodos sobrecarregados tenham diferentes parâmetros de método.) Agora que as assinaturas são exclusivas, você pode continuar com a convenção MVC e usar o mesmo nome para os métodos de exclusão HttpPost e HttpGet.

Se o departamento já foi excluído, o método `AnyAsync` retorna falso e o aplicativo apenas volta para o método de Índice.

Se um erro de simultaneidade é capturado, o código exibe novamente a página Confirmação de exclusão e fornece um sinalizador que indica que ela deve exibir uma mensagem de erro de simultaneidade.

### <a name="update-the-delete-view"></a>Atualizar a exibição Excluir

Em *Views/Departments/Delete.cshtml*, substitua o código gerado por scaffolding pelo seguinte código que adiciona um campo de mensagem de erro e campos ocultos às propriedades DepartmentID e RowVersion. As alterações são realçadas.

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

Isso faz as seguintes alterações:

* Adiciona uma mensagem de erro entre os cabeçalhos `h2` e `h3`.

* Substitua FirstMidName por FullName no campo **Administrador**.

* Remove o campo RowVersion.

* Adiciona um campo oculto à propriedade `RowVersion`.

Execute o aplicativo e acesse a página Índice de Departamentos. Clique com o botão direito do mouse na hiperlink **Excluir** do departamento de inglês e selecione **Abrir em uma nova guia** e, em seguida, na primeira guia, clique no hiperlink **Editar** no departamento de inglês.

Na primeira janela, altere um dos valores e clique em **Salvar**:

![Página Editar Departamento após a alteração antes da exclusão](concurrency/_static/edit-after-change-for-delete.png)

Na segunda guia, clique em **Excluir**. Você verá a mensagem de erro de simultaneidade e os valores de Departamento serão atualizados com o que está atualmente no banco de dados.

![Página Confirmação de Exclusão de Departamento com erro de simultaneidade](concurrency/_static/delete-error.png)

Se você clicar em **Excluir** novamente, será redirecionado para a página Índice, que mostra que o departamento foi excluído.

## <a name="update-details-and-create-views"></a>Exibições Atualizar Detalhes e Criar

Opcionalmente, você pode limpar o código gerado por scaffolding nas exibições Detalhes e Criar.

Substitua o código em *Views/Departments/Details.cshtml* para excluir a coluna RowVersion e mostrar o nome completo do Administrador.

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

Substitua o código em *Views/Departments/Create.cshtml* para adicionar uma opção Selecionar à lista suspensa.

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a>Obter o código

[Baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Recursos adicionais

 Para obter mais informações sobre como lidar com a simultaneidade no EF Core, consulte [Conflitos de simultaneidade](/ef/core/saving/concurrency).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Aprendeu sobre conflitos de simultaneidade
> * Adicionou uma propriedade de acompanhamento
> * Criou um controlador e exibições de Departamentos
> * Atualizou a exibição do Índice
> * Atualizou métodos de Edição
> * Atualizou a exibição de Edição
> * Testou conflitos de simultaneidade
> * Atualizou a página Excluir
> * Atualizou as exibições de Detalhes e Criar

Vá para o próximo tutorial para aprender a implementar a herança de tabela por hierarquia para as entidades Instructor e Student.

> [!div class="nextstepaction"]
> [Avançar: implementar a herança de tabela por hierarquia](inheritance.md)
