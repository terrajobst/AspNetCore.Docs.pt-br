---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
title: 'Tutorial: Implementar a funcionalidade CRUD com o Entity Framework no ASP.NET MVC | Microsoft Docs'
description: Examinar e personalizar a criar, ler, atualizar, excluir (CRUD) código que o scaffolding do MVC cria automaticamente em controladores e modos de exibição.
author: tdykstra
ms.author: riande
ms.date: 01/11/2019
ms.topic: tutorial
ms.assetid: a2f70ba4-83d1-4002-9255-24732726c4f2
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 9c6f8f3a2ffc0a9c5e15111ae47c331dab24ff43
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341713"
---
# <a name="tutorial-implement-crud-functionality-with-the-entity-framework-in-aspnet-mvc"></a>Tutorial: Implementar a funcionalidade CRUD com o Entity Framework no ASP.NET MVC

No [tutorial anterior](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md), você criou um aplicativo MVC que armazena e exibe os dados usando o Entity Framework (EF) 6 e o LocalDB do SQL Server. Neste tutorial, você examine e personalize a criar, ler, atualizar, excluir (CRUD) código que o scaffolding do MVC cria automaticamente para você em controladores e modos de exibição.

> [!NOTE]
> É uma prática comum implementar o padrão de repositório para criar uma camada de abstração entre o controlador e a camada de acesso a dados. Para manter esses tutoriais simples e voltada para ensinar a usar o EF 6 em si, eles não usam repositórios. Para obter informações sobre como implementar repositórios, consulte o [mapa de conteúdo de acesso do ASP.NET dados](../../../../whitepapers/aspnet-data-access-content-map.md).

Aqui estão exemplos das páginas da web que você cria:

![Uma captura de tela da página de detalhes do aluno.](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image1.png)

![Uma captura de tela do aluno Criar página.](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image2.png)

![Uma captura de tela ot o aluno Excluir página.](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image3.png)

Neste tutorial, você:

> [!div class="checklist"]
> * Criar uma página de detalhes
> * Atualizar a página Criar
> * Atualize o método HttpPost Edit
> * Atualizar a página Excluir
> * Conexões de banco de dados fechar
> * Lidar com transações

## <a name="prerequisites"></a>Pré-requisitos

* [Criar o modelo de dados do Entity Framework](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md)

## <a name="create-a-details-page"></a>Criar uma página de detalhes

O código gerado automaticamente para os alunos `Index` página deixada de fora a `Enrollments` propriedade, porque essa propriedade contém uma coleção. No `Details` página, você exibirá o conteúdo da coleção em uma tabela HTML.

Na *Controllers\StudentController.cs*, o método de ação para o `Details` exibir usa o [localizar](https://msdn.microsoft.com/library/gg696418(v=VS.103).aspx) método para recuperar uma única `Student` entidade.

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample1.cs)]

O valor da chave é passado para o método como o `id` parâmetro e é proveniente *dados da rota* no **detalhes** hiperlink na página de índice.

### <a name="tip-route-data"></a>Dica: **Dados de rota**

Dados de rota são dados que o associador de modelo encontrado em um segmento de URL especificado na tabela de roteamento. Por exemplo, a rota padrão especifica `controller`, `action`, e `id` segmentos:

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample2.cs?highlight=3)]

Na URL a seguir, a rota padrão mapeia `Instructor` como o `controller`, `Index` como o `action` e 1 como o `id`; esses são valores de dados de rota.

`http://localhost:1230/Instructor/Index/1?courseID=2021`

`?courseID=2021` é um valor de cadeia de caracteres de consulta. O associador de modelo também funcionará se você passar o `id` como um valor de cadeia de caracteres de consulta:

`http://localhost:1230/Instructor/Index?id=1&CourseID=2021`

As URLs são criadas por `ActionLink` instruções na exibição do Razor. No código a seguir, o `id` parâmetro corresponde à rota padrão e, portanto, `id` é adicionado aos dados de rota.

[!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample3.cshtml)]

No código a seguir, `courseID` não corresponde a um parâmetro de rota padrão e, portanto, ele é adicionado como uma cadeia de caracteres de consulta.

[!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample4.cshtml)]

### <a name="to-create-the-details-page"></a>Para criar a página de detalhes

1. Abra *Views\Student\Details.cshtml*.

   Cada campo é exibido usando um `DisplayFor` auxiliares, conforme mostrado no exemplo a seguir:

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample5.cshtml)]

