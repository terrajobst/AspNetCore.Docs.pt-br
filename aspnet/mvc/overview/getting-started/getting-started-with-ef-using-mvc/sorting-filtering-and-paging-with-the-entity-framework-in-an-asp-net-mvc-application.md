---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Tutorial: Adicionar classificação, filtragem e paginação com o Entity Framework em um aplicativo ASP.NET MVC | Microsoft Docs'
author: tdykstra
description: Neste tutorial, você adiciona a classificação, filtragem e a funcionalidade de paginação para o **alunos** página de índice. Você também pode criar uma página de agrupamento simples.
ms.author: riande
ms.date: 01/14/2019
ms.assetid: d5723e46-41fe-4d09-850a-e03b9e285bfa
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 1f18a15d39d58ffb4ac48cfccee6519d33294e85
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444189"
---
# <a name="tutorial-add-sorting-filtering-and-paging-with-the-entity-framework-in-an-aspnet-mvc-application"></a>Tutorial: Adicionar classificação, filtragem e paginação com o Entity Framework em um aplicativo ASP.NET MVC

No [tutorial anterior](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md), você implementou um conjunto de páginas da web para operações CRUD básicas para `Student` entidades. Neste tutorial, você adiciona a classificação, filtragem e a funcionalidade de paginação para o **alunos** página de índice. Você também pode criar uma página de agrupamento simples.

A imagem a seguir mostra a aparência de página quando terminar. Os títulos de coluna são links que o usuário pode clicar para classificar por essa coluna. Clicar em um título de coluna alterna repetidamente entre a ordem de classificação ascendente e descendente.

![Students_Index_page_with_paging](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

Neste tutorial, você:

> [!div class="checklist"]
> * Adicionar links de classificação de coluna
> * Adicione uma caixa de pesquisa
> * Adicionar paginação
> * Criar uma página sobre

## <a name="prerequisites"></a>Pré-requisitos

* [Implementar a funcionalidade CRUD básica](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md)

## <a name="add-column-sort-links"></a>Adicionar links de classificação de coluna

Para adicionar uma classificação à página índice de alunos, você alterará a `Index` método da `Student` controlador e adicione código para o `Student` indexa a exibição.

### <a name="add-sorting-functionality-to-the-index-method"></a>Adicionar a funcionalidade de classificação ao método Index

- Na *Controllers\StudentController.cs*, substitua o `Index` método com o código a seguir:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

Esse código recebe um parâmetro `sortOrder` da cadeia de caracteres de consulta na URL. O valor de cadeia de caracteres de consulta é fornecido pelo ASP.NET MVC como um parâmetro para o método de ação. O parâmetro é uma cadeia de caracteres que é "Name" ou "Date", opcionalmente seguido por um sublinhado e a cadeia de caracteres "desc" para especificar a ordem decrescente. A ordem de classificação crescente é padrão.

Na primeira vez que a página Índice é solicitada, não há nenhuma cadeia de caracteres de consulta. Os alunos são exibidos em ordem crescente pela `LastName`, que é o padrão, conforme estabelecido pelo caso fall-through no `switch` instrução. Quando o usuário clica em um hiperlink de título de coluna, o valor `sortOrder` apropriado é fornecido na cadeia de caracteres de consulta.

Os dois `ViewBag` as variáveis são usadas para que o modo de exibição pode configurar os hiperlinks de título de coluna com os valores de cadeia de caracteres de consulta apropriados:

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

Essas são instruções ternárias. A primeira delas Especifica que, se o `sortOrder` parâmetro é nulo ou vazio, `ViewBag.NameSortParm` deve ser definido como "nome\_desc"; caso contrário, ele deve ser definido como uma cadeia de caracteres vazia. Essas duas instruções permitem que a exibição defina os hiperlinks de título de coluna da seguinte maneira:

| Ordem de classificação atual | Hiperlink do sobrenome | Hiperlink de data |
| --- | --- | --- |
| Sobrenome ascendente | descending | ascending |
| Sobrenome descendente | ascending | ascending |
| Data ascendente | ascending | descending |
| Data descendente | ascending | ascending |

Usa o método [LINQ to Entities](/dotnet/framework/data/adonet/ef/language-reference/linq-to-entities) para especificar a coluna para classificar por. O código cria um <xref:System.Linq.IQueryable%601> variável antes do `switch` instrução, modifica-o na `switch` instrução e chama o `ToList` método após o `switch` instrução. Quando você cria e modifica variáveis `IQueryable`, nenhuma consulta é enviada para o banco de dados. A consulta não é executada até que você converta a `IQueryable` objeto em uma coleção chamando um método como `ToList`. Portanto, esse código resulta em uma única consulta que não é executada até que o `return View` instrução.

Como uma alternativa para escrever instruções LINQ diferentes para cada ordem de classificação, você pode criar dinamicamente uma instrução LINQ. Para obter informações sobre o LINQ dinâmico, consulte [LINQ dinâmico](https://go.microsoft.com/fwlink/?LinkID=323957).

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Adicionar hiperlinks de título de coluna à exibição índice de alunos

1. Na *Views\Student\Index.cshtml*, substitua o `<tr>` e `<th>` elementos para a linha de cabeçalho com o código realçado:

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cshtml?highlight=5-15)]

   Esse código usa as informações a `ViewBag` valores de cadeia de caracteres de propriedades para configurar hiperlinks com a consulta apropriada.

