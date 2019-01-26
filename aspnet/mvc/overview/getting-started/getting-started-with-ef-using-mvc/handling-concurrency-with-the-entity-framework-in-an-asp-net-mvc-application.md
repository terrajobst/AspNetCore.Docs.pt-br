---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Tutorial: Manipular a simultaneidade com o EF em um aplicativo ASP.NET MVC 5'
description: Este tutorial mostra como usar a simultaneidade otimista para lidar com conflitos quando vários usuários atualizam a mesma entidade ao mesmo tempo.
author: tdykstra
ms.author: riande
ms.date: 01/21/2019
ms.topic: tutorial
ms.assetid: be0c098a-1fb2-457e-b815-ddca601afc65
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 2b41b51de5fd6d87b9905ba2bb3ddf7cedb47e69
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889828"
---
# <a name="tutorial-handle-concurrency-with-ef-in-an-aspnet-mvc-5-app"></a>Tutorial: Manipular a simultaneidade com o EF em um aplicativo ASP.NET MVC 5

Nos tutoriais anteriores, você aprendeu como atualizar dados. Este tutorial mostra como usar a simultaneidade otimista para lidar com conflitos quando vários usuários atualizam a mesma entidade ao mesmo tempo. Você alterar as páginas da web que funcionam com o `Department` entidade para que eles manipulam os erros de simultaneidade. As ilustrações a seguir mostram as páginas Editar e Excluir, incluindo algumas mensagens exibidas se ocorre um conflito de simultaneidade.

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image10.png)

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image15.png)

Neste tutorial, você:

> [!div class="checklist"]
> * Saiba mais sobre conflitos de simultaneidade
> * Adicionar a simultaneidade otimista
> * Modificar controlador departamento
> * Tratamento de simultaneidade de teste
> * Atualizar a página Excluir

## <a name="prerequisites"></a>Pré-requisitos