2. Após o `EnrollmentDate` campo e imediatamente antes do fechamento `</dl>` marca, adicione o código realçado para exibir uma lista de registros, conforme mostrado no exemplo a seguir:

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample6.cshtml?highlight=8-29)]

    Se o recuo do código estiver incorreto depois cole o código, pressione **Ctrl**+**K**, **Ctrl**+**1!d** para formatar -lo.

    Esse código percorre as entidades na propriedade de navegação `Enrollments`. Para cada `Enrollment` entidade na propriedade, ele exibe o título do curso e a classificação. O título do curso é recuperado do `Course` entidade que é armazenada em de `Course` propriedade de navegação do `Enrollments` entidade. Todos esses dados são recuperados do banco de dados automaticamente quando for necessário. Em outras palavras, você está usando o carregamento lento aqui. Você não especificou *o carregamento adiantado* para o `Courses` propriedade de navegação, portanto, os registros não foram recuperados na mesma consulta que obteve os alunos. Em vez disso, na primeira vez que você tentar acessar o `Enrollments` propriedade de navegação, uma nova consulta é enviada para o banco de dados para recuperar os dados. Você pode ler mais sobre o carregamento lento e o carregamento adiantado na [lendo dados relacionados](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) tutorial posteriormente nessa série.

3. Abra a página de detalhes, iniciando o programa (**Ctrl**+**F5**), selecionando o **alunos** guia e, em seguida, clicando no **detalhes** link para Alexander Carson. (Se você pressionar **Ctrl**+**F5** enquanto o *details. cshtml* arquivo estiver aberto, você receberá um erro HTTP 400. Isso ocorre porque o Visual Studio tenta executar a página de detalhes, mas ele não foi atingido por um link que especifica o aluno para exibir. Se isso acontecer, remover o "Aluno/detalhes" da URL e tente novamente, ou, feche o navegador, clique com botão direito no projeto e clique em **modo de exibição** > **exibir no navegador**.)

    Você pode ver a lista de cursos e notas do aluno selecionado.

4. Feche o navegador.

## <a name="update-the-create-page"></a>Atualizar a página Criar

