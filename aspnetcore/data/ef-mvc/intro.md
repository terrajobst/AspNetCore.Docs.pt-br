---
title: 'Tutorial: Introdução ao EF Core em um aplicativo Web ASP.NET MVC'
description: Este é o primeiro de uma série de tutoriais que explica como criar o aplicativo de exemplo Contoso University do zero.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: f7b557c8e560393ae886c46fad95c48ccbcc65b4
ms.sourcegitcommit: 5e3797a02ff3c48bb8cb9ad4320bfd169ebe8aba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56102962"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>Tutorial: Introdução ao EF Core em um aplicativo Web ASP.NET MVC

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

O aplicativo Web de exemplo Contoso University demonstra como criar aplicativos Web ASP.NET Core 2.2 MVC usando o EF (Entity Framework) Core 2.0 e o Visual Studio 2017.

O aplicativo de exemplo é um site de uma Contoso University fictícia. Ele inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor. Este é o primeiro de uma série de tutoriais que explica como criar o aplicativo de exemplo Contoso University do zero.

O EF Core 2.0 é a última versão do EF, mas ainda não tem todos os recursos do EF 6.x. Para obter informações sobre como escolher entre o EF 6.x e o EF Core, consulte [EF Core vs. EF6.x](/ef/efcore-and-ef6/). Se você escolher o EF 6.x, confira [a versão anterior desta série de tutoriais](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

> [!NOTE]
> Para obter a versão ASP.NET Core 1.1 deste tutorial, confira a [versão VS 2017 Atualização 2 deste tutorial em formato PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/efmvc1.1.pdf).

Neste tutorial, você:

> [!div class="checklist"]
> * Criar um aplicativo Web MVC ASP.NET Core
> * Configurar o estilo do site
> * Aprender sobre pacotes NuGet do EF Core
> * Criar o modelo de dados
> * Criar o contexto de banco de dados
> * Registrar o SchoolContext
> * Inicializar o BD com os dados de teste
> * Criar um controlador e exibições
> * Exibir o banco de dados

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [](~/includes/net-core-prereqs.md)]

## <a name="troubleshooting"></a>Solução de problemas

Caso tenha um problema que não consiga resolver, em geral, você poderá encontrar a solução comparando o código com o [projeto concluído](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final). Para obter uma lista de erros comuns e como resolvê-los, consulte [a seção Solução de problemas do último tutorial da série](advanced.md#common-errors). Caso não encontre o que precisa na seção, poste uma pergunta no StackOverflow.com sobre o [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou o [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

> [!TIP]
> Esta é uma série de dez tutoriais, cada um se baseando no que é feito nos tutoriais anteriores. Considere a possibilidade de salvar uma cópia do projeto após a conclusão bem-sucedida de cada tutorial. Caso tenha problemas, comece novamente no tutorial anterior em vez de voltar ao início de toda a série.

## <a name="contoso-university-web-app"></a>Aplicativo Web Contoso University

O aplicativo que você criará nestes tutoriais é um site simples de uma universidade.

Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores. Estas são algumas das telas que você criará.

![Página Índice de Alunos](intro/_static/students-index.png)

![Página Editar Alunos](intro/_static/student-edit.png)

O estilo de interface do usuário desse site foi mantido perto do que é gerado pelos modelos internos, de modo que o tutorial possa se concentrar principalmente em como usar o Entity Framework.

## <a name="create-aspnet-core-mvc-web-app"></a>Criar um aplicativo Web MVC ASP.NET Core

Abra o Visual Studio e crie um novo projeto Web ASP.NET Core C# chamado "ContosoUniversity".

* No menu **Arquivo**, selecione **Novo > Projeto**.

* No painel esquerdo, selecione **Instalado > Visual C# > Web**.

* Selecione o modelo de projeto **Aplicativo Web ASP.NET Core**.

* Insira **ContosoUniversity** como o nome e clique em **OK**.

  ![Caixa de diálogo Novo Projeto](intro/_static/new-project2.png)

* Aguarde a caixa de diálogo **Novo Aplicativo Web ASP.NET Core (.NET Core)** ser exibida

  ![Caixa de diálogo Novo projeto ASP.NET Core](intro/_static/new-aspnet2.png)

* Selecione **ASP.NET Core 2.2** e o modelo **Aplicativo Web (Model-View-Controller)**.

  **Observação:** Este tutorial exige o ASP.NET Core 2.2 e o EF Core 2.0 ou posterior.

* Verifique se a opção **Autenticação** está definida como **Sem Autenticação**.

* Clique em **OK**

## <a name="set-up-the-site-style"></a>Configurar o estilo do site

Algumas alterações simples configurarão o menu do site, o layout e a home page.

Abra *Views/Shared/_Layout.cshtml* e faça as seguintes alterações:

* Altere cada ocorrência de "ContosoUniversity" para "Contoso University". Há três ocorrências.

* Adicione entradas de menu **Sobre**, **Alunos**, **Cursos**, **Instrutores** e **Departamentos** e exclua a entrada de menu **Privacidade**.

As alterações são realçadas.

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,32-36,51)]

