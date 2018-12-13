---
title: Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar classes de gerenciamento de filmes em um banco de dados usando o EF Core (Entity Framework Core).
ms.author: riande
monikerRange: '>= aspnetcore-2.2'
ms.date: 12/3/2018
uid: tutorials/razor-pages/model
ms.openlocfilehash: 91fee1db820493be671fecaee3cfb4c1b7df8bd3
ms.sourcegitcommit: 49faca2644590fc081d86db46ea5e29edfc28b7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2018
ms.locfileid: "53121357"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a>Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[](~/includes/rp/download.md)]

Nesta seção, classes são adicionadas para o gerenciamento de filmes em um banco de dados. Essas classes são usadas com o [EF Core](/ef/core) (Entity Framework Core) para trabalhar com um banco de dados. O EF Core é uma estrutura ORM (mapeamento relacional de objetos) que simplifica o código de acesso a dados.

As classes de modelo são conhecidas como classes POCO (de "objetos CLR básicos") porque não têm nenhuma dependência do EF Core. Elas definem as propriedades dos dados que são armazenados no banco de dados.

[Exiba ou baixe](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages-start/sample/) a amostra.

## <a name="add-a-data-model"></a>Adicionar um modelo de dados

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**. Nomeie a pasta *Models*.

Clique com o botão direito do mouse na pasta *Modelos*. Selecione **Adicionar** > **Classe**. Dê à classe o nome **Movie**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Adicione uma pasta chamada *Models*.
* Adicionar uma classe denominada *Movie.cs* à pasta *Modelos*.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** e então selecione **Adicionar** > **Nova Pasta**. Nomeie a pasta *Models*.
* Clique com o botão direito do mouse na pasta *Modelos* e, em seguida, selecione **Adicionar** > **Novo Arquivo**.
* Na caixa de diálogo **Novo Arquivo**:

  * Selecione **Geral** no painel esquerdo.
  * Selecione **Classe Vazia** no painel central.
  * Nomeie a classe **Movie** e selecione **Novo**.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<!-- End of VS tabs -->

---

Crie o projeto para verificar se não há erros de compilação.

## <a name="scaffold-the-movie-model"></a>Fazer scaffold do modelo de filme

Nesta seção, é feito o scaffold do modelo de filme. Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Crie uma pasta *Pages/Movies*:

* Clique com o botão direito do mouse na pasta *Pages* > **Adicionar** > **Nova Pasta**.
* Dê à pasta o nome *Movies*

Clique com o botão direito do mouse na pasta *Pages/Movies* > **Adicionar** > **Novo Item Gerado por Scaffold**.

![Imagem das instruções anteriores.](model/_static/sca.png)

Na caixa de diálogo **Adicionar Scaffold**, selecione **Razor Pages usando o Entity Framework (CRUD)** > **Adicionar**.

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:

* Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)**.
* Na linha **Classe de contexto de dados**, selecione o sinal **+** (+) e aceite o nome gerado **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Selecione **Adicionar**.

![Imagem das instruções anteriores.](model/_static/arp.png)

O arquivo *appsettings.json* é atualizado com a cadeia de conexão usada para se conectar a um banco de dados local.

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).
* Instale a ferramenta de scaffolding:

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **No Windows**, execute o comando a seguir:

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **No macOS e Linux**, execute o comando a seguir:

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Abra uma janela de comando no diretório do projeto (o diretório que contém os arquivos *Program.cs*, *Startup.cs* e *.csproj*).
* Execute o seguinte comando:

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

O processo de scaffold cria e atualiza os arquivos a seguir:

### <a name="files-created"></a>Arquivos criados

* *Pages/Movies*: Create, Delete, Details, Edit e Index.
* *Data/RazorPagesMovieContext.cs*

### <a name="file-updated"></a>Arquivo atualizado

* *Startup.cs*

Os arquivos criados e atualizados são explicados na próxima seção.

<a name="pmc"></a>

## <a name="initial-migration"></a>Migração inicial

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

<!-- VS -------------------------->

Nesta seção, o PMC (Console de Gerenciador de Pacotes) é usado para:

* Adicione uma migração inicial.
* Atualize o banco de dados com a migração inicial.

No menu **Ferramentas**, selecione **Gerenciador de pacotes NuGet** > **Console do Gerenciador de pacotes**.

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

No PMC, insira os seguintes comandos:

```PMC
Add-Migration Initial
Update-Database
```

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- Mac -------------------------->

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---  
<!-- End of VS tabs -->

O comando `ef migrations add InitialCreate` gera código para criar o esquema de banco de dados inicial. O esquema se baseia no modelo especificado no `DbContext` (no arquivo *Models/RazorPagesMovieContext.cs*). O argumento `InitialCreate` é usado para nomear as migrações. Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é selecionado.

O comando `ef database update` executa o método `Up` no arquivo *Migrations/\<time-stamp>_InitialCreate.cs*. O método `Up` cria o banco de dados.

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="examine-the-context-registered-with-dependency-injection"></a>Examinar o contexto registrado com a injeção de dependência

O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection). Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo. Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor. O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.

A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.

Examine o método `Startup.ConfigureServices`. A linha destacada foi adicionada pelo scaffolder:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

O `RazorPagesMovieContext` coordena a funcionalidade do EF Core (Criar, Ler, Atualizar, Excluir etc.) para o modelo `Movie`. O contexto de dados (`RazorPagesMovieContext`) deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). O contexto de dados especifica quais entidades são incluídas no modelo de dados.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

O código anterior cria uma propriedade [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades. Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados. Uma entidade corresponde a uma linha da tabela.

O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.
<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

<!-- End of VS tabs -->

---

O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial. O esquema é baseado no modelo especificado no `RazorPagesMovieContext` (no arquivo *Data/RazorPagesMovieContext.cs*). O argumento `Initial` é usado para nomear as migrações. Qualquer nome pode ser usado, mas, por convenção, um nome que descreve a migração é usado. Consulte [Introdução às migrações](xref:data/ef-mvc/migrations#introduction-to-migrations) para obter mais informações.

O comando `Update-Database` executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.

<a name="test"></a>

### <a name="test-the-app"></a>Testar o aplicativo

* Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).

Se você obtiver o erro:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Você perdeu a [etapa de migrações](#pmc).

* Teste o link **Criar**.

  ![Criar página](model/_static/conan.png)
  
  > [!NOTE]
  > Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`. Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades com idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal e formatos de data diferentes do inglês dos EUA, o aplicativo precisa ser globalizado. Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).

* Teste os links **Editar**, **Detalhes** e **Excluir**.

O tutorial a seguir explica os arquivos criados por scaffolding.

> [!div class="step-by-step"]
> [Anterior: Introdução](xref:tutorials/razor-pages/razor-pages-start)
> [Próximo: Páginas Razor geradas por scaffolding](xref:tutorials/razor-pages/page)
