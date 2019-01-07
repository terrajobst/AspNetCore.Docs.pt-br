---
title: Criar APIs Web com o ASP.NET Core e o MongoDB
author: prkhandelwal
description: Este tutorial demonstra como criar uma API Web do ASP.NET Core usando um banco de dados NoSQL do MongoDB.
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 11/29/2018
uid: tutorials/first-mongo-app
ms.openlocfilehash: bd9a36c5eb06542c820e71e937b8da10f735a0f8
ms.sourcegitcommit: 68a3081dd175d6518d1bfa31b4712bd8a2dd3864
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53577832"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="1f4d3-103">Criar uma API Web com o ASP.NET Core e o MongoDB</span><span class="sxs-lookup"><span data-stu-id="1f4d3-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="1f4d3-104">Por [Pratik Khandelwal](https://twitter.com/K2Prk) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="1f4d3-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="1f4d3-105">Este tutorial cria uma API Web que executa as operações CRUD (criar, ler, atualizar e excluir) em um banco de dados NoSQL do [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="1f4d3-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="1f4d3-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1f4d3-107">Configurar o MongoDB</span><span class="sxs-lookup"><span data-stu-id="1f4d3-107">Configure MongoDB</span></span>
> * <span data-ttu-id="1f4d3-108">Criar um banco de dados do MongoDB</span><span class="sxs-lookup"><span data-stu-id="1f4d3-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="1f4d3-109">Definir uma coleção e um esquema do MongoDB</span><span class="sxs-lookup"><span data-stu-id="1f4d3-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="1f4d3-110">Executar operações CRUD do MongoDB a partir de uma API Web</span><span class="sxs-lookup"><span data-stu-id="1f4d3-110">Perform MongoDB CRUD operations from a web API</span></span>

<span data-ttu-id="1f4d3-111">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1f4d3-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1f4d3-112">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="1f4d3-112">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1f4d3-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f4d3-113">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="1f4d3-114">SDK 2.2 ou posterior do .NET Core</span><span class="sxs-lookup"><span data-stu-id="1f4d3-114">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="1f4d3-115">[Visual Studio 2017 versão 15.9 ou posterior](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) com a carga de trabalho **ASP.NET e desenvolvimento para a Web**</span><span class="sxs-lookup"><span data-stu-id="1f4d3-115">[Visual Studio 2017 version 15.9 or later](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="1f4d3-116">MongoDB</span><span class="sxs-lookup"><span data-stu-id="1f4d3-116">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1f4d3-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f4d3-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="1f4d3-118">SDK 2.2 ou posterior do .NET Core</span><span class="sxs-lookup"><span data-stu-id="1f4d3-118">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="1f4d3-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f4d3-119">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="1f4d3-120">C# para Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f4d3-120">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="1f4d3-121">MongoDB</span><span class="sxs-lookup"><span data-stu-id="1f4d3-121">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1f4d3-122">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1f4d3-122">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="1f4d3-123">SDK 2.2 ou posterior do .NET Core</span><span class="sxs-lookup"><span data-stu-id="1f4d3-123">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="1f4d3-124">Visual Studio para Mac versão 7.7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="1f4d3-124">Visual Studio for Mac version 7.7 or later</span></span>](https://www.visualstudio.com/downloads/)
* [<span data-ttu-id="1f4d3-125">MongoDB</span><span class="sxs-lookup"><span data-stu-id="1f4d3-125">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="1f4d3-126">Configurar o MongoDB</span><span class="sxs-lookup"><span data-stu-id="1f4d3-126">Configure MongoDB</span></span>

<span data-ttu-id="1f4d3-127">Se você usar o Windows, o MongoDB será instalado em *C:\Arquivos de Programas\MongoDB* por padrão.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-127">If using Windows, MongoDB is installed at *C:\Program Files\MongoDB* by default.</span></span> <span data-ttu-id="1f4d3-128">Adicione *C:\Arquivos de Programas\MongoDB\Server\<número_de_versão>\bin* à variável de ambiente `Path`.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-128">Add *C:\Program Files\MongoDB\Server\<version_number>\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="1f4d3-129">Essa alteração possibilita o acesso ao MongoDB a partir de qualquer lugar em seu computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-129">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="1f4d3-130">Use o Shell do mongo nas etapas a seguir para criar um banco de dados, fazer coleções e armazenar documentos.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-130">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="1f4d3-131">Para saber mais sobre os comandos de Shell do mongo, consulte [Como trabalhar com o Shell do mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="1f4d3-131">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="1f4d3-132">Escolha um diretório no seu computador de desenvolvimento para armazenar os dados.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-132">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="1f4d3-133">Por exemplo, *C:\BooksData* no Windows.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-133">For example, *C:\BooksData* on Windows.</span></span> <span data-ttu-id="1f4d3-134">Crie o diretório se não houver um.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-134">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="1f4d3-135">O Shell do mongo não cria novos diretórios.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-135">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="1f4d3-136">Abra um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-136">Open a command shell.</span></span> <span data-ttu-id="1f4d3-137">Execute o comando a seguir para se conectar ao MongoDB na porta padrão 27017.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-137">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="1f4d3-138">Lembre-se de substituir `<data_directory_path>` pelo diretório escolhido na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-138">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="1f4d3-139">Abra outra instância do shell de comando.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-139">Open another command shell instance.</span></span> <span data-ttu-id="1f4d3-140">Conecte-se ao banco de dados de testes padrão executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-140">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="1f4d3-141">Execute o seguinte em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-141">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="1f4d3-142">Se ele ainda não existir, um banco de dados chamado *BookstoreDb* será criado.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-142">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="1f4d3-143">Se o banco de dados existir, a conexão dele será aberta para transações.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-143">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="1f4d3-144">Crie uma coleção `Books` usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-144">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="1f4d3-145">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-145">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="1f4d3-146">Defina um esquema para a coleção `Books` e insira dois documentos usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-146">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="1f4d3-147">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-147">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

1. <span data-ttu-id="1f4d3-148">Visualize os documentos no banco de dados usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-148">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="1f4d3-149">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-149">The following result is displayed:</span></span>

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

    <span data-ttu-id="1f4d3-150">O esquema adiciona uma propriedade `_id` gerada automaticamente do tipo `ObjectId` para cada documento.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-150">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="1f4d3-151">O banco de dados está pronto.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-151">The database is ready.</span></span> <span data-ttu-id="1f4d3-152">Você pode começar a criar a API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-152">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="1f4d3-153">Criar o projeto da API Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1f4d3-153">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1f4d3-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f4d3-154">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1f4d3-155">Acesse **Arquivo** > **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-155">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="1f4d3-156">Selecione **Aplicativo Web do ASP.NET Core**, nomeie o projeto como *BooksApi* e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-156">Select **ASP.NET Core Web Application**, name the project *BooksApi*, and click **OK**.</span></span>
1. <span data-ttu-id="1f4d3-157">Selecione a estrutura de destino **.NET Core** e **ASP.NET Core 2.1**.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-157">Select the **.NET Core** target framework and **ASP.NET Core 2.1**.</span></span> <span data-ttu-id="1f4d3-158">Selecione o modelo de projeto **API** e clique em **OK**:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-158">Select the **API** project template, and click **OK**:</span></span>
1. <span data-ttu-id="1f4d3-159">Na janela **Console do Gerenciador de Pacotes**, navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-159">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="1f4d3-160">Execute o seguinte comando para instalar o driver .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-160">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version 2.7.2
    ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1f4d3-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f4d3-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="1f4d3-162">Execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-162">Run the following commands in a command shell:</span></span>

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    <span data-ttu-id="1f4d3-163">Um novo projeto de API Web do ASP.NET Core direcionado ao .NET Core é gerado e aberto no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-163">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="1f4d3-164">Clique em **Sim** quando for exibida a notificação *Os ativos necessários para compilar e depurar estão ausentes em “BooksApi”. Adicioná-los?*.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-164">Click **Yes** when the *Required assets to build and debug are missing from 'BooksApi'. Add them?* notification appears.</span></span>
1. <span data-ttu-id="1f4d3-165">Abra **Terminal Integrado** e navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-165">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="1f4d3-166">Execute o seguinte comando para instalar o driver .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-166">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v 2.7.2
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1f4d3-167">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="1f4d3-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="1f4d3-168">Vá para **Arquivo** > **Nova Solução** > **.NET Core** > **Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-168">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="1f4d3-169">Selecione o modelo de projeto C# **API Web ASP.NET Core** e clique em **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-169">Select the **ASP.NET Core Web API** C# project template, and click **Next**.</span></span>
1. <span data-ttu-id="1f4d3-170">Selecione **.NET Core 2.2** na lista suspensa **Estrutura de destino** e clique em **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-170">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and click **Next**.</span></span>
1. <span data-ttu-id="1f4d3-171">Insira *BooksApi* para o **Nome do Projeto**e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-171">Enter *BooksApi* for the **Project Name**, and click **Create**.</span></span>
1. <span data-ttu-id="1f4d3-172">No painel **Solução**, clique com o botão direito do mouse no nó **Dependências** do projeto e selecione **Adicionar Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-172">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="1f4d3-173">Insira *MongoDB.Driver* na caixa de pesquisa, selecione o pacote *MongoDB.Driver* e clique em **Adicionar Pacote**.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-173">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and click **Add Package**.</span></span>
1. <span data-ttu-id="1f4d3-174">Clique no botão **Aceitar** na caixa de diálogo **Aceitação da Licença**.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-174">Click the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-a-model"></a><span data-ttu-id="1f4d3-175">Adicionar um modelo</span><span class="sxs-lookup"><span data-stu-id="1f4d3-175">Add a model</span></span>

1. <span data-ttu-id="1f4d3-176">Adicione um diretório *Modelos* à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-176">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="1f4d3-177">Adicione uma classe `Book` ao diretório *Modelos* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-177">Add a `Book` class to the *Models* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs)]

<span data-ttu-id="1f4d3-178">Na classe anterior, a propriedade `Id` é necessária para mapear o objeto de Common Language Runtime (CLR) para a coleção do MongoDB.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-178">In the preceding class, the `Id` property is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span> <span data-ttu-id="1f4d3-179">Outras propriedades na classe são decoradas com o atributo `[BsonElement]`.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-179">Other properties in the class are decorated with the `[BsonElement]` attribute.</span></span> <span data-ttu-id="1f4d3-180">O valor do atributo representa o nome da propriedade da coleção do MongoDB.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-180">The attribute's value represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-crud-operations-class"></a><span data-ttu-id="1f4d3-181">Adicionar uma classe de operações CRUD</span><span class="sxs-lookup"><span data-stu-id="1f4d3-181">Add a CRUD operations class</span></span>

1. <span data-ttu-id="1f4d3-182">Adicione um diretório *Serviços* à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-182">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="1f4d3-183">Adicione uma classe `BookService` ao diretório *Serviços* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-183">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

1. <span data-ttu-id="1f4d3-184">Adicione uma cadeia de conexão do MongoDB a *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-184">Add the MongoDB connection string to *appsettings.json*:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-4)]

    <span data-ttu-id="1f4d3-185">A propriedade `BookstoreDb` anterior é acessada no construtor de classe `BookService`.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-185">The preceding `BookstoreDb` property is accessed in the `BookService` class constructor.</span></span>

1. <span data-ttu-id="1f4d3-186">No `Startup.ConfigureServices`, registre a classe `BookService` com o sistema de Injeção de Dependência:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-186">In `Startup.ConfigureServices`, register the `BookService` class with the Dependency Injection system:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

    <span data-ttu-id="1f4d3-187">O registro de serviço anterior é necessário para dar suporte à injeção do construtor no consumo de classes.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-187">The preceding service registration is necessary to support constructor injection in consuming classes.</span></span>

<span data-ttu-id="1f4d3-188">A classe `BookService` usa os seguintes membros `MongoDB.Driver` para executar operações CRUD em relação ao banco de dados:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-188">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="1f4d3-189">`MongoClient` &ndash; Lê a instância do servidor para executar operações de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-189">`MongoClient` &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="1f4d3-190">O construtor dessa classe é fornecido na cadeia de conexão do MongoDB:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-190">The constructor of this class is provided the MongoDB connection string:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="1f4d3-191">`IMongoDatabase` &ndash; Representa o banco de dados Mongo para execução de operações.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-191">`IMongoDatabase` &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="1f4d3-192">Este tutorial usa o método genérico `GetCollection<T>(collection)` na interface para obter acesso a dados em uma coleção específica.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-192">This tutorial uses the generic `GetCollection<T>(collection)` method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="1f4d3-193">Operações CRUD podem ser executadas em relação à coleção depois que esse método é chamado.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-193">CRUD operations can be performed against the collection after this method is called.</span></span> <span data-ttu-id="1f4d3-194">Na chamada de método `GetCollection<T>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-194">In the `GetCollection<T>(collection)` method call:</span></span>
  * <span data-ttu-id="1f4d3-195">`collection` representa o nome da coleção.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-195">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="1f4d3-196">`T` representa o tipo de objeto CLR armazenado na coleção.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-196">`T` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="1f4d3-197">`GetCollection<T>(collection)` retorna um objeto `MongoCollection` que representa a coleção.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-197">`GetCollection<T>(collection)` returns a `MongoCollection` object representing the collection.</span></span> <span data-ttu-id="1f4d3-198">Neste tutorial, os seguintes métodos são invocados na coleção:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-198">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="1f4d3-199">`Find<T>` &ndash; Retorna todos os documentos na coleção que correspondem aos critérios de pesquisa fornecidos.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-199">`Find<T>` &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="1f4d3-200">`InsertOne` &ndash; Insere o objeto fornecido como um novo documento na coleção.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-200">`InsertOne` &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="1f4d3-201">`ReplaceOne` &ndash; Substitui o documento único que corresponde aos critérios de pesquisa definidos com o objeto fornecido.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-201">`ReplaceOne` &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>
* <span data-ttu-id="1f4d3-202">`DeleteOne` &ndash; Exclui um documento único que corresponde aos critérios de pesquisa fornecidos.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-202">`DeleteOne` &ndash; Deletes a single document matching the provided search criteria.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="1f4d3-203">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="1f4d3-203">Add a controller</span></span>

1. <span data-ttu-id="1f4d3-204">Adicione uma classe `BooksController` ao diretório *Controladores* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-204">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

    <span data-ttu-id="1f4d3-205">O controlador da API Web anterior:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-205">The preceding web API controller:</span></span>

    * <span data-ttu-id="1f4d3-206">Usa a classe `BookService` para executar operações CRUD.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-206">Uses the `BookService` class to perform CRUD operations.</span></span>
    * <span data-ttu-id="1f4d3-207">Contém métodos de ação para dar suporte a solicitações GET, POST, PUT e DELETE HTTP.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-207">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
1. <span data-ttu-id="1f4d3-208">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-208">Build and run the app.</span></span>
1. <span data-ttu-id="1f4d3-209">Navegue até `http://localhost:<port>/api/books` no seu navegador.</span><span class="sxs-lookup"><span data-stu-id="1f4d3-209">Navigate to `http://localhost:<port>/api/books` in your browser.</span></span> <span data-ttu-id="1f4d3-210">A seguinte resposta JSON é exibida:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-210">The following JSON response is displayed:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="1f4d3-211">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="1f4d3-211">Next steps</span></span>

<span data-ttu-id="1f4d3-212">Para saber mais sobre a criação de APIs Web do ASP.NET Core, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="1f4d3-212">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
