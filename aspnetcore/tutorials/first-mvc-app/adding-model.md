---
title: Adicione um modelo a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Adicione um modelo para um aplicativo simples do ASP.NET Core.
ms.author: riande
ms.date: 8/15/2019
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 2fac37e7069fb2a464d4de1da8912197f7adf8a8
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73761094"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a>Adicione um modelo a um aplicativo ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Tom Dykstra](https://github.com/tdykstra)

Nesta seção, você adiciona classes para gerenciamento de filmes em um banco de dados. Essas classes serão a parte “**M**odel” parte do aplicativo **M**VC.

Você usa essas classes com o [Entity Framework Core](/ef/core) (EF Core) para trabalhar com um banco de dados. O EF Core é uma estrutura ORM (de mapeamento relacional de objetos) que simplifica o código de acesso a dados que você precisa escrever.

As classes de modelo que você cria são conhecidas como classes de dados POCO (de **o**bjetos **C**L**R** **b**ásicos) porque elas não têm nenhuma dependência no EF Core. Elas apenas definem as propriedades dos dados que serão armazenados no banco de dados.

Neste tutorial, você escreve as classes de modelo primeiro e o EF Core cria o banco de dados. Uma abordagem alternativa não abordada aqui é gerar classes de modelo de um banco de dados existente. Para obter informações sobre essa abordagem, consulte [ASP.NET Core – Banco de dados existente](/ef/core/get-started/aspnetcore/existing-db).

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Adicionar uma classe de modelo de dados

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**. Dê ao arquivo o nome de *Movie.cs*.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Adicione um arquivo chamado *Movie.cs* à pasta *Modelos*.

---

Atualize o arquivo *Movie.cs* com o seguinte código:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

A classe `Movie` contém um campo `Id`, que é exigido pelo banco de dados para a chave primária.

O atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) em `ReleaseDate` especifica o tipo de dados (`Date`). Com esse atributo:

  * O usuário não precisa inserir informações de tempo no campo de data.
  * Somente a data é exibida, não as informações de tempo.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) são abordados em um tutorial posterior.

## <a name="add-nuget-packages"></a>Adicionar pacotes do NuGet

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).

![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

No PMC, execute o seguinte comando:

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

O comando anterior adiciona o provedor SQL Server do EF Core. O pacote do provedor instala o pacote do EF Core como uma dependência. Pacotes adicionais são instalados automaticamente na etapa de scaffolding posteriormente no tutorial.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a>Criar uma classe de contexto de banco de dados

Uma classe de contexto de banco de dados é necessária para coordenar a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir) para o modelo `Movie`. O contexto de banco de dados é derivado de [Microsoft. EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) e especifica as entidades a serem incluídas no modelo de dados.

Crie uma pasta de *Dados*.

Adicione um arquivo *Data/MvcMovieContext.cs* com o seguinte código: 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades. Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados. Uma entidade corresponde a uma linha da tabela.

<a name="reg"></a>

## <a name="register-the-database-context"></a>Registrar o contexto do banco de dados

O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection). Os serviços (como o contexto de BD do EF Core) devem ser registrados com a DI durante a inicialização do aplicativo. Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor. O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial. Nesta seção, você registra o contexto do banco de dados com o contêiner DI.

Adicione os demonstrativos do `using` a seguir à parte superior do *Startup.cs*:

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Adicione o código realçado a seguir a `Startup.ConfigureServices`:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a>Adicionar uma cadeia de conexão de banco de dados

Adicione uma cadeia de conexão ao arquivo *appsettings.json*:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

Compile o projeto como uma verificação de erros do compilador.

## <a name="scaffold-movie-pages"></a>Aplicar scaffold a páginas de filme

Use a ferramenta scaffolding para produzir páginas CRUD (criar, ler, atualizar e excluir) para o modelo de filme.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Controladores* **> Adicionar > Novo Item com Scaffold**.

![exibição da etapa acima](adding-model/_static/add_controller21.png)

Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

Preencha a caixa de diálogo **Adicionar Controlador**:

* **Classe de modelo:** *Movie (MvcMovie.Models)*
* **Classe de contexto de dados:** *MvcMovieContext (MvcMovie. Data)*

![Adicionar contexto de dados](adding-model/_static/dc3.png)

* **Exibições:** mantenha o padrão de cada opção marcado
* **Nome do controlador:** mantenha o *MoviesController* padrão
* Selecione **Adicionar**

O Visual Studio cria:

* Um controlador de filmes (*Controllers/MoviesController.cs*)
* Arquivos de exibição do Razor para as páginas Criar, Excluir, Detalhes, Editar e Índice (*Views/Movies/\*.cshtml*)

