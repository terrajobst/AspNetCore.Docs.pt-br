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
ms.openlocfilehash: 09d73e25667822b8748a00cc76ad6d4f0e5fe290
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511399"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="3bf9a-102">Criar uma API Web com o ASP.NET Core e o MongoDB</span><span class="sxs-lookup"><span data-stu-id="3bf9a-102">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="3bf9a-103">Este tutorial cria uma API Web que executa as operações CRUD (criar, ler, atualizar e excluir) em um banco de dados NoSQL do [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="3bf9a-103">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="3bf9a-104">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-104">In this tutorial, you learn how to:</span></span>

* <span data-ttu-id="3bf9a-105">Configurar o MongoDB</span><span class="sxs-lookup"><span data-stu-id="3bf9a-105">Configure MongoDB</span></span>
* <span data-ttu-id="3bf9a-106">Criar um banco de dados do MongoDB</span><span class="sxs-lookup"><span data-stu-id="3bf9a-106">Create a MongoDB database</span></span>
* <span data-ttu-id="3bf9a-107">Definir uma coleção e um esquema do MongoDB</span><span class="sxs-lookup"><span data-stu-id="3bf9a-107">Define a MongoDB collection and schema</span></span>
* <span data-ttu-id="3bf9a-108">Executar operações CRUD do MongoDB a partir de uma API Web</span><span class="sxs-lookup"><span data-stu-id="3bf9a-108">Perform MongoDB CRUD operations from a web API</span></span>
* <span data-ttu-id="3bf9a-109">Personalizar a serialização JSON</span><span class="sxs-lookup"><span data-stu-id="3bf9a-109">Customize JSON serialization</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3bf9a-110">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="3bf9a-110">Prerequisites</span></span>

