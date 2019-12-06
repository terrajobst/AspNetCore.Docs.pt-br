---
page_type: sample
description: Este tutorial demonstra como criar uma API Web com o ASP.NET Core usando um banco de dados MongoDB NoSQL.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: aspnetcore-webapi-mongodb
ms.openlocfilehash: 01f9cf237dcf2a9b95c181c2cb87ef9f59102244
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881166"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Criar uma API Web com o ASP.NET Core e o MongoDB

Este tutorial cria uma API Web que executa as operações CRUD (criar, ler, atualizar e excluir) em um banco de dados NoSQL do [MongoDB](https://www.mongodb.com/what-is-mongodb).

Neste tutorial, você aprenderá como:

* Configurar o MongoDB
* Criar um banco de dados do MongoDB
* Definir uma coleção e um esquema do MongoDB
* Executar operações CRUD do MongoDB a partir de uma API Web
* Personalizar a serialização JSON

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* [SDK do .NET Core 3.0 ou posterior](https://www.microsoft.com/net/download/all)
* [Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) com a carga de trabalho **ASP.NET e desenvolvimento para a Web**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

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

  > [!NOTE]
  > As IDs mostradas neste artigo não corresponderão às IDs de quando você executar esse exemplo.

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

1. Acesse **Arquivo** > **Novo** > **Projeto**.
1. Selecione o tipo de projeto **aplicativo Web ASP.NET Core** e, em seguida, **Avançar**.
1. Nomeie o projeto como *BooksApi* e selecione **criar**.
1. Selecione a estrutura de destino **.NET Core** e **ASP.NET Core 3.0**. Selecione o modelo de projeto **API** e, em seguida, **Criar**.
1. Visite a [Galeria do NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .net para MongoDB. Na janela **Console do Gerenciador de Pacotes**, navegue até a raiz do projeto. Execute o seguinte comando para instalar o driver .NET para MongoDB:

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

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
    * É anotada com [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) para designar essa propriedade como a chave primária do documento.
    * É anotado com [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) para permitir a passagem do parâmetro como o tipo `string` em vez de uma estrutura [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) . O Mongo processa a conversão de `string` para `ObjectId`.

    A propriedade `BookName` é anotada com o atributo [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) . O valor do atributo de `Name` representa o nome da propriedade da coleção do MongoDB.

## <a name="add-a-configuration-model"></a>Adicionar um modelo de configuração

1. Adicione os seguintes valores de configuração de banco de dados no *appsettings.json*:

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. Adicione um arquivo *BookstoreDatabaseSettings.cs* no diretório *Modelos* com o código a seguir:

    ```csharp
    namespace BooksApi.Models
    {
        public class BookstoreDatabaseSettings : IBookstoreDatabaseSettings
        {
            public string BooksCollectionName { get; set; }
            public string ConnectionString { get; set; }
            public string DatabaseName { get; set; }
        }

        public interface IBookstoreDatabaseSettings
        {
            string BooksCollectionName { get; set; }
            string ConnectionString { get; set; }
            string DatabaseName { get; set; }
        }
    }
    ```

    A classe precedente `BookstoreDatabaseSettings` é usada para armazenar os valores de propriedade `BookstoreDatabaseSettings` do arquivo *appsettings.json*. Os nomes de propriedade JSON e C# são nomeados de forma idêntica para facilitar o processo de mapeamento.

1. Adicione o código realçado a seguir a `Startup.ConfigureServices`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddControllers();
    }
    ```

    No código anterior:

    * A instância de configuração à qual a seção `BookstoreDatabaseSettings` do arquivo *appsettings.json* é associada é registrada no contêiner de DI (Injeção de Dependência). Por exemplo, a propriedade `ConnectionString` de um objeto `BookstoreDatabaseSettings` é populada com a propriedade `BookstoreDatabaseSettings:ConnectionString` no *appsettings.json*.
    * A interface `IBookstoreDatabaseSettings` é registrada na DI com um [tempo de vida do serviço](xref:fundamentals/dependency-injection#service-lifetimes) singleton. Quando inserida, a instância da interface é resolvida para um objeto `BookstoreDatabaseSettings`.

1. Adicione o seguinte código na parte superior do *Startup.cs* para resolver as referências `BookstoreDatabaseSettings` e `IBookstoreDatabaseSettings`:

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a>Adicionar um serviço de operações CRUD

1. Adicione um diretório *Serviços* à raiz do projeto.
1. Adicione uma classe `BookService` ao diretório *Serviços* com o seguinte código:

    ```csharp
    using BooksApi.Models;
    using MongoDB.Driver;
    using System.Collections.Generic;
    using System.Linq;

    namespace BooksApi.Services
    {
        public class BookService
        {
            private readonly IMongoCollection<Book> _books;

            public BookService(IBookstoreDatabaseSettings settings)
            {
                var client = new MongoClient(settings.ConnectionString);
                var database = client.GetDatabase(settings.DatabaseName);

                _books = database.GetCollection<Book>(settings.BooksCollectionName);
            }

            public List<Book> Get() =>
                _books.Find(book => true).ToList();

            public Book Get(string id) =>
                _books.Find<Book>(book => book.Id == id).FirstOrDefault();

            public Book Create(Book book)
            {
                _books.InsertOne(book);
                return book;
            }

            public void Update(string id, Book bookIn) =>
                _books.ReplaceOne(book => book.Id == id, bookIn);

            public void Remove(Book bookIn) =>
                _books.DeleteOne(book => book.Id == bookIn.Id);

            public void Remove(string id) =>
                _books.DeleteOne(book => book.Id == id);
        }
    }
    ```

    No código anterior, uma instância `IBookstoreDatabaseSettings` é recuperada da DI por meio da injeção de construtor. Essa técnica fornece acesso para os valores de configuração do *appsettings.json* que foram adicionados na seção [Adicionar um modelo de configuração](#add-a-configuration-model).

1. Adicione o código realçado a seguir a `Startup.ConfigureServices`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers();
    }
    ```

    No código anterior, a classe `BookService` é registrada com a DI para dar suporte à injeção de construtor nas classes consumidoras. O tempo de vida do serviço singleton é mais apropriado porque `BookService` usa uma dependência direta de `MongoClient`. De acordo com as [Diretrizes oficiais de reutilização do cliente Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), o `MongoClient` deve ser registrado na DI com um tempo de vida do serviço singleton.

1. Adicione o seguinte código na parte superior do *Startup.cs* para resolver a referências `BookService`:


    ```csharp
    using BooksApi.Services;
    ```

A classe `BookService` usa os seguintes membros `MongoDB.Driver` para executar operações CRUD em relação ao banco de dados:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; lê a instância do servidor para executar operações de banco de dados. O construtor dessa classe é fornecido na cadeia de conexão do MongoDB:

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

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

```csharp
using BooksApi.Models;
using BooksApi.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace BooksApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BooksController : ControllerBase
    {
        private readonly BookService _bookService;

        public BooksController(BookService bookService)
        {
            _bookService = bookService;
        }

        [HttpGet]
        public ActionResult<List<Book>> Get() =>
            _bookService.Get();

        [HttpGet("{id:length(24)}", Name = "GetBook")]
        public ActionResult<Book> Get(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            return book;
        }

        [HttpPost]
        public ActionResult<Book> Create(Book book)
        {
            _bookService.Create(book);

            return CreatedAtRoute("GetBook", new { id = book.Id.ToString() }, book);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, Book bookIn)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Update(id, bookIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Remove(book.Id);

            return NoContent();
        }
    }
}
```

O controlador da API Web anterior:

* Usa a classe `BookService` para executar operações CRUD.
* Contém métodos de ação para dar suporte a solicitações GET, POST, PUT e DELETE HTTP.
* Chama o <xref:System.Web.Http.ApiController.CreatedAtRoute*> no método de ação `Create` para retornar uma resposta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). O código de status 201 é a resposta padrão para um método HTTP POST que cria um recurso no servidor. `CreatedAtRoute` também adiciona um cabeçalho `Location` à resposta. O cabeçalho `Location` especifica o URI do livro recém-criado.

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

1. Navegue até `http://localhost:<port>/api/books/{id here}` para testar o método de ação `Get` sobrecarregado do controlador. A seguinte resposta JSON é exibida:

    ```json
    {
      "id":"{ID}",
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

1. O JSON.NET foi removido da estrutura compartilhada do ASP.NET. Adicione uma referência de pacote a [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).

1. Em `Startup.ConfigureServices`, encadeie o seguinte código realçado para a chamada de método `AddMvc`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers()
            .AddNewtonsoftJson(options => options.UseMemberCasing());
    }
    ```

    Com a alteração anterior, os nomes de propriedade na resposta JSON serializada da API Web correspondem aos respectivos nomes de propriedade no tipo de objeto CLR. Por exemplo, a propriedade `Author` da classe `Book` é serializada como `Author`.

1. Em *Models/book. cs*, anote a propriedade `BookName` com o seguinte atributo [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) :

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    O valor do atributo `[JsonProperty]` de `Name` representa o nome da propriedade da resposta JSON serializada da API Web.

1. Adicione o seguinte código à parte superior de *Models/Book.cs* para resolver a referência de atributo `[JsonProperty]`:

    ```csharp
    using Newtonsoft.Json;
    ```

1. Repita as etapas definidas na seção [Testar a API Web](#test-the-web-api). Observe a diferença em nomes de propriedade JSON.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Para saber mais sobre a criação de APIs Web do ASP.NET Core, confira os seguintes recursos:

* [Versão deste artigo no YouTube](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [Criar APIs Web com o ASP.NET Core](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)
