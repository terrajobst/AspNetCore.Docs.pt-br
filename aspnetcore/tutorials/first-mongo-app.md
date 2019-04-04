---
title: Criar APIs Web com o ASP.NET Core e o MongoDB
author: prkhandelwal
description: Este tutorial demonstra como criar uma API Web do ASP.NET Core usando um banco de dados NoSQL do MongoDB.
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 01/31/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 5b8a0c963940d65545579b7120edac3571e4ad2a
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750683"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Criar uma API Web com o ASP.NET Core e o MongoDB

Por [Pratik Khandelwal](https://twitter.com/K2Prk) e [Scott Addie](https://twitter.com/Scott_Addie)

Este tutorial cria uma API Web que executa as operações CRUD (criar, ler, atualizar e excluir) em um banco de dados NoSQL do [MongoDB](https://www.mongodb.com/what-is-mongodb).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar o MongoDB
> * Criar um banco de dados do MongoDB
> * Definir uma coleção e um esquema do MongoDB
> * Executar operações CRUD do MongoDB a partir de uma API Web

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [SDK 2.2 ou posterior do .NET Core](https://www.microsoft.com/net/download/all)
* [Visual Studio 2017 versão 15.9 ou posterior](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) com a carga de trabalho **ASP.NET e desenvolvimento para a Web**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [SDK 2.2 ou posterior do .NET Core](https://www.microsoft.com/net/download/all)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* [SDK 2.2 ou posterior do .NET Core](https://www.microsoft.com/net/download/all)
* [Visual Studio para Mac versão 7.7 ou posterior](https://www.visualstudio.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Configurar o MongoDB

Se usar o Windows, o MongoDB será instalado em *C:\\Arquivos de Programas\\MongoDB* por padrão. Adicione *C:\\Arquivos de Programas\\MongoDB\\Servidor\\\<número_de_versão>\\bin* à variável de ambiente `Path`. Essa alteração possibilita o acesso ao MongoDB a partir de qualquer lugar em seu computador de desenvolvimento.

Use o Shell do mongo nas etapas a seguir para criar um banco de dados, fazer coleções e armazenar documentos. Para saber mais sobre os comandos de Shell do mongo, consulte [Como trabalhar com o Shell do mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Escolha um diretório no seu computador de desenvolvimento para armazenar os dados. Por exemplo, *C:\\BooksData* no Windows. Crie o diretório se não houver um. O Shell do mongo não cria novos diretórios.
1. Abra um shell de comando. Execute o comando a seguir para se conectar ao MongoDB na porta padrão 27017. Lembre-se de substituir `<data_directory_path>` pelo diretório escolhido na etapa anterior.

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. Abra outra instância do shell de comando. Conecte-se ao banco de dados de testes padrão executando o seguinte comando:

    ```console
    mongo
    ```

1. Execute o seguinte em um shell de comando:

    ```console
    use BookstoreDb
    ```

    Se ele ainda não existir, um banco de dados chamado *BookstoreDb* será criado. Se o banco de dados existir, a conexão dele será aberta para transações.

1. Crie uma coleção `Books` usando o seguinte comando:

    ```console
    db.createCollection('Books')
    ```

    O seguinte resultado é exibido:

    ```console
    { "ok" : 1 }
    ```

1. Defina um esquema para a coleção `Books` e insira dois documentos usando o seguinte comando:

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    O seguinte resultado é exibido:

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

1. Visualize os documentos no banco de dados usando o seguinte comando:

    ```console
    db.Books.find({}).pretty()
    ```

    O seguinte resultado é exibido:

    ```console
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
      "Name" : "Design Patterns",
      "Price" : 54.93,
      "Category" : "Computers",
      "Author" : "Ralph Johnson"
    }
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
      "Name" : "Clean Code",
      "Price" : 43.15,
      "Category" : "Computers",
      "Author" : "Robert C. Martin"
    }
    ```

    O esquema adiciona uma propriedade `_id` gerada automaticamente do tipo `ObjectId` para cada documento.

O banco de dados está pronto. Você pode começar a criar a API Web do ASP.NET Core.

## <a name="create-the-aspnet-core-web-api-project"></a>Criar o projeto da API Web do ASP.NET Core

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Acesse **Arquivo** > **Novo** > **Projeto**.
1. Selecione **Aplicativo Web do ASP.NET Core**, nomeie o projeto como *BooksApi* e clique em **OK**.
1. Selecione a estrutura de destino **.NET Core** e **ASP.NET Core 2.2**. Selecione o modelo de projeto **API** e clique em **OK**:
1. Visite a [Galeria do NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .NET para MongoDB. Na janela **Console do Gerenciador de Pacotes**, navegue até a raiz do projeto. Execute o seguinte comando para instalar o driver .NET para MongoDB:

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Execute os seguintes comandos em um shell de comando:

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    Um novo projeto de API Web do ASP.NET Core direcionado ao .NET Core é gerado e aberto no Visual Studio Code.

1. Clique em **Sim** quando for exibida a notificação *Os ativos necessários para compilar e depurar estão ausentes em “BooksApi”. Adicioná-los?*.
1. Visite a [Galeria do NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .NET para MongoDB. Abra **Terminal Integrado** e navegue até a raiz do projeto. Execute o seguinte comando para instalar o driver .NET para MongoDB:

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Vá para **Arquivo** > **Nova Solução** > **.NET Core** > **Aplicativo**.
1. Selecione o modelo de projeto C# **API Web ASP.NET Core** e clique em **Avançar**.
1. Selecione **.NET Core 2.2** na lista suspensa **Estrutura de destino** e clique em **Avançar**.
1. Insira *BooksApi* para o **Nome do Projeto**e clique em **Criar**.
1. No painel **Solução**, clique com o botão direito do mouse no nó **Dependências** do projeto e selecione **Adicionar Pacotes**.
1. Insira *MongoDB.Driver* na caixa de pesquisa, selecione o pacote *MongoDB.Driver* e clique em **Adicionar Pacote**.
1. Clique no botão **Aceitar** na caixa de diálogo **Aceitação da Licença**.

---

## <a name="add-a-model"></a>Adicionar um modelo

1. Adicione um diretório *Modelos* à raiz do projeto.
1. Adicione uma classe `Book` ao diretório *Modelos* com o seguinte código:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs)]

Na classe anterior, a propriedade `Id`:

* É necessária para mapear o objeto CLR (Common Language Runtime) para a coleção do MongoDB.
* É anotada com `[BsonId]` para designar essa propriedade como a chave primária do documento.
* É anotada com `[BsonRepresentation(BsonType.ObjectId)]` para permitir a passagem do parâmetro como tipo `string`, em vez de `ObjectId`. O Mongo processa a conversão de `string` para `ObjectId`.

Outras propriedades na classe são anotadas com o atributo `[BsonElement]`. O valor do atributo representa o nome da propriedade da coleção do MongoDB.

## <a name="add-a-crud-operations-class"></a>Adicionar uma classe de operações CRUD

1. Adicione um diretório *Serviços* à raiz do projeto.
1. Adicione uma classe `BookService` ao diretório *Serviços* com o seguinte código:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

1. Adicione uma cadeia de conexão do MongoDB a *appsettings.json*:

    [!code-csharp[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-4)]

    A propriedade `BookstoreDb` anterior é acessada no construtor de classe `BookService`.

1. No `Startup.ConfigureServices`, registre a classe `BookService` com o sistema de Injeção de Dependência:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

    O registro de serviço anterior é necessário para dar suporte à injeção do construtor no consumo de classes.

A classe `BookService` usa os seguintes membros `MongoDB.Driver` para executar operações CRUD em relação ao banco de dados:

* `MongoClient` &ndash; Lê a instância do servidor para executar operações de banco de dados. O construtor dessa classe é fornecido na cadeia de conexão do MongoDB:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* `IMongoDatabase` &ndash; Representa o banco de dados Mongo para execução de operações. Este tutorial usa o método genérico `GetCollection<T>(collection)` na interface para obter acesso a dados em uma coleção específica. Operações CRUD podem ser executadas em relação à coleção depois que esse método é chamado. Na chamada de método `GetCollection<T>(collection)`:
  * `collection` representa o nome da coleção.
  * `T` representa o tipo de objeto CLR armazenado na coleção.

`GetCollection<T>(collection)` retorna um objeto `MongoCollection` que representa a coleção. Neste tutorial, os seguintes métodos são invocados na coleção:

* `Find<T>` &ndash; Retorna todos os documentos na coleção que correspondem aos critérios de pesquisa fornecidos.
* `InsertOne` &ndash; Insere o objeto fornecido como um novo documento na coleção.
* `ReplaceOne` &ndash; Substitui o documento único que corresponde aos critérios de pesquisa definidos com o objeto fornecido.
* `DeleteOne` &ndash; Exclui um documento único que corresponde aos critérios de pesquisa fornecidos.

## <a name="add-a-controller"></a>Adicionar um controlador

1. Adicione uma classe `BooksController` ao diretório *Controladores* com o seguinte código:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

    O controlador da API Web anterior:

    * Usa a classe `BookService` para executar operações CRUD.
    * Contém métodos de ação para dar suporte a solicitações GET, POST, PUT e DELETE HTTP.
    * O método <xref:System.Web.Http.ApiController.CreatedAtRoute*> retorna uma resposta 201, que é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor. `CreatedAtRoute` também adiciona um cabeçalho Local à resposta. O cabeçalho Location especifica o URI do item de tarefas pendentes recém-criado. Consulte [10.2.2 201 criado](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
1. Compile e execute o aplicativo.
1. Navegue até `http://localhost:<port>/api/books` no seu navegador. A seguinte resposta JSON é exibida:

    ```json
    [
      {
        "id":"5bfd996f7b8e48dc15ff215d",
        "bookName":"Design Patterns",
        "price":54.93,
        "category":"Computers",
        "author":"Ralph Johnson"
      },
      {
        "id":"5bfd996f7b8e48dc15ff215e",
        "bookName":"Clean Code",
        "price":43.15,
        "category":"Computers",
        "author":"Robert C. Martin"
      }
    ]
    ```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a criação de APIs Web do ASP.NET Core, confira os seguintes recursos:

* <xref:web-api/index>
* <xref:web-api/action-return-types>
