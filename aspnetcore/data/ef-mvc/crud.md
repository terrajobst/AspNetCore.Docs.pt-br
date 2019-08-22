---
title: 'Tutorial: Implementar a funcionalidade CRUD - ASP.NET MVC com EF Core'
description: Neste tutorial, você examinará e personalizará o código CRUD (criar, ler, atualizar e excluir) que o scaffolding do MVC cria automaticamente para você em controladores e exibições.
author: tdykstra
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/04/2019
ms.topic: tutorial
uid: data/ef-mvc/crud
ms.openlocfilehash: 975738200f9ea621c38b395be123b1d05c78298e
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886314"
---
# <a name="tutorial-implement-crud-functionality---aspnet-mvc-with-ef-core"></a>Tutorial: Implementar a funcionalidade CRUD - ASP.NET MVC com EF Core

No tutorial anterior, você criou um aplicativo MVC que armazena e exibe dados usando o Entity Framework e o LocalDB do SQL Server. Neste tutorial, você examinará e personalizará o código CRUD (criar, ler, atualizar e excluir) que o scaffolding do MVC cria automaticamente para você em controladores e exibições.

> [!NOTE]
> É uma prática comum implementar o padrão de repositório para criar uma camada de abstração entre o controlador e a camada de acesso a dados. Para manter esses tutoriais simples e com foco no ensino de como usar o Entity Framework em si, eles não usam repositórios. Para obter informações sobre repositórios com o EF, consulte [o último tutorial desta série](advanced.md).

Neste tutorial, você:

> [!div class="checklist"]
> * Personalizar a página Detalhes
> * Atualizar a página Criar
> * Atualizar a página Editar
> * Atualizar a página Excluir
> * Fechará conexões de banco de dados

## <a name="prerequisites"></a>Pré-requisitos

* [Introdução ao EF Core e ASP.NET Core MVC](intro.md)

## <a name="customize-the-details-page"></a>Personalizar a página Detalhes

O código gerado por scaffolding da página Índice de Alunos omitiu a propriedade `Enrollments`, porque essa propriedade contém uma coleção. Na página **Detalhes**, você exibirá o conteúdo da coleção em uma tabela HTML.

Em *Controllers/StudentsController.cs*, o método de ação para a exibição Detalhes usa o método `SingleOrDefaultAsync` para recuperar uma única entidade `Student`. Adicione um código que chama `Include`. Os métodos `ThenInclude` e `AsNoTracking`, conforme mostrado no código realçado a seguir.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

Os métodos `Include` e `ThenInclude` fazem com que o contexto carregue a propriedade de navegação `Student.Enrollments` e, dentro de cada registro, a propriedade de navegação `Enrollment.Course`.  Você aprenderá mais sobre esses métodos no tutorial [Ler dados relacionados](read-related-data.md).

O método `AsNoTracking` melhora o desempenho em cenários em que as entidades retornadas não serão atualizadas no tempo de vida do contexto atual. Você aprenderá mais sobre `AsNoTracking` ao final deste tutorial.

### <a name="route-data"></a>Dados de rota

O valor de chave que é passado para o método `Details` é obtido dos *dados de rota*. Dados de rota são dados que o associador de modelos encontrou em um segmento da URL. Por exemplo, a rota padrão especifica os segmentos de controlador, ação e ID:

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

Na URL a seguir, a rota padrão mapeia Instructor como o controlador, Index como a ação e 1 como a ID; esses são valores de dados de rota.

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

A última parte da URL ("?courseID=2021") é um valor de cadeia de caracteres de consulta. O associador de modelos passará o valor da ID para o parâmetro `id` do método `Index` se você passá-lo como um valor de cadeia de caracteres de consulta:

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

Na página Índice, as URLs de hiperlinks são criadas por instruções de auxiliar de marcação na exibição do Razor. No código Razor a seguir, o parâmetro `id` corresponde à rota padrão e, portanto, `id` é adicionada aos dados de rota.

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

Isso gera o seguinte HTML quando `item.ID` é 6:

```html
<a href="/Students/Edit/6">Edit</a>
```

No código a seguir Razor, `studentID` não corresponde a um parâmetro na rota padrão e, portanto, ela é adicionada como uma cadeia de caracteres de consulta.

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