A criação automática desses arquivos é conhecida como *scaffolding*.

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).

* No Linux, exporte o caminho da ferramenta Scaffold:

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Execute o seguinte comando:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).

* Execute o seguinte comando:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

Você não pode usar as páginas com scaffold ainda porque o banco de dados não existe. Se você executar o aplicativo e clicar no link do **aplicativo de filme** , receberá uma mensagem de erro *não é possível abrir o banco de dados* ou *nenhuma tabela: filme* .

<a name="migration"></a>

## <a name="initial-migration"></a>Migração inicial

Use o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para criar o banco de dados. As migrações são um conjunto de ferramentas que permitem criar e atualizar um banco de dados para corresponder ao seu modelo de dados.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).

No PMC, digite os seguintes comandos:

```PMC
Add-Migration InitialCreate
Update-Database
```

* `Add-Migration InitialCreate`: gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* . O argumento `InitialCreate` é o nome da migração. Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado. Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados. O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`.

* `Update-Database`: atualiza o banco de dados para a migração mais recente, que o comando anterior criou. Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.

  O comando de atualização de banco de dados gera o seguinte aviso: 

  > Nenhum tipo foi especificado para a coluna decimal 'Preço' no tipo de entidade 'Filme'. Isso fará com que valores sejam truncados silenciosamente se não couberem na precisão e na escala padrão. Especifique explicitamente o tipo de coluna do SQL Server que pode acomodar todos os valores usando 'HasColumnType()'.

  Você pode ignorar esse aviso, ele será corrigido em um tutorial posterior.

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Execute os seguintes comandos da CLI do .NET Core:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* `ef migrations add InitialCreate`: gera um arquivo de migração de *migrações/{timestamp} _InitialCreate. cs* . O argumento `InitialCreate` é o nome da migração. Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado. Como essa é a primeira migração, a classe gerada contém o código para criar o esquema de banco de dados. O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs*).

* `ef database update`: atualiza o banco de dados para a migração mais recente, que o comando anterior criou. Esse comando executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.

[!INCLUDE [ more information on the CLI tools for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a>A classe InitialCreate

Examine o arquivo de migração *Migrations/{timestamp}_InitialCreate.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

 O método `Up` cria a tabela de filmes e configura `Id` como a chave primária. O método `Down` reverte as alterações de esquema feitas pela migração `Up`.

<a name="test"></a>

## <a name="test-the-app"></a>Testar o aplicativo

* Execute o aplicativo e clique no link **Aplicativo de Filme**.

  Se você receber uma exceção semelhante a uma das seguintes:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  Você provavelmente perdeu a [etapa de migrações](#migration).

* Teste a página **Criar**. Inserir e enviar dados.

  > [!NOTE]
  > Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`. Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado. Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Teste os links **Editar**, **Detalhes** e **Excluir**.

## <a name="dependency-injection-in-the-controller"></a>Injeção de dependência no controlador

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador. O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador. O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modelos fortemente tipados e a palavra-chave @model

Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`. O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.

O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição. Essa abordagem fortemente tipada permite a verificação de código em tempo de compilação. O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições.

Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

O parâmetro `id` geralmente é passado como dados de rota. Por exemplo, `https://localhost:5001/movies/details/1` define:

* O controlador para o controlador `movies` (o primeiro segmento de URL).
* A ação para `details` (o segundo segmento de URL).
* A ID como 1 (o último segmento de URL).

Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:

`https://localhost:5001/movies/details?id=1`

O parâmetro `id` é definido como um [tipo que permite valor nulo](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), caso um valor de ID não seja fornecido.

Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:

```csharp
return View(movie);
   ```

Examine o conteúdo do arquivo *Views/Movies/Details.cshtml*:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

A instrução `@model` na parte superior do arquivo de exibição especifica o tipo de objeto que a exibição espera. Quando o controlador de filme foi criado, a seguinte instrução `@model` foi incluída:

```HTML
@model MvcMovie.Models.Movie
   ```

