---
title: Páginas Razor com o Entity Framework Core no ASP.NET Core – Tutorial 1 de 8
author: rick-anderson
description: Mostra como criar um aplicativo das Páginas do Razor usando o Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 1a9d83be9180b1d32ab941932eb3cab8612dff01
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213396"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>Páginas Razor com o Entity Framework Core no ASP.NET Core – Tutorial 1 de 8

Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Este é o primeiro de uma série de tutoriais que mostram como usar o Entity Framework (EF) Core em um aplicativo [ASP.NET Core Razor Pages](xref:razor-pages/index) . O tutorial cria um site de uma Contoso University fictícia. O site inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor. O tutorial usa a abordagem Code First. Para obter informações sobre como seguir este tutorial usando a abordagem do banco de dados primeiro, consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/16897).

[Baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Instruções de download](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* Se você for novo no Razor Pages, confira a série de tutoriais de [Introdução ao Razor Pages](xref:tutorials/razor-pages/razor-pages-start) antes de iniciar este.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>Mecanismos de banco de dados

As instruções do Visual Studio usam [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), uma versão do SQL Server Express que é executada somente no Windows.

As instruções do Visual Studio Code usam o [SQLite](https://www.sqlite.org/), um mecanismo de banco de dados multiplataforma.

Se você optar por usar o SQLite, baixe e instale uma ferramenta de terceiros para gerenciar e exibir um banco de dados SQLite, como o [Navegador de BD para SQLite](https://sqlitebrowser.org/).

## <a name="troubleshooting"></a>Solução de problemas

Se você encontrar um problema que não possa resolver, compare seu código com o [projeto concluído](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Uma boa maneira de obter ajuda é postando uma pergunta no StackOverflow.com usando a [tag ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou a [tag EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-sample-app"></a>O aplicativo de exemplo

O aplicativo criado nesses tutoriais é um site básico de universidade. Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores. Veja a seguir algumas das telas criadas no tutorial.

![Página Índice de Alunos](intro/_static/students-index30.png)

![Página Editar Alunos](intro/_static/student-edit30.png)

O estilo de interface do usuário deste site baseia-se nos modelos de projeto internos. O foco do tutorial está em como usar o EF Core, não em como personalizar a interface do usuário.

Siga o link na parte superior da página para obter o código-fonte do projeto concluído. A pasta *cu30* tem o código para a versão ASP.NET Core 3.0 do tutorial. Os arquivos que refletem o estado do código para os tutoriais 1-7 podem ser encontrados na pasta *cu30snapshots*.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Para executar o aplicativo depois de baixar o projeto concluído:

* Exclua três arquivos e uma pasta que tenha *SQLite* no nome.
* Crie o projeto.
* No PMC (Console do Gerenciador de Pacotes), execute o seguinte comando:

  ```powershell
  Update-Database
  ```

* Execute o projeto para propagar o banco de dados.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Para executar o aplicativo depois de baixar o projeto concluído:

* Exclua *ContosoUniversity.csproj* e altere o nome de *ContosoUniversitySQLite.csproj* para *ContosoUniversity.csproj*.
* Exclua *Startup.cs* e altere o nome de *StartupSQLite.cs* para *Startup.cs*.
* Exclua *appSettings.json* e altere o nome de *appSettingsSQLite.json* para *appSettings.json*.
* Exclua a pasta *Migrations* e altere o nome de *MigrationsSQL* para *Migrations*.
* Crie o projeto.
* Em um prompt de comando na pasta do projeto, execute os seguintes comandos:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* Em sua ferramenta do SQLite, execute a seguinte instrução SQL:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Execute o projeto para propagar o banco de dados.

---

## <a name="create-the-web-app-project"></a>Criar o projeto de aplicativo Web

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **arquivo** do Visual Studio, selecione **novo** **projeto**de >.
* Selecione **Aplicativo Web ASP.NET Core**.
* Nomeie o projeto *ContosoUniversity*. É importante usar esse nome exato, incluindo maiúsculas e minúsculas, para que os namespaces correspondam quando o código for copiado e colado.
* Selecione **.NET Core** e **ASP.NET Core 3.0** na lista suspensa e, em seguida, selecione **Aplicativo Web**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Em um terminal, navegue até a pasta na qual a pasta do projeto deve ser criada.

* Execute os comandos a seguir para criar um projeto do Razor Pages `cd` para a nova pasta do projeto:

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>Configurar o estilo do site

Configure o cabeçalho, o rodapé e o menu do site atualizando *Pages/Shared/_Layout.cshtml*:

* Altere cada ocorrência de "ContosoUniversity" para "Contoso University". Há três ocorrências.

* Exclua as entradas de menu **Início** e **Privacidade**, então adicione as entradas para **Sobre**, **Alunos**, **Cursos**, **Instrutores** e **Departamentos**.

As alterações são realçadas.

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

Em *Pages/Index.cshtml*, substitua o conteúdo do arquivo pelo seguinte código para substituir o texto sobre o ASP.NET Core pelo texto sobre este aplicativo:

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

Execute o aplicativo para verificar se o página inicial aparece.

## <a name="the-data-model"></a>O modelo de dados

As seções a seguir criam um modelo de dados:

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

Um aluno pode ser registrado em qualquer quantidade de cursos e um curso pode ter qualquer quantidade de alunos registrados.

## <a name="the-student-entity"></a>A entidade Student

![Diagrama da entidade Student](intro/_static/student-entity.png)

* Crie uma pasta *Modelos* na pasta do projeto. 

* Crie *Models/Student.cs* com o seguinte código:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

A propriedade `ID` se torna a coluna de chave primária da tabela de banco de dados que corresponde a essa classe. Por padrão, o EF Core interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária. Portanto, o nome alternativo reconhecido automaticamente para a chave primária da classe `Student` é `StudentID`.

A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships). As propriedades de navegação armazenam outras entidades que estão relacionadas a essa entidade. Nesse caso, a propriedade `Enrollments` de uma entidade `Student` armazena todas as entidades `Enrollment` relacionadas àquele Aluno. Por exemplo, se uma linha Aluno no banco de dados tiver duas linhas Registro relacionadas, a propriedade de navegação `Enrollments` conterá duas entidades de Registro. 

No banco de dados, uma linha de Registro estará relacionada a uma linha de Aluno se sua coluna StudentID contiver o valor da ID do aluno. Por exemplo, suponha que uma linha de aluno tenha ID=1. As linhas de registro relacionadas terão StudentID = 1. StudentID é uma *chave estrangeira* na tabela de Registro. 

A propriedade `Enrollments` é definida como `ICollection<Enrollment>` porque pode haver várias entidades de registro relacionadas. Você pode usar outros tipos de coleção, como `List<Enrollment>` ou `HashSet<Enrollment>`. Quando `ICollection<Enrollment>` é usado, o EF Core cria uma coleção `HashSet<Enrollment>` por padrão.

## <a name="the-enrollment-entity"></a>A entidade Enrollment

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

Crie *Models/Enrollment.cs* com o seguinte código:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

A propriedade `EnrollmentID` é a chave primária; essa entidade usa o padrão `classnameID`, em vez de `ID` por si mesmo. Para um modelo de dados de produção, escolha um padrão e use-o de forma consistente. Este tutorial usa ambos apenas para ilustrar que os dois funcionam. Usar `ID` sem `classname` facilita a implementação de alguns tipos de alterações no modelo de dados.

A propriedade `Grade` é um `enum`. O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor [anulável](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/). Uma nota nula é diferente de uma nota zero&mdash;nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.

A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`. Uma entidade `Enrollment` está associada a uma entidade `Student` e, portanto, a propriedade contém uma única entidade `Student`.

A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`. Uma entidade `Enrollment` está associada a uma entidade `Course`.

O EF Core interpreta uma propriedade como uma chave estrangeira se ela é nomeada `<navigation property name><primary key property name>`. Por exemplo, `StudentID` é a chave estrangeira para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`. Propriedades de chave estrangeira também podem ser nomeadas `<primary key property name>`. Por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`.

## <a name="the-course-entity"></a>A entidade Course

![Diagrama de entidade Curso](intro/_static/course-entity.png)

Crie *Models/Course.cs* com o seguinte código:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

A propriedade `Enrollments` é uma propriedade de navegação. Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.

O atributo `DatabaseGenerated` permite que o aplicativo especifique a chave primária em vez de fazer com que ela seja gerada pelo banco de dados.

Compile o projeto para validar que não há erros de compilador.

## <a name="scaffold-student-pages"></a>Aplicar scaffold a páginas de Aluno

Nesta seção, você usa a ferramenta de scaffolding do ASP.NET Core para gerar:

* Uma classe *contexto* do EF Core. O contexto é a classe principal que coordena a funcionalidade do Entity Framework para determinado modelo de dados. Ele deriva da classe `Microsoft.EntityFrameworkCore.DbContext`.
* Páginas do Razor que lidam com as operações CRUD (criar, ler, atualizar e excluir) para a entidade `Student`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Crie uma pasta *Alunos* na pasta *Páginas*.
* Em **Gerenciador de soluções**, clique com o botão direito do mouse na pasta *páginas/alunos* e selecione **Adicionar** > **novo item com Scaffold**.
* Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor Pages usando Entity Framework (CRUD)** > **Adicionar**.
* Na caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)** :
  * Na lista suspensa **classe Modelo**, selecione **Aluno (ContosoUniversity.Models)** .
  * Na linha **Classe de contexto de dados**, selecione o sinal de **+** (adição).
  * Altere o nome do contexto de dados de *ContosoUniversity.Models.ContosoUniversityContext* para *ContosoUniversity.Data.SchoolContext*.
  * Selecione **Adicionar**.

Os seguintes pacotes são instalados automaticamente:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Execute os seguintes comandos da CLI do .NET Core para instalar os pacotes NuGet necessários:
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  O pacote Microsoft.VisualStudio.Web.CodeGeneration.Design é necessário para o scaffolding. Embora o aplicativo não use o SQL Server, a ferramenta de scaffolding precisa do pacote do SQL Server.

* Crie uma pasta *Pages/Students*.

* Execute o comando a seguir para instalar a [ferramenta de scaffolding aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* Execute o comando a seguir para aplicar scaffold às páginas do aluno.

  **No Windows**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  **No macOS ou no Linux**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

Se você tiver um problema com a etapa anterior, compile o projeto e repita a etapa de scaffold.

O processo de scaffolding:

* Cria páginas do Razor na pasta *Pages/Students:*
  * *Create.cshtml* e *Create.cshtml.cs*
  * *Delete.cshtml* e *Delete.cshtml.cs*
  * *Details.cshtml* e *Details.cshtml.cs*
  * *Edit.cshtml* e *Edit.cshtml.cs*
  * *Index.cshtml* e *Index.cshtml.cs*
* Cria *Data/SchoolContext.cs*.
* Adiciona o contexto à injeção de dependência em *Startup.cs*.
* Adiciona uma cadeia de conexão de banco de dados a *appsettings.json*.

## <a name="database-connection-string"></a>Cadeia de conexão de banco de dados

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

A cadeia de conexão especifica um [LocalDB do SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção. Por padrão, o LocalDB cria arquivos *.mdf* no diretório `C:/Users/<user>`.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Altere a cadeia de conexão para apontar para um arquivo de banco de dados SQLite chamado *CU.db*:

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Atualizar a classe do contexto de banco de dados

A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de banco de dados. O contexto deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). O contexto especifica quais entidades são incluídas no modelo de dados. Neste projeto, a classe é chamada `SchoolContext`.

Atualize *SchoolContext.cs* com o seguinte código:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

O código destacado cria uma propriedade [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades. Na terminologia do EF Core:

* Um conjunto de entidades normalmente corresponde a uma tabela de banco de dados.
* Uma entidade corresponde a uma linha da tabela.

Como um conjunto de entidades contém várias entidades, as propriedades DBSet devem ser nomes no plural. Como a ferramenta scaffolding criou um`Student` DBSet, essa etapa o altera para o `Students` no plural. 

Para fazer com que o código do Razor Pages corresponda ao novo nome do DBSet, faça uma alteração global em todo o projeto de `_context.Student` para `_context.Students`.  Há oito ocorrências.

Compile o projeto para verificar se não há erros de compilador.

## <a name="startupcs"></a>Startup.cs

O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection). Serviços (como o contexto de banco de dados do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo. Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor. O código de construtor que obtém uma instância de contexto do banco de dados é mostrado mais adiante no tutorial.

A ferramenta de scaffolding registrou automaticamente a classe de contexto com o contêiner de injeção de dependência.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Em `ConfigureServices`, as linhas destacadas foram adicionadas pelo scaffolder:

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Em `ConfigureServices`, verifique se o código adicionado pelo scaffolder chama `UseSqlite`.

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.

## <a name="create-the-database"></a>Criar o banco de dados

Atualize *Program.cs* para criar o banco de dados se ele não existir:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

O método [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) não executará nenhuma ação se existir um banco de dados para o contexto. Se não existir nenhum banco de dados, ele criará o banco de dados e o esquema. `EnsureCreated` habilita o seguinte fluxo de trabalho para manipular alterações no modelo de dados:

* Exclua o banco de dados. Qualquer dado existente é perdido.
* Altere o modelo de dados. Por exemplo, adicione um campo `EmailAddress`.
* Execute o aplicativo.
* `EnsureCreated` cria um banco de dados com o novo esquema.

Esse fluxo de trabalho funciona bem no início do desenvolvimento, quando o esquema está evoluindo rapidamente, desde que você não precise preservar os dados. A situação é diferente quando os dados que foram inseridos no banco de dados precisam ser preservados. Quando esse for o caso, use migrações.

Posteriormente na série de tutoriais, você excluirá o banco de dados que foi criado pelo `EnsureCreated` e usará migrações em vez disso. Um banco de dados criado pelo `EnsureCreated` não pode ser atualizado usando migrações.

### <a name="test-the-app"></a>Testar o aplicativo

* Execute o aplicativo.
* Selecione o link **Alunos** e **Criar Novo**.
* Teste os links Editar, Detalhes e Excluir.

## <a name="seed-the-database"></a>Propagar o banco de dados

O método `EnsureCreated` cria um banco de dados vazio. Esta seção adiciona um código que preenche o banco de dados com os dados de teste.

Crie *Data/DbInitializer.cs* com o seguinte código:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  O código verifica se há alunos no banco de dados. Se não houver nenhum aluno, ele adicionará dados de teste ao banco de dados. Ele carrega os dados de teste em matrizes, em vez de em coleções de `List<T>`, para otimizar o desempenho.

* Em *Program.cs*, substitua a chamada `EnsureCreated` por uma chamada `DbInitializer.Initialize`:

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Interrompa o aplicativo se ele estiver em execução e execute o seguinte comando no **PMC (Console do Gerenciador de Pacotes)** :

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Pare o aplicativo se ele estiver em execução e exclua o arquivo *CU.db*.

---

* Reinicie o aplicativo.

* Selecione a página Alunos para ver os dados propagados.

## <a name="view-the-database"></a>Exibir o banco de dados

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Abra o **SSOX** (Pesquisador de Objetos do SQL Server) no menu **Exibir** do Visual Studio.
* No SSOX, selecione **(localdb)\MSSQLLocalDB > Bancos de Dados > SchoolContext-{GUID}** . O nome do banco de dados é gerado usando o nome do contexto fornecido anteriormente, além de um traço e um GUID.
* Expanda o nó **Tabelas**.
* Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas inseridas na tabela.
* Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Código** para ver como o modelo `Student` é mapeado para o esquema de tabela `Student`.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Use a ferramenta SQLite para exibir o esquema de banco de dados e os dados propagados. O arquivo de banco de dados é chamado *CU.db* e está localizado na pasta do projeto.

---

## <a name="asynchronous-code"></a>Código assíncrono

A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.

Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso. Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados. Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S. Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações. Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor pode manipular mais tráfego sem atrasos.

O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução. Para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.

No código a seguir, a palavra-chave [async](/dotnet/csharp/language-reference/keywords/async), o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* A palavra-chave `async` instrui o compilador a:
  * Gerar retornos de chamada para partes do corpo do método.
  * Criar o objeto [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) que é retornado.
* O tipo retornado `Task<T>` representa um trabalho em andamento.
* A palavra-chave `await` faz com que o compilador divida o método em duas partes. A primeira parte termina com a operação que é iniciada de forma assíncrona. A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.
* `ToListAsync` é a versão assíncrona do método de extensão `ToList`.

Algumas coisas a serem consideradas ao escrever um código assíncrono que usa o EF Core:

* Somente instruções que fazem com que consultas ou comandos sejam enviados ao banco de dados são executadas de forma assíncrona. Isso inclui `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`. Isso não inclui instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Um contexto do EF Core não é thread-safe: não tente realizar várias operações em paralelo.
* Para aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca (como para paginação) usam o código assíncrono se eles chamarem métodos do EF Core que enviam consultas ao banco de dados.

Para obter mais informações sobre a programação assíncrona, consulte [Visão geral de Async](/dotnet/standard/async) e [Programação assíncrona com async e await](/dotnet/csharp/programming-guide/concepts/async/).

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

> [!div class="step-by-step"]
> [Próximo tutorial](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

O aplicativo Web de exemplo Contoso University demonstra como criar um aplicativo Razor Pages do ASP.NET Core usando o EF (Entity Framework) Core.

O aplicativo de exemplo é um site de uma Contoso University fictícia. Ele inclui funcionalidades como admissão de alunos, criação de cursos e atribuições de instrutor. Esta página é a primeira de uma série de tutoriais que explica como criar o aplicativo de exemplo Contoso University.

[Baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Instruções de download](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

Familiaridade com as [Páginas do Razor](xref:razor-pages/index). Os novos programadores devem concluir a [Introdução às Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start) antes de começar esta série.

## <a name="troubleshooting"></a>Solução de problemas

Caso tenha um problema que não consiga resolver, em geral, você poderá encontrar a solução comparando o código com o [projeto concluído](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Uma boa maneira de obter ajuda é postando uma pergunta no [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) sobre o [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) ou [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).

## <a name="the-contoso-university-web-app"></a>O aplicativo Web Contoso University

O aplicativo criado nesses tutoriais é um site básico de universidade.

Os usuários podem exibir e atualizar informações de alunos, cursos e instrutores. Veja a seguir algumas das telas criadas no tutorial.

![Página Índice de Alunos](intro/_static/students-index.png)

![Página Editar Alunos](intro/_static/student-edit.png)

O estilo de interface do usuário deste site é próximo ao que é gerado pelos modelos internos. O foco do tutorial é o EF Core com as Páginas do Razor, não a interface do usuário.

## <a name="create-the-contosouniversity-razor-pages-web-app"></a>Criar o aplicativo Web Razor Pages da ContosoUniversity

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **arquivo** do Visual Studio, selecione **novo** **projeto**de >.
* Crie um novo Aplicativo Web ASP.NET Core. Nomeie o projeto **ContosoUniversity**. É importante nomear o projeto *ContosoUniversity* para que os namespaces sejam correspondentes quando o código for copiado/colado.
* Selecione **ASP.NET Core 2.1** na lista suspensa e selecione **Aplicativo Web**.

Para ver imagens das etapas anteriores, confira [Criar um aplicativo Web do Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).
Execute o aplicativo.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>Configurar o estilo do site

Algumas alterações configuram o menu do site, o layout e a home page. Atualize *Pages/Shared/_Layout.cshtml* com as seguintes alterações:

* Altere cada ocorrência de "ContosoUniversity" para "Contoso University". Há três ocorrências.

* Adicione entradas de menu para **Alunos**, **Cursos**, **Instrutores** e **Departamentos** e exclua a entrada de menu **Contato**.

As alterações são realçadas. (Toda a marcação *não* é exibida.)

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

Em *Pages/Index.cshtml*, substitua o conteúdo do arquivo pelo seguinte código para substituir o texto sobre o ASP.NET e MVC pelo texto sobre este aplicativo:

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>Criar o modelo de dados

Crie classes de entidade para o aplicativo Contoso University. Comece com as três seguintes entidades:

![Diagrama de Modelo de Dados Course-Enrollment-Student](intro/_static/data-model-diagram.png)

Há uma relação um-para-muitos entre as entidades `Student` e `Enrollment`. Há uma relação um-para-muitos entre as entidades `Course` e `Enrollment`. Um aluno pode se registrar em qualquer quantidade de cursos. Um curso pode ter qualquer quantidade de alunos registrados.

Nas seções a seguir, é criada uma classe para cada uma dessas entidades.

### <a name="the-student-entity"></a>A entidade Student

![Diagrama da entidade Student](intro/_static/student-entity.png)

Crie uma pasta *Models*. Na pasta *Models*, crie um arquivo de classe chamado *Student.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

A propriedade `ID` se torna a coluna de chave primária da tabela de BD (banco de dados) que corresponde a essa classe. Por padrão, o EF Core interpreta uma propriedade nomeada `ID` ou `classnameID` como a chave primária. Em `classnameID`, `classname` é o nome da classe. A chave primária alternativa reconhecida automaticamente é `StudentID` no exemplo anterior.

A propriedade `Enrollments` é uma [propriedade de navegação](/ef/core/modeling/relationships). As propriedades de navegação vinculam-se a outras entidades que estão relacionadas a essa entidade. Nesse caso, a propriedade `Enrollments` de uma `Student entity` armazena todas as entidades `Enrollment` relacionadas a essa `Student`. Por exemplo, se uma linha Aluno no BD tiver duas linhas Registro relacionadas, a propriedade de navegação `Enrollments` conterá duas entidades `Enrollment`. Uma linha `Enrollment` relacionada é uma linha que contém o valor de chave primária do aluno na coluna `StudentID`. Por exemplo, suponha que o aluno com ID=1 tenha duas linhas na tabela `Enrollment`. A tabela `Enrollment` tem duas linhas com `StudentID` = 1. `StudentID` é uma chave estrangeira na tabela `Enrollment` que especifica o aluno na tabela `Student`.

Se uma propriedade de navegação puder armazenar várias entidades, a propriedade de navegação deverá ser um tipo de lista, como `ICollection<T>`. `ICollection<T>` pode ser especificado ou um tipo como `List<T>` ou `HashSet<T>`. Quando `ICollection<T>` é usado, o EF Core cria uma coleção `HashSet<T>` por padrão. As propriedades de navegação que armazenam várias entidades são provenientes de relações muitos para muitos e um-para-muitos.

### <a name="the-enrollment-entity"></a>A entidade Enrollment

![Diagrama da entidade Enrollment](intro/_static/enrollment-entity.png)

Na pasta *Models*, crie *Enrollment.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

A propriedade `EnrollmentID` é a chave primária. Essa entidade usa o padrão `classnameID` em vez de `ID` como a entidade `Student`. Normalmente, os desenvolvedores escolhem um padrão e o usam em todo o modelo de dados. Em um tutorial posterior, o uso de uma ID sem nome de classe é mostrado para facilitar a implementação da herança no modelo de dados.

A propriedade `Grade` é um `enum`. O ponto de interrogação após a declaração de tipo `Grade` indica que a propriedade `Grade` permite valor nulo. Uma nota nula é diferente de uma nota zero – nulo significa que uma nota não é conhecida ou que ainda não foi atribuída.

A propriedade `StudentID` é uma chave estrangeira e a propriedade de navegação correspondente é `Student`. Uma entidade `Enrollment` está associada a uma entidade `Student` e, portanto, a propriedade contém uma única entidade `Student`. A entidade `Student` é distinta da propriedade de navegação `Student.Enrollments`, que contém várias entidades `Enrollment`.

A propriedade `CourseID` é uma chave estrangeira e a propriedade de navegação correspondente é `Course`. Uma entidade `Enrollment` está associada a uma entidade `Course`.

O EF Core interpreta uma propriedade como uma chave estrangeira se ela é nomeada `<navigation property name><primary key property name>`. Por exemplo, `StudentID` para a propriedade de navegação `Student`, pois a chave primária da entidade `Student` é `ID`. Propriedades de chave estrangeira também podem ser nomeadas `<primary key property name>`. Por exemplo, `CourseID`, pois a chave primária da entidade `Course` é `CourseID`.

### <a name="the-course-entity"></a>A entidade Course

![Diagrama de entidade Curso](intro/_static/course-entity.png)

Na pasta *Models*, crie *Course.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

A propriedade `Enrollments` é uma propriedade de navegação. Uma entidade `Course` pode estar relacionada a qualquer quantidade de entidades `Enrollment`.

O atributo `DatabaseGenerated` permite que o aplicativo especifique a chave primária em vez de fazer com que ela seja gerada pelo BD.

## <a name="scaffold-the-student-model"></a>Gere um modelo de aluno por scaffold

Nesta seção, é feito o scaffold do modelo de aluno. Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo de aluno.

* Crie o projeto.
* Crie a pasta *Pages/Students*.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Em **Gerenciador de soluções**, clique com o botão direito do mouse na pasta *páginas/alunos* > **Adicionar** > **novo item com Scaffold**.
* Na caixa de diálogo **Adicionar Scaffold** , selecione **Razor Pages usando Entity Framework (CRUD)** > **Adicionar**.

Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)** :

* Na lista suspensa **classe Modelo**, selecione **Aluno (ContosoUniversity.Models)** .
* Na linha **Classe de contexto de dados**, selecione o sinal de (mais) **+** e altere o nome gerado para **ContosoUniversity.Models.SchoolContext**.
* Na lista suspensa **Classe de contexto de dados**, selecione **ContosoUniversity.Models.SchoolContext**
* Selecione **Adicionar**.

![Caixa de diálogo CRUD](intro/_static/s1.png)

Confira [Fazer scaffold do modelo de filme](xref:tutorials/razor-pages/model#scaffold-the-movie-model) se tiver problemas na etapa anterior.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Execute os comandos a seguir para fazer scaffold do modelo de aluno.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

O processo de scaffold criou e alterou os seguintes arquivos:

### <a name="files-created"></a>Arquivos criados

* *Pages/Students* Criar, Excluir, Detalhes, Editar, Índice.
* *Data/SchoolContext.cs*

### <a name="file-updates"></a>Atualizações de arquivo

* *Startup.cs*: alterações a esse arquivo serão detalhadas na próxima seção.
* *appsettings.json*: a cadeia de conexão usada para se conectar a um banco de dados local é adicionada.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Examinar o contexto registrado com a injeção de dependência

O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection). Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo. Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor. O código de construtor que obtém uma instância de contexto do BD é mostrado mais adiante no tutorial.

A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.

Examine o método `ConfigureServices` em *Startup.cs*. A linha destacada foi adicionada pelo scaffolder:

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.

## <a name="update-main"></a>Atualizar o principal

Em *Program.cs*, modifique o método `Main` para fazer o seguinte:

* Obtenha uma instância de contexto de BD do contêiner de injeção de dependência.
* Chame o [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).
* Descarte o contexto quando o método `EnsureCreated` for concluído.

O código a seguir mostra o arquivo *Program.cs* atualizado.

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated` garante que o banco de dados do contexto exista. Se ele existir, nenhuma ação será realizada. Se ele não existir, o banco de dados e todos os seus esquemas serão criados. `EnsureCreated` não usa migrações para criar o banco de dados. Um banco de dados criado com `EnsureCreated` não pode ser atualizado posteriormente usando migrações.

`EnsureCreated` é chamado na inicialização do aplicativo, que permite que o seguinte fluxo de trabalho:

* Exclua o BD.
* Altere o esquema de BD (por exemplo, adicione um campo `EmailAddress`).
* Execute o aplicativo.
* `EnsureCreated` cria um BD com a coluna `EmailAddress`.

`EnsureCreated` é conveniente no início do desenvolvimento quando o esquema está evoluindo rapidamente. Mais tarde, no tutorial do banco de dados, é excluído e as migrações são usadas.

### <a name="test-the-app"></a>Testar o aplicativo

Execute o aplicativo e aceite a política de cookies. Este aplicativo não armazena informações pessoais. Você pode ler sobre a política de cookies em [Suporte para a RGPD (Regulamento Geral sobre a Proteção de Dados) da UE](xref:security/gdpr).

* Selecione o link **Alunos** e **Criar Novo**.
* Teste os links Editar, Detalhes e Excluir.

## <a name="examine-the-schoolcontext-db-context"></a>Examine o contexto de BD SchoolContext

A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de BD. O contexto de dados deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). O contexto de dados especifica quais entidades são incluídas no modelo de dados. Neste projeto, a classe é chamada `SchoolContext`.

Atualize *SchoolContext.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

O código destacado cria uma propriedade [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para cada conjunto de entidades. Na terminologia do EF Core:

* Um conjunto de entidades normalmente corresponde a uma tabela de BD.
* Uma entidade corresponde a uma linha da tabela.

`DbSet<Enrollment>` e `DbSet<Course>` podem ser omitidos. O EF Core inclui-os de forma implícita porque a entidade `Student` referencia a entidade `Enrollment` e a entidade `Enrollment` referencia a entidade `Course`. Para este tutorial, mantenha `DbSet<Enrollment>` e `DbSet<Course>` no `SchoolContext`.

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

A cadeia de conexão especifica um [LocalDB do SQL Server](/sql/database-engine/configure-windows/sql-server-2016-express-localdb). LocalDB é uma versão leve do Mecanismo de Banco de Dados do SQL Server Express destinado ao desenvolvimento de aplicativos, e não ao uso em produção. O LocalDB é iniciado sob demanda e executado no modo de usuário e, portanto, não há nenhuma configuração complexa. Por padrão, o LocalDB cria arquivos *.mdf* de BD no diretório `C:/Users/<user>`.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Adicionar um código para inicializar o BD com os dados de teste

O EF Core cria um BD vazio. Nesta seção, um método `Initialize` é escrito para populá-lo com os dados de teste.

Na pasta *Dados*, crie um novo arquivo de classe chamado *DbInitializer.cs* e adicione o seguinte código:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

Observação: o código anterior usa `Models` para o namespace (`namespace ContosoUniversity.Models`) em vez de `Data`. `Models` é consistente com o código gerado pelo scaffolder. Para saber mais, confira [este problema de scaffolding do GitHub](https://github.com/aspnet/Scaffolding/issues/822).

O código verifica se há alunos no BD. Se não houver nenhum aluno no BD, o BD será inicializado com os dados de teste. Ele carrega os dados de teste em matrizes em vez de em coleções `List<T>` para otimizar o desempenho.

O método `EnsureCreated` cria o BD automaticamente para o contexto de BD. Se o BD existir, `EnsureCreated` retornará sem modificar o BD.

Em *Program.cs*, modifique o método `Main` para chamar `Initialize`:

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Interrompa o aplicativo se ele estiver em execução e execute o seguinte comando no **PMC (Console do Gerenciador de Pacotes)** :

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Pare o aplicativo se ele estiver em execução e exclua o arquivo *CU.db*.

---

## <a name="view-the-db"></a>Exibir o BD

O nome do banco de dados é gerado usando o nome do contexto fornecido anteriormente, além de um traço e um GUID. Assim, o nome do banco de dados será "SchoolContext-{GUID}". O GUID será diferente para cada usuário.
Abra o **SSOX** (Pesquisador de Objetos do SQL Server) no menu **Exibir** do Visual Studio.
No SSOX, clique em **(localdb)\MSSQLLocalDB > Bancos de Dados > SchoolContext-{GUID}** .

Expanda o nó **Tabelas**.

Clique com o botão direito do mouse na tabela **Aluno** e clique em **Exibir Dados** para ver as colunas criadas e as linhas inseridas na tabela.

## <a name="asynchronous-code"></a>Código assíncrono

A programação assíncrona é o modo padrão do ASP.NET Core e EF Core.

Um servidor Web tem um número limitado de threads disponíveis e, em situações de alta carga, todos os threads disponíveis podem estar em uso. Quando isso acontece, o servidor não pode processar novas solicitações até que os threads são liberados. Com um código síncrono, muitos threads podem ser vinculados enquanto realmente não são fazendo nenhum trabalho porque estão aguardando a conclusão da E/S. Com um código assíncrono, quando um processo está aguardando a conclusão da E/S, seu thread é liberado para o servidor para ser usado para processar outras solicitações. Como resultado, o código assíncrono permite que os recursos do servidor sejam usados com mais eficiência, e o servidor fica capacitado a manipular mais tráfego sem atrasos.

O código assíncrono introduz uma pequena quantidade de sobrecarga em tempo de execução. Para situações de baixo tráfego, o impacto no desempenho é insignificante, enquanto para situações de alto tráfego, a melhoria de desempenho potencial é significativa.

No código a seguir, a palavra-chave [async](/dotnet/csharp/language-reference/keywords/async), o valor retornado `Task<T>`, a palavra-chave `await` e o método `ToListAsync` fazem o código ser executado de forma assíncrona.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* A palavra-chave `async` instrui o compilador a:
  * Gerar retornos de chamada para partes do corpo do método.
  * Criar automaticamente o objeto [Task](/dotnet/api/system.threading.tasks.task) que é retornado. Para obter mais informações, consulte [Tipo de retorno de Tarefa](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* O tipo de retorno implícito `Task` representa um trabalho em andamento.
* A palavra-chave `await` faz com que o compilador divida o método em duas partes. A primeira parte termina com a operação que é iniciada de forma assíncrona. A segunda parte é colocada em um método de retorno de chamada que é chamado quando a operação é concluída.
* `ToListAsync` é a versão assíncrona do método de extensão `ToList`.

Algumas coisas a serem consideradas ao escrever um código assíncrono que usa o EF Core:

* Somente instruções que fazem com que consultas ou comandos sejam enviados ao BD são executadas de forma assíncrona. Isso inclui `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` e `SaveChangesAsync`. Isso não inclui instruções que apenas alteram um `IQueryable`, como `var students = context.Students.Where(s => s.LastName == "Davolio")`.
* Um contexto do EF Core não é thread-safe: não tente realizar várias operações em paralelo.
* Para aproveitar os benefícios de desempenho do código assíncrono, verifique se os pacotes de biblioteca (como para paginação) usam o código assíncrono se eles chamam métodos do EF Core que enviam consultas ao BD.

Para obter mais informações sobre a programação assíncrona, consulte [Visão geral de Async](/dotnet/standard/async) e [Programação assíncrona com async e await](/dotnet/csharp/programming-guide/concepts/async/).

No próximo tutorial, as operações CRUD (criar, ler, atualizar e excluir) básicas são examinadas.



## <a name="additional-resources"></a>Recursos adicionais

* [Versão do YouTube deste tutorial](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [Avançar](xref:data/ef-rp/crud)

::: moniker-end