Em *Views/Home/Index.cshtml*, substitua o conteúdo do arquivo pelo seguinte código para substituir o texto sobre o ASP.NET e MVC pelo texto sobre este aplicativo:

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

Pressione CTRL+F5 para executar o projeto ou escolha **Depurar > Iniciar sem Depuração** no menu. Você verá a home page com guias para as páginas que você criará nestes tutoriais.

![Home page da Contoso University](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a>Sobre pacotes NuGet do EF Core

Para adicionar o suporte do EF Core a um projeto, instale o provedor de banco de dados que você deseja ter como destino. Este tutorial usa o SQL Server e o pacote de provedor é [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/). Esse pacote está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), portanto você não precisa referenciar o pacote se o aplicativo tem uma referência de pacote ao pacote `Microsoft.AspNetCore.App`.

Este pacote e suas dependências (`Microsoft.EntityFrameworkCore` e `Microsoft.EntityFrameworkCore.Relational`) fornecem suporte de tempo de execução para o EF. Você adicionará um pacote de ferramentas posteriormente, no tutorial [Migrações](migrations.md).

Para obter informações sobre outros provedores de banco de dados que estão disponíveis para o Entity Framework Core, consulte [Provedores de banco de dados](/ef/core/providers/).

## <a name="create-the-data-model"></a>Criar o modelo de dados

Em seguida, você criará as classes de entidade para o aplicativo Contoso University. Você começará com as três entidades a seguir.

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

Há uma relação um-para-muitos entre as entidades `Student` e `Enrollment`, e uma relação um-para-muitos entre as entidades `Course` e `Enrollment`. Em outras palavras, um aluno pode ser registrado em qualquer quantidade de cursos e um curso pode ter qualquer quantidade de alunos registrados.

Nas seções a seguir, você criará uma classe para cada uma dessas entidades.

### <a name="the-student-entity"></a>A entidade Student

![Diagrama da entidade Student](intro/_static/student-entity.png)

Na pasta *Models*, crie um arquivo de classe chamado *Student.cs* e substitua o código de modelo pelo código a seguir.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

A propriedade `ID` se tornará a coluna de chave primária da tabela de banco de dados que corresponde a essa classe. Por padrão, o Entity Framework interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária.

A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships). As propriedades de navegação armazenam outras entidades que estão relacionadas a essa entidade. Nesse caso, a propriedade `Enrollments` de uma `Student entity` armazenará todas as entidades `Enrollment` relacionadas a essa entidade `Student`. Em outras palavras, se determinada linha Aluno no banco de dados tiver duas linhas Registro relacionadas (linhas que contêm o valor de chave primária do aluno na coluna de chave estrangeira StudentID), a propriedade de navegação `Enrollments` dessa entidade `Student` conterá as duas entidades `Enrollment`.

