---
title: Criar APIs Web com o ASP.NET Core e o MongoDB
author: prkhandelwal
description: Este tutorial demonstra como criar uma API Web do ASP.NET Core usando um banco de dados NoSQL do MongoDB.
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 01/31/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 95a5f8bdb4b302d6bdae7b5809b54f1b263e6ee4
ms.sourcegitcommit: 1a7000630e55da90da19b284e1b2f2f13a393d74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012858"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="7c499-103">Criar uma API Web com o ASP.NET Core e o MongoDB</span><span class="sxs-lookup"><span data-stu-id="7c499-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="7c499-104">Por [Pratik Khandelwal](https://twitter.com/K2Prk) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="7c499-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="7c499-105">Este tutorial cria uma API Web que executa as operações CRUD (criar, ler, atualizar e excluir) em um banco de dados NoSQL do [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="7c499-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="7c499-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="7c499-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7c499-107">Configurar o MongoDB</span><span class="sxs-lookup"><span data-stu-id="7c499-107">Configure MongoDB</span></span>
> * <span data-ttu-id="7c499-108">Criar um banco de dados do MongoDB</span><span class="sxs-lookup"><span data-stu-id="7c499-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="7c499-109">Definir uma coleção e um esquema do MongoDB</span><span class="sxs-lookup"><span data-stu-id="7c499-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="7c499-110">Executar operações CRUD do MongoDB a partir de uma API Web</span><span class="sxs-lookup"><span data-stu-id="7c499-110">Perform MongoDB CRUD operations from a web API</span></span>

<span data-ttu-id="7c499-111">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7c499-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7c499-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="7c499-112">Prerequisites</span></span>

# [<a name="visual-studio"></a><span data-ttu-id="7c499-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c499-113">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="7c499-114">SDK do .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="7c499-114">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="7c499-115">[Visual Studio 2017 versão 15.9 ou posterior](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) com a carga de trabalho **ASP.NET e desenvolvimento para a Web**</span><span class="sxs-lookup"><span data-stu-id="7c499-115">[Visual Studio 2017 version 15.9 or later](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="7c499-116">MongoDB</span><span class="sxs-lookup"><span data-stu-id="7c499-116">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# [<a name="visual-studio-code"></a><span data-ttu-id="7c499-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c499-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="7c499-118">SDK do .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="7c499-118">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="7c499-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c499-119">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="7c499-120">C# para Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c499-120">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="7c499-121">MongoDB</span><span class="sxs-lookup"><span data-stu-id="7c499-121">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# [<a name="visual-studio-for-mac"></a><span data-ttu-id="7c499-122">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c499-122">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="7c499-123">SDK do .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="7c499-123">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="7c499-124">Visual Studio para Mac versão 7.7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="7c499-124">Visual Studio for Mac version 7.7 or later</span></span>](https://www.visualstudio.com/downloads/)
* [<span data-ttu-id="7c499-125">MongoDB</span><span class="sxs-lookup"><span data-stu-id="7c499-125">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="7c499-126">Configurar o MongoDB</span><span class="sxs-lookup"><span data-stu-id="7c499-126">Configure MongoDB</span></span>

<span data-ttu-id="7c499-127">Se usar o Windows, o MongoDB será instalado em *C:\\Arquivos de Programas\\MongoDB* por padrão.</span><span class="sxs-lookup"><span data-stu-id="7c499-127">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="7c499-128">Adicione *C:\\Arquivos de Programas\\MongoDB\\Servidor\\\<número_de_versão>\\bin* à variável de ambiente `Path`.</span><span class="sxs-lookup"><span data-stu-id="7c499-128">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="7c499-129">Essa alteração possibilita o acesso ao MongoDB a partir de qualquer lugar em seu computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="7c499-129">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="7c499-130">Use o Shell do mongo nas etapas a seguir para criar um banco de dados, fazer coleções e armazenar documentos.</span><span class="sxs-lookup"><span data-stu-id="7c499-130">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="7c499-131">Para saber mais sobre os comandos de Shell do mongo, consulte [Como trabalhar com o Shell do mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="7c499-131">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="7c499-132">Escolha um diretório no seu computador de desenvolvimento para armazenar os dados.</span><span class="sxs-lookup"><span data-stu-id="7c499-132">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="7c499-133">Por exemplo, *C:\\BooksData* no Windows.</span><span class="sxs-lookup"><span data-stu-id="7c499-133">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="7c499-134">Crie o diretório se não houver um.</span><span class="sxs-lookup"><span data-stu-id="7c499-134">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="7c499-135">O Shell do mongo não cria novos diretórios.</span><span class="sxs-lookup"><span data-stu-id="7c499-135">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="7c499-136">Abra um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="7c499-136">Open a command shell.</span></span> <span data-ttu-id="7c499-137">Execute o comando a seguir para se conectar ao MongoDB na porta padrão 27017.</span><span class="sxs-lookup"><span data-stu-id="7c499-137">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="7c499-138">Lembre-se de substituir `<data_directory_path>` pelo diretório escolhido na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="7c499-138">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="7c499-139">Abra outra instância do shell de comando.</span><span class="sxs-lookup"><span data-stu-id="7c499-139">Open another command shell instance.</span></span> <span data-ttu-id="7c499-140">Conecte-se ao banco de dados de testes padrão executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7c499-140">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="7c499-141">Execute o seguinte em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="7c499-141">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="7c499-142">Se ele ainda não existir, um banco de dados chamado *BookstoreDb* será criado.</span><span class="sxs-lookup"><span data-stu-id="7c499-142">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="7c499-143">Se o banco de dados existir, a conexão dele será aberta para transações.</span><span class="sxs-lookup"><span data-stu-id="7c499-143">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="7c499-144">Crie uma coleção `Books` usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7c499-144">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="7c499-145">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="7c499-145">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="7c499-146">Defina um esquema para a coleção `Books` e insira dois documentos usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7c499-146">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="7c499-147">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="7c499-147">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

1. <span data-ttu-id="7c499-148">Visualize os documentos no banco de dados usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="7c499-148">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="7c499-149">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="7c499-149">The following result is displayed:</span></span>

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

    <span data-ttu-id="7c499-150">O esquema adiciona uma propriedade `_id` gerada automaticamente do tipo `ObjectId` para cada documento.</span><span class="sxs-lookup"><span data-stu-id="7c499-150">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="7c499-151">O banco de dados está pronto.</span><span class="sxs-lookup"><span data-stu-id="7c499-151">The database is ready.</span></span> <span data-ttu-id="7c499-152">Você pode começar a criar a API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c499-152">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="7c499-153">Criar o projeto da API Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c499-153">Create the ASP.NET Core web API project</span></span>

# [<a name="visual-studio"></a><span data-ttu-id="7c499-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c499-154">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7c499-155">Acesse **Arquivo** > **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="7c499-155">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="7c499-156">Selecione **Aplicativo Web do ASP.NET Core**, nomeie o projeto como *BooksApi* e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="7c499-156">Select **ASP.NET Core Web Application**, name the project *BooksApi*, and click **OK**.</span></span>
1. <span data-ttu-id="7c499-157">Selecione a estrutura de destino **.NET Core** e **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="7c499-157">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="7c499-158">Selecione o modelo de projeto **API** e clique em **OK**:</span><span class="sxs-lookup"><span data-stu-id="7c499-158">Select the **API** project template, and click **OK**:</span></span>
1. <span data-ttu-id="7c499-159">Visite a [Galeria do NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .NET para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="7c499-159">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="7c499-160">Na janela **Console do Gerenciador de Pacotes**, navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="7c499-160">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="7c499-161">Execute o seguinte comando para instalar o driver .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="7c499-161">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

# [<a name="visual-studio-code"></a><span data-ttu-id="7c499-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c499-162">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="7c499-163">Execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="7c499-163">Run the following commands in a command shell:</span></span>

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    <span data-ttu-id="7c499-164">Um novo projeto de API Web do ASP.NET Core direcionado ao .NET Core é gerado e aberto no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7c499-164">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="7c499-165">Clique em **Sim** quando for exibida a notificação *Os ativos necessários para compilar e depurar estão ausentes em “BooksApi”. Adicioná-los?*.</span><span class="sxs-lookup"><span data-stu-id="7c499-165">Click **Yes** when the *Required assets to build and debug are missing from 'BooksApi'. Add them?* notification appears.</span></span>
1. <span data-ttu-id="7c499-166">Visite a [Galeria do NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .NET para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="7c499-166">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="7c499-167">Abra **Terminal Integrado** e navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="7c499-167">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="7c499-168">Execute o seguinte comando para instalar o driver .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="7c499-168">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# [<a name="visual-studio-for-mac"></a><span data-ttu-id="7c499-169">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="7c499-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="7c499-170">Vá para **Arquivo** > **Nova Solução** > **.NET Core** > **Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="7c499-170">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="7c499-171">Selecione o modelo de projeto C# **API Web ASP.NET Core** e clique em **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="7c499-171">Select the **ASP.NET Core Web API** C# project template, and click **Next**.</span></span>
1. <span data-ttu-id="7c499-172">Selecione **.NET Core 2.2** na lista suspensa **Estrutura de destino** e clique em **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="7c499-172">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and click **Next**.</span></span>
1. <span data-ttu-id="7c499-173">Insira *BooksApi* para o **Nome do Projeto**e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="7c499-173">Enter *BooksApi* for the **Project Name**, and click **Create**.</span></span>
1. <span data-ttu-id="7c499-174">No painel **Solução**, clique com o botão direito do mouse no nó **Dependências** do projeto e selecione **Adicionar Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="7c499-174">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="7c499-175">Insira *MongoDB.Driver* na caixa de pesquisa, selecione o pacote *MongoDB.Driver* e clique em **Adicionar Pacote**.</span><span class="sxs-lookup"><span data-stu-id="7c499-175">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and click **Add Package**.</span></span>
1. <span data-ttu-id="7c499-176">Clique no botão **Aceitar** na caixa de diálogo **Aceitação da Licença**.</span><span class="sxs-lookup"><span data-stu-id="7c499-176">Click the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-a-model"></a><span data-ttu-id="7c499-177">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="7c499-177">Add a model</span></span>

1. <span data-ttu-id="7c499-178">Adicione um diretório *Modelos* à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="7c499-178">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="7c499-179">Adicione uma classe `Book` ao diretório *Modelos* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="7c499-179">Add a `Book` class to the *Models* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs)]

<span data-ttu-id="7c499-180">Na classe anterior, a propriedade `Id`:</span><span class="sxs-lookup"><span data-stu-id="7c499-180">In the preceding class, the `Id` property:</span></span>

* <span data-ttu-id="7c499-181">É necessária para mapear o objeto CLR (Common Language Runtime) para a coleção do MongoDB.</span><span class="sxs-lookup"><span data-stu-id="7c499-181">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
* <span data-ttu-id="7c499-182">É anotada com `[BsonId]` para designar essa propriedade como a chave primária do documento.</span><span class="sxs-lookup"><span data-stu-id="7c499-182">Is annotated with `[BsonId]` to designate this property as the document's primary key.</span></span>
* <span data-ttu-id="7c499-183">É anotada com `[BsonRepresentation(BsonType.ObjectId)]` para permitir a passagem do parâmetro como tipo `string`, em vez de `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="7c499-183">Is annotated with `[BsonRepresentation(BsonType.ObjectId)]` to allow passing the parameter as type `string` instead of `ObjectId`.</span></span> <span data-ttu-id="7c499-184">O Mongo processa a conversão de `string` para `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="7c499-184">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

<span data-ttu-id="7c499-185">Outras propriedades na classe são anotadas com o atributo `[BsonElement]`.</span><span class="sxs-lookup"><span data-stu-id="7c499-185">Other properties in the class are annotated with the `[BsonElement]` attribute.</span></span> <span data-ttu-id="7c499-186">O valor do atributo representa o nome da propriedade da coleção do MongoDB.</span><span class="sxs-lookup"><span data-stu-id="7c499-186">The attribute's value represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-crud-operations-class"></a><span data-ttu-id="7c499-187">Adicionar uma classe de operações CRUD</span><span class="sxs-lookup"><span data-stu-id="7c499-187">Add a CRUD operations class</span></span>

1. <span data-ttu-id="7c499-188">Adicione um diretório *Serviços* à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="7c499-188">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="7c499-189">Adicione uma classe `BookService` ao diretório *Serviços* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="7c499-189">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

1. <span data-ttu-id="7c499-190">Adicione uma cadeia de conexão do MongoDB a *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7c499-190">Add the MongoDB connection string to *appsettings.json*:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-4)]

    <span data-ttu-id="7c499-191">A propriedade `BookstoreDb` anterior é acessada no construtor de classe `BookService`.</span><span class="sxs-lookup"><span data-stu-id="7c499-191">The preceding `BookstoreDb` property is accessed in the `BookService` class constructor.</span></span>

1. <span data-ttu-id="7c499-192">No `Startup.ConfigureServices`, registre a classe `BookService` com o sistema de Injeção de Dependência:</span><span class="sxs-lookup"><span data-stu-id="7c499-192">In `Startup.ConfigureServices`, register the `BookService` class with the Dependency Injection system:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

    <span data-ttu-id="7c499-193">O registro de serviço anterior é necessário para dar suporte à injeção do construtor no consumo de classes.</span><span class="sxs-lookup"><span data-stu-id="7c499-193">The preceding service registration is necessary to support constructor injection in consuming classes.</span></span>

<span data-ttu-id="7c499-194">A classe `BookService` usa os seguintes membros `MongoDB.Driver` para executar operações CRUD em relação ao banco de dados:</span><span class="sxs-lookup"><span data-stu-id="7c499-194">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* `MongoClient` <span data-ttu-id="7c499-195">&ndash; Lê a instância do servidor para execução de operações de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7c499-195">&ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="7c499-196">O construtor dessa classe é fornecido na cadeia de conexão do MongoDB:</span><span class="sxs-lookup"><span data-stu-id="7c499-196">The constructor of this class is provided the MongoDB connection string:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* `IMongoDatabase` <span data-ttu-id="7c499-197">&ndash; Representa o banco de dados Mongo para execução de operações.</span><span class="sxs-lookup"><span data-stu-id="7c499-197">&ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="7c499-198">Este tutorial usa o método genérico `GetCollection<T>(collection)` na interface para obter acesso a dados em uma coleção específica.</span><span class="sxs-lookup"><span data-stu-id="7c499-198">This tutorial uses the generic `GetCollection<T>(collection)` method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="7c499-199">Operações CRUD podem ser executadas em relação à coleção depois que esse método é chamado.</span><span class="sxs-lookup"><span data-stu-id="7c499-199">CRUD operations can be performed against the collection after this method is called.</span></span> <span data-ttu-id="7c499-200">Na chamada de método `GetCollection<T>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="7c499-200">In the `GetCollection<T>(collection)` method call:</span></span>
  * `collection` <span data-ttu-id="7c499-201">representa o nome da coleção.</span><span class="sxs-lookup"><span data-stu-id="7c499-201">represents the collection name.</span></span>
  * `T` <span data-ttu-id="7c499-202">representa o tipo de objeto CLR armazenado na coleção.</span><span class="sxs-lookup"><span data-stu-id="7c499-202">represents the CLR object type stored in the collection.</span></span>

`GetCollection<T>(collection)` <span data-ttu-id="7c499-203">retorna um objeto `MongoCollection` que representa a coleção.</span><span class="sxs-lookup"><span data-stu-id="7c499-203">returns a `MongoCollection` object representing the collection.</span></span> <span data-ttu-id="7c499-204">Neste tutorial, os seguintes métodos são invocados na coleção:</span><span class="sxs-lookup"><span data-stu-id="7c499-204">In this tutorial, the following methods are invoked on the collection:</span></span>

* `Find<T>` <span data-ttu-id="7c499-205">&ndash; Retorna todos os documentos na coleção que correspondem aos critérios de pesquisa fornecidos.</span><span class="sxs-lookup"><span data-stu-id="7c499-205">&ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* `InsertOne` <span data-ttu-id="7c499-206">&ndash; Insere o objeto fornecido como um novo documento na coleção.</span><span class="sxs-lookup"><span data-stu-id="7c499-206">&ndash; Inserts the provided object as a new document in the collection.</span></span>
* `ReplaceOne` <span data-ttu-id="7c499-207">&ndash; Substitui o documento único que corresponde aos critérios de pesquisa definidos com o objeto fornecido.</span><span class="sxs-lookup"><span data-stu-id="7c499-207">&ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>
* `DeleteOne` <span data-ttu-id="7c499-208">&ndash; Exclui um documento único que corresponde aos critérios de pesquisa fornecidos.</span><span class="sxs-lookup"><span data-stu-id="7c499-208">&ndash; Deletes a single document matching the provided search criteria.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="7c499-209">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="7c499-209">Add a controller</span></span>

1. <span data-ttu-id="7c499-210">Adicione uma classe `BooksController` ao diretório *Controladores* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="7c499-210">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

    <span data-ttu-id="7c499-211">O controlador da API Web anterior:</span><span class="sxs-lookup"><span data-stu-id="7c499-211">The preceding web API controller:</span></span>

    * <span data-ttu-id="7c499-212">Usa a classe `BookService` para executar operações CRUD.</span><span class="sxs-lookup"><span data-stu-id="7c499-212">Uses the `BookService` class to perform CRUD operations.</span></span>
    * <span data-ttu-id="7c499-213">Contém métodos de ação para dar suporte a solicitações GET, POST, PUT e DELETE HTTP.</span><span class="sxs-lookup"><span data-stu-id="7c499-213">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
    * <span data-ttu-id="7c499-214">O método <xref:System.Web.Http.ApiController.CreatedAtRoute*> retorna uma resposta 201, que é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="7c499-214">The <xref:System.Web.Http.ApiController.CreatedAtRoute*> method returns a 201 response, which is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> `CreatedAtRoute` <span data-ttu-id="7c499-215">também adiciona um cabeçalho Local à resposta.</span><span class="sxs-lookup"><span data-stu-id="7c499-215">also adds a Location header to the response.</span></span> <span data-ttu-id="7c499-216">O cabeçalho Location especifica o URI do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="7c499-216">The Location header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="7c499-217">Consulte [10.2.2 201 criado](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="7c499-217">See [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
1. <span data-ttu-id="7c499-218">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7c499-218">Build and run the app.</span></span>
1. <span data-ttu-id="7c499-219">Navegue até `http://localhost:<port>/api/books` no seu navegador.</span><span class="sxs-lookup"><span data-stu-id="7c499-219">Navigate to `http://localhost:<port>/api/books` in your browser.</span></span> <span data-ttu-id="7c499-220">A seguinte resposta JSON é exibida:</span><span class="sxs-lookup"><span data-stu-id="7c499-220">The following JSON response is displayed:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="7c499-221">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="7c499-221">Next steps</span></span>

<span data-ttu-id="7c499-222">Para saber mais sobre a criação de APIs Web do ASP.NET Core, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="7c499-222">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="7c499-223">Versão deste tutorial no YouTube</span><span class="sxs-lookup"><span data-stu-id="7c499-223">Youtube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