* [Assíncrono e procedimentos armazenados](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="concurrency-conflicts"></a>Conflitos de simultaneidade

Um conflito de simultaneidade ocorre quando um usuário exibe dados de uma entidade para editá-los e, em seguida, outro usuário atualiza os mesmos dados da entidade antes que a primeira alteração do usuário seja gravada no banco de dados. Se você não habilitar a detecção desses conflitos, a última pessoa que atualizar o banco de dados substituirá as outras alterações do usuário. Em muitos aplicativos, esse risco é aceitável: se houver poucos usuários ou poucas atualizações ou se não for realmente crítico se algumas alterações forem substituídas, o custo de programação para simultaneidade poderá superar o benefício. Nesse caso, você não precisa configurar o aplicativo para lidar com conflitos de simultaneidade.

### <a name="pessimistic-concurrency-locking"></a>Simultaneidade pessimista (bloqueio)

Se o aplicativo precisar evitar a perda acidental de dados em cenários de simultaneidade, uma maneira de fazer isso será usar bloqueios de banco de dados. Isso é chamado *simultaneidade pessimista*. Por exemplo, antes de ler uma linha de um banco de dados, você solicita um bloqueio para o acesso somente leitura ou de atualização. Se você bloquear uma linha para o acesso de atualização, nenhum outro usuário terá permissão para bloquear a linha para o acesso somente leitura ou de atualização, porque ele obterá uma cópia dos dados que estão sendo alterados. Se você bloquear uma linha para o acesso somente leitura, outros também poderão bloqueá-la para o acesso somente leitura, mas não para atualização.

O gerenciamento de bloqueios traz desvantagens. Ele pode ser complexo de ser programado. Exige recursos de gerenciamento de banco de dados significativos e pode causar problemas de desempenho, conforme o número de usuários de um aplicativo aumenta. Por esses motivos, nem todos os sistemas de gerenciamento de banco de dados dão suporte à simultaneidade pessimista. O Entity Framework fornece nenhum suporte interno para ele, e este tutorial não mostra como implementá-lo.

### <a name="optimistic-concurrency"></a>Simultaneidade otimista

É a alternativa à simultaneidade pessimista *simultaneidade otimista*. Simultaneidade otimista significa permitir que conflitos de simultaneidade ocorram e responder adequadamente se eles ocorrerem. Por exemplo, John executa a página Editar departamentos, as alterações a **orçamento** quantidade no departamento de inglês de US $350.000,00 para US $0,00.

Antes de John clica **salvar**, Jane executa a mesma página e as alterações a **Start Date** campo 9/1/2007 para 8/8/2013.

John clica **salve** primeiro e vê sua alteração quando o navegador retorna à página de índice, Jane, em seguida, clica **salvar**. O que acontece em seguida é determinado pela forma como você lida com conflitos de simultaneidade. Algumas das opções incluem o seguinte:

- Controle qual propriedade um usuário modificou e atualize apenas as colunas correspondentes no banco de dados. No cenário de exemplo, nenhum dado é perdido, porque propriedades diferentes foram atualizadas pelos dois usuários. Na próxima vez que alguém navegar pelo departamento de inglês, eles verão as alterações de John e Jane — uma data de início do 8/8/2013 e um orçamento de Zero dólar.

    Esse método de atualização pode reduzir a quantidade de conflitos que podem resultar em perda de dados, mas ele não poderá evitar a perda de dados se forem feitas alterações concorrentes à mesma propriedade de uma entidade. Se o Entity Framework funciona dessa maneira depende de como o código de atualização é implementado. Geralmente, isso não é prático em um aplicativo Web, porque pode exigir que você mantenha grandes quantidades de estado para manter o controle de todos os valores de propriedade originais de uma entidade, bem como novos valores. A manutenção de grandes quantidades de estado pode afetar o desempenho do aplicativo, pois exige recursos do servidor ou deve ser incluída na própria página da Web (por exemplo, em campos ocultos) ou em um cookie.
- Você pode deixar a alteração de Jane substituir alterações de julio. Na próxima vez que alguém navegar pelo departamento de inglês, eles verão 8/8/2013 e o valor de US $350.000,00 restaurado. Isso é chamado de um cenário *O cliente vence* ou *O último vence*. (Todos os valores do cliente têm precedência sobre o conteúdo do armazenamento de dados.) Conforme observado na introdução a esta seção, se você não fizer nenhuma codificação para a manipulação de simultaneidade, isso ocorrerá automaticamente.
- Você pode impedir que as alterações de Jane sejam atualizados no banco de dados. Normalmente, você seria exibir uma mensagem de erro, mostrar o estado atual dos dados e permitir a ela reaplicar as alterações dela se ela ainda desejar fazê-las. Isso é chamado de um cenário *O armazenamento vence*. (Os valores do armazenamento de dados têm precedência sobre os valores enviados pelo cliente.) Você implementará o cenário O armazenamento vence neste tutorial. Esse método garante que nenhuma alteração é substituída sem que um usuário seja alertado sobre o que está acontecendo.

### <a name="detecting-concurrency-conflicts"></a>Detectando conflitos de simultaneidade

Você pode resolver conflitos manipulando [OptimisticConcurrencyException](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) as exceções que o Entity Framework gera. Para saber quando gerar essas exceções, o Entity Framework precisa poder detectar conflitos. Portanto, é necessário configurar o banco de dados e o modelo de dados de forma adequada. Algumas opções para habilitar a detecção de conflitos incluem as seguintes:

- Na tabela de banco de dados, inclua uma coluna de acompanhamento que pode ser usada para determinar quando uma linha é alterada. Você pode configurar o Entity Framework para incluir essa coluna na `Where` cláusula SQL `Update` ou `Delete` comandos.

    O tipo de dados da coluna de acompanhamento é normalmente [rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx). O [rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx) valor é um número sequencial que é incrementado sempre que a linha é atualizada. Em um `Update` ou `Delete` comando, o `Where` cláusula inclui o valor original da coluna de acompanhamento (a versão de linha original). Se a linha que está sendo atualizada tiver sido alterada por outro usuário, o valor na `rowversion` coluna é diferente do valor original, portanto, o `Update` ou `Delete` instrução não é possível localizar a linha a ser atualizada devido a `Where` cláusula. Quando o Entity Framework não localiza nenhuma linha foi atualizada pela `Update` ou `Delete` de comando (ou seja, quando o número de linhas afetadas seja zero), ele interpreta isso como um conflito de simultaneidade.
- Configure o Entity Framework para incluir os valores originais de cada coluna na tabela de `Where` cláusula de `Update` e `Delete` comandos.

    Como a primeira opção, se nada na linha foi alterada desde que a linha foi lido pela primeira vez, o `Where` cláusula não retornará uma linha a ser atualizada, que o Entity Framework interpreta como um conflito de simultaneidade. Para tabelas de banco de dados que têm muitas colunas, essa abordagem pode resultar em grandes `Where` cláusulas e pode exigir que você mantenha grandes quantidades de estado. Conforme observado anteriormente, manter grandes quantidades de estado pode afetar o desempenho do aplicativo. Portanto, essa abordagem geralmente não é recomendada e não é o método usado neste tutorial.

    Se você quiser implementar essa abordagem à simultaneidade, você precisará marcar todas as propriedades de chave não primária na entidade que você deseja controlar a simultaneidade para adicionando o [ConcurrencyCheck](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute.aspx) de atributo a eles. Que alteração permite que o Entity Framework incluir todas as colunas no SQL `WHERE` cláusula de `UPDATE` instruções.

O restante deste tutorial, você adicionará uma [rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx) acompanhamento de propriedade para o `Department` entidade, criar um controlador e exibições e teste para verificar se tudo está funcionando corretamente.

## <a name="add-optimistic-concurrency"></a>Adicionar a simultaneidade otimista

Na *Models\Department.cs*, adicione uma propriedade de controle chamada `RowVersion`:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=20-22)]

