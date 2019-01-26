---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application
title: 'Tutorial: Saiba mais sobre cenários avançados do EF para um aplicativo Web do MVC 5'
description: Este tutorial inclui apresenta vários tópicos que são úteis para consideração quando você vai além das noções básicas de desenvolvimento de aplicativos web ASP.NET que usam o Entity Framework Code First.
author: tdykstra
ms.author: riande
ms.date: 01/22/2019
ms.topic: tutorial
ms.assetid: f35a9b0c-49ef-4cde-b06d-19d1543feb0b
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application
msc.type: authoredcontent
ms.openlocfilehash: d0208c8890467ec6044d807aeee7c7ae02e18790
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889997"
---
# <a name="tutorial-learn-about-advanced-ef-scenarios-for-an-mvc-5-web-app"></a>Tutorial: Saiba mais sobre cenários avançados do EF para um aplicativo Web do MVC 5

No tutorial anterior você implementou a herança de tabela por hierarquia. Este tutorial inclui apresenta vários tópicos que são úteis para consideração quando você vai além das noções básicas de desenvolvimento de aplicativos web ASP.NET que usam o Entity Framework Code First. As seções primeiro tem instruções passo a passo que explicam como o código e usando o Visual Studio para concluir as tarefas de seções a seguir apresentam vários tópicos com breves introduções seguidas de links para recursos para obter mais informações.

Para a maioria desses tópicos, você trabalhará com as páginas que você já criou. Para usar o SQL bruto para fazer atualizações em massa, você criará uma nova página que atualiza o número de créditos de todos os cursos no banco de dados:

![Update_Course_Credits_initial_page](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image1.png)

Neste tutorial, você:

> [!div class="checklist"]
> * Executar consultas SQL brutas
> * Executar consultas sem controle
> * Examinar o SQL consultas enviadas ao banco de dados

Você também aprenderá sobre:

> [!div class="checklist"]
> * Criando uma camada de abstração
> * Classes de proxy
> * Detecção automática de alterações
> * Validação automática
> * Entity Framework Power Tools
> * Código de origem do Entity Framework

## <a name="prerequisite"></a>Pré-requisito

* [Implementação de herança](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="perform-raw-sql-queries"></a>Executar consultas SQL brutas

A API do Entity Framework Code First inclui métodos que permitem passar comandos SQL diretamente para o banco de dados. Você tem as seguintes opções:

- Use o [DbSet.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.dbset.sqlquery.aspx) método para consultas que retornam tipos de entidade. Os objetos retornados devem ser do tipo esperado pelo `DbSet` objeto e elas são rastreadas automaticamente pelo contexto de banco de dados, a menos que você desative o controle. (Consulte a seção a seguir o [AsNoTracking](https://msdn.microsoft.com/library/system.data.entity.dbextensions.asnotracking.aspx) método.)
- Use o [Database.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.database.sqlquery.aspx) método para consultas que retornam tipos que não são entidades. Os dados retornados não são controlados pelo contexto de banco de dados, mesmo se esse método é usado para recuperar tipos de entidade.
- Use o [Database.ExecuteSqlCommand](https://msdn.microsoft.com/library/gg679456.aspx) para comandos sem consulta.

Uma das vantagens de usar o Entity Framework é que ele evita vincular o código de forma muito próxima a um método específico de armazenamento de dados. Ele faz isso pela geração de consultas SQL e comandos para você, que também libera você da necessidade de escrevê-los. Mas há casos excepcionais, quando você precisa executar consultas SQL específicas que você criou manualmente, e esses métodos tornam possível para que você possa lidar com essas exceções.

Como é sempre verdadeiro quando você executa comandos SQL em um aplicativo Web, é necessário tomar precauções para proteger o site contra ataques de injeção de SQL. Uma maneira de fazer isso é usar consultas parametrizadas para garantir que as cadeias de caracteres enviadas por uma página da Web não possam ser interpretadas como comandos SQL. Neste tutorial, você usará consultas parametrizadas ao integrar a entrada do usuário a uma consulta.

### <a name="calling-a-query-that-returns-entities"></a>Chamar uma consulta que retorna entidades

O [DbSet&lt;TEntity&gt; ](https://msdn.microsoft.com/library/gg696460.aspx) classe fornece um método que você pode usar para executar uma consulta que retorna uma entidade do tipo `TEntity`. Para ver como isso funciona, você alterará o código a `Details` método da `Department` controlador.

Na *DepartmentController.cs*, no `Details` método, substitua o `db.Departments.FindAsync` chamada de método com um `db.Departments.SqlQuery` chamada de método, conforme mostrado no seguinte código realçado:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample1.cs?highlight=8-14)]

Para verificar se o novo código funciona corretamente, selecione a guia **Departamentos** e, em seguida, **Detalhes** de um dos departamentos. Verifique se todos os dados exibe conforme o esperado.

### <a name="calling-a-query-that-returns-other-types-of-objects"></a>Chamar uma consulta que retorna outros tipos de objetos

Anteriormente, você criou uma grade de estatísticas de alunos para a página Sobre que mostrava o número de alunos para cada data de registro. O código que faz isso no *HomeController.cs* usa LINQ:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample2.cs)]

Suponha que você deseja escrever o código que recupera esses dados diretamente no SQL em vez de usar o LINQ. Para fazer isso, você precisa executar uma consulta que retorna algo diferente de objetos de entidade, que significa que você precisa usar o [Database.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.database.sqlquery(v=VS.103).aspx) método.

Na *HomeController.cs*, substitua a instrução LINQ no `About` método com uma instrução SQL, conforme mostrado no seguinte código realçado:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample3.cs?highlight=3-18)]