1. Na *Controllers\StudentController.cs*, substitua o <xref:System.Web.Mvc.HttpPostAttribute> `Create` método de ação com o código a seguir. Esse código adiciona uma `try-catch` bloco e remove `ID` do <xref:System.Web.Mvc.BindAttribute> atributo para o método gerado por scaffolding:

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample7.cs?highlight=3,5-6,13-18)]

    Esse código adiciona o `Student` entidade criada pelo associador de modelo ASP.NET MVC para o `Students` entidade definida e, em seguida, salva as alterações no banco de dados. *Associador de modelo* refere-se a funcionalidade do ASP.NET MVC que torna mais fácil para você trabalhar com os dados enviados por um formulário; um associador de modelo converte postado formulário valores para tipos CLR e passa para o método de ação em parâmetros. Nesse caso, o associador de modelo cria uma instância de um `Student` os valores de entidade usando a propriedade do `Form` coleção.

    Você removeu `ID` da associação de atributo porque `ID` é o valor de chave primária do SQL Server será definido automaticamente quando a linha é inserida. Entrada do usuário não define o `ID` valor.

    <a id="overpost"></a>

    ### <a name="security-warning---the-validateantiforgerytoken-attribute-helps-prevent-cross-site-request-forgerysecurityxsrfcsrf-prevention-in-aspnet-mvc-and-web-pagesmd-attacks-it-requires-a-corresponding-htmlantiforgerytoken-statement-in-the-view-which-youll-see-later"></a>Aviso de segurança - a `ValidateAntiForgeryToken` atributo ajuda a impedir [falsificação de solicitação intersite](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md) ataques. Ele requer um correspondente `Html.AntiForgeryToken()` instrução no modo de exibição, você verá mais tarde.

    O `Bind` atributo é uma maneira de proteger contra *overposting* em cenários de criação. Por exemplo, suponha que o `Student` entidade inclui um `Secret` propriedade que você não deseja que essa página da web para definir.

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample8.cs?highlight=7)]

    Mesmo se você não tiver um `Secret` campo na página da web, um hacker poderia usar uma ferramenta como [fiddler](http://fiddler2.com/home), ou escrever um JavaScript para postar um `Secret` valor de formulário. Sem o <xref:System.Web.Mvc.BindAttribute> atributo limitando os campos que o associador de modelos usa quando cria uma `Student` instância<em>,</em> associador de modelos seleciona esse `Secret` valor de formulário e usá-lo para criar o `Student` instância de entidade. Em seguida, seja qual for o valor que o invasor especificou para o campo de formulário `Secret`, ele é atualizado no banco de dados. A imagem a seguir mostra o fiddler ferramenta adicionando o `Secret` campo (com o valor "OverPost") para os valores de formulário postados.

    ![](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image5.png)

    Em seguida, o valor "OverPost" é adicionado com êxito à propriedade `Secret` da linha inserida, embora você nunca desejou que a página da Web pudesse definir essa propriedade.

    É melhor usar o parâmetro `Include` com o atributo `Bind` em campos de *lista de permissão*. Também é possível usar o parâmetro `Exclude` para campos de *lista de bloqueio* que deseja excluir. O motivo `Include` é mais seguro é que quando você adiciona uma nova propriedade à entidade, o novo campo não será automaticamente protegido por um `Exclude` lista.

    Você pode impedir o excesso de postagem em cenários de edição é ler a entidade do banco de dados primeiro e, em seguida, chamando `TryUpdateModel`, passando uma lista explícita de propriedades permitidas. Que é o método usado nestes tutoriais.

    Uma maneira alternativa para evitar o excesso de postagem preferido por muitos desenvolvedores é usar modelos de exibição em vez de classes de entidade com ligação de modelo. Inclua apenas as propriedades que você deseja atualizar no modelo de exibição. Quando o associador de modelos MVC tiver terminado, copie as propriedades do modelo de exibição para a instância da entidade, opcionalmente usando uma ferramenta como [AutoMapper](http://automapper.org/). Use o banco de dados. Entrada na instância de entidade para definir seu estado como não alterado e, em seguida, definir Property("PropertyName"). IsModified como true em cada propriedade de entidade que está incluída no modelo de exibição. Esse método funciona nos cenários de edição e criação.

    Diferente de `Bind` atributo, o `try-catch` bloco é a única alteração que você fez o código gerado por scaffolding. Se uma exceção que é derivada de <xref:System.Data.DataException> é capturada enquanto as alterações estão sendo salvas, uma mensagem de erro genérica é exibida. Às vezes, as exceções <xref:System.Data.DataException> são causadas por algo externo ao aplicativo, em vez de por um erro de programação e, portanto, o usuário é aconselhado a tentar novamente. Embora não implementado nesta amostra, um aplicativo de qualidade de produção registrará a exceção em log. Para obter mais informações, consulte a seção **Log para informações** em [Monitoramento e telemetria (criando aplicativos de nuvem do mundo real com o Azure)](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry.md#log).

    O código na *Views\Student\Create.cshtml* é semelhante ao que você viu na *details. cshtml*, exceto pelo fato `EditorFor` e `ValidationMessageFor` auxiliares são usados para cada campo em vez de `DisplayFor`. Aqui está o código relevante:

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample9.cshtml)]

    *Create.chstml* também inclui `@Html.AntiForgeryToken()`, que funciona com o `ValidateAntiForgeryToken` atributo no controlador para ajudar a evitar [falsificação de solicitação intersite](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md) ataques.

    Nenhuma alteração é necessária no *Create. cshtml*.

2. Execute a página iniciando o programa selecionando o **alunos** guia e, em seguida, clicando em **criar novo**.

3. Insira nomes e uma data inválida e clique em **criar** para ver a mensagem de erro.

    Essa é a validação do lado do servidor que você obtém por padrão. Um tutorial posterior, você verá como adicionar atributos que geram o código para a validação do lado do cliente. O seguinte código realçado mostra a verificação de validação no modelo de **criar** método.

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample10.cs?highlight=1)]

4. Altere a data para um valor válido e clique em **Criar** para ver o novo aluno ser exibido na página **Índice**.

5. Feche o navegador.

## <a name="update-httppost-edit-method"></a>Atualizar o método HttpPost Edit