2. Execute a página e clique no **Sobrenome** e **data de registro** títulos de coluna para verificar se a classificação funciona.

   Depois de clicar na **Sobrenome** título, os alunos são exibidos na ordem do último nome decrescente.

## <a name="add-a-search-box"></a>Adicione uma caixa de pesquisa

Para adicionar filtragem à página de índice de alunos, você adicionará uma caixa de texto e um botão Enviar para o modo de exibição e fará alterações correspondentes no `Index` método. A caixa de texto permite inserir uma cadeia de caracteres a ser pesquisado nos campos de nome e último nome.

### <a name="add-filtering-functionality-to-the-index-method"></a>Adicionar a funcionalidade de filtragem a método Index

- Na *Controllers\StudentController.cs*, substitua o `Index` método com o código a seguir (as alterações são realçadas):

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs?highlight=1,7-11)]

O código adiciona uma `searchString` parâmetro para o `Index` método. O valor de cadeia de caracteres de pesquisa é recebido em uma caixa de texto que você adicionará à exibição Índice. Ele também adiciona um `where` cláusula à instrução LINQ que seleciona somente os alunos cujo nome ou sobrenome contém a cadeia de caracteres de pesquisa. A instrução que adiciona o <xref:System.Linq.Queryable.Where%2A> cláusula é executada somente se houver um valor a ser pesquisado.

> [!NOTE]
> Em muitos casos, você pode chamar o mesmo método em um conjunto de entidades do Entity Framework ou como um método de extensão em uma coleção em memória. Os resultados são normalmente os mesmos, mas em alguns casos podem ser diferentes.
>
> Por exemplo, a implementação do .NET Framework a `Contains` método retorna todas as linhas quando você passar uma cadeia de caracteres vazia para ele, mas o provedor do Entity Framework para SQL Server Compact 4.0 retorna zero linhas de cadeias de caracteres vazias. Portanto, o código de exemplo (colocando a `Where` instrução dentro de um `if` instrução) torna-se de que você obter os mesmos resultados para todas as versões do SQL Server. Além disso, a implementação do .NET Framework a `Contains` método executa uma comparação que diferencia maiusculas de minúsculas por padrão, mas os provedores SQL Server do Entity Framework executam comparações de maiusculas e minúsculas por padrão. Portanto, chamar o `ToUpper` método para fazer o teste de maiusculas e minúsculas explicitamente garante que eles não são alterados quando você altera o código mais tarde para usar um repositório, que retornará um `IEnumerable` coleção em vez de um `IQueryable` objeto. (Quando você chama o método `Contains` em uma coleção `IEnumerable`, obtém a implementação do .NET Framework; quando chama-o em um objeto `IQueryable`, obtém a implementação do provedor de banco de dados.)
>
> Tratamento de nulos também pode ser diferente para provedores de banco de dados diferente ou quando você usa um `IQueryable` objeto comparado a quando você usa um `IEnumerable` coleção. Por exemplo, em alguns cenários de um `Where` da condição, como `table.Column != 0` não pode retornar colunas que tenham `null` como o valor. Para obter mais informações, consulte [tratamento incorreto de nulos variáveis na cláusula 'where'](https://data.uservoice.com/forums/72025-entity-framework-feature-suggestions/suggestions/1015361-incorrect-handling-of-null-variables-in-where-cl).

### <a name="add-a-search-box-to-the-student-index-view"></a>Adicionar uma caixa de pesquisa à exibição índice de alunos

1. Na *Views\Student\Index.cshtml*, adicione o código realçado imediatamente antes da abertura `table` marca para criar uma legenda, uma caixa de texto e um **pesquisa** botão.

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml?highlight=4-11)]

