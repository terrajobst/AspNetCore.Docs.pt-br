---
title: Criar uma API Web com o ASP.NET Core e o MongoDB
author: prkhandelwal
description: Este tutorial demonstra como criar uma API Web com o ASP.NET Core usando um banco de dados MongoDB NoSQL.
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 06/10/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 426b4c0dee290153b9b1bf83deec14fa728183cb
ms.sourcegitcommit: f5762967df3be8b8c868229e679301f2f7954679
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048089"
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
> * Personalizar a serialização JSON

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [SDK 2.2 ou posterior do .NET Core](https://www.microsoft.com/net/download/all)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com carga de trabalho **ASP.NET e desenvolvimento Web**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [SDK 2.2 ou posterior do .NET Core](https://www.microsoft.com/net/download/all)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* [SDK 2.2 ou posterior do .NET Core](https://www.microsoft.com/net/download/all)
* [Visual Studio para Mac versão 7.7 ou posterior](https://visualstudio.microsoft.com/downloads/)
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
1. Selecione o tipo de projeto **aplicativo Web ASP.NET Core** e, em seguida, **Avançar**.
1. Nomeie o projeto como *BooksApi* e selecione **criar**.
1. Selecione a estrutura de destino **.NET Core** e **ASP.NET Core 2.2**. Selecione o modelo de projeto **API** e, em seguida, **Criar**.
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

1. Depois que o ícone de chama do OmniSharp na barra de status ficar verde, uma caixa de diálogo perguntará **Os ativos necessários para criar e depurar estão ausentes do 'BooksApi'. Deseja adicioná-los?** . Selecione **Sim** na barra superior.
1. Visite a [Galeria do NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .NET para MongoDB. Abra **Terminal Integrado** e navegue até a raiz do projeto. Execute o seguinte comando para instalar o driver .NET para MongoDB:

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Vá para **Arquivo** > **Nova Solução** >  **.NET Core** > **Aplicativo**.
1. Selecione o modelo de projeto C# da **API Web do ASP.NET Core** e, em seguida, **Avançar**.
1. Selecione **.NET Core 2.2** na lista suspensa **Estrutura de Destino** e, em seguida, **Avançar**.
1. Insira *BooksApi* para o **Nome do Projeto** e selecione **Criar**.
1. No painel **Solução**, clique com o botão direito do mouse no nó **Dependências** do projeto e selecione **Adicionar Pacotes**.
1. Insira *MongoDB.Driver* na caixa de pesquisa, escolha o pacote *MongoDB.Driver* e selecione **Adicionar Pacote**.
1. Selecione o botão **Aceitar** na caixa de diálogo **Aceitação da Licença**.

---

## <a name="add-an-entity-model"></a>Adicionar um modelo de entidade

1. Adicione um diretório *Modelos* à raiz do projeto.
1. Adicione uma classe `Book` ao diretório *Modelos* com o seguinte código:

    ```csharp
    using MongoDB.Bson;
    using MongoDB.Bson.Serialization.Attributes;
    
    namespace BooksApi.Models
    {
        public class Book
        {
            [BsonId]
            [BsonRepresentation(BsonType.ObjectId)]
            public string Id { get; set; }
    
            [BsonElement("Name")]
            public string BookName { get; set; }
    
            public decimal Price { get; set; }
    
            public string Category { get; set; }
    
            public string Author { get; set; }
        }
    }
    ```

    Na classe anterior, a propriedade `Id`:
    
    * É necessária para mapear o objeto CLR (Common Language Runtime) para a coleção do MongoDB.
    * É anotada com [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) para ser designada como a chave primária do documento.
    * É anotada com [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) para permitir a passagem do parâmetro como o tipo `string` em vez de uma estrutura [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm). O Mongo processa a conversão de `string` para `ObjectId`.
    
    A propriedade `BookName` é anotada com o atributo [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm). O valor do atributo de `Name` representa o nome da propriedade da coleção do MongoDB.

## <a name="add-a-configuration-model"></a>Adicionar um modelo de configuração

1. Adicione os seguintes valores de configuração de banco de dados no *appsettings.json*:

    [!code-json[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-6)]

1. Adicione um arquivo *BookstoreDatabaseSettings.cs* no diretório *Modelos* com o código a seguir:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/BookstoreDatabaseSettings.cs)]

    A classe precedente `BookstoreDatabaseSettings` é usada para armazenar os valores de propriedade `BookstoreDatabaseSettings` do arquivo *appsettings.json*. Os nomes de propriedade JSON e C# são nomeados de forma idêntica para facilitar o processo de mapeamento.

1. Adicione o código realçado a seguir a `Startup.ConfigureServices`:

    [!code-csharp[](first-mongo-app/sample_snapshot/BooksApi/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

    No código anterior:

    * A instância de configuração à qual a seção `BookstoreDatabaseSettings` do arquivo *appsettings.json* é associada é registrada no contêiner de DI (Injeção de Dependência). Por exemplo, a propriedade `ConnectionString` de um objeto `BookstoreDatabaseSettings` é populada com a propriedade `BookstoreDatabaseSettings:ConnectionString` no *appsettings.json*.
    * A interface `IBookstoreDatabaseSettings` é registrada na DI com um [tempo de vida do serviço](xref:fundamentals/dependency-injection#service-lifetimes) singleton. Quando inserida, a instância da interface é resolvida para um objeto `BookstoreDatabaseSettings`.

1. Adicione o seguinte código na parte superior do *Startup.cs* para resolver as referências `BookstoreDatabaseSettings` e `IBookstoreDatabaseSettings`:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Adicionar um serviço de operações CRUD

1. Adicione um diretório *Serviços* à raiz do projeto.
1. Adicione uma classe `BookService` ao diretório *Serviços* com o seguinte código:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

    No código anterior, uma instância `IBookstoreDatabaseSettings` é recuperada da DI por meio da injeção de construtor. Essa técnica fornece acesso para os valores de configuração do *appsettings.json* que foram adicionados na seção [Adicionar um modelo de configuração](#add-a-configuration-model).

1. Adicione o código realçado a seguir a `Startup.ConfigureServices`:

    [!code-csharp[](first-mongo-app/sample_snapshot/BooksApi/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

    No código anterior, a classe `BookService` é registrada com a DI para dar suporte à injeção de construtor nas classes consumidoras. O tempo de vida do serviço singleton é mais apropriado porque `BookService` usa uma dependência direta de `MongoClient`. De acordo com as [Diretrizes oficiais de reutilização do cliente Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), o `MongoClient` deve ser registrado na DI com um tempo de vida do serviço singleton.

1. Adicione o seguinte código na parte superior do *Startup.cs* para resolver a referências `BookService`:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_UsingBooksApiServices)]

A classe `BookService` usa os seguintes membros `MongoDB.Driver` para executar operações CRUD em relação ao banco de dados:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; lê a instância do servidor para executar operações de banco de dados. O construtor dessa classe é fornecido na cadeia de conexão do MongoDB:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; representa o banco de dados Mongo para execução de operações. Este tutorial usa o método [GetCollection\<TDocument>(coleção)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) genérico na interface para obter acesso aos dados em uma coleção específica. Execute operações CRUD em relação à coleção depois que esse método for chamado. Na chamada de método `GetCollection<TDocument>(collection)`:
  * `collection` representa o nome da coleção.
  * `TDocument` representa o tipo de objeto CLR armazenado na coleção.

`GetCollection<TDocument>(collection)` retorna um objeto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) que representa a coleção. Neste tutorial, os seguintes métodos são invocados na coleção:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; exclui um único documento que correspondem aos critérios de pesquisa fornecidos.
* [Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; retorna todos os documentos na coleção que correspondem aos critérios de pesquisa fornecidos.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; insere o objeto fornecido como um novo documento na coleção.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; substitui o único documento que corresponde aos critérios de pesquisa fornecidos com o objeto fornecido.

## <a name="add-a-controller"></a>Adicionar um controlador

Adicione uma classe `BooksController` ao diretório *Controladores* com o seguinte código:

[!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

O controlador da API Web anterior:

* Usa a classe `BookService` para executar operações CRUD.
* Contém métodos de ação para dar suporte a solicitações GET, POST, PUT e DELETE HTTP.
* Chama o <xref:System.Web.Http.ApiController.CreatedAtRoute*> no método de ação `Create` para retornar uma resposta [HTTP 201](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). O código de status 201 é a resposta padrão para um método HTTP POST que cria um recurso no servidor. `CreatedAtRoute` também adiciona um cabeçalho `Location` à resposta. O cabeçalho `Location` especifica o URI do livro recém-criado.

## <a name="test-the-web-api"></a>Testar a API Web

1. Compile e execute o aplicativo.

1. Navegue até `http://localhost:<port>/api/books` para testar o método de ação `Get` sem parâmetros do controlador. A seguinte resposta JSON é exibida:

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

1. Navegue até `http://localhost:<port>/api/books/5bfd996f7b8e48dc15ff215e` para testar o método de ação `Get` sobrecarregado do controlador. A seguinte resposta JSON é exibida:

    ```json
    {
      "id":"5bfd996f7b8e48dc15ff215e",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a>Configurar opções de serialização JSON

Há dois detalhes alterar sobre as respostas JSON retornadas na seção [Testar a API Web](#test-the-web-api):

* O uso de maiúsculas e minúsculas padrão dos nomes da propriedade deve ser alterado para corresponder ao uso de maiúsculas e minúsculas Pascal dos nomes de propriedade do objeto CLR.
* A propriedade `bookName` deve ser retornada como `Name`.

Para cumprir os requisitos anteriores, faça as seguintes alterações:

1. Em `Startup.ConfigureServices`, encadeie o seguinte código realçado para a chamada de método `AddMvc`:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

    Com a alteração anterior, os nomes de propriedade na resposta JSON serializada da API Web correspondem aos respectivos nomes de propriedade no tipo de objeto CLR. Por exemplo, a propriedade `Author` da classe `Book` é serializada como `Author`.

1. Em *Models/Book.cs*, anote a propriedade `BookName` com o seguinte atributo [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm):

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

    O valor do atributo `[JsonProperty]` de `Name` representa o nome da propriedade da resposta JSON serializada da API Web.

1. Adicione o seguinte código à parte superior de *Models/Book.cs* para resolver a referência de atributo `[JsonProperty]`:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Repita as etapas definidas na seção [Testar a API Web](#test-the-web-api). Observe a diferença em nomes de propriedade JSON.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a criação de APIs Web do ASP.NET Core, confira os seguintes recursos:

* [Versão deste artigo no YouTube](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