1. Substitua os <xref:System.Web.Mvc.HttpPostAttribute> `Edit` método de ação com o código a seguir:

   [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample11.cs)]

   > [!NOTE]
   > Na *Controllers\StudentController.cs*, o `HttpGet Edit` método (aquele sem o `HttpPost` atributo) usa o `Find` método para recuperar o selecionado `Student` entidade, como você viu no `Details`método. Não é necessário alterar esse método.

   Essas alterações implementam uma prática recomendada de segurança para impedir [excesso de postagem](#overpost), o scaffolder gerado um `Bind` de atributos e adicionados a entidade criada pelo associador de modelos ao conjunto de entidades com um sinalizador modificado. Código não é recomendado porque o `Bind` atributo limpa os dados pré-existentes nos campos não listados no `Include` parâmetro. No futuro, o scaffolder de controlador MVC será atualizado para que ele não gera `Bind` atributos para métodos de edição.

   O novo código lê a entidade existente e chama <xref:System.Web.Mvc.Controller.TryUpdateModel%2A> atualizar campos de entrada do usuário nos dados de formulário postados. Conjuntos de controle automático de alterações do Entity Framework a [EntityState.Modified](<xref:System.Data.EntityState.Modified>) sinalizador na entidade. Quando o [SaveChanges](https://msdn.microsoft.com/library/system.data.entity.dbcontext.savechanges(v=VS.103).aspx) método é chamado, o <xref:System.Data.EntityState.Modified> sinalizador faz com que o Entity Framework criar instruções SQL para atualizar a linha de banco de dados. [Conflitos de simultaneidade](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md) são ignorados, e todas as colunas da linha de banco de dados são atualizadas, incluindo aqueles que o usuário não tiver sido alterado. (Um tutorial posterior mostra como tratar conflitos de simultaneidade, e se você quiser apenas campos individuais a serem atualizados no banco de dados, você pode definir a entidade para [EntityState.Unchanged](<xref:System.Data.EntityState.Unchanged>) e defina os campos individuais como [ EntityState.Modified](<xref:System.Data.EntityState.Modified>).)

   Para evitar o excesso de postagem, os campos que você deseja que sejam atualizáveis pela página de edição estão na lista de permissões no `TryUpdateModel` parâmetros. Atualmente, não há nenhum campo extra que está sendo protegido, mas listar os campos que você deseja que o associador de modelos associe garante que, se você adicionar campos ao modelo de dados no futuro, eles serão automaticamente protegidos até que você adicione-os aqui de forma explícita.

   Como resultado dessas alterações, a assinatura do método do método HttpPost Edit é o mesmo que o método de edição HttpGet; Portanto, você já renomeou o método EditPost.

   > [!TIP]
   >
   > **Estados da entidade e os métodos de SaveChanges e anexar**
   >
   > O contexto de banco de dados controla se as entidades em memória estão em sincronia com suas linhas correspondentes no banco de dados, e essas informações determinam o que acontece quando você chama o método `SaveChanges`. Por exemplo, quando você passa uma nova entidade para o [Add](https://msdn.microsoft.com/library/system.data.entity.dbset.add(v=vs.103).aspx) método, o que o estado da entidade é definido como `Added`. Em seguida, quando você chama o [SaveChanges](https://msdn.microsoft.com/library/system.data.entity.dbcontext.savechanges(v=VS.103).aspx) método, o contexto de banco de dados emite um SQL `INSERT` comando.
   >
   > Uma entidade pode estar em um dos seguintes [estados](xref:System.Data.EntityState):
   >
   > - `Added`. A entidade ainda não existe no banco de dados. O `SaveChanges` método deve emitir uma `INSERT` instrução.
   > - `Unchanged`. Nada precisa ser feito com essa entidade pelo método `SaveChanges`. Ao ler uma entidade do banco de dados, a entidade começa com esse status.
   > - `Modified`. Alguns ou todos os valores de propriedade da entidade foram modificados. O `SaveChanges` método deve emitir uma `UPDATE` instrução.
   > - `Deleted`. A entidade foi marcada para exclusão. O `SaveChanges` método deve emitir uma `DELETE` instrução.
   > - `Detached`. A entidade não está sendo controlada pelo contexto de banco de dados.
   >
   > Em um aplicativo da área de trabalho, em geral, as alterações de estado são definidas automaticamente. Em um tipo de área de trabalho do aplicativo, você lê uma entidade e fazer alterações em alguns de seus valores de propriedade. Isso faz com que seu estado da entidade seja alterado automaticamente para `Modified`. Em seguida, quando você chama `SaveChanges`, o Entity Framework gera um SQL `UPDATE` instrução que atualiza apenas as propriedades reais que você alterou.
   >
   > A natureza desconectada do aplicativos web não permite que essa sequência contínua. O [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx) que lê uma entidade é descartada depois que uma página é renderizada. Quando o `HttpPost` `Edit` método de ação é chamado, uma nova solicitação é feita e você tem uma nova instância das [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx), para que você precise definir manualmente o estado da entidade para `Modified.` e em seguida, quando você chama `SaveChanges`, o Entity Framework atualiza todas as colunas da linha de banco de dados, porque o contexto não tem como saber quais propriedades foram alteradas.
   >
   > Se você quiser que o SQL `Update` instrução para atualizar somente os campos que o usuário realmente foram alterado, você pode salvar os valores originais de alguma forma (como campos ocultos) para que eles estejam disponíveis quando o `HttpPost` `Edit` método é chamado. Em seguida, você pode criar uma `Student` entidade usando os valores originais, chamada a `Attach` método com a versão original da entidade, atualize os valores da entidade para os novos valores e, em seguida, chame `SaveChanges.` para obter mais informações, consulte [ Estados da entidade e SaveChanges](/ef/ef6/saving/change-tracking/entity-state) e [dados locais](/ef/ef6/querying/local-data).

   O código HTML e Razor no *Views\Student\Edit.cshtml* é semelhante ao que você viu na *Create. cshtml*, e nenhuma alteração é necessária.

2. Execute a página iniciando o programa selecionando o **alunos** guia e, em seguida, clicando em um **editar** hiperlink.

3. Altere alguns dos dados e clique em **Salvar**. Você verá os dados alterados na página de índice.

4. Feche o navegador.

## <a name="update-the-delete-page"></a>Atualizar a página Excluir

No *Controllers\StudentController.cs*, o código de modelo para o <xref:System.Web.Mvc.HttpGetAttribute> `Delete` método usa o `Find` método para recuperar o selecionados `Student` entidade, como você viu no `Details` e `Edit` métodos. No entanto, para implementar uma mensagem de erro personalizada quando a chamada a `SaveChanges` falhar, você adicionará uma funcionalidade a esse método e à sua exibição correspondente.

Como você viu para operações de atualização e criação, as operações de exclusão exigem dois métodos de ação. O método que é chamado em resposta a uma solicitação GET mostra uma exibição que dá ao usuário a oportunidade de aprovar ou cancelar a operação de exclusão. Se o usuário aprová-la, uma solicitação POST será criada. Quando isso acontece, o `HttpPost` `Delete` método é chamado e, em seguida, esse método, na verdade, executa a operação de exclusão.

Você adicionará uma `try-catch` bloco para o <xref:System.Web.Mvc.HttpPostAttribute> `Delete` método para tratar os erros que podem ocorrer quando o banco de dados é atualizado. Se ocorrer um erro, o <xref:System.Web.Mvc.HttpPostAttribute> `Delete` chamadas de método de <xref:System.Web.Mvc.HttpGetAttribute> `Delete` método, passando um parâmetro que indica que ocorreu um erro. O <xref:System.Web.Mvc.HttpGetAttribute> `Delete` método, em seguida, exibe novamente a página de confirmação, junto com a mensagem de erro, dando ao usuário uma oportunidade de cancelar ou tentar novamente.

1. Substitua os <xref:System.Web.Mvc.HttpGetAttribute> `Delete` método de ação com o código a seguir, que gerencia o relatório de erros:

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample12.cs?highlight=1,7-10)]

    Este código aceita uma [parâmetro opcional](https://msdn.microsoft.com/library/dd264739.aspx) que indica se o método foi chamado após uma falha ao salvar as alterações. Esse parâmetro é `false` quando o `HttpGet` `Delete` método for chamado sem uma falha anterior. Quando ele é chamado o `HttpPost` `Delete` método em resposta a um erro de atualização de banco de dados, o parâmetro é `true` e uma mensagem de erro é passada para o modo de exibição.

2. Substitua os <xref:System.Web.Mvc.HttpPostAttribute> `Delete` método de ação (chamado `DeleteConfirmed`) com o código a seguir, que executa a operação de exclusão real e captura os erros de atualização do banco de dados.

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample13.cs)]

    Esse código recupera a entidade selecionada, em seguida, chama o [remova](https://msdn.microsoft.com/library/system.data.entity.dbset.remove(v=vs.103).aspx) método para definir o status da entidade `Deleted`. Quando `SaveChanges` é chamado, um SQL `DELETE` comando é gerado. Você também alterou o nome do método de ação de `DeleteConfirmed` para `Delete`. O código gerado por scaffolding chamado a `HttpPost` `Delete` método `DeleteConfirmed` para dar o `HttpPost` método uma assinatura exclusiva. (O CLR exige que métodos sobrecarregados tenham diferentes parâmetros de método.) Agora que as assinaturas são exclusivas, você pode continuar com a convenção do MVC e usar o mesmo nome para o `HttpPost` e `HttpGet` excluir métodos.

    Se a melhoria do desempenho de um aplicativo de alto volume for uma prioridade, você poderia evitar uma consulta SQL desnecessária para recuperar a linha, substituindo as linhas de código que chamem o `Find` e `Remove` métodos com o código a seguir:

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample14.cs)]

    Esse código instancia uma `Student` entidade usando apenas o valor de chave primária e, em seguida, define o estado da entidade para `Deleted`. Isso é tudo o que o Entity Framework precisa para excluir a entidade.

    Conforme observado, o `HttpGet` `Delete` método não exclui os dados. Executando uma operação de exclusão em resposta a um GET de solicitação (ou para fato, a execução de qualquer operação de edição, criação ou qualquer outra operação que altera os dados) cria um risco à segurança. Para obter mais informações, consulte [ASP.NET MVC dica n º 46 — não use Excluir Links porque eles criaram buracos na segurança](http://stephenwalther.com/blog/archive/2009/01/21/asp.net-mvc-tip-46-ndash-donrsquot-use-delete-links-because.aspx) no blog de Stephen Walther.

3. Na *Views\Student\Delete.cshtml*, adicione uma mensagem de erro entre o `h2` título e o `h3` título, conforme mostrado no exemplo a seguir:

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample15.cshtml?highlight=2)]