2. Execute a página, insira uma cadeia de caracteres de pesquisa e clique em **pesquisa** para verificar se a filtragem está funcionando.

   Observe que a URL não contiver a "uma" cadeia de pesquisa, que significa que se você marcar essa página, você não obterá a lista filtrada quando você usa o indicador. Isso também se aplica os links de classificação de coluna, como eles serão classificar a lista inteira. Você alterará a **pesquisa** botão usar cadeias de caracteres de consulta para os critérios de filtro mais tarde no tutorial.

## <a name="add-paging"></a>Adicionar paginação

Para adicionar paginação à página de índice de alunos, comece instalando o **PagedList.Mvc** pacote do NuGet. Em seguida, você fará alterações adicionais na `Index` método e adicionar links de paginação para o `Index` modo de exibição. **PagedList.Mvc** é um dos muitos paginação boa e classificação pacotes para o ASP.NET MVC e seu uso aqui destina-se apenas como um exemplo, não como uma recomendação para que ele sobre outras opções.

### <a name="install-the-pagedlistmvc-nuget-package"></a>Instale o pacote PagedList.MVC NuGet

O NuGet **PagedList.Mvc** pacote instala automaticamente o **PagedList** pacote como uma dependência. O **PagedList** pacote instala um `PagedList` métodos de tipo e a extensão de coleção para `IQueryable` e `IEnumerable` coleções. Os métodos de extensão criam uma única página de dados em um `PagedList` coleção fora de seu `IQueryable` ou `IEnumerable`e o `PagedList` coleção fornece várias propriedades e métodos que facilitam a paginação. O **PagedList.Mvc** pacote instala um auxiliar de paginação que exibe os botões de paginação.

1. Dos **ferramentas** menu, selecione **Gerenciador de pacotes NuGet** e, em seguida, **Package Manager Console**.

2. No **Package Manager Console** janela, verifique se o **origem do pacote** é **nuget.org** e o **projeto padrão** é **ContosoUniversity**e, em seguida, digite o seguinte comando:

   ```text
   Install-Package PagedList.Mvc
   ```

3. Compile o projeto.

### <a name="add-paging-functionality-to-the-index-method"></a>Adicionar a funcionalidade de paginação ao método Index

1. Na *Controllers\StudentController.cs*, adicione uma `using` instrução para o `PagedList` namespace:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

2. Substitua o método `Index` pelo seguinte código:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs?highlight=1,3,7-16,41-43)]

   Este código adiciona um `page` parâmetro, um parâmetro de ordem de classificação atual e um parâmetro de filtro atual à assinatura do método:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

   Na primeira vez em que a página é exibida, ou se o usuário ainda não clicou em uma paginação ou classificação de link, todos os parâmetros são nulos. Se um link de paginação é clicado, o `page` variável contém o número da página para exibir.

   Um `ViewBag` propriedade fornece a exibição com a ordem de classificação atual, pois isso precisa ser incluído nos links de paginação para manter a ordem de classificação igual durante a paginação:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

   Outra propriedade, `ViewBag.CurrentFilter`, fornece a exibição com a cadeia de caracteres de filtro atual. Esse valor precisa ser incluído nos links de paginação para manter as configurações de filtro durante a paginação e precisa ser restaurado para a caixa de texto quando a página é exibida novamente. Se a cadeia de caracteres de pesquisa for alterada durante a paginação, a página precisará ser redefinida como 1, porque o novo filtro pode resultar na exibição de dados diferentes. A cadeia de caracteres de pesquisa é alterada quando um valor é inserido na caixa de texto e o botão Enviar é pressionado. Nesse caso, o `searchString` parâmetro não for nulo.

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

   No final do método, o `ToPagedList` método de extensão em que os alunos `IQueryable` objeto converte a consulta de alunos em uma única página de alunos em um tipo de coleção que dá suporte à paginação. A única página de alunos é então passada para o modo de exibição:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

   O método `ToPagedList` usa um número de página. Os dois pontos de interrogação representam o [operador de coalescência nula](/dotnet/csharp/language-reference/operators/null-coalescing-operator). O operador de união de nulo define um valor padrão para um tipo que permite valor nulo; a expressão `(page ?? 1)` significa retornar o valor de `page` se ele tiver um valor ou retornar 1 se `page` for nulo.

### <a name="add-paging-links-to-the-student-index-view"></a>Adicionar links de paginação à exibição índice de alunos