* [<span data-ttu-id="3bf9a-111">SDK do .NET Core 3.0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="3bf9a-111">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="3bf9a-112">[Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) com a carga de trabalho **ASP.NET e desenvolvimento para a Web**</span><span class="sxs-lookup"><span data-stu-id="3bf9a-112">[Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="3bf9a-113">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3bf9a-113">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a><span data-ttu-id="3bf9a-114">Configurar o MongoDB</span><span class="sxs-lookup"><span data-stu-id="3bf9a-114">Configure MongoDB</span></span>

<span data-ttu-id="3bf9a-115">Se usar o Windows, o MongoDB será instalado em *C:\\Arquivos de Programas\\MongoDB* por padrão.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-115">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="3bf9a-116">Adicione *C:\\Arquivos de Programas\\MongoDB\\Servidor\\\<número_de_versão>\\bin* à variável de ambiente `Path`.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-116">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="3bf9a-117">Essa alteração possibilita o acesso ao MongoDB a partir de qualquer lugar em seu computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-117">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="3bf9a-118">Use o Shell do mongo nas etapas a seguir para criar um banco de dados, fazer coleções e armazenar documentos.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-118">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="3bf9a-119">Para saber mais sobre os comandos de Shell do mongo, consulte [Como trabalhar com o Shell do mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="3bf9a-119">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="3bf9a-120">Escolha um diretório no seu computador de desenvolvimento para armazenar os dados.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-120">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="3bf9a-121">Por exemplo, *C:\\BooksData* no Windows.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-121">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="3bf9a-122">Crie o diretório se não houver um.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-122">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="3bf9a-123">O Shell do mongo não cria novos diretórios.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-123">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="3bf9a-124">Abra um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-124">Open a command shell.</span></span> <span data-ttu-id="3bf9a-125">Execute o comando a seguir para se conectar ao MongoDB na porta padrão 27017.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-125">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="3bf9a-126">Lembre-se de substituir `<data_directory_path>` pelo diretório escolhido na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-126">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="3bf9a-127">Abra outra instância do shell de comando.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-127">Open another command shell instance.</span></span> <span data-ttu-id="3bf9a-128">Conecte-se ao banco de dados de testes padrão executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-128">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="3bf9a-129">Execute o seguinte em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-129">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="3bf9a-130">Se ele ainda não existir, um banco de dados chamado *BookstoreDb* será criado.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-130">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="3bf9a-131">Se o banco de dados existir, a conexão dele será aberta para transações.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-131">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="3bf9a-132">Crie uma coleção `Books` usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-132">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="3bf9a-133">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-133">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="3bf9a-134">Defina um esquema para a coleção `Books` e insira dois documentos usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-134">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="3bf9a-135">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-135">The following result is displayed:</span></span>

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
  > <span data-ttu-id="3bf9a-136">As IDs mostradas neste artigo não corresponderão às IDs de quando você executar esse exemplo.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-136">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="3bf9a-137">Visualize os documentos no banco de dados usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-137">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="3bf9a-138">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-138">The following result is displayed:</span></span>

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

    <span data-ttu-id="3bf9a-139">O esquema adiciona uma propriedade `_id` gerada automaticamente do tipo `ObjectId` para cada documento.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-139">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="3bf9a-140">O banco de dados está pronto.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-140">The database is ready.</span></span> <span data-ttu-id="3bf9a-141">Você pode começar a criar a API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-141">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="3bf9a-142">Criar o projeto da API Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3bf9a-142">Create the ASP.NET Core web API project</span></span>

1. <span data-ttu-id="3bf9a-143">Acesse **Arquivo** > **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-143">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="3bf9a-144">Selecione o tipo de projeto **aplicativo Web ASP.NET Core** e, em seguida, **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-144">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="3bf9a-145">Nomeie o projeto como *BooksApi* e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-145">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="3bf9a-146">Selecione a estrutura de destino **.NET Core** e **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-146">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="3bf9a-147">Selecione o modelo de projeto **API** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-147">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="3bf9a-148">Visite a [Galeria do NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .net para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-148">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="3bf9a-149">Na janela **Console do Gerenciador de Pacotes**, navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-149">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="3bf9a-150">Execute o seguinte comando para instalar o driver .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-150">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a><span data-ttu-id="3bf9a-151">Adicionar um modelo de entidade</span><span class="sxs-lookup"><span data-stu-id="3bf9a-151">Add an entity model</span></span>

1. <span data-ttu-id="3bf9a-152">Adicione um diretório *Modelos* à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-152">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="3bf9a-153">Adicione uma classe `Book` ao diretório *Modelos* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-153">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

    <span data-ttu-id="3bf9a-154">Na classe anterior, a propriedade `Id`:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-154">In the preceding class, the `Id` property:</span></span>

    * <span data-ttu-id="3bf9a-155">É necessária para mapear o objeto CLR (Common Language Runtime) para a coleção do MongoDB.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-155">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="3bf9a-156">É anotada com [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) para designar essa propriedade como a chave primária do documento.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-156">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="3bf9a-157">É anotado com [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) para permitir a passagem do parâmetro como o tipo `string` em vez de uma estrutura [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="3bf9a-157">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="3bf9a-158">O Mongo processa a conversão de `string` para `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-158">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

    <span data-ttu-id="3bf9a-159">A propriedade `BookName` é anotada com o atributo [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) .</span><span class="sxs-lookup"><span data-stu-id="3bf9a-159">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="3bf9a-160">O valor do atributo de `Name` representa o nome da propriedade da coleção do MongoDB.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-160">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="3bf9a-161">Adicionar um modelo de configuração</span><span class="sxs-lookup"><span data-stu-id="3bf9a-161">Add a configuration model</span></span>

1. <span data-ttu-id="3bf9a-162">Adicione os seguintes valores de configuração de banco de dados no *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-162">Add the following database configuration values to *appsettings.json*:</span></span>

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. <span data-ttu-id="3bf9a-163">Adicione um arquivo *BookstoreDatabaseSettings.cs* no diretório *Modelos* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-163">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

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

    <span data-ttu-id="3bf9a-164">A classe precedente `BookstoreDatabaseSettings` é usada para armazenar os valores de propriedade *do arquivo*appsettings.json`BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-164">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="3bf9a-165">Os nomes de propriedade JSON e C# são nomeados de forma idêntica para facilitar o processo de mapeamento.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-165">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="3bf9a-166">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-166">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

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

    <span data-ttu-id="3bf9a-167">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-167">In the preceding code:</span></span>

    * <span data-ttu-id="3bf9a-168">A instância de configuração à qual a seção *do arquivo*appsettings.json`BookstoreDatabaseSettings` é associada é registrada no contêiner de DI (Injeção de Dependência).</span><span class="sxs-lookup"><span data-stu-id="3bf9a-168">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="3bf9a-169">Por exemplo, a propriedade `BookstoreDatabaseSettings` de um objeto `ConnectionString` é populada com a propriedade `BookstoreDatabaseSettings:ConnectionString` no *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-169">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="3bf9a-170">A interface `IBookstoreDatabaseSettings` é registrada na DI com um [tempo de vida do serviço](xref:fundamentals/dependency-injection#service-lifetimes) singleton.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-170">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="3bf9a-171">Quando inserida, a instância da interface é resolvida para um objeto `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-171">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="3bf9a-172">Adicione o seguinte código na parte superior do *Startup.cs* para resolver as referências `BookstoreDatabaseSettings` e `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-172">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="3bf9a-173">Adicionar um serviço de operações CRUD</span><span class="sxs-lookup"><span data-stu-id="3bf9a-173">Add a CRUD operations service</span></span>

1. <span data-ttu-id="3bf9a-174">Adicione um diretório *Serviços* à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-174">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="3bf9a-175">Adicione uma classe `BookService` ao diretório *Serviços* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-175">Add a `BookService` class to the *Services* directory with the following code:</span></span>

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

    <span data-ttu-id="3bf9a-176">No código anterior, uma instância `IBookstoreDatabaseSettings` é recuperada da DI por meio da injeção de construtor.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-176">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="3bf9a-177">Essa técnica fornece acesso para os valores de configuração do *appsettings.json* que foram adicionados na seção [Adicionar um modelo de configuração](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="3bf9a-177">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="3bf9a-178">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-178">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

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

    <span data-ttu-id="3bf9a-179">No código anterior, a classe `BookService` é registrada com a DI para dar suporte à injeção de construtor nas classes consumidoras.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-179">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="3bf9a-180">O tempo de vida do serviço singleton é mais apropriado porque `BookService` usa uma dependência direta de `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-180">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="3bf9a-181">De acordo com as [Diretrizes oficiais de reutilização do cliente Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), o `MongoClient` deve ser registrado na DI com um tempo de vida do serviço singleton.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-181">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="3bf9a-182">Adicione o seguinte código na parte superior do *Startup.cs* para resolver a referências `BookService`:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-182">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>


    ```csharp
    using BooksApi.Services;
    ```

<span data-ttu-id="3bf9a-183">A classe `BookService` usa os seguintes membros `MongoDB.Driver` para executar operações CRUD em relação ao banco de dados:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-183">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="3bf9a-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; lê a instância de servidor para executar operações de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="3bf9a-185">O construtor dessa classe é fornecido na cadeia de conexão do MongoDB:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-185">The constructor of this class is provided the MongoDB connection string:</span></span>

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* <span data-ttu-id="3bf9a-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; representa o banco de dados Mongo para executar operações.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="3bf9a-187">Este tutorial usa o método [GetCollection\<TDocument>(coleção)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) genérico na interface para obter acesso aos dados em uma coleção específica.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-187">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="3bf9a-188">Execute operações CRUD em relação à coleção depois que esse método for chamado.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-188">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="3bf9a-189">Na chamada de método `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-189">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="3bf9a-190">`collection` representa o nome da coleção.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-190">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="3bf9a-191">`TDocument` representa o tipo de objeto CLR armazenado na coleção.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-191">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="3bf9a-192">`GetCollection<TDocument>(collection)` retorna um objeto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) que representa a coleção.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-192">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="3bf9a-193">Neste tutorial, os seguintes métodos são invocados na coleção:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-193">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="3bf9a-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; exclui um único documento correspondente aos critérios de pesquisa fornecidos.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="3bf9a-195">[Localizar\<TDocument >](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; retorna todos os documentos na coleção que correspondem aos critérios de pesquisa fornecidos.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-195">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="3bf9a-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; insere o objeto fornecido como um novo documento na coleção.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="3bf9a-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; substitui o documento único que corresponde aos critérios de pesquisa fornecidos com o objeto fornecido.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="3bf9a-198">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="3bf9a-198">Add a controller</span></span>

<span data-ttu-id="3bf9a-199">Adicione uma classe `BooksController` ao diretório *Controladores* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-199">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

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

<span data-ttu-id="3bf9a-200">O controlador da API Web anterior:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-200">The preceding web API controller:</span></span>

* <span data-ttu-id="3bf9a-201">Usa a classe `BookService` para executar operações CRUD.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-201">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="3bf9a-202">Contém métodos de ação para dar suporte a solicitações GET, POST, PUT e DELETE HTTP.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-202">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="3bf9a-203">Chama o <xref:System.Web.Http.ApiController.CreatedAtRoute*> no método de ação `Create` para retornar uma resposta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="3bf9a-203">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="3bf9a-204">O código de status 201 é a resposta padrão para um método HTTP POST que cria um recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-204">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="3bf9a-205">`CreatedAtRoute` também adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-205">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="3bf9a-206">O cabeçalho `Location` especifica o URI do livro recém-criado.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-206">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="3bf9a-207">Testar a API Web</span><span class="sxs-lookup"><span data-stu-id="3bf9a-207">Test the web API</span></span>

1. <span data-ttu-id="3bf9a-208">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-208">Build and run the app.</span></span>

1. <span data-ttu-id="3bf9a-209">Navegue até `http://localhost:<port>/api/books` para testar o método de ação `Get` sem parâmetros do controlador.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-209">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="3bf9a-210">A seguinte resposta JSON é exibida:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-210">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="3bf9a-211">Navegue até `http://localhost:<port>/api/books/{id here}` para testar o método de ação `Get` sobrecarregado do controlador.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-211">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="3bf9a-212">A seguinte resposta JSON é exibida:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-212">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="3bf9a-213">Configurar opções de serialização JSON</span><span class="sxs-lookup"><span data-stu-id="3bf9a-213">Configure JSON serialization options</span></span>

<span data-ttu-id="3bf9a-214">Há dois detalhes alterar sobre as respostas JSON retornadas na seção [Testar a API Web](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="3bf9a-214">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="3bf9a-215">O uso de maiúsculas e minúsculas padrão dos nomes da propriedade deve ser alterado para corresponder ao uso de maiúsculas e minúsculas Pascal dos nomes de propriedade do objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-215">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="3bf9a-216">A propriedade `bookName` deve ser retornada como `Name`.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-216">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="3bf9a-217">Para cumprir os requisitos anteriores, faça as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-217">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="3bf9a-218">O JSON.NET foi removido da estrutura compartilhada do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-218">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="3bf9a-219">Adicione uma referência de pacote a [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="3bf9a-219">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="3bf9a-220">Em `Startup.ConfigureServices`, encadeie o seguinte código realçado para a chamada de método `AddMvc`:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-220">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

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

    <span data-ttu-id="3bf9a-221">Com a alteração anterior, os nomes de propriedade na resposta JSON serializada da API Web correspondem aos respectivos nomes de propriedade no tipo de objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-221">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="3bf9a-222">Por exemplo, a propriedade `Book` da classe `Author` é serializada como `Author`.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-222">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="3bf9a-223">Em *Models/book. cs*, anote a propriedade `BookName` com o seguinte atributo [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) :</span><span class="sxs-lookup"><span data-stu-id="3bf9a-223">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    <span data-ttu-id="3bf9a-224">O valor do atributo `[JsonProperty]` de `Name` representa o nome da propriedade da resposta JSON serializada da API Web.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-224">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="3bf9a-225">Adicione o seguinte código à parte superior de *Models/Book.cs* para resolver a referência de atributo `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-225">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

    ```csharp
    using Newtonsoft.Json;
    ```

1. <span data-ttu-id="3bf9a-226">Repita as etapas definidas na seção [Testar a API Web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="3bf9a-226">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="3bf9a-227">Observe a diferença em nomes de propriedade JSON.</span><span class="sxs-lookup"><span data-stu-id="3bf9a-227">Notice the difference in JSON property names.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3bf9a-228">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="3bf9a-228">Next steps</span></span>

<span data-ttu-id="3bf9a-229">Para saber mais sobre a criação de APIs Web do ASP.NET Core, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="3bf9a-229">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="3bf9a-230">Versão deste artigo no YouTube</span><span class="sxs-lookup"><span data-stu-id="3bf9a-230">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [<span data-ttu-id="3bf9a-231">Criar APIs Web com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3bf9a-231">Create web APIs with ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)