Isso gera o seguinte HTML quando `item.ID` é 6:

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

Para obter mais informações sobre os auxiliares de marca, confira <xref:mvc/views/tag-helpers/intro>.

### <a name="add-enrollments-to-the-details-view"></a>Adicionar registros à exibição Detalhes

Abra *Views/Students/Details.cshtml*. Cada campo é exibido usando auxiliares `DisplayNameFor` e `DisplayFor`, conforme mostrado no seguinte exemplo:

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

Após o último campo e imediatamente antes da marcação `</dl>` de fechamento, adicione o seguinte código para exibir uma lista de registros:

[!code-html[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

Se o recuo do código estiver incorreto depois de colar o código, pressione CTRL-K-D para corrigi-lo.

Esse código percorre as entidades na propriedade de navegação `Enrollments`. Para cada registro, ele exibe o nome do curso e a nota. O título do curso é recuperado da entidade Course, que é armazenada na propriedade de navegação `Course` da entidade Enrollments.

Execute o aplicativo, selecione a guia **Alunos** e clique no link **Detalhes** de um aluno. Você verá a lista de cursos e notas do aluno selecionado:

![Página Detalhes do Aluno](crud/_static/student-details.png)

## <a name="update-the-create-page"></a>Atualizar a página Criar

Em *StudentsController.cs*, modifique o método HttpPost `Create` adicionando um bloco try-catch e removendo a ID do atributo `Bind`.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

Esse código adiciona a entidade Student criada pelo associador de modelos do ASP.NET Core MVC ao conjunto de entidades Student e, em seguida, salva as alterações no banco de dados. (Associador de modelos refere-se à funcionalidade do ASP.NET Core MVC que facilita o trabalho com os dados enviados por um formulário. Um associador de modelos converte os valores de formulário postados em tipos CLR e passa-os para o método de ação em parâmetros. Nesse caso, o associador de modelos cria uma instância de uma entidade Student usando valores de propriedade da coleção Form.)

Você removeu `ID` do atributo `Bind` porque a ID é o valor de chave primária que o SQL Server definirá automaticamente quando a linha for inserida. A entrada do usuário não define o valor da ID.

Além do atributo `Bind`, o bloco try-catch é a única alteração que você fez no código gerado por scaffolding. Se uma exceção que é derivada de `DbUpdateException` é capturada enquanto as alterações estão sendo salvas, uma mensagem de erro genérica é exibida. Às vezes, as exceções `DbUpdateException` são causadas por algo externo ao aplicativo, em vez de por um erro de programação e, portanto, o usuário é aconselhado a tentar novamente. Embora não implementado nesta amostra, um aplicativo de qualidade de produção registrará a exceção em log. Para obter mais informações, consulte a seção **Log para informações** em [Monitoramento e telemetria (criando aplicativos de nuvem do mundo real com o Azure)](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).

O atributo `ValidateAntiForgeryToken` ajuda a impedir ataques CSRF (solicitação intersite forjada). O token é injetado automaticamente na exibição pelo [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) e é incluído quando o formulário é enviado pelo usuário. O token é validado pelo atributo `ValidateAntiForgeryToken`. Para obter mais informações sobre o CSRF, consulte [Falsificação antissolicitação](../../security/anti-request-forgery.md).

<a id="overpost"></a>

### <a name="security-note-about-overposting"></a>Observação de segurança sobre o excesso de postagem

O atributo `Bind` que o código gerado por scaffolding inclui no método `Create` é uma maneira de proteger contra o excesso de postagem em cenários de criação. Por exemplo, suponha que a entidade Student inclua uma propriedade `Secret` que você não deseja que essa página da Web defina.

```csharp
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
    public DateTime EnrollmentDate { get; set; }
    public string Secret { get; set; }
}
```

Mesmo se você não tiver um campo `Secret` na página da Web, um invasor poderá usar uma ferramenta como o Fiddler ou escrever um JavaScript para postar um valor de formulário `Secret`. Sem o atributo `Bind` limitando os campos que o associador de modelos usa quando ele cria uma instância de Student, o associador de modelos seleciona esse valor de formulário `Secret` e usa-o para criar a instância da entidade Student. Em seguida, seja qual for o valor que o invasor especificou para o campo de formulário `Secret`, ele é atualizado no banco de dados. A imagem a seguir mostra a ferramenta Fiddler adicionando o campo `Secret` (com o valor "OverPost") aos valores de formulário postados.

![Fiddler adicionando o campo Secreto](crud/_static/fiddler.png)

Em seguida, o valor "OverPost" é adicionado com êxito à propriedade `Secret` da linha inserida, embora você nunca desejou que a página da Web pudesse definir essa propriedade.

Impeça o excesso de postagem em cenários de edição lendo a entidade do banco de dados primeiro e, em seguida, chamando `TryUpdateModel`, passando uma lista explícita de propriedades permitidas. Esse é o método usado nestes tutoriais.

Uma maneira alternativa de impedir o excesso de postagem preferida por muitos desenvolvedores é usar modelos de exibição em vez de classes de entidade com o model binding. Inclua apenas as propriedades que você deseja atualizar no modelo de exibição. Quando o associador de modelos MVC tiver concluído, copie as propriedades do modelo de exibição para a instância da entidade, opcionalmente usando uma ferramenta como o AutoMapper. Use `_context.Entry` na instância de entidade para definir seu estado como `Unchanged` e, em seguida, defina `Property("PropertyName").IsModified` como verdadeiro em cada propriedade da entidade incluída no modelo de exibição. Esse método funciona nos cenários de edição e criação.

### <a name="test-the-create-page"></a>Testar a página Criar

O código em *Views/Students/Create.cshtml* usa os auxiliares de marcação `label`, `input` e `span` (para mensagens de validação) para cada campo.

Execute o aplicativo, selecione a guia **Alunos** e, em seguida, clique em **Criar Novo**.

Insira nomes e uma data. Tente inserir uma data inválida se o navegador permitir fazer isso. (Alguns navegadores forçam o uso de um seletor de data.) Em seguida, clique em **Criar** para ver a mensagem de erro.

![Erro de validação de data](crud/_static/date-error.png)

Essa é a validação do lado do servidor que você obtém por padrão; em um tutorial posterior, você verá como adicionar atributos que gerarão o código para a validação do lado do cliente também. O código realçado a seguir mostra a verificação de validação de modelo no método `Create`.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

Altere a data para um valor válido e clique em **Criar** para ver o novo aluno ser exibido na página **Índice**.

## <a name="update-the-edit-page"></a>Atualizar a página Editar

Em *StudentController.cs*, o método HttpGet `Edit` (aquele sem o atributo `HttpPost`) usa o método `SingleOrDefaultAsync` para recuperar a entidade Student selecionada, como você viu no método `Details`. Não é necessário alterar esse método.

### <a name="recommended-httppost-edit-code-read-and-update"></a>Código HttpPost Edit recomendado: Ler e atualizar

Substitua o método de ação HttpPost Edit pelo código a seguir.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

Essas alterações implementam uma melhor prática de segurança para evitar o excesso de postagem. O scaffolder gerou um atributo `Bind` e adicionou a entidade criada pelo associador de modelos ao conjunto de entidades com um sinalizador `Modified`. Esse código não é recomendado para muitos cenários porque o atributo `Bind` limpa os dados pré-existentes nos campos não listados no parâmetro `Include`.

O novo código lê a entidade existente e chama `TryUpdateModel` para atualizar os campos na entidade recuperada [com base na entrada do usuário nos dados de formulário postados](xref:mvc/models/model-binding). O controle automático de alterações do Entity Framework define o sinalizador `Modified` nos campos alterados pela entrada de formulário. Quando o método `SaveChanges` é chamado, o Entity Framework cria instruções SQL para atualizar a linha de banco de dados. Os conflitos de simultaneidade são ignorados e somente as colunas de tabela que foram atualizadas pelo usuário são atualizadas no banco de dados. (Um tutorial posterior mostra como lidar com conflitos de simultaneidade.)

Como uma melhor prática para evitar o excesso de postagem, os campos que você deseja que sejam atualizáveis pela página **Editar** estão na lista de permissões nos parâmetros `TryUpdateModel`. (A cadeia de caracteres vazia antes da lista de campos na lista de parâmetros destina-se ao uso de um prefixo com os nomes de campos de formulário.) Atualmente, não há nenhum campo extra que está sendo protegido, mas listar os campos que você deseja que o associador de modelos associe garante que, se você adicionar campos ao modelo de dados no futuro, eles serão automaticamente protegidos até que você adicione-os aqui de forma explícita.

Como resultado dessas alterações, a assinatura do método HttpPost `Edit` é a mesma do método HttpGet `Edit`; portanto, você já renomeou o método `EditPost`.

### <a name="alternative-httppost-edit-code-create-and-attach"></a>Código HttpPost Edit alternativo: Criar e anexar

O código de edição HttpPost recomendado garante que apenas as colunas alteradas sejam atualizadas e preserva os dados nas propriedades que você não deseja incluir para o model binding. No entanto, a abordagem de primeira leitura exige uma leitura de banco de dados extra e pode resultar em um código mais complexo para lidar com conflitos de simultaneidade. Uma alternativa é anexar uma entidade criada pelo associador de modelos ao contexto do EF e marcá-la como modificada. (Não atualize o projeto com esse código; ele é mostrado somente para ilustrar uma abordagem opcional.)

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

Use essa abordagem quando a interface do usuário da página da Web incluir todos os campos na entidade e puder atualizar qualquer um deles.

O código gerado por scaffolding usa a abordagem "criar e anexar", mas apenas captura exceções `DbUpdateConcurrencyException` e retorna códigos de erro 404.  O exemplo mostrado captura qualquer exceção de atualização de banco de dados e exibe uma mensagem de erro.

### <a name="entity-states"></a>Estados da entidade

O contexto de banco de dados controla se as entidades em memória estão em sincronia com suas linhas correspondentes no banco de dados, e essas informações determinam o que acontece quando você chama o método `SaveChanges`. Por exemplo, quando você passa uma nova entidade para o método `Add`, o estado dessa entidade é definido como `Added`. Em seguida, quando você chama o método `SaveChanges`, o contexto de banco de dados emite um comando SQL INSERT.

Uma entidade pode estar em um dos seguintes estados:

* `Added`. A entidade ainda não existe no banco de dados. O método `SaveChanges` emite uma instrução INSERT.

* `Unchanged`. Nada precisa ser feito com essa entidade pelo método `SaveChanges`. Ao ler uma entidade do banco de dados, a entidade começa com esse status.

* `Modified`. Alguns ou todos os valores de propriedade da entidade foram modificados. O método `SaveChanges` emite uma instrução UPDATE.

* `Deleted`. A entidade foi marcada para exclusão. O método `SaveChanges` emite uma instrução DELETE.

* `Detached`. A entidade não está sendo controlada pelo contexto de banco de dados.

Em um aplicativo da área de trabalho, em geral, as alterações de estado são definidas automaticamente. Você lê uma entidade e faz alterações em alguns de seus valores de propriedade. Isso faz com que seu estado da entidade seja alterado automaticamente para `Modified`. Em seguida, quando você chama `SaveChanges`, o Entity Framework gera uma instrução SQL UPDATE que atualiza apenas as propriedades reais que você alterou.

Em um aplicativo Web, o `DbContext` que inicialmente lê uma entidade e exibe seus dados a serem editados é descartado depois que uma página é renderizada. Quando o método de ação HttpPost `Edit` é chamado, é feita uma nova solicitação da Web e você tem uma nova instância do `DbContext`. Se você ler novamente a entidade nesse novo contexto, simulará o processamento da área de trabalho.

Mas se você não desejar fazer a operação de leitura extra, precisará usar o objeto de entidade criado pelo associador de modelos.  A maneira mais simples de fazer isso é definir o estado da entidade como Modificado, como é feito no código HttpPost Edit alternativo mostrado anteriormente. Em seguida, quando você chama `SaveChanges`, o Entity Framework atualiza todas as colunas da linha de banco de dados, porque o contexto não tem como saber quais propriedades foram alteradas.

Caso deseje evitar a abordagem de primeira leitura, mas também deseje que a instrução SQL UPDATE atualize somente os campos que o usuário realmente alterar, o código será mais complexo. É necessário salvar os valores originais de alguma forma (por exemplo, usando campos ocultos) para que eles estejam disponíveis quando o método HttpPost `Edit` for chamado. Em seguida, você pode criar uma entidade Student usando os valores originais, chamar o método `Attach` com a versão original da entidade, atualizar os valores da entidade para os novos valores e, em seguida, chamar `SaveChanges`.

### <a name="test-the-edit-page"></a>Testar a página Editar

Execute o aplicativo, selecione a guia **Alunos** e, em seguida, clique em um hiperlink **Editar**.

![Página Editar Alunos](crud/_static/student-edit.png)

Altere alguns dos dados e clique em **Salvar**. A página **Índice** será aberta e você verá os dados alterados.

## <a name="update-the-delete-page"></a>Atualizar a página Excluir

Em *StudentController.cs*, o código de modelo para o método HttpGet `Delete` usa o método `SingleOrDefaultAsync` para recuperar a entidade Student selecionada, como você viu nos métodos Details e Edit. No entanto, para implementar uma mensagem de erro personalizada quando a chamada a `SaveChanges` falhar, você adicionará uma funcionalidade a esse método e à sua exibição correspondente.

Como você viu para operações de atualização e criação, as operações de exclusão exigem dois métodos de ação. O método chamado em resposta a uma solicitação GET mostra uma exibição que dá ao usuário uma oportunidade de aprovar ou cancelar a operação de exclusão. Se o usuário aprová-la, uma solicitação POST será criada. Quando isso acontece, o método HttpPost `Delete` é chamado e, em seguida, esse método executa, de fato, a operação de exclusão.

Você adicionará um bloco try-catch ao método HttpPost `Delete` para tratar os erros que podem ocorrer quando o banco de dados é atualizado. Se ocorrer um erro, o método HttpPost Delete chamará o método HttpGet Delete, passando a ele um parâmetro que indica que ocorreu um erro. Em seguida, o método HttpGet Delete exibe novamente a página de confirmação, junto com a mensagem de erro, dando ao usuário a oportunidade de cancelar ou tentar novamente.

Substitua o método de ação HttpGet `Delete` pelo código a seguir, que gerencia o relatório de erros.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

Este código aceita um parâmetro opcional que indica se o método foi chamado após uma falha ao salvar as alterações. Esse parâmetro é falso quando o método HttpGet `Delete` é chamado sem uma falha anterior. Quando ele é chamado pelo método HttpPost `Delete` em resposta a um erro de atualização de banco de dados, o parâmetro é verdadeiro, e uma mensagem de erro é passada para a exibição.

### <a name="the-read-first-approach-to-httppost-delete"></a>A abordagem de primeira leitura para HttpPost Delete

Substitua o método de ação HttpPost `Delete` (chamado `DeleteConfirmed`) pelo código a seguir, que executa a operação de exclusão real e captura os erros de atualização de banco de dados.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6-9,11-12,16-21)]