4. Execute a página iniciando o programa selecionando o **alunos** guia e, em seguida, clicando em um **excluir** hiperlink.

5. Escolher **exclua** na página que diz **tem certeza de que você deseja excluir este?**.

    Exibe a página de índice sem o aluno excluído. (Você verá um exemplo de código na ação de manipulação de erros do [tutorial de simultaneidade](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md).)

## <a name="close-database-connections"></a>Conexões de banco de dados fechar

Para fechar conexões de banco de dados e liberar os recursos que eles contêm assim que possível, descarte a instância de contexto quando você terminar com ele. Isto é porque o código gerado por scaffolding fornece um [Dispose](https://msdn.microsoft.com/library/system.idisposable.dispose(v=vs.110).aspx) método no final do `StudentController` classe *StudentController.cs*, conforme mostrado no exemplo a seguir:

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample16.cs)]

A base `Controller` já classe implementa o `IDisposable` interface, portanto, esse código simplesmente adiciona uma substituição para o `Dispose(bool)` método para descartar explicitamente a instância do contexto.

## <a name="handle-transactions"></a>Lidar com transações

Por padrão, o Entity Framework implementa transações de forma implícita. Em cenários em que você pode fazer alterações em várias linhas ou tabelas e, em seguida, chama `SaveChanges`, automaticamente, o Entity Framework torna-se de que todas as suas alterações ter êxito ou falhar. Se algumas alterações forem feitas pela primeira vez e, em seguida, ocorrer um erro, essas alterações serão revertidas automaticamente. Para cenários em que você precisa de mais controle&mdash;por exemplo, se você quiser incluir operações feitas fora do Entity Framework em uma transação&mdash;consulte [trabalhando com transações](/ef/ef6/saving/transactions).

## <a name="additional-resources"></a>Recursos adicionais

Agora você tem um conjunto completo de páginas que executam operações CRUD simples para `Student` entidades. Você usou os auxiliares de MVC para gerar os elementos de interface do usuário para campos de dados. Para obter mais informações sobre os auxiliares de MVC, consulte [auxiliares de HTML usando um formato de renderização](/previous-versions/aspnet/dd410596(v=vs.98)) (o artigo é para o MVC 3, mas ainda é relevante para o MVC 5).

Encontre links para outros recursos do EF 6 [acesso a dados ASP.NET – recursos recomendados](../../../../whitepapers/aspnet-data-access-content-map.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Uma página de detalhes
> * Atualizada a página criar
> * Atualizado o método HttpPost Edit
> * Atualizada a página Excluir
> * Conexões fechadas de banco de dados
> * Transações de manipulado

Avance para o próximo artigo para aprender a adicionar a classificação, filtragem e paginação ao projeto.
> [!div class="nextstepaction"]
> [Classificação, filtragem e paginação](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md)