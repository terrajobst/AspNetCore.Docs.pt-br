---
title: Adicionar um novo campo a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Saiba como usar as Migrações do Entity Framework Code First para adicionar um novo campo a um modelo e migrar essa alteração para um banco de dados.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: cf8bb67703b564a711105123117498c94ab44e68
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750515"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a>Adicionar um novo campo a um aplicativo ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Nesta seção, as Migrações do [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First são usadas para:

* Adicionar um novo campo ao modelo.
* Migrar o novo campo para o banco de dados.

Ao usar o Code First do EF para criar automaticamente um banco de dados, o Code First:

* Adiciona uma tabela no banco de dados para rastrear o esquema do banco de dados.
* Verifica se o banco de dados está em sincronia com as classes de modelo das quais foi gerado. Se ele não estiver sincronizado, o EF gerará uma exceção. Isso facilita a descoberta de problemas de código/banco de dados inconsistente.

## <a name="add-a-rating-property-to-the-movie-model"></a>Adicionar uma Propriedade de Classificação ao Modelo de Filme

Adicionar uma propriedade `Rating` a *Models/Movie.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Compile o aplicativo (Ctrl+Shift+B).

Como adicionou um novo campo à classe `Movie`, você também precisará atualizar a lista de permissões de associação para que essa nova propriedade seja incluída. Em *MoviesController.cs*, atualize o atributo `[Bind]` dos métodos de ação `Create` e `Edit` para incluir a propriedade `Rating`:

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

Atualize os modelos de exibição para exibir, criar e editar a nova propriedade `Rating` na exibição do navegador.

Edite o arquivo */Views/Movies/Index.cshtml* e adicione um campo `Rating`:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

Atualize */Views/Movies/Create.cshtml* com um campo `Rating`.

# <a name="visual-studio--visual-studio-for-mactabvisual-studiovisual-studio-mac"></a>[Visual Studio/Visual Studio para Mac](#tab/visual-studio+visual-studio-mac)

Copie/cole o “grupo de formulário” anterior e permita que o IntelliSense ajude você a atualizar os campos. O IntelliSense funciona com os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro).

![O desenvolvedor digitou a letra R para o valor do atributo asp-for no segundo elemento de rótulo da exibição. Um menu contextual do IntelliSense foi exibido, mostrando os campos disponíveis, incluindo Classificação, que é realçada na lista automaticamente. Quando o desenvolvedor clicar no campo ou pressionar Enter no teclado, o valor será definido como Classificação.](new-field/_static/cr.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

Atualize a classe `SeedData` para que ela forneça um valor para a nova coluna. Uma alteração de amostra é mostrada abaixo, mas é recomendável fazer essa alteração em cada `new Movie`.

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

O aplicativo não funcionará até que o BD seja atualizado para incluir o novo campo. Se ele tiver sido executado agora, o seguinte `SqlException` será gerado:

`SqlException: Invalid column name 'Rating'.`

Este erro ocorre porque a classe de modelo Movie atualizada é diferente do esquema da tabela Movie do banco de dados existente. (Não há nenhuma coluna `Rating` na tabela de banco de dados.)

Existem algumas abordagens para resolver o erro:

1. Faça com que o Entity Framework remova automaticamente e recrie o banco de dados com base no novo esquema de classe de modelo. Essa abordagem é muito conveniente no início do ciclo de desenvolvimento, quando você está fazendo o desenvolvimento ativo em um banco de dados de teste. Ela permite que você desenvolva rapidamente o modelo e o esquema de banco de dados juntos. No entanto, a desvantagem é que você perde os dados existentes no banco de dados – portanto, você não deseja usar essa abordagem em um banco de dados de produção! Muitas vezes, o uso de um inicializador para propagar um banco de dados com os dados de teste automaticamente é uma maneira produtiva de desenvolver um aplicativo. Isso é uma boa abordagem para o desenvolvimento inicial e ao usar o SQLite.

2. Modifique explicitamente o esquema do banco de dados existente para que ele corresponda às classes de modelo. A vantagem dessa abordagem é que você mantém os dados. Faça essa alteração manualmente ou criando um script de alteração de banco de dados.

3. Use as Migrações do Code First para atualizar o esquema de banco de dados.

Para este tutorial, as Migrações do Code First são usadas.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.

  ![Menu do PMC](adding-model/_static/pmc.png)

No PMC, insira os seguintes comandos:

```powershell
Add-Migration Rating
Update-Database
```

O comando `Add-Migration` informa a estrutura de migração para examinar o atual modelo `Movie` com o atual esquema de BD `Movie` e criar o código necessário para migrar o BD para o novo modelo.

O nome “Classificação” é arbitrário e é usado para nomear o arquivo de migração. É útil usar um nome significativo para o arquivo de migração.

Se você excluir todos os registros do BD, o método de inicialização propagará o BD e incluirá o campo `Rating`.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

Excluir o banco de dados e usar as migrações para recriar o banco de dados. Para excluir o banco de dados, exclua o arquivo de banco de dados (*MvcMovie.db*). Depois, execute o comando `ef database update`:

```console
dotnet ef database update
```

---
<!-- End of VS tabs -->

Execute o aplicativo e verifique se você pode criar/editar/exibir filmes com um campo `Rating`. Você deve adicionar o campo `Rating` aos modelos de exibição `Edit`, `Details` e `Delete`.

> [!div class="step-by-step"]
> [Anterior](search.md)
> [Próximo](validation.md)