O [Timestamp](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute.aspx) atributo especifica que essa coluna será incluída na `Where` cláusula da `Update` e `Delete` comandos enviados para o banco de dados. O atributo é chamado [Timestamp](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute.aspx) porque as versões anteriores do SQL Server usavam um SQL [timestamp](https://msdn.microsoft.com/library/ms182776(v=SQL.90).aspx) tipo de dados antes do SQL [rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx) substituí-lo. O tipo de .net para [rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx) é uma matriz de bytes.

Se você preferir usar a API fluente, você pode usar o [IsConcurrencyToken](https://msdn.microsoft.com/library/gg679501(v=VS.103).aspx) método para especificar a propriedade de controle, conforme mostrado no exemplo a seguir:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

Adicionando uma propriedade, você alterou o modelo de banco de dados e, portanto, precisa fazer outra migração. No PMC (Console do Gerenciador de Pacotes), Insira os seguintes comandos:

[!code-console[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cmd)]

## <a name="modify-department-controller"></a>Modificar controlador departamento

Na *Controllers\DepartmentController.cs*, adicione um `using` instrução:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

No *DepartmentController.cs* file, altere todas as quatro ocorrências de "LastName" para "FullName", para que o departamento administrador nas listas suspensas conterá o nome completo do instrutor em vez de apenas o último nome.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs?highlight=1)]

Substitua o código existente para o `HttpPost` `Edit` método com o código a seguir:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

Se o método `FindAsync` retornar nulo, isso indicará que o departamento foi excluído por outro usuário. O código mostrado usa os valores de formulário postados para criar uma entidade de departamento, de modo que a página Editar possa ser exibida novamente com uma mensagem de erro. Como alternativa, você não precisará recriar a entidade de departamento se exibir apenas uma mensagem de erro sem exibir novamente os campos de departamento.