1. Na *Views\Student\Index.cshtml*, substitua o código existente pelo código a seguir. As alterações são realçadas.

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cshtml?highlight=1-3,6,9,14,17,24,30,55-56,58-59)]

   A instrução `@model` na parte superior da página especifica que a exibição agora obtém um objeto `PagedList`, em vez de um objeto `List`.

   O `using` instrução para `PagedList.Mvc` fornece acesso para o auxiliar do MVC para os botões de paginação.

   O código usa uma sobrecarga [BeginForm](/previous-versions/aspnet/dd492719(v=vs.108)) que permite que ele especifique [FormMethod.Get](/previous-versions/aspnet/dd460179(v=vs.100)).

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cshtml?highlight=1)]

   O padrão [BeginForm](/previous-versions/aspnet/dd492719(v=vs.108)) envia dados de formulário com um POST, o que significa que os parâmetros são passados no corpo da mensagem HTTP e não na URL como cadeias de caracteres de consulta. Quando você especifica HTTP GET, os dados de formulário são passados na URL como cadeias de consulta, o que permite aos usuários marcar a URL. O [diretrizes do W3C para o uso de HTTP GET](http://www.w3.org/2001/tag/doc/whenToUseGet.html) recomendável que você deve usar GET quando a ação não resulta em uma atualização.

   A caixa de texto é inicializada com a cadeia de caracteres de pesquisa atual quando você clica em uma nova página, você pode ver a cadeia de caracteres de pesquisa atual.

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cshtml?highlight=1)]

   Os links de cabeçalho de coluna usam a cadeia de caracteres de consulta para passar a cadeia de caracteres de pesquisa atual para o controlador, de modo que o usuário possa classificar nos resultados do filtro:

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cshtml?highlight=1)]

   O número atual de página e total de páginas é exibido.

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cshtml)]

   Se não houver nenhuma página para exibir, "Página 0 de 0" é mostrada. (Nesse caso, o número da página é maior que a contagem de páginas porque `Model.PageNumber` for 1, e `Model.PageCount` é 0.)

   Os botões de paginação são exibidos pelo `PagedListPager` auxiliar:

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml)]

   O `PagedListPager` auxiliar fornece várias opções que você pode personalizar, incluindo URLs e estilo. Para obter mais informações, consulte [TroyGoode / PagedList](https://github.com/TroyGoode/PagedList) no site do GitHub.

2. Execute a página.

   Clique nos links de paginação em ordens de classificação diferentes para verificar se a paginação funciona. Em seguida, insira uma cadeia de caracteres de pesquisa e tente fazer a paginação novamente para verificar se ela também funciona corretamente com a classificação e filtragem.

## <a name="create-an-about-page"></a>Criar uma página sobre

O Contoso University do site sobre a página, você exibirá quantos alunos se registraram para cada data de registro. Isso exige agrupamento e cálculos simples nos grupos. Para fazer isso, você fará o seguinte:

- Criar uma classe de modelo de exibição para os dados que você precisa passar para a exibição.
- Modificar a `About` método no `Home` controlador.
- Modificar o `About` modo de exibição.

### <a name="create-the-view-model"></a>Criar o modelo de exibição

Criar uma *ViewModels* pasta na pasta do projeto. Nessa pasta, adicione um arquivo de classe *EnrollmentDateGroup.cs* e substitua o código de modelo pelo código a seguir:

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cs)]

### <a name="modify-the-home-controller"></a>Modificar o controlador Home

1. Na *HomeController.cs*, adicione o seguinte `using` instruções na parte superior do arquivo:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cs)]

2. Adicione uma variável de classe para o contexto de banco de dados imediatamente após a chave de abertura para a classe:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cs?highlight=3)]

3. Substitua o método `About` pelo seguinte código:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample21.cs)]

   A instrução LINQ agrupa as entidades de alunos por data de registro, calcula o número de entidades em cada grupo e armazena os resultados em uma coleção de objetos de modelo de exibição `EnrollmentDateGroup`.

4. Adicionar um `Dispose` método:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample22.cs)]

### <a name="modify-the-about-view"></a>Modificar a exibição Sobre

1. Substitua o código na *Views\Home\About.cshtml* arquivo pelo código a seguir:

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample23.cshtml)]

2. Execute o aplicativo e clique no **sobre** link.

   Exibe a contagem de alunos para cada data de registro em uma tabela.

   ![About_page](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image9.png)

## <a name="get-the-code"></a>Obter o código

[Baixe o projeto concluído](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>Recursos adicionais

Links para outros recursos do Entity Framework pode ser encontrado na [acesso a dados ASP.NET – recursos recomendados](../../../../whitepapers/aspnet-data-access-content-map.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Adicionar links de classificação de coluna
> * Adicione uma caixa de pesquisa
> * Adicionar paginação
> * Criar uma página sobre

Avance para o próximo artigo para saber como usar a interceptação de comando e resiliência de conexão.
> [!div class="nextstepaction"]
> [Interceptação de comando e resiliência de Conexão](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application.md)