Execute a página sobre. Verifique se que ele exibe os mesmos dados que antes.

### <a name="calling-an-update-query"></a>Chamar uma consulta Update

Suponha que os administradores da Contoso University desejam ser capaz de realizar alterações em massa no banco de dados, como alterar o número de créditos para cada curso. Se a universidade tiver uma grande quantidade de cursos, poderá ser ineficiente recuperá-los como entidades e alterá-los individualmente. Nesta seção, você implementará uma página da web que permite ao usuário especificar um fator pelo qual alterar o número de créditos para todos os cursos e fará a alteração executando um SQL `UPDATE` instrução. 

Na *CourseContoller.cs*, adicione `UpdateCourseCredits` métodos para `HttpGet` e `HttpPost`:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample4.cs)]

Quando o controlador processa uma `HttpGet` solicitação, nada é retornado no `ViewBag.RowsAffected` variável e o modo de exibição exibe uma caixa de texto vazia e um botão Enviar.

Quando o **atualização** botão é clicado, o `HttpPost` método é chamado, e `multiplier` tem o valor inserido na caixa de texto. O código, em seguida, executa o SQL que atualiza os cursos e retorna o número de linhas afetadas para o modo de exibição de `ViewBag.RowsAffected` variável. Quando a exibição obtém um valor nessa variável, ele exibe o número de linhas atualizadas em vez da caixa de texto e botão Enviar.

Na *CourseController.cs*, clique em um dos `UpdateCourseCredits` métodos e depois clique em **adicionar exibição**. O **adicionar exibição** caixa de diálogo é exibida. Deixe os padrões e selecione **adicionar**.

Na *Views\Course\UpdateCourseCredits.cshtml*, substitua o código de modelo pelo código a seguir:

[!code-cshtml[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample5.cshtml)]

Execute o método `UpdateCourseCredits` selecionando a guia **Cursos**, adicionando, em seguida, "/UpdateCourseCredits" ao final da URL na barra de endereços do navegador (por exemplo: `http://localhost:50205/Course/UpdateCourseCredits`). Insira um número na caixa de texto:

![Update_Course_Credits_initial_page_with_2_entered](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image1.png)

Clique em **Atualizar**. Você ver o número de linhas afetadas.

Clique em **Voltar para a Lista** para ver a lista de cursos com o número revisado de créditos.