Se uma propriedade de navegação pode armazenar várias entidades (como em relações muitos para muitos ou um-para-muitos), o tipo precisa ser uma lista na qual entradas podem ser adicionadas, excluídas e atualizadas, como `ICollection<T>`. Especifique `ICollection<T>` ou um tipo, como `List<T>` ou `HashSet<T>`. Se você especificar `ICollection<T>`, o EF criará uma coleção `HashSet<T>` por padrão.

### <a name="the-enrollment-entity"></a>A entidade Enrollment

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

Na pasta *Models*, crie *Enrollment.cs* e substitua o código existente pelo seguinte código:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

A propriedade `EnrollmentID` será a chave primária; essa entidade usa o padrão `classnameID` em vez de `ID` por si só, como você viu na entidade `Student`. Normalmente, você escolhe um padrão e usa-o em todo o modelo de dados. Aqui, a variação ilustra que você pode usar qualquer um dos padrões. Em um [tutorial posterior](inheritance.md), você verá como usar uma ID sem nome de classe facilita a implementação da herança no modelo de dados.

A propriedade `Grade` é um `enum`. O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor nulo. Uma nota nula é diferente de uma nota zero – nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.

A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`. Uma entidade `Enrollment` é associada a uma entidade `Student`, de modo que a propriedade possa armazenar apenas uma única entidade `Student` (ao contrário da propriedade de navegação `Student.Enrollments` que você viu anteriormente, que pode armazenar várias entidades `Enrollment`).

A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`. Uma entidade `Enrollment` está associada a uma entidade `Course`.

O Entity Framework interpreta uma propriedade como uma propriedade de chave estrangeira se ela é nomeada `<navigation property name><primary key property name>` (por exemplo, `StudentID` para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`). As propriedades de chave estrangeira também podem ser nomeadas apenas `<primary key property name>` (por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`).

### <a name="the-course-entity"></a>A entidade Course

![Diagrama de entidade Curso](intro/_static/course-entity.png)

Na pasta *Models*, crie *Course.cs* e substitua o código existente pelo seguinte código:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

A propriedade `Enrollments` é uma propriedade de navegação. Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.

Falaremos mais sobre o atributo `DatabaseGenerated` em um [tutorial posterior](complex-data-model.md) desta série. Basicamente, esse atributo permite que você insira a chave primária do curso, em vez de fazer com que ela seja gerada pelo banco de dados.

## <a name="create-the-database-context"></a>Criar o contexto de banco de dados