A exibição armazena o original `RowVersion` valor em um campo oculto e o método recebe no `rowVersion` parâmetro. Antes de chamar `SaveChanges`, você precisa colocar isso no valor da propriedade `RowVersion` original na coleção `OriginalValues` da entidade. Em seguida, quando o Entity Framework cria um SQL `UPDATE` de comando, que o comando incluirá uma `WHERE` cláusula que procura por uma linha que tenha o original `RowVersion` valor.

Se nenhuma linha for afetada pelo `UPDATE` comando (nenhuma linha tem original `RowVersion` valor), o Entity Framework gerará uma `DbUpdateConcurrencyException` exceção e o código no `catch` bloco obtém afetado `Department` entidade da exceção objeto.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

Esse objeto tem os novos valores inseridos pelo usuário no seu `Entity` propriedade e você pode obter os valores lidos do banco de dados chamando o `GetDatabaseValues` método.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

O `GetDatabaseValues` método retornará nulo se alguém tiver excluído a linha do banco de dados; caso contrário, você precisará converter o objeto retornado para o `Department` classe para acessar o `Department` propriedades. (Porque você já tiver marcado para exclusão, `databaseEntry` seria nulo somente se o departamento foi excluído após `FindAsync` executa e antes de `SaveChanges` é executado.)

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

Em seguida, o código adiciona uma mensagem de erro personalizada para cada coluna que tem valores de banco de dados diferentes do que o usuário inseriu na página Editar:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

Uma mensagem de erro explica o que aconteceu e o que fazer sobre ele:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

Por fim, o código define a `RowVersion` valor da `Department` objeto para o novo valor recuperado do banco de dados. Esse novo valor `RowVersion` será armazenado no campo oculto quando a página Editar for exibida novamente, e na próxima vez que o usuário clicar em **Salvar**, somente os erros de simultaneidade que ocorrem desde a nova exibição da página Editar serão capturados.

Na *Views\Department\Edit.cshtml*, adicione um campo oculto para salvar as `RowVersion` imediatamente após o campo oculto para o valor da propriedade, o `DepartmentID` propriedade:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cshtml?highlight=18)]

## <a name="test-concurrency-handling"></a>Tratamento de simultaneidade de teste

Executar o site e clique em **departamentos**.

Clique com botão direito do **editar** hiperlink para o departamento de inglês e selecione **abrir em uma nova guia,** , em seguida, clique no **editar** hiperlink no departamento de inglês. As duas guias exibem as mesmas informações.

Altere um campo na primeira guia do navegador e clique em **Salvar**.

O navegador mostra a página Índice com o valor alterado.

Altere um campo na segunda guia do navegador e clique em **salvar**. Você verá uma mensagem de erro:

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image10.png)

Clique em **Salvar** novamente. O valor inserido na segunda guia do navegador é salva com o valor original dos dados alterados no navegador primeiro. Você verá os valores salvos quando a página Índice for exibida.

## <a name="update-the-delete-page"></a>Atualizar a página Excluir

Para a página Excluir, o Entity Framework detecta conflitos de simultaneidade causados pela edição por outra pessoa do departamento de maneira semelhante. Quando o `HttpGet` `Delete` método exibe o modo de exibição de confirmação, o modo de exibição inclui original `RowVersion` valor em um campo oculto. Que valor, em seguida, está disponível para o `HttpPost` `Delete` método é chamado quando o usuário confirmar a exclusão. Quando o Entity Framework cria o SQL `DELETE` de comando, ele inclui um `WHERE` cláusula com original `RowVersion` valor. Se os resultados do comando em nenhuma linha afetada (o que significa que a linha foi alterada depois que a página de confirmação de exclusão foi exibida), é gerada uma exceção de simultaneidade e o `HttpGet Delete` método é chamado com um sinalizador de erro definido como `true` para exibir novamente a página de confirmação com uma mensagem de erro. Também é possível que o zero linha foi afetada porque a linha foi excluída por outro usuário, portanto, nesse caso, será exibida uma mensagem de erro diferentes.

