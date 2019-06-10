---
title: Criar uma API Web com o ASP.NET Core e o MongoDB
author: prkhandelwal
description: Este tutorial demonstra como criar uma API Web com o ASP.NET Core usando um banco de dados MongoDB NoSQL.
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 06/04/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 6a8c5d75f562b38015101e039a2f5d96a5491595
ms.sourcegitcommit: 5dd2ce9709c9e41142771e652d1a4bd0b5248cec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692554"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="a5949-103">Criar uma API Web com o ASP.NET Core e o MongoDB</span><span class="sxs-lookup"><span data-stu-id="a5949-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="a5949-104">Por [Pratik Khandelwal](https://twitter.com/K2Prk) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="a5949-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="a5949-105">Este tutorial cria uma API Web que executa as operações CRUD (criar, ler, atualizar e excluir) em um banco de dados NoSQL do [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="a5949-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="a5949-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="a5949-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a5949-107">Configurar o MongoDB</span><span class="sxs-lookup"><span data-stu-id="a5949-107">Configure MongoDB</span></span>
> * <span data-ttu-id="a5949-108">Criar um banco de dados do MongoDB</span><span class="sxs-lookup"><span data-stu-id="a5949-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="a5949-109">Definir uma coleção e um esquema do MongoDB</span><span class="sxs-lookup"><span data-stu-id="a5949-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="a5949-110">Executar operações CRUD do MongoDB a partir de uma API Web</span><span class="sxs-lookup"><span data-stu-id="a5949-110">Perform MongoDB CRUD operations from a web API</span></span>

<span data-ttu-id="a5949-111">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a5949-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a5949-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="a5949-112">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5949-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5949-113">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="a5949-114">SDK 2.2 ou posterior do .NET Core</span><span class="sxs-lookup"><span data-stu-id="a5949-114">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="a5949-115">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com carga de trabalho **ASP.NET e desenvolvimento Web**</span><span class="sxs-lookup"><span data-stu-id="a5949-115">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="a5949-116">MongoDB</span><span class="sxs-lookup"><span data-stu-id="a5949-116">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a5949-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5949-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="a5949-118">SDK 2.2 ou posterior do .NET Core</span><span class="sxs-lookup"><span data-stu-id="a5949-118">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="a5949-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5949-119">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="a5949-120">C# para Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5949-120">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="a5949-121">MongoDB</span><span class="sxs-lookup"><span data-stu-id="a5949-121">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a5949-122">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a5949-122">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="a5949-123">SDK 2.2 ou posterior do .NET Core</span><span class="sxs-lookup"><span data-stu-id="a5949-123">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="a5949-124">Visual Studio para Mac versão 7.7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="a5949-124">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="a5949-125">MongoDB</span><span class="sxs-lookup"><span data-stu-id="a5949-125">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="a5949-126">Configurar o MongoDB</span><span class="sxs-lookup"><span data-stu-id="a5949-126">Configure MongoDB</span></span>

<span data-ttu-id="a5949-127">Se usar o Windows, o MongoDB será instalado em *C:\\Arquivos de Programas\\MongoDB* por padrão.</span><span class="sxs-lookup"><span data-stu-id="a5949-127">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="a5949-128">Adicione *C:\\Arquivos de Programas\\MongoDB\\Servidor\\\<número_de_versão>\\bin* à variável de ambiente `Path`.</span><span class="sxs-lookup"><span data-stu-id="a5949-128">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="a5949-129">Essa alteração possibilita o acesso ao MongoDB a partir de qualquer lugar em seu computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="a5949-129">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="a5949-130">Use o Shell do mongo nas etapas a seguir para criar um banco de dados, fazer coleções e armazenar documentos.</span><span class="sxs-lookup"><span data-stu-id="a5949-130">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="a5949-131">Para saber mais sobre os comandos de Shell do mongo, consulte [Como trabalhar com o Shell do mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="a5949-131">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="a5949-132">Escolha um diretório no seu computador de desenvolvimento para armazenar os dados.</span><span class="sxs-lookup"><span data-stu-id="a5949-132">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="a5949-133">Por exemplo, *C:\\BooksData* no Windows.</span><span class="sxs-lookup"><span data-stu-id="a5949-133">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="a5949-134">Crie o diretório se não houver um.</span><span class="sxs-lookup"><span data-stu-id="a5949-134">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="a5949-135">O Shell do mongo não cria novos diretórios.</span><span class="sxs-lookup"><span data-stu-id="a5949-135">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="a5949-136">Abra um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="a5949-136">Open a command shell.</span></span> <span data-ttu-id="a5949-137">Execute o comando a seguir para se conectar ao MongoDB na porta padrão 27017.</span><span class="sxs-lookup"><span data-stu-id="a5949-137">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="a5949-138">Lembre-se de substituir `<data_directory_path>` pelo diretório escolhido na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="a5949-138">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="a5949-139">Abra outra instância do shell de comando.</span><span class="sxs-lookup"><span data-stu-id="a5949-139">Open another command shell instance.</span></span> <span data-ttu-id="a5949-140">Conecte-se ao banco de dados de testes padrão executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="a5949-140">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="a5949-141">Execute o seguinte em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="a5949-141">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="a5949-142">Se ele ainda não existir, um banco de dados chamado *BookstoreDb* será criado.</span><span class="sxs-lookup"><span data-stu-id="a5949-142">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="a5949-143">Se o banco de dados existir, a conexão dele será aberta para transações.</span><span class="sxs-lookup"><span data-stu-id="a5949-143">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="a5949-144">Crie uma coleção `Books` usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="a5949-144">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="a5949-145">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="a5949-145">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="a5949-146">Defina um esquema para a coleção `Books` e insira dois documentos usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="a5949-146">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="a5949-147">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="a5949-147">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

1. <span data-ttu-id="a5949-148">Visualize os documentos no banco de dados usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="a5949-148">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="a5949-149">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="a5949-149">The following result is displayed:</span></span>

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

    <span data-ttu-id="a5949-150">O esquema adiciona uma propriedade `_id` gerada automaticamente do tipo `ObjectId` para cada documento.</span><span class="sxs-lookup"><span data-stu-id="a5949-150">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="a5949-151">O banco de dados está pronto.</span><span class="sxs-lookup"><span data-stu-id="a5949-151">The database is ready.</span></span> <span data-ttu-id="a5949-152">Você pode começar a criar a API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a5949-152">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="a5949-153">Criar o projeto da API Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5949-153">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5949-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5949-154">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a5949-155">Acesse **Arquivo** > **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="a5949-155">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="a5949-156">Selecione o tipo de projeto **aplicativo Web ASP.NET Core** e, em seguida, **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="a5949-156">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="a5949-157">Nomeie o projeto como *BooksApi* e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="a5949-157">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="a5949-158">Selecione a estrutura de destino **.NET Core** e **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="a5949-158">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="a5949-159">Selecione o modelo de projeto **API** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a5949-159">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="a5949-160">Visite a [Galeria do NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .NET para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="a5949-160">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="a5949-161">Na janela **Console do Gerenciador de Pacotes**, navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="a5949-161">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="a5949-162">Execute o seguinte comando para instalar o driver .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="a5949-162">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a5949-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5949-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="a5949-164">Execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="a5949-164">Run the following commands in a command shell:</span></span>

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    <span data-ttu-id="a5949-165">Um novo projeto de API Web do ASP.NET Core direcionado ao .NET Core é gerado e aberto no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a5949-165">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="a5949-166">Depois que o ícone de chama do OmniSharp na barra de status ficar verde, uma caixa de diálogo perguntará **Os ativos necessários para criar e depurar estão ausentes do 'BooksApi'. Deseja adicioná-los?** .</span><span class="sxs-lookup"><span data-stu-id="a5949-166">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="a5949-167">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="a5949-167">Select **Yes**.</span></span>
1. <span data-ttu-id="a5949-168">Visite a [Galeria do NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .NET para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="a5949-168">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="a5949-169">Abra **Terminal Integrado** e navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="a5949-169">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="a5949-170">Execute o seguinte comando para instalar o driver .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="a5949-170">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a5949-171">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a5949-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a5949-172">Vá para **Arquivo** > **Nova Solução** >  **.NET Core** > **Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="a5949-172">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="a5949-173">Selecione o modelo de projeto C# da **API Web do ASP.NET Core** e, em seguida, **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="a5949-173">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="a5949-174">Selecione **.NET Core 2.2** na lista suspensa **Estrutura de Destino** e, em seguida, **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="a5949-174">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="a5949-175">Insira *BooksApi* para o **Nome do Projeto** e selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a5949-175">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="a5949-176">No painel **Solução**, clique com o botão direito do mouse no nó **Dependências** do projeto e selecione **Adicionar Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="a5949-176">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="a5949-177">Insira *MongoDB.Driver* na caixa de pesquisa, escolha o pacote *MongoDB.Driver* e selecione **Adicionar Pacote**.</span><span class="sxs-lookup"><span data-stu-id="a5949-177">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="a5949-178">Selecione o botão **Aceitar** na caixa de diálogo **Aceitação da Licença**.</span><span class="sxs-lookup"><span data-stu-id="a5949-178">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="a5949-179">Adicionar um modelo de entidade</span><span class="sxs-lookup"><span data-stu-id="a5949-179">Add an entity model</span></span>

1. <span data-ttu-id="a5949-180">Adicione um diretório *Modelos* à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="a5949-180">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="a5949-181">Adicione uma classe `Book` ao diretório *Modelos* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a5949-181">Add a `Book` class to the *Models* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs)]

    <span data-ttu-id="a5949-182">Na classe anterior, a propriedade `Id`:</span><span class="sxs-lookup"><span data-stu-id="a5949-182">In the preceding class, the `Id` property:</span></span>
    
    * <span data-ttu-id="a5949-183">É necessária para mapear o objeto CLR (Common Language Runtime) para a coleção do MongoDB.</span><span class="sxs-lookup"><span data-stu-id="a5949-183">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="a5949-184">É anotada com [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) para ser designada como a chave primária do documento.</span><span class="sxs-lookup"><span data-stu-id="a5949-184">Is annotated with [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="a5949-185">É anotada com [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) para permitir a passagem do parâmetro como o tipo `string` em vez de uma estrutura [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm).</span><span class="sxs-lookup"><span data-stu-id="a5949-185">Is annotated with [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="a5949-186">O Mongo processa a conversão de `string` para `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="a5949-186">Mongo handles the conversion from `string` to `ObjectId`.</span></span>
    
    <span data-ttu-id="a5949-187">As outras propriedades da classe são anotadas com o atributo [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm).</span><span class="sxs-lookup"><span data-stu-id="a5949-187">Other properties in the class are annotated with the [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="a5949-188">O valor do atributo representa o nome da propriedade da coleção do MongoDB.</span><span class="sxs-lookup"><span data-stu-id="a5949-188">The attribute's value represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="a5949-189">Adicionar um modelo de configuração</span><span class="sxs-lookup"><span data-stu-id="a5949-189">Add a configuration model</span></span>

1. <span data-ttu-id="a5949-190">Adicione os seguintes valores de configuração de banco de dados no *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a5949-190">Add the following database configuration values to *appsettings.json*:</span></span>

    [!code-json[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="a5949-191">Adicione um arquivo *BookstoreDatabaseSettings.cs* no diretório *Modelos* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="a5949-191">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/BookstoreDatabaseSettings.cs)]

    <span data-ttu-id="a5949-192">A classe precedente `BookstoreDatabaseSettings` é usada para armazenar os valores de propriedade `BookstoreDatabaseSettings` do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a5949-192">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="a5949-193">Os nomes de propriedade JSON e C# são nomeados de forma idêntica para facilitar o processo de mapeamento.</span><span class="sxs-lookup"><span data-stu-id="a5949-193">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="a5949-194">Adicione o seguinte código ao `Startup.ConfigureServices`, antes de chamar `AddMvc`:</span><span class="sxs-lookup"><span data-stu-id="a5949-194">Add the following code to `Startup.ConfigureServices`, before the call to `AddMvc`:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureDatabaseSettings)]

    <span data-ttu-id="a5949-195">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="a5949-195">In the preceding code:</span></span>

    * <span data-ttu-id="a5949-196">A instância de configuração à qual a seção `BookstoreDatabaseSettings` do arquivo *appsettings.json* é associada é registrada no contêiner de DI (Injeção de Dependência).</span><span class="sxs-lookup"><span data-stu-id="a5949-196">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="a5949-197">Por exemplo, a propriedade `ConnectionString` de um objeto `BookstoreDatabaseSettings` é populada com a propriedade `BookstoreDatabaseSettings:ConnectionString` no *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a5949-197">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="a5949-198">A interface `IBookstoreDatabaseSettings` é registrada na DI com um [tempo de vida do serviço](xref:fundamentals/dependency-injection#service-lifetimes) singleton.</span><span class="sxs-lookup"><span data-stu-id="a5949-198">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="a5949-199">Quando inserida, a instância da interface é resolvida para um objeto `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="a5949-199">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="a5949-200">Adicione o seguinte código na parte superior do *Startup.cs* para resolver as referências `BookstoreDatabaseSettings` e `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="a5949-200">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="a5949-201">Adicionar um serviço de operações CRUD</span><span class="sxs-lookup"><span data-stu-id="a5949-201">Add a CRUD operations service</span></span>

1. <span data-ttu-id="a5949-202">Adicione um diretório *Serviços* à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="a5949-202">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="a5949-203">Adicione uma classe `BookService` ao diretório *Serviços* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a5949-203">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

    <span data-ttu-id="a5949-204">No código anterior, uma instância `IBookstoreDatabaseSettings` é recuperada da DI por meio da injeção de construtor.</span><span class="sxs-lookup"><span data-stu-id="a5949-204">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="a5949-205">Essa técnica fornece acesso para os valores de configuração do *appsettings.json* que foram adicionados na seção [Adicionar um modelo de configuração](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="a5949-205">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="a5949-206">Em `Startup.ConfigureServices`, registre a classe `BookService` com a DI:</span><span class="sxs-lookup"><span data-stu-id="a5949-206">In `Startup.ConfigureServices`, register the `BookService` class with DI:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=9)]

    <span data-ttu-id="a5949-207">No código anterior, a classe `BookService` é registrada com a DI para dar suporte à injeção de construtor nas classes consumidoras.</span><span class="sxs-lookup"><span data-stu-id="a5949-207">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="a5949-208">O tempo de vida do serviço singleton é mais apropriado porque `BookService` usa uma dependência direta de `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="a5949-208">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="a5949-209">De acordo com as [Diretrizes oficiais de reutilização do cliente Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), o `MongoClient` deve ser registrado na DI com um tempo de vida do serviço singleton.</span><span class="sxs-lookup"><span data-stu-id="a5949-209">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="a5949-210">Adicione o seguinte código na parte superior do *Startup.cs* para resolver a referências `BookService`:</span><span class="sxs-lookup"><span data-stu-id="a5949-210">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="a5949-211">A classe `BookService` usa os seguintes membros `MongoDB.Driver` para executar operações CRUD em relação ao banco de dados:</span><span class="sxs-lookup"><span data-stu-id="a5949-211">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="a5949-212">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; lê a instância do servidor para executar operações de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a5949-212">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="a5949-213">O construtor dessa classe é fornecido na cadeia de conexão do MongoDB:</span><span class="sxs-lookup"><span data-stu-id="a5949-213">The constructor of this class is provided the MongoDB connection string:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="a5949-214">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; representa o banco de dados Mongo para execução de operações.</span><span class="sxs-lookup"><span data-stu-id="a5949-214">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="a5949-215">Este tutorial usa o método [GetCollection<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) genérico na interface para obter acesso aos dados em uma coleção específica.</span><span class="sxs-lookup"><span data-stu-id="a5949-215">This tutorial uses the generic [GetCollection<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="a5949-216">Execute operações CRUD em relação à coleção depois que esse método for chamado.</span><span class="sxs-lookup"><span data-stu-id="a5949-216">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="a5949-217">Na chamada de método `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="a5949-217">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="a5949-218">`collection` representa o nome da coleção.</span><span class="sxs-lookup"><span data-stu-id="a5949-218">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="a5949-219">`TDocument` representa o tipo de objeto CLR armazenado na coleção.</span><span class="sxs-lookup"><span data-stu-id="a5949-219">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="a5949-220">`GetCollection<TDocument>(collection)` retorna um objeto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) que representa a coleção.</span><span class="sxs-lookup"><span data-stu-id="a5949-220">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="a5949-221">Neste tutorial, os seguintes métodos são invocados na coleção:</span><span class="sxs-lookup"><span data-stu-id="a5949-221">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="a5949-222">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; exclui um único documento que correspondem aos critérios de pesquisa fornecidos.</span><span class="sxs-lookup"><span data-stu-id="a5949-222">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="a5949-223">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; retorna todos os documentos na coleção que correspondem aos critérios de pesquisa fornecidos.</span><span class="sxs-lookup"><span data-stu-id="a5949-223">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="a5949-224">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; insere o objeto fornecido como um novo documento na coleção.</span><span class="sxs-lookup"><span data-stu-id="a5949-224">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="a5949-225">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; substitui o único documento que corresponde aos critérios de pesquisa fornecidos com o objeto fornecido.</span><span class="sxs-lookup"><span data-stu-id="a5949-225">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="a5949-226">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="a5949-226">Add a controller</span></span>

<span data-ttu-id="a5949-227">Adicione uma classe `BooksController` ao diretório *Controladores* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a5949-227">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

<span data-ttu-id="a5949-228">O controlador da API Web anterior:</span><span class="sxs-lookup"><span data-stu-id="a5949-228">The preceding web API controller:</span></span>

* <span data-ttu-id="a5949-229">Usa a classe `BookService` para executar operações CRUD.</span><span class="sxs-lookup"><span data-stu-id="a5949-229">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="a5949-230">Contém métodos de ação para dar suporte a solicitações GET, POST, PUT e DELETE HTTP.</span><span class="sxs-lookup"><span data-stu-id="a5949-230">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="a5949-231">Chama o <xref:System.Web.Http.ApiController.CreatedAtRoute*> no método de ação `Create` para retornar uma resposta [HTTP 201](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="a5949-231">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="a5949-232">O código de status 201 é a resposta padrão para um método HTTP POST que cria um recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="a5949-232">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="a5949-233">`CreatedAtRoute` também adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="a5949-233">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="a5949-234">O cabeçalho `Location` especifica o URI do livro recém-criado.</span><span class="sxs-lookup"><span data-stu-id="a5949-234">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="a5949-235">Testar a API Web</span><span class="sxs-lookup"><span data-stu-id="a5949-235">Test the web API</span></span>

1. <span data-ttu-id="a5949-236">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a5949-236">Build and run the app.</span></span>

1. <span data-ttu-id="a5949-237">Navegue até `http://localhost:<port>/api/books` para testar o método de ação `Get` sem parâmetros do controlador.</span><span class="sxs-lookup"><span data-stu-id="a5949-237">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="a5949-238">A seguinte resposta JSON é exibida:</span><span class="sxs-lookup"><span data-stu-id="a5949-238">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="a5949-239">Navegue até `http://localhost:<port>/api/books/5bfd996f7b8e48dc15ff215e` para testar o método de ação `Get` sobrecarregado do controlador.</span><span class="sxs-lookup"><span data-stu-id="a5949-239">Navigate to `http://localhost:<port>/api/books/5bfd996f7b8e48dc15ff215e` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="a5949-240">A seguinte resposta JSON é exibida:</span><span class="sxs-lookup"><span data-stu-id="a5949-240">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"5bfd996f7b8e48dc15ff215e",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="next-steps"></a><span data-ttu-id="a5949-241">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="a5949-241">Next steps</span></span>

<span data-ttu-id="a5949-242">Para saber mais sobre a criação de APIs Web do ASP.NET Core, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="a5949-242">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="a5949-243">Versão deste artigo no YouTube</span><span class="sxs-lookup"><span data-stu-id="a5949-243">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