Para obter mais informações sobre consultas SQL brutas, consulte [consultas SQL brutas](https://msdn.microsoft.com/data/jj592907) no MSDN.

## <a name="no-tracking-queries"></a>Consultas sem controle

Quando um contexto de banco de dados recupera linhas de tabela e cria objetos de entidade que as representam, por padrão, ele controla se as entidades em memória estão em sincronia com o que está no banco de dados. Os dados em memória atuam como um cache e são usados quando uma entidade é atualizada. Esse cache costuma ser desnecessário em um aplicativo Web porque as instâncias de contexto são normalmente de curta duração (uma nova é criada e descartada para cada solicitação) e o contexto que lê uma entidade normalmente é descartado antes que essa entidade seja usada novamente.

Você pode desabilitar o controle de objetos de entidade na memória usando o [AsNoTracking](https://msdn.microsoft.com/library/gg679352(v=vs.103).aspx) método. Os cenários típicos em que talvez você deseje fazer isso incluem os seguintes:

- Uma consulta recupera um grande volume de dados que podem aprimorar o desempenho visivelmente desativar o rastreamento.
- Você deseja anexar uma entidade para atualizá-lo, mas você recuperou anteriormente a mesma entidade para uma finalidade diferente. Como a entidade já está sendo controlada pelo contexto de banco de dados, não é possível anexar a entidade que você deseja alterar. Uma maneira de lidar com essa situação é usar o `AsNoTracking` opção com a consulta anterior.

Para obter um exemplo que demonstra como usar o [AsNoTracking](https://msdn.microsoft.com/library/gg679352(v=vs.103).aspx) método, consulte [a versão anterior deste tutorial](../../older-versions/getting-started-with-ef-5-using-mvc-4/advanced-entity-framework-scenarios-for-an-mvc-web-application.md). Esta versão do tutorial não define o sinalizador modificado em uma entidade de associador de modelo criado no método de edição, portanto, não precisa `AsNoTracking`.

## <a name="examine-sql-sent-to-database"></a>Examinar o SQL enviado ao banco de dados

Às vezes, é útil poder ver as consultas SQL reais que são enviadas ao banco de dados. Em um tutorial anterior, você viu como fazer isso no código de interceptador; Agora você verá algumas maneiras de fazer isso sem escrever código de interceptor. Para testá-la, você veja uma consulta simple e, em seguida, examinar o que acontece a ela conforme você adiciona opções tal eager carregando, filtragem e classificação.

Na *controladores/CourseController*, substitua o `Index` método com o código a seguir, para interromper temporariamente o carregamento adiantado:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample6.cs)]

Agora defina um ponto de interrupção a `return` instrução (F9 com o cursor nessa linha). Pressione **F5** para executar o projeto no modo de depuração e selecione a página de índice do curso. Quando o código atinge o ponto de interrupção, examinar o `sql` variável. Você vê a consulta que é enviada para o SQL Server. É um simples `Select` instrução.

[!code-json[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample7.json)]

Clique na lupa para ver a consulta a **Visualizador de texto**.

![](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image10.png)

Agora você adicionará uma lista suspensa para a página de índice de cursos para que os usuários podem filtrar para um determinado departamento. Você classificará os cursos por título, e você especificará o carregamento adiantado para a `Department` propriedade de navegação.

Na *CourseController.cs*, substitua o `Index` método com o código a seguir:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample8.cs)]

Restaurar o ponto de interrupção no `return` instrução.

O método recebe o valor selecionado da lista suspensa no `SelectedDepartment` parâmetro. Se nada estiver selecionado, esse parâmetro será nulo.

Um `SelectList` coleção que contém todos os departamentos é passada para o modo de exibição para a lista suspensa. Os parâmetros passados para o `SelectList` construtor especifique o nome do campo de valor, o nome do campo de texto e o item selecionado.

Para o `Get` método da `Course` repositório, o código especifica uma expressão de filtro, uma ordem de classificação e o carregamento adiantado para a `Department` propriedade de navegação. A expressão de filtro sempre retorna `true` se nenhuma opção estiver selecionada na lista suspensa (ou seja, `SelectedDepartment` é nulo).

Na *Views\Course\Index.cshtml*, imediatamente antes da abertura `table` marca, adicione o seguinte código para criar a lista suspensa e um botão de envio:

[!code-cshtml[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample9.cshtml)]

Com o ponto de interrupção ainda definida, execute a página de índice do curso. Prossiga com as primeira vezes que o código atinge um ponto de interrupção, para que a página é exibida no navegador. Selecione um departamento na lista suspensa e clique em **filtro**.

Desta vez o primeiro ponto de interrupção será para a consulta de departamentos para obter a lista suspensa. Pular essa etapa e exibir o `query` variável na próxima vez que o código atinge o ponto de interrupção para ver o que o `Course` consulta agora se parece com. Você verá algo semelhante ao seguinte:

[!code-sql[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample10.sql)]