A classe principal que coordena a funcionalidade do Entity Framework para determinado modelo de dados é a classe de contexto de banco de dados. Você cria essa classe derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`. No código, especifique quais entidades são incluídas no modelo de dados. Também personalize o comportamento específico do Entity Framework. Neste projeto, a classe é chamada `SchoolContext`.

Na pasta do projeto, crie uma pasta chamada *Dados*.

Na pasta *Dados*, crie um novo arquivo de classe chamado *SchoolContext.cs* e substitua o código de modelo pelo seguinte código:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Esse código cria uma propriedade `DbSet` para cada conjunto de entidades. Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados, enquanto uma entidade corresponde a uma linha na tabela.

Você pode omitir as instruções `DbSet<Enrollment>` e `DbSet<Course>` e elas funcionarão da mesma maneira. O Entity Framework inclui-os de forma implícita porque a entidade `Student` referencia a entidade `Enrollment` e a entidade `Enrollment` referencia a entidade `Course`.

Quando o banco de dados é criado, o EF cria tabelas que têm nomes iguais aos nomes de propriedade `DbSet`. Em geral, os nomes de propriedade de coleções são plurais (Alunos em vez de Aluno), mas os desenvolvedores não concordam sobre se os nomes de tabela devem ser pluralizados ou não. Para esses tutoriais, você substituirá o comportamento padrão especificando nomes singulares de tabela no DbContext. Para fazer isso, adicione o código realçado a seguir após a última propriedade DbSet.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a>Registrar o SchoolContext

O ASP.NET Core implementa a [injeção de dependência](../../fundamentals/dependency-injection.md) por padrão. Serviços (como o contexto de banco de dados do EF) são registrados com injeção de dependência durante a inicialização do aplicativo. Os componentes que exigem esses serviços (como controladores MVC) recebem esses serviços por meio de parâmetros do construtor. Você verá o código de construtor do controlador que obtém uma instância de contexto mais adiante neste tutorial.

Para registrar `SchoolContext` como um serviço, abra *Startup.cs* e adicione as linhas realçadas ao método `ConfigureServices`.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=3-4)]

O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto `DbContextOptionsBuilder`. Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.

Adicione instruções `using` aos namespaces `ContosoUniversity.Data` e `Microsoft.EntityFrameworkCore` e, em seguida, compile o projeto.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Abra o arquivo *appsettings.json* e adicione uma cadeia de conexão, conforme mostrado no exemplo a seguir.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

A cadeia de conexão especifica um banco de dados LocalDB do SQL Server. LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção. O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa. Por padrão, o LocalDB cria arquivos de banco de dados *.mdf* no diretório `C:/Users/<user>`.

## <a name="initialize-db-with-test-data"></a>Inicializar o BD com os dados de teste

O Entity Framework criará um banco de dados vazio para você. Nesta seção, você escreve um método que é chamado depois que o banco de dados é criado para populá-lo com os dados de teste.

Aqui, você usará o método `EnsureCreated` para criar o banco de dados automaticamente. Em um [tutorial posterior](migrations.md), você verá como manipular as alterações do modelo usando as Migrações do Code First para alterar o esquema de banco de dados, em vez de remover e recriar o banco de dados.

Na pasta *Data*, crie um novo arquivo de classe chamado *DbInitializer.cs* e substitua o código de modelo pelo código a seguir, que faz com que um banco de dados seja criado, quando necessário, e carrega dados de teste no novo banco de dados.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

O código verifica se há alunos no banco de dados e, se não há, ele pressupõe que o banco de dados é novo e precisa ser propagado com os dados de teste. Ele carrega os dados de teste em matrizes em vez de em coleções `List<T>` para otimizar o desempenho.

Em *Program.cs*, modifique o método `Main` para fazer o seguinte na inicialização do aplicativo:

* Obtenha uma instância de contexto de banco de dados do contêiner de injeção de dependência.
* Chame o método de semente passando a ele o contexto.
* Descarte o contexto quando o método de semente for concluído.

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

Adicione instruções `using`:

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

Nos tutoriais mais antigos, você poderá ver um código semelhante no método `Configure` em *Startup.cs*. Recomendamos que você use o método `Configure` apenas para configurar o pipeline de solicitação. O código de inicialização do aplicativo pertence ao método `Main`.

Agora, na primeira vez que você executar o aplicativo, o banco de dados será criado e propagado com os dados de teste. Sempre que você alterar o modelo de dados, exclua o banco de dados, atualize o método de semente e comece novamente com um novo banco de dados da mesma maneira. Nos próximos tutoriais, você verá como modificar o banco de dados quando o modelo de dados for alterado, sem excluí-lo e recriá-lo.

## <a name="create-controller-and-views"></a>Criar um controlador e exibições

Em seguida, você usará o mecanismo de scaffolding no Visual Studio para adicionar um controlador MVC e exibições que usam o EF para consultar e salvar dados.

A criação automática de métodos de ação CRUD e exibições é conhecida como scaffolding. O scaffolding difere da geração de código, em que o código gerado por scaffolding é um ponto de partida que você pode modificar de acordo com seus requisitos, enquanto que normalmente o código gerado não é modificado. Quando precisar personalizar o código gerado, use classes parciais ou regenere o código quando as coisas mudarem.

* Clique com o botão direito do mouse na pasta **Controladores** no **Gerenciador de Soluções** e selecione **Adicionar > Novo Item Gerado por Scaffolding**.

Se a caixa de diálogo **Adicionar Dependências do MVC** for exibida:

* [Atualize o Visual Studio para a última versão](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017). Versões do Visual Studio anteriores a 15.5 mostram essa caixa de diálogo.
* Se não puder atualizar, selecione **ADICIONAR** e, em seguida, siga as etapas de adição do controlador novamente.

* Na caixa de diálogo **Adicionar Scaffolding**:

  * Selecione **Controlador MVC com exibições, usando o Entity Framework**.

  * Clique em **Adicionar**. A caixa de diálogo **Adicionar Controlador MVC com exibições, usando o Entity Framework** é exibida.

    ![Gerar aluno por scaffolding](intro/_static/scaffold-student2.png)

  * Na **classe Model**, selecione **Aluno**.

  * Na **Classe de contexto de dados** selecione **SchoolContext**.

  * Aceite o **StudentsController** padrão como o nome.

  * Clique em **Adicionar**.

  Quando você clica em **Adicionar**, o mecanismo de scaffolding do Visual Studio cria um arquivo *StudentsController.cs* e um conjunto de exibições (arquivos *.cshtml*) que funcionam com o controlador.

(O mecanismo de scaffolding também poderá criar o contexto de banco de dados para você se não criá-lo manualmente primeiro como fez anteriormente para este tutorial. Especifique uma nova classe de contexto na caixa **Adicionar Controlador** clicando no sinal de adição à direita de **Classe de contexto de dados**.  Em seguida, o Visual Studio criará a classe `DbContext`, bem como o controlador e as exibições.)

Você observará que o controlador usa um `SchoolContext` como parâmetro de construtor.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

A injeção de dependência do ASP.NET Core é responsável por passar uma instância de `SchoolContext` para o controlador. Você configurou isso no arquivo *Startup.cs* anteriormente.

O controlador contém um método de ação `Index`, que exibe todos os alunos no banco de dados. O método obtém uma lista de alunos do conjunto de entidades Students pela leitura da propriedade `Students` da instância de contexto de banco de dados:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Você aprenderá sobre os elementos de programação assíncronos nesse código mais adiante no tutorial.

A exibição *Views/Students/Index.cshtml* mostra esta lista em uma tabela:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Pressione CTRL+F5 para executar o projeto ou escolha **Depurar > Iniciar sem Depuração** no menu.

Clique na guia Alunos para ver os dados de teste inserido pelo método `DbInitializer.Initialize`. Dependendo da largura da janela do navegador, você verá o link da guia `Student` na parte superior da página ou precisará clicar no ícone de navegação no canto superior direito para ver o link.

![Home page estreita da Contoso University](intro/_static/home-page-narrow.png)

![Página Índice de Alunos](intro/_static/students-index.png)

## <a name="view-the-database"></a>Exibir o banco de dados

Quando você iniciou o aplicativo, o método `DbInitializer.Initialize` chamou `EnsureCreated`. O EF observou que não havia nenhum banco de dados e, portanto, ele criou um; em seguida, o restante do código do método `Initialize` populou o banco de dados com os dados. Use o **SSOX** (Pesquisador de Objetos do SQL Server) para exibir o banco de dados no Visual Studio.

Feche o navegador.

Se a janela do SSOX ainda não estiver aberta, selecione-a no menu **Exibir** do Visual Studio.

No SSOX, clique em **(localdb)\MSSQLLocalDB > Bancos de Dados** e, em seguida, clique na entrada do nome do banco de dados que está na cadeia de conexão no arquivo *appsettings.json*.

Expanda o nó **Tabelas** para ver as tabelas no banco de dados.

![Tabelas no SSOX](intro/_static/ssox-tables.png)

Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas que foram inseridas na tabela.

![Tabela Aluno no SSOX](intro/_static/ssox-student-table.png)

Os arquivos de banco de dados <em>.mdf</em> e <em>.ldf</em> estão na pasta <em>C:\Users\\<yourusername></em>.

Como você está chamando `EnsureCreated` no método inicializador executado na inicialização do aplicativo, agora você pode fazer uma alteração na classe `Student`, excluir o banco de dados, executar novamente o aplicativo e o banco de dados será recriado automaticamente para que ele corresponda à alteração. Por exemplo, se você adicionar uma propriedade `EmailAddress` à classe `Student`, verá uma nova coluna `EmailAddress` na tabela recriada.

## <a name="conventions"></a>Convenções

A quantidade de código feita para que o Entity Framework possa criar um banco de dados completo para você é mínima, devido ao uso de convenções ou de suposições feitas pelo Entity Framework.

* Os nomes de propriedades `DbSet` são usadas como nomes de tabela. Para entidades não referenciadas por uma propriedade `DbSet`, os nomes de classe de entidade são usados como nomes de tabela.

* Os nomes de propriedade de entidade são usados para nomes de coluna.

* As propriedades de entidade que são nomeadas ID ou classnameID são reconhecidas como propriedades de chave primária.

* Uma propriedade é interpretada como uma propriedade de chave estrangeira se ela é nomeada *<navigation property name><primary key property name>* (por exemplo, `StudentID` para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`). As propriedades de chave estrangeira também podem ser nomeadas apenas *<primary key property name>* (por exemplo, `EnrollmentID`, pois a chave primária da entidade `Enrollment` é `EnrollmentID`).