Essa diretiva `@model` permite o acesso ao filme que o controlador passou para a exibição. O objeto `Model` é fortemente tipado. Por exemplo, na exibição *Details.cshtml*, o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado. Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.

Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies. Observe como o código cria um objeto `List` quando ele chama o método `View`. O código passa esta lista `Movies` do método de ação `Index` para a exibição:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Quando o controlador de filmes foi criado, o scaffolding incluiu a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml*:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado. Por exemplo, na exibição *Index.cshtml*, o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`. Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código.

## <a name="additional-resources"></a>Recursos adicionais

* [Auxiliares de Marcas](xref:mvc/views/tag-helpers/intro)
* [Globalização e localização](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Anterior – Adicionando uma exibição](adding-view.md)
> [Próximo – Trabalhando com o SQL](working-with-sql.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Adicionar uma classe de modelo de dados

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Clique com o botão direito do mouse na pasta *Models* > **Adicionar** > **Classe**. Dê à classe o nome **Movie**.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a>Fazer scaffold do modelo de filme

Nesta seção, é feito o scaffold do modelo de filme. Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Controladores* **> Adicionar > Novo Item com Scaffold**.

![exibição da etapa acima](adding-model/_static/add_controller21.png)

Na caixa de diálogo **Adicionar Scaffold**, selecione **Controlador MVC com exibições, usando o Entity Framework > Adicionar**.

![Caixa de diálogo Adicionar Scaffold](adding-model/_static/add_scaffold21.png)

Preencha a caixa de diálogo **Adicionar Controlador**:

* **Classe de modelo:** *Movie (MvcMovie.Models)*
* **Classe de contexto de dados:** selecione o ícone **+** e adicione o **MvcMovie.Models.MvcMovieContext** padrão

![Adicionar contexto de dados](adding-model/_static/dc.png)

* **Exibições:** mantenha o padrão de cada opção marcado
* **Nome do controlador:** mantenha o *MoviesController* padrão
* Selecione **Adicionar**

![Caixa de diálogo Adicionar Controlador](adding-model/_static/add_controller2.png)

O Visual Studio cria:

* Uma [classe de contexto de banco de dados](xref:data/ef-mvc/intro#create-the-database-context) do Entity Framework Core (*Data/MvcMovieContext.cs*)
* Um controlador de filmes (*Controllers/MoviesController.cs*)
* Arquivos de exibição do Razor para as páginas Criar, Excluir, Detalhes, Editar e Índice (*Views/Movies/\*.cshtml*)

A criação automática do contexto de banco de dados e das exibições e métodos de ação [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (criar, ler, atualizar e excluir) é conhecida como *scaffolding*.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).
* Instale a ferramenta de scaffolding:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* No Linux, exporte o caminho da ferramenta Scaffold:

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Execute o seguinte comando:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).
* Instale a ferramenta de scaffolding:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Execute o seguinte comando:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

Se você executar o aplicativo e clicar no link **Filme do MVC**, receberá um erro semelhante ao seguinte:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

``` error
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

Você precisa criar o banco de dados e usará o recurso [Migrações](xref:data/ef-mvc/migrations) do EF Core para fazer isso. As Migrações permitem criar um banco de dados que corresponde ao seu modelo de dados e atualizar o esquema de banco de dados quando o modelo de dados é alterado.

<a name="pmc"></a>

## <a name="initial-migration"></a>Migração inicial

Nesta seção, há estas tarefas:

* Adicionar uma migração inicial.
* Atualize o banco de dados com a migração inicial.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes** (PMC).

   ![Menu do PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. No PMC, digite os seguintes comandos:

   ```PMC
   Add-Migration Initial
   Update-Database
   ```

   O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial.

   O esquema de banco de dados é baseado no modelo especificado na classe `MvcMovieContext`. O argumento `Initial` é o nome da migração. Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado. Para obter mais informações, consulte <xref:data/ef-mvc/migrations>.

   O comando `Update-Database` executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

O comando `ef migrations add InitialCreate` gera código para criar o esquema de banco de dados inicial.

O esquema do banco de dados é baseado no modelo especificado na classe `MvcMovieContext` (no arquivo *Data/MvcMovieContext.cs*). O argumento `InitialCreate` é o nome da migração. Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.

---

## <a name="examine-the-context-registered-with-dependency-injection"></a>Examinar o contexto registrado com a injeção de dependência

O ASP.NET Core foi criado com a [DI (injeção de dependência)](xref:fundamentals/dependency-injection). Os serviços (como o contexto de BD do EF Core) são registrados com a DI durante a inicialização do aplicativo. Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor. O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da DI.

Examine o seguinte método `Startup.ConfigureServices`. A linha destacada foi adicionada pelo scaffolder:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

O `MvcMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`. O contexto de dados (`MvcMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). O contexto de dados especifica quais entidades são incluídas no modelo de dados:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades. Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados. Uma entidade corresponde a uma linha da tabela.

O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Você criou um contexto de BD e o registrou no contêiner da DI.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testar o aplicativo

* Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).

Se você receber uma exceção de banco de dados semelhante ao seguinte:

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Você perdeu a [etapa de migrações](#pmc).

* Teste o link **Criar**. Inserir e enviar dados.

  > [!NOTE]
  > Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`. Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado. Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Teste os links **Editar**, **Detalhes** e **Excluir**.

Examine a classe `Startup`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

o código realçado acima mostra o contexto de banco de dados do filme que está sendo adicionado ao contêiner [Injeção de Dependência](xref:fundamentals/dependency-injection):

* `services.AddDbContext<MvcMovieContext>(options =>` especifica o banco de dados a ser usado e a cadeia de conexão.
* `=>` é um [operador lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)

Abra o arquivo *Controllers/MoviesController.cs* e examine o construtor:

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

O construtor usa a [Injeção de Dependência](xref:fundamentals/dependency-injection) para injetar o contexto de banco de dados (`MvcMovieContext`) no controlador. O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Modelos fortemente tipados e a palavra-chave @model

Anteriormente neste tutorial, você viu como um controlador pode passar dados ou objetos para uma exibição usando o dicionário `ViewData`. O dicionário `ViewData` é um objeto dinâmico que fornece uma maneira conveniente de associação tardia para passar informações para uma exibição.

O MVC também fornece a capacidade de passar objetos de modelo fortemente tipados para uma exibição. Essa abordagem fortemente tipada habilita uma melhor verificação do tempo de compilação do código. O mecanismo de scaffolding usou essa abordagem (ou seja, passando um modelo fortemente tipado) com a classe `MoviesController` e as exibições quando ele criou os métodos e as exibições.

Examine o método `Details` gerado no arquivo *Controllers/MoviesController.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

O parâmetro `id` geralmente é passado como dados de rota. Por exemplo, `https://localhost:5001/movies/details/1` define:

* O controlador para o controlador `movies` (o primeiro segmento de URL).
* A ação para `details` (o segundo segmento de URL).
* A ID como 1 (o último segmento de URL).

Você também pode passar a `id` com uma cadeia de consulta da seguinte maneira:

`https://localhost:5001/movies/details?id=1`

O parâmetro `id` é definido como um [tipo que permite valor nulo](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`), caso um valor de ID não seja fornecido.

Um [expressão lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) é passada para `FirstOrDefaultAsync` para selecionar as entidades de filmes que correspondem ao valor da cadeia de consulta ou de dados da rota.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Se for encontrado um filme, uma instância do modelo `Movie` será passada para a exibição `Details`:

```csharp
return View(movie);
   ```

Examine o conteúdo do arquivo *Views/Movies/Details.cshtml*:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Incluindo uma instrução `@model` na parte superior do arquivo de exibição, você pode especificar o tipo de objeto esperado pela exibição. Quando você criou o controlador de filmes, a seguinte instrução `@model` foi automaticamente incluída na parte superior do arquivo *Details.cshtml*:

```HTML
@model MvcMovie.Models.Movie
   ```

Esta diretiva `@model` permite acessar o filme que o controlador passou para a exibição usando um objeto `Model` fortemente tipado. Por exemplo, na exibição *Details.cshtml*, o código passa cada campo de filme para os Auxiliares de HTML `DisplayNameFor` e `DisplayFor` com o objeto `Model` fortemente tipado. Os métodos `Create` e `Edit` e as exibições também passam um objeto de modelo `Movie`.

Examine a exibição *Index.cshtml* e o método `Index` no controlador Movies. Observe como o código cria um objeto `List` quando ele chama o método `View`. O código passa esta lista `Movies` do método de ação `Index` para a exibição:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Quando você criou o controlador de filmes, o scaffolding incluiu automaticamente a seguinte instrução `@model` na parte superior do arquivo *Index.cshtml*:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

A diretiva `@model` permite acessar a lista de filmes que o controlador passou para a exibição usando um objeto `Model` fortemente tipado. Por exemplo, na exibição *Index.cshtml*, o código executa um loop pelos filmes com uma instrução `foreach` no objeto `Model` fortemente tipado:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Como o objeto `Model` é fortemente tipado (como um objeto `IEnumerable<Movie>`), cada item no loop é tipado como `Movie`. Entre outros benefícios, isso significa que você obtém a verificação do tempo de compilação do código:

## <a name="additional-resources"></a>Recursos adicionais

* [Auxiliares de Marcas](xref:mvc/views/tag-helpers/intro)
* [Globalização e localização](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Anterior – Adicionando uma exibição](adding-view.md)
> [Próximo – Trabalhando com o SQL](working-with-sql.md)

::: moniker-end
