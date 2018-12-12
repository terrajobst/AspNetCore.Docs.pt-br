---
title: Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar classes de gerenciamento de filmes em um banco de dados usando o EF Core (Entity Framework Core).
ms.author: riande
ms.date: 05/30/2018
uid: tutorials/razor-pages/model
ms.openlocfilehash: 0d33901805d6728fb8006f14d41090b874ab28b1
ms.sourcegitcommit: e8d80ff566bfe505b43389d7bc4551edb1c0c872
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52549104"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a>Adicionar um modelo a um aplicativo Páginas Razor no ASP.NET Core

::: moniker range=">= aspnetcore-2.1"

[!INCLUDE [model1](~/includes/RP/model1.md)]

## <a name="add-a-data-model"></a>Adicionar um modelo de dados

No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**. Nomeie a pasta *Models*.

Clique com o botão direito do mouse na pasta *Modelos*. Selecione **Adicionar** > **Classe**. Nomeie a classe **Movie** e substitua o conteúdo da classe `Movie` pelo seguinte código:

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie21/Models/Movie1.cs?name=snippet)]

## <a name="scaffold-the-movie-model"></a>Fazer scaffold do modelo de filme

Nesta seção, é feito o scaffold do modelo de filme. Ou seja, a ferramenta de scaffolding gera páginas para operações de CRUD (Criar, Ler, Atualizar e Excluir) para o modelo do filme.

Crie uma pasta *Pages/Movies*:

* No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Páginas* > **Adicionar** > **Nova Pasta**.
* Dê à pasta o nome *Movies*

No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Páginas/Filmes* pasta > **Adicionar** > **Novo item com scaffold**.

![Imagem das instruções anteriores.](model/_static/sca.png)

Na caixa de diálogo **Adicionar Scaffold**, selecione **Razor Pages usando o Entity Framework (CRUD)** > **Adicionar**.

![Imagem das instruções anteriores.](model/_static/add_scaffold.png)

Conclua a caixa de diálogo **Adicionar Razor Pages usando o Entity Framework (CRUD)**:

* Na lista suspensa **Classe de modelo**, selecione **Filme (RazorPagesMovie.Models)**.
* Na linha **Classe de contexto de dados**, selecione o sinal **+** (+) e aceite o nome gerado **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Selecione **Adicionar**.

![Imagem das instruções anteriores.](model/_static/arp.png)

O processo de scaffold cria e atualiza os arquivos a seguir:

### <a name="files-created"></a>Arquivos criados

* *Pages/Movies*: Criar, Excluir, Detalhes, Editar, Índice. Essas páginas serão detalhadas no próximo tutorial.
* *Data/RazorPagesMovieContext.cs*

### <a name="file-updated"></a>Arquivo atualizado

* *Startup.cs*: alterações nesse arquivo serão detalhadas na próxima seção.
* *appsettings.json*: a cadeia de conexão usada para se conectar a um banco de dados local é adicionada.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Examinar o contexto registrado com a injeção de dependência

O ASP.NET Core é construído com a [injeção de dependência](xref:fundamentals/dependency-injection). Serviços (como o contexto de BD do EF Core) são registrados com injeção de dependência durante a inicialização do aplicativo. Os componentes que exigem esses serviços (como as Páginas do Razor) recebem esses serviços por meio de parâmetros do construtor. O código de construtor que obtém uma instância de contexto do BD será mostrado mais adiante no tutorial.

A ferramenta de scaffolding criou automaticamente um contexto de BD e o registrou no contêiner da injeção de dependência.

Examine o método `Startup.ConfigureServices`. A linha destacada foi adicionada pelo scaffolder:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie21/Startup.cs?name=snippet_ConfigureServices&highlight=12-13)]

A classe principal que coordena a funcionalidade do EF Core de um modelo de dados é a classe de contexto de BD. O contexto de dados deriva de [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext). O contexto de dados especifica quais entidades são incluídas no modelo de dados. Neste projeto, a classe é chamada `RazorPagesMovieContext`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie21/Data/RazorPagesMovieContext.cs)]

O código anterior cria uma propriedade [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) para o conjunto de entidades. Na terminologia do Entity Framework, um conjunto de entidades normalmente corresponde a uma tabela de banco de dados. Uma entidade corresponde a uma linha da tabela.

O nome da cadeia de conexão é passado para o contexto com a chamada de um método em um objeto [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions). Para o desenvolvimento local, o [sistema de configuração do ASP.NET Core](xref:fundamentals/configuration/index) lê a cadeia de conexão do arquivo *appsettings.json*.

<a name="pmc"></a>
## <a name="perform-initial-migration"></a>Executar a migração inicial

Nesta seção, você deve usar o PMC (Console de Gerenciador de Pacotes) para:

* Adicione uma migração inicial.
* Atualize o banco de dados com a migração inicial.

No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

No PMC, insira os seguintes comandos:

```PMC
Add-Migration Initial
Update-Database
```

Como alternativa, é possível usar os seguintes comandos do .NET Core CLI na pasta do projeto:

```console
dotnet ef migrations add Initial
dotnet ef database update
```

Ignore a mensagem de aviso a seguir, que você corrigirá em um tutorial posterior:

```console
Microsoft.EntityFrameworkCore.Model.Validation[30000]
      No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'ForHasColumnType()'.
```

O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial. O esquema é baseado no modelo especificado no `RazorPagesMovieContext` (no arquivo *Data/RazorPagesMovieContext.cs*). O argumento `Initial` é usado para nomear as migrações. Você pode usar qualquer nome, mas, por convenção, escolha um nome que descreve a migração. Consulte [Introdução às migrações](xref:data/ef-mvc/migrations#introduction-to-migrations) para obter mais informações.

O comando `Update-Database` executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.

Se você obtiver o erro:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Você perdeu a [etapa de migrações](#pmc).

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!INCLUDE [model1](~/includes/RP/model1.md)]

## <a name="add-a-data-model"></a>Adicionar um modelo de dados

No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **RazorPagesMovie** > **Adicionar** > **Nova Pasta**. Nomeie a pasta *Models*.

Clique com o botão direito do mouse na pasta *Modelos*. Selecione **Adicionar** > **Classe**. Nomeie a classe **Movie** e adicione as seguintes propriedades:

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<a name="cs"></a>
### <a name="add-a-database-connection-string"></a>Adicionar uma cadeia de conexão de banco de dados

Adicione uma cadeia de conexão ao arquivo *appsettings.json*.

[!code-json[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=8-10)]

<a name="reg"></a>
###  <a name="register-the-database-context"></a>Registrar o contexto de banco de dados

Registre o contexto do banco de dados com o contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) no [método ConfigureServices da classe Startup](xref:fundamentals/startup#the-startup-class) (*Startup.cs*):

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=3-5,7-9)]

Compile o projeto para verificar se não há erros.

<a name="pmc"></a>
## <a name="add-scaffold-tooling-and-perform-initial-migration"></a>Adicionar ferramentas de scaffold e executar a migração inicial

Nesta seção, você deve usar o PMC (Console de Gerenciador de Pacotes) para:

* Adicione o pacote de geração de código da Web do Visual Studio. Esse pacote é necessário para executar o mecanismo de scaffolding.
* Adicionar uma migração inicial.
* Atualize o banco de dados com a migração inicial.

No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.

  ![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

No PMC, insira os seguintes comandos:

```powershell
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design -Version 2.0.3
Add-Migration Initial
Update-Database
```

Como alternativa, os seguintes comandos da CLI do .NET Core podem ser usados:

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet ef migrations add Initial
dotnet ef database update
```

Ignore a mensagem a seguir:

```console
Microsoft.EntityFrameworkCore.Model.Validation[30000]
      No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'ForHasColumnType()'
```

você corrigirá isso no próximo tutorial.

O comando `Install-Package` instala as ferramentas necessárias para executar o mecanismo de scaffolding.

O comando `Add-Migration` gera código para criar o esquema de banco de dados inicial. O esquema é baseado no modelo especificado no `DbContext` (no arquivo *Models/MovieContext.cs*). O argumento `Initial` é usado para nomear as migrações. Você pode usar qualquer nome, mas, por convenção, escolha um nome que descreve a migração. Consulte [Introdução às migrações](xref:data/ef-mvc/migrations#introduction-to-migrations) para obter mais informações.

O comando `Update-Database` executa o método `Up` no arquivo *Migrations/{time-stamp}_InitialCreate.cs*, que cria o banco de dados.

[!INCLUDE [model 4windows](~/includes/RP/model4Win.md)]

[!INCLUDE [model 4](~/includes/RP/model4tbl.md)]

::: moniker-end

<a name="test"></a>

### <a name="test-the-app"></a>Testar o aplicativo

* Executar o aplicativo e acrescentar `/Movies` à URL no navegador (`http://localhost:port/movies`).
* Teste o link **Criar**.

  > [!NOTE]
  > Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`. Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades de idiomas diferentes do inglês que usam uma vírgula (“,”) para um ponto decimal e formatos de data diferentes do inglês dos EUA, você deve globalizar o aplicativo. Para obter instruções sobre a globalização, consulte [esse problema no GitHub](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).

  ![Criar página](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* Teste os links **Editar**, **Detalhes** e **Excluir**.

Se você receber uma exceção SQL, verifique se executou migrações e atualizou o banco de dados.

O tutorial a seguir explica os arquivos criados por scaffolding.

> [!div class="step-by-step"]
> [Anterior: Introdução](xref:tutorials/razor-pages/razor-pages-start)
> [Próximo: Páginas Razor geradas por scaffolding](xref:tutorials/razor-pages/page)