O comportamento convencional pode ser substituído. Por exemplo, você pode especificar os nomes de tabela de forma explícita, conforme visto anteriormente neste tutorial. Além disso, você pode definir nomes de coluna e qualquer propriedade como a chave primária ou chave estrangeira, como você verá em um [tutorial posterior](complex-data-model.md) desta série.

## <a name="asynchronous-code"></a>Código assíncrono

A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.

Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso. Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados. Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S. Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações. Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor fica capacitado a manipular mais tráfego sem atrasos.

O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução, mas para situações de baixo tráfego, o impacto no desempenho é insignificante, ao passo que, em situações de alto tráfego, a melhoria de desempenho potencial é significativa.

No código a seguir, a palavra-chave `async`, o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* A palavra-chave `async` instrui o compilador a gerar retornos de chamada para partes do corpo do método e a criar automaticamente o objeto `Task<IActionResult>` que é retornado.

* O tipo de retorno `Task<IActionResult>` representa um trabalho em andamento com um resultado do tipo `IActionResult`.

* A palavra-chave `await` faz com que o compilador divida o método em duas partes. A primeira parte termina com a operação que é iniciada de forma assíncrona. A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.

* `ToListAsync` é a versão assíncrona do método de extensão `ToList`.

Algumas coisas a serem consideradas quando você estiver escrevendo um código assíncrono que usa o Entity Framework:

* Somente instruções que fazem com que consultas ou comandos sejam enviados ao banco de dados são executadas de forma assíncrona. Isso inclui, por exemplo, `ToListAsync`, `SingleOrDefaultAsync` e `SaveChangesAsync`. Isso não inclui, por exemplo, instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.

* Um contexto do EF não é thread-safe: não tente realizar várias operações em paralelo. Quando você chamar qualquer método assíncrono do EF, sempre use a palavra-chave `await`.

* Se desejar aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca que você está usando (por exemplo, para paginação) também usam o código assíncrono se eles chamam métodos do Entity Framework que fazem com que consultas sejam enviadas ao banco de dados.

Para obter mais informações sobre a programação assíncrona no .NET, consulte [Visão geral da programação assíncrona](/dotnet/articles/standard/async).

## <a name="get-the-code"></a>Obter o código

[Baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Criou um aplicativo Web MVC ASP.NET Core
> * Configurar o estilo do site
> * Aprendeu sobre pacotes NuGet do EF Core
> * Adicionou o modelo de dados
> * Criou o contexto de banco de dados
> * Registrou o SchoolContext
> * Inicializou o BD com os dados de teste
> * Criou um controlador e exibições
> * Exibiu o banco de dados

No tutorial a seguir, você aprenderá a executar operações CRUD (criar, ler, atualizar e excluir) básicas.

Vá para o próximo artigo para aprender a executar operações CRUD (criar, ler, atualizar e excluir) básicas.
> [!div class="nextstepaction"]
> [Implementar a funcionalidade CRUD básica](crud.md)