Esse código recupera a entidade selecionada e, em seguida, chama o método `Remove` para definir o status da entidade como `Deleted`. Quando `SaveChanges` é chamado, um comando SQL DELETE é gerado.

### <a name="the-create-and-attach-approach-to-httppost-delete"></a>A abordagem "criar e anexar" para HttpPost Delete

Se a melhoria do desempenho de um aplicativo de alto volume for uma prioridade, você poderá evitar uma consulta SQL desnecessária criando uma instância de uma entidade Student usando somente o valor de chave primária e, em seguida, definindo o estado da entidade como `Deleted`. Isso é tudo o que o Entity Framework precisa para excluir a entidade. (Não coloque esse código no projeto; ele está aqui apenas para ilustrar uma alternativa.)

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

Se a entidade tiver dados relacionados, eles também deverão ser excluídos. Verifique se a exclusão em cascata está configurada no banco de dados. Com essa abordagem para a exclusão de entidade, o EF talvez não perceba que há entidades relacionadas a serem excluídas.

### <a name="update-the-delete-view"></a>Atualizar a exibição Excluir

Em *Views/Student/Delete.cshtml*, adicione uma mensagem de erro entre o cabeçalho h2 e o cabeçalho h3, conforme mostrado no seguinte exemplo:

[!code-html[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

Execute o aplicativo, selecione a guia **Alunos** e, em seguida, clique em um hiperlink **Excluir**:

![Página Confirmação de exclusão](crud/_static/student-delete.png)

Clique em **Excluir**. A página Índice será exibida sem o aluno excluído. (Você verá um exemplo de código de tratamento de erro em ação no tutorial sobre simultaneidade.)

## <a name="close-database-connections"></a>Fechará conexões de banco de dados

Para liberar os recursos contidos em uma conexão de banco de dados, a instância de contexto precisa ser descartada assim que possível quando você tiver terminado. A [injeção de dependência](../../fundamentals/dependency-injection.md) interna do ASP.NET Core cuida dessa tarefa para você.

Em *Startup.cs*, chame o [método de extensão AddDbContext](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs) para provisionar a classe `DbContext` no contêiner de DI do ASP.NET Core. Esse método define o tempo de vida do serviço como `Scoped` por padrão. `Scoped` significa que o tempo de vida do objeto de contexto coincide com o tempo de vida da solicitação da Web, e o método `Dispose` será chamado automaticamente ao final da solicitação da Web.

## <a name="handle-transactions"></a>Lidar com transações

Por padrão, o Entity Framework implementa transações de forma implícita. Em cenários em que são feitas alterações em várias linhas ou tabelas e, em seguida, `SaveChanges` é chamado, o Entity Framework verifica automaticamente se todas as alterações tiveram êxito ou se falharam. Se algumas alterações forem feitas pela primeira vez e, em seguida, ocorrer um erro, essas alterações serão revertidas automaticamente. Para cenários em que você precisa de mais controle – por exemplo, se desejar incluir operações feitas fora do Entity Framework em uma transação –, consulte [Transações](/ef/core/saving/transactions).

## <a name="no-tracking-queries"></a>Consultas sem controle

Quando um contexto de banco de dados recupera linhas de tabela e cria objetos de entidade que as representam, por padrão, ele controla se as entidades em memória estão em sincronia com o que está no banco de dados. Os dados em memória atuam como um cache e são usados quando uma entidade é atualizada. Esse cache costuma ser desnecessário em um aplicativo Web porque as instâncias de contexto são normalmente de curta duração (uma nova é criada e descartada para cada solicitação) e o contexto que lê uma entidade normalmente é descartado antes que essa entidade seja usada novamente.

Desabilite o controle de objetos de entidade em memória chamando o método `AsNoTracking`. Os cenários típicos em que talvez você deseje fazer isso incluem os seguintes:

* Durante o tempo de vida do contexto, não é necessário atualizar entidades nem que o EF [carregue automaticamente as propriedades de navegação com entidades recuperadas por consultas separadas](read-related-data.md). Com frequência, essas condições são atendidas nos métodos de ação HttpGet de um controlador.

* Você está executando uma consulta que recupera um volume grande de dados e apenas uma pequena parte dos dados retornados será atualizada. Pode ser mais eficiente desativar o controle para a consulta grande e executar uma consulta posteriormente para as poucas entidades que precisam ser atualizadas.

* Você deseja anexar uma entidade para atualizá-la, mas anteriormente, recuperou a mesma entidade para uma finalidade diferente. Como a entidade já está sendo controlada pelo contexto de banco de dados, não é possível anexar a entidade que você deseja alterar. Uma maneira de lidar com essa situação é chamar `AsNoTracking` na consulta anterior.

Para obter mais informações, consulte [Controle vs. Sem controle](/ef/core/querying/tracking).

## <a name="get-the-code"></a>Obter o código

[Baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Personalizou a página Detalhes
> * Atualizou a página Criar
> * Atualizou a página Editar
> * Atualizou a página Excluir
> * Fechou conexões de banco de dados

Vá para o próximo tutorial para saber como expandir a funcionalidade da página **Índice** adicionando classificação, filtragem e paginação.

> [!div class="nextstepaction"]
> [Avançar: classificação, filtragem e paginação](sort-filter-page.md)