Você pode ver que a consulta agora é um `JOIN` consulta que carrega `Department` dados junto com o `Course` dados e que ele inclui um `WHERE` cláusula.

Remover o `var sql = courses.ToString()` linha.

## <a name="create-an-abstraction-layer"></a>Criar uma camada de abstração

Muitos desenvolvedores escrevem um código para implementar padrões de repositório e unidade de trabalho como um wrapper em torno do código que funciona com o Entity Framework. Esses padrões destinam-se a criar uma camada de abstração entre a camada de acesso a dados e a camada da lógica de negócios de um aplicativo. A implementação desses padrões pode ajudar a isolar o aplicativo de alterações no armazenamento de dados e pode facilitar o teste de unidade automatizado ou TDD (desenvolvimento orientado por testes). No entanto, escrever código adicional para implementar esses padrões nem sempre é a melhor opção para aplicativos que usam o EF, por vários motivos:

- A própria classe de contexto do EF isola o código de código específico a um armazenamento de dados.
- A classe de contexto do EF pode atuar como uma classe de unidade de trabalho para as atualizações de banco de dados feitas com o EF.
- Recursos introduzidos no Entity Framework 6 tornam mais fácil de implementar o TDD sem escrever código de repositório.

Para obter mais informações sobre como implementar o repositório e unidade de padrões de trabalho, consulte [a versão do Entity Framework 5 desta série de tutoriais](../../older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application.md). Para obter informações sobre as maneiras de implementar o TDD no Entity Framework 6, consulte os seguintes recursos:

- [Como o EF6 permite Mocking DbSets com mais facilidade](http://thedatafarm.com/data-access/how-ef6-enables-mocking-dbsets-more-easily/)
- [Teste com uma estrutura de simulação](https://msdn.microsoft.com/data/dn314429)
- [Teste com seus próprio duplicatas de teste](https://msdn.microsoft.com/data/dn314431)

<a id="proxies"></a>

## <a name="proxy-classes"></a>Classes de proxy

Quando o Entity Framework cria instâncias de entidade (por exemplo, quando você executar uma consulta), ele cria geralmente-los como instâncias de um tipo derivado gerado dinamicamente que atua como um proxy para a entidade. Por exemplo, consulte as seguintes duas imagens de depurador. Na primeira imagem, você vê que o `student` variável é o esperado `Student` digite imediatamente depois que você criar uma instância de entidade. A segunda imagem, depois que o EF foi usado para ler uma entidade student de banco de dados, consulte a classe de proxy.

![Antes de classe de proxy](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image12.png)

![Após a classe de proxy](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image13.png)

Essa classe de proxy substitui algumas propriedades virtuais da entidade para inserir os ganchos para executar as ações automaticamente quando a propriedade é acessada. Uma função que esse mecanismo é usado para é carregamento lento.

Na maioria das vezes você não precisa estar atento esse uso de proxies, mas há exceções:

- Em alguns cenários você talvez queira impedir que o Entity Framework desde a criação de instâncias de proxy. Por exemplo, quando você estiver serializando entidades geralmente você quer as classes POCO, não as classes de proxy. É uma maneira de evitar problemas de serialização serializar objetos de transferência de dados (DTOs) em vez de objetos de entidade, conforme mostrado na [usando a API da Web com o Entity Framework](../../../../web-api/overview/data/using-web-api-with-entity-framework/part-1.md) tutorial. Outra maneira é [desabilitar a criação do proxy](https://msdn.microsoft.com/data/jj592886.aspx).
- Quando você instancia uma classe de entidade usando o `new` operador, você não obtém uma instância do proxy. Isso significa que você não obtém a funcionalidade, como o controle de alterações automático e o carregamento lento. Isso é normalmente okey; Você geralmente não precisa o carregamento lento, pois você está criando uma nova entidade que não está no banco de dados e geralmente não precisa se você estiver marcando explicitamente a entidade como o controle de alterações `Added`. No entanto, se você precisar carregamento lento e você precisa de controle de alterações, você pode criar novas instâncias de entidade com proxies usando o [Create](https://msdn.microsoft.com/library/gg679504.aspx) método o `DbSet` classe.
- Talvez você queira obter um tipo de entidade real de um tipo de proxy. Você pode usar o [GetObjectType](https://msdn.microsoft.com/library/system.data.objects.objectcontext.getobjecttype.aspx) método o `ObjectContext` classe para obter o tipo de entidade real de uma instância do tipo de proxy.

Para obter mais informações, consulte [trabalhar com Proxies](https://msdn.microsoft.com/data/JJ592886.aspx) no MSDN.

## <a name="automatic-change-detection"></a>Detecção automática de alterações

O Entity Framework determina como uma entidade foi alterada (e, portanto, quais atualizações precisam ser enviadas ao banco de dados), comparando os valores atuais de uma entidade com os valores originais. Os valores originais são armazenados quando a entidade é consultada ou anexada. Alguns dos métodos que causam a detecção automática de alterações são os seguintes:

- `DbSet.Find`
- `DbSet.Local`
- `DbSet.Remove`
- `DbSet.Add`
- `DbSet.Attach`
- `DbContext.SaveChanges`
- `DbContext.GetValidationErrors`
- `DbContext.Entry`
- `DbChangeTracker.Entries`

Se você estiver controlando um grande número de entidades e chamar um desses métodos muitas vezes em um loop, você pode obter melhorias significativas de desempenho desativando temporariamente a detecção de alterações automático usando o [AutoDetectChangesEnabled](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbcontextconfiguration.autodetectchangesenabled.aspx) propriedade. Para obter mais informações, consulte [detectando alterações automaticamente](https://msdn.microsoft.com/data/jj556205) no MSDN.

## <a name="automatic-validation"></a>Validação automática

Quando você chama o `SaveChanges` método, por padrão, o Entity Framework valida os dados em todas as propriedades de todas as entidades alteradas antes de atualizar o banco de dados. Se você tiver atualizado um grande número de entidades e você já tiver validado os dados, esse trabalho é desnecessário e você pode tornar o processo de salvar as alterações levam menos tempo, desativando temporariamente a validação. Você pode fazer isso usando o [ValidateOnSaveEnabled](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbcontextconfiguration.validateonsaveenabled.aspx) propriedade. Para obter mais informações, consulte [validação](https://msdn.microsoft.com/data/gg193959) no MSDN.

## <a name="entity-framework-power-tools"></a>Entity Framework Power Tools

[Entity Framework Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) é um suplemento do Visual Studio que foi usado para criar diagramas de modelo de dados mostrado nestes tutoriais. As ferramentas também podem fazer outra função como gerar classes de entidade com base nas tabelas no banco de dados existente para que você possa usar o banco de dados com o Code First. Depois de instalar as ferramentas, algumas opções adicionais aparecem nos menus de contexto. Por exemplo, quando você clique com botão direito em sua classe de contexto **Gerenciador de soluções**, você vê e **Entity Framework** opção. Isso lhe dá a capacidade de gerar um diagrama. Quando você estiver usando o Code First não é possível alterar o modelo de dados no diagrama, mas você pode mover as coisas para torná-lo mais fácil de entender.

![Diagrama do EF](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image15.png)

## <a name="entity-framework-source-code"></a>Código de origem do Entity Framework

O código-fonte para o Entity Framework 6 está disponível em [GitHub](https://github.com/aspnet/EntityFramework6). Você pode registrar bugs, e você pode contribuir com seus próprios aprimoramentos para o código-fonte EF.

Embora o código-fonte estiver aberto, o Entity Framework é totalmente suportado como um produto da Microsoft. A equipe do Microsoft Entity Framework mantém controle sobre quais contribuições são aceitas e testa todas as alterações de código para garantir a qualidade de cada versão.

## <a name="acknowledgments"></a>Agradecimentos

- Tom Dykstra escreveu a versão original deste tutorial, foi co-autor de atualização do EF 5 e escreveu a atualização do EF 6. Tom é um escritor de programação sênior na Microsoft Web Platform e ferramentas de equipe de conteúdo.
- [Rick Anderson](https://blogs.msdn.com/b/rickandy/) (twitter [ @RickAndMSFT ](http://twitter.com/RickAndMSFT)) realizou a maior parte do trabalho atualizando o tutorial para o EF 5 e MVC 4 e foi co-autor a atualização do EF 6. Rick é um escritor de programação sênior da Microsoft, concentrando-se no Azure e no MVC.
- [Rowan Miller](http://www.romiller.com) e outros membros da equipe do Entity Framework assistido com revisões de código e ajudaram a depurar vários problemas com as migrações que surgiram durante estamos foram atualizando o tutorial para o EF 5 e 6 do EF.

## <a name="troubleshoot-common-errors"></a>Solucionar erros comuns

### <a name="cannot-createshadow-copy"></a>Não é possível criar/sombra cópia

Mensagem de erro:

> Não é possível criar/shadow copy '&lt;filename&gt;' quando esse arquivo já existe.

Solução

Aguarde alguns segundos e atualize a página.

### <a name="update-database-not-recognized"></a>Update-Database não reconhecido

Mensagem de erro (da `Update-Database` comando no PMC):

> O termo 'Update-Database' não é reconhecido como o nome de um cmdlet, função, arquivo de script ou programa operável. Verifique a ortografia do nome, ou se um caminho foi incluído, verifique se o caminho está correto e tente novamente.

Solução

Saia do Visual Studio. Reabra o projeto e tente novamente.

### <a name="validation-failed"></a>Falha na validação

Mensagem de erro (da `Update-Database` comando no PMC):

> Falha na validação de uma ou mais entidades. Consulte a propriedade 'EntityValidationErrors' para obter mais detalhes.

Solução

Uma causa desse problema é erros de validação quando o `Seed` execuções de método. Ver [propagando e depurando Entity Framework (EF) BDs](https://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx) para obter dicas sobre como depurar o `Seed` método.

### <a name="http-50019-error"></a>HTTP 500.19 erro

Mensagem de erro:

> Erro HTTP 500.19 - erro interno do servidor a página solicitada não pode ser acessado porque os dados de configuração relacionados para a página são inválidos.

Solução

Uma maneira que você pode obter esse erro é de várias cópias da solução, cada um deles usando o mesmo número de porta. Normalmente, você pode resolver esse problema saindo todas as instâncias do Visual Studio, em seguida, reiniciar o projeto que você está trabalhando. Se isso não funcionar, tente alterar o número da porta. Clique com botão direito no arquivo de projeto e, em seguida, clique em Propriedades. Selecione o **Web** guia e, em seguida, alterar o número da porta a **Url do projeto** caixa de texto.

### <a name="error-locating-sql-server-instance"></a>Erro ao localizar a instância do SQL Server

Mensagem de erro:

> Ocorreu um erro relacionado à rede ou específico a uma instância ao estabelecer uma conexão com o SQL Server. O servidor não foi encontrado ou não estava acessível. Verifique se o nome da instância está correto e se o SQL Server está configurado para permitir conexões remotas. (provedor: Adaptadores de Rede do SQL, erro: 26 – Erro ao localizar a instância/o servidor especificado)

Solução

Verifique a cadeia de conexão. Se você excluiu manualmente o banco de dados, altere o nome do banco de dados na cadeia de caracteres de construção.

## <a name="get-the-code"></a>Obter o código

[Baixe o projeto concluído](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>Recursos adicionais

 Para obter mais informações sobre como trabalhar com dados usando o Entity Framework, consulte o [página de documentação do EF no MSDN](https://msdn.microsoft.com/data/ee712907) e [acesso de dados do ASP.NET – recursos recomendados](../../../../whitepapers/aspnet-data-access-content-map.md).

Para obter mais informações sobre como implantar seu aplicativo web depois que ela foi criada, consulte [implantação da Web do ASP.NET – recursos recomendados](../../../../whitepapers/aspnet-web-deployment-content-map.md) na biblioteca MSDN.

Para obter informações sobre outros tópicos relacionados ao MVC, como autenticação e autorização, consulte a [MVC do ASP.NET – recursos recomendados](../recommended-resources-for-mvc.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Executadas consultas SQL brutas
> * Executadas consultas sem controle
> * Examinado consultas SQL enviadas ao banco de dados

Você também aprendeu como:

> [!div class="checklist"]
> * Criando uma camada de abstração
> * Classes de proxy
> * Detecção automática de alterações
> * Validação automática
> * Entity Framework Power Tools
> * Código de origem do Entity Framework

Isso conclui esta série de tutoriais sobre como usar o Entity Framework em um aplicativo ASP.NET MVC. Se você quiser saber mais sobre o Database First do EF, consulte a série de tutoriais de banco de dados primeiro.
> [!div class="nextstepaction"]
> [Primeiro do banco de dados do Entity Framework](../database-first-development/setting-up-database.md)