Na *DepartmentController.cs*, substitua o `HttpGet` `Delete` método com o código a seguir:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]

O método aceita um parâmetro opcional que indica se a página está sendo exibida novamente após um erro de simultaneidade. Se esse sinalizador estiver `true`, uma mensagem de erro é enviada para o modo de exibição usando um `ViewBag` propriedade.

Substitua o código na `HttpPost` `Delete` método (chamado `DeleteConfirmed`) com o código a seguir:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cs)]

No código gerado por scaffolding que acabou de ser substituído, esse método aceitou apenas uma ID de registro:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cs)]

Você alterou esse parâmetro para um `Department` instância da entidade criada pelo associador de modelo. Isso fornece acesso para o `RowVersion` valor da propriedade além da chave do registro.

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cs)]

Você também alterou o nome do método de ação de `DeleteConfirmed` para `Delete`. O código gerado por scaffolding chamado a `HttpPost` `Delete` método `DeleteConfirmed` para dar o `HttpPost` método uma assinatura exclusiva. (O CLR exige métodos sobrecarregados tenham diferentes parâmetros de método). Agora que as assinaturas são exclusivas, você pode continuar com a convenção do MVC e usar o mesmo nome para o `HttpPost` e `HttpGet` excluir métodos.

Se um erro de simultaneidade é capturado, o código exibe novamente a página Confirmação de exclusão e fornece um sinalizador que indica que ela deve exibir uma mensagem de erro de simultaneidade.

Na *Views\Department\Delete.cshtml*, substitua o código gerado automaticamente pelo código a seguir que adiciona um campo de mensagem de erro e campos ocultos para as propriedades DepartmentID e RowVersion. As alterações são realçadas.

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml?highlight=9-10,21,52-54)]

Esse código adiciona uma mensagem de erro entre o `h2` e `h3` títulos:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cshtml)]

Ele substitui `LastName` com `FullName` no `Administrator` campo:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cshtml)]

Por fim, ele adiciona campos ocultos para o `DepartmentID` e `RowVersion` propriedades após o `Html.BeginForm` instrução:

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cshtml)]

Execute a página de índice de departamentos. Clique com botão direito do **excluir** hiperlink para o departamento de inglês e selecione **abrir em uma nova guia,** , em seguida, na primeira guia, clique no **editar** hiperlink no departamento de inglês.

Na primeira janela, alterar um dos valores e clique em **salvar**.

A página de índice confirma a alteração.

Na segunda guia, clique em **Excluir**.

Você verá a mensagem de erro de simultaneidade e os valores de Departamento serão atualizados com o que está atualmente no banco de dados.

![Department_Delete_confirmation_page_with_concurrency_error](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image15.png)

Se você clicar em **Excluir** novamente, será redirecionado para a página Índice, que mostra que o departamento foi excluído.

## <a name="get-the-code"></a>Obter o código

[Baixe o projeto concluído](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>Recursos adicionais

Links para outros recursos do Entity Framework podem ser encontradas na [acesso a dados ASP.NET – recursos recomendados](../../../../whitepapers/aspnet-data-access-content-map.md).

Para obter informações sobre outras maneiras de lidar com vários cenários de simultaneidade, consulte [padrões de simultaneidade otimista](https://msdn.microsoft.com/data/jj592904) e [trabalhando com valores de propriedade](https://msdn.microsoft.com/data/jj592677) no MSDN. O próximo tutorial mostra como implementar a herança de tabela por hierarquia para o `Instructor` e `Student` entidades.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Aprendeu sobre conflitos de simultaneidade
> * Adicionada a simultaneidade otimista
> * Controlador de departamento modificado
> * Tratamento de simultaneidade testado
> * Atualizada a página Excluir

Avance para o próximo artigo para saber como implementar a herança no modelo de dados.
> [!div class="nextstepaction"]
> [Implementar a herança no modelo de dados](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md)