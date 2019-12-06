---
title: Criar uma API Web com o ASP.NET Core e o MongoDB
author: prkhandelwal
description: Este tutorial demonstra como criar uma API Web com o ASP.NET Core usando um banco de dados MongoDB NoSQL.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 1425abbfc7bce6bdc445f4e41d9e004405c96e13
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880342"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="3fffb-103">Criar uma API Web com o ASP.NET Core e o MongoDB</span><span class="sxs-lookup"><span data-stu-id="3fffb-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="3fffb-104">Por [Pratik Khandelwal](https://twitter.com/K2Prk) e [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="3fffb-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3fffb-105">Este tutorial cria uma API Web que executa as operações CRUD (criar, ler, atualizar e excluir) em um banco de dados NoSQL do [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="3fffb-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="3fffb-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="3fffb-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3fffb-107">Configurar o MongoDB</span><span class="sxs-lookup"><span data-stu-id="3fffb-107">Configure MongoDB</span></span>
> * <span data-ttu-id="3fffb-108">Criar um banco de dados do MongoDB</span><span class="sxs-lookup"><span data-stu-id="3fffb-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="3fffb-109">Definir uma coleção e um esquema do MongoDB</span><span class="sxs-lookup"><span data-stu-id="3fffb-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="3fffb-110">Executar operações CRUD do MongoDB a partir de uma API Web</span><span class="sxs-lookup"><span data-stu-id="3fffb-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="3fffb-111">Personalizar a serialização JSON</span><span class="sxs-lookup"><span data-stu-id="3fffb-111">Customize JSON serialization</span></span>

<span data-ttu-id="3fffb-112">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3fffb-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3fffb-113">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="3fffb-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3fffb-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3fffb-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="3fffb-115">SDK do .NET Core 3.0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="3fffb-115">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="3fffb-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a carga de trabalho de **desenvolvimento da Web e do ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="3fffb-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="3fffb-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3fffb-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3fffb-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3fffb-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="3fffb-119">SDK do .NET Core 3.0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="3fffb-119">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="3fffb-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3fffb-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="3fffb-121">C# para Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3fffb-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="3fffb-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3fffb-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3fffb-123">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3fffb-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="3fffb-124">SDK do .NET Core 3.0 ou posterior</span><span class="sxs-lookup"><span data-stu-id="3fffb-124">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="3fffb-125">Visual Studio para Mac versão 7.7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="3fffb-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="3fffb-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3fffb-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="3fffb-127">Configurar o MongoDB</span><span class="sxs-lookup"><span data-stu-id="3fffb-127">Configure MongoDB</span></span>

<span data-ttu-id="3fffb-128">Se usar o Windows, o MongoDB será instalado em *C:\\Arquivos de Programas\\MongoDB* por padrão.</span><span class="sxs-lookup"><span data-stu-id="3fffb-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="3fffb-129">Adicione *C:\\Arquivos de Programas\\MongoDB\\Servidor\\\<número_de_versão>\\bin* à variável de ambiente `Path`.</span><span class="sxs-lookup"><span data-stu-id="3fffb-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="3fffb-130">Essa alteração possibilita o acesso ao MongoDB a partir de qualquer lugar em seu computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="3fffb-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="3fffb-131">Use o Shell do mongo nas etapas a seguir para criar um banco de dados, fazer coleções e armazenar documentos.</span><span class="sxs-lookup"><span data-stu-id="3fffb-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="3fffb-132">Para saber mais sobre os comandos de Shell do mongo, consulte [Como trabalhar com o Shell do mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="3fffb-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="3fffb-133">Escolha um diretório no seu computador de desenvolvimento para armazenar os dados.</span><span class="sxs-lookup"><span data-stu-id="3fffb-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="3fffb-134">Por exemplo, *C:\\BooksData* no Windows.</span><span class="sxs-lookup"><span data-stu-id="3fffb-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="3fffb-135">Crie o diretório se não houver um.</span><span class="sxs-lookup"><span data-stu-id="3fffb-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="3fffb-136">O Shell do mongo não cria novos diretórios.</span><span class="sxs-lookup"><span data-stu-id="3fffb-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="3fffb-137">Abra um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="3fffb-137">Open a command shell.</span></span> <span data-ttu-id="3fffb-138">Execute o comando a seguir para se conectar ao MongoDB na porta padrão 27017.</span><span class="sxs-lookup"><span data-stu-id="3fffb-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="3fffb-139">Lembre-se de substituir `<data_directory_path>` pelo diretório escolhido na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="3fffb-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="3fffb-140">Abra outra instância do shell de comando.</span><span class="sxs-lookup"><span data-stu-id="3fffb-140">Open another command shell instance.</span></span> <span data-ttu-id="3fffb-141">Conecte-se ao banco de dados de testes padrão executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3fffb-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="3fffb-142">Execute o seguinte em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3fffb-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="3fffb-143">Se ele ainda não existir, um banco de dados chamado *BookstoreDb* será criado.</span><span class="sxs-lookup"><span data-stu-id="3fffb-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="3fffb-144">Se o banco de dados existir, a conexão dele será aberta para transações.</span><span class="sxs-lookup"><span data-stu-id="3fffb-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="3fffb-145">Crie uma coleção `Books` usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3fffb-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="3fffb-146">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="3fffb-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="3fffb-147">Defina um esquema para a coleção `Books` e insira dois documentos usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3fffb-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="3fffb-148">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="3fffb-148">The following result is displayed:</span></span>

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
   > <span data-ttu-id="3fffb-149">As IDs mostradas neste artigo não corresponderão às IDs de quando você executar esse exemplo.</span><span class="sxs-lookup"><span data-stu-id="3fffb-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="3fffb-150">Visualize os documentos no banco de dados usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3fffb-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="3fffb-151">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="3fffb-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="3fffb-152">O esquema adiciona uma propriedade `_id` gerada automaticamente do tipo `ObjectId` para cada documento.</span><span class="sxs-lookup"><span data-stu-id="3fffb-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="3fffb-153">O banco de dados está pronto.</span><span class="sxs-lookup"><span data-stu-id="3fffb-153">The database is ready.</span></span> <span data-ttu-id="3fffb-154">Você pode começar a criar a API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3fffb-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="3fffb-155">Criar o projeto da API Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fffb-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3fffb-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3fffb-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3fffb-157">Acesse **Arquivo** > **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="3fffb-158">Selecione o tipo de projeto **aplicativo Web ASP.NET Core** e, em seguida, **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="3fffb-159">Nomeie o projeto como *BooksApi* e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="3fffb-160">Selecione a estrutura de destino **.NET Core** e **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="3fffb-161">Selecione o modelo de projeto **API** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="3fffb-162">Visite a [Galeria do NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .net para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="3fffb-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="3fffb-163">Na janela **Console do Gerenciador de Pacotes**, navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="3fffb-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="3fffb-164">Execute o seguinte comando para instalar o driver .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="3fffb-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3fffb-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3fffb-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3fffb-166">Execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3fffb-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="3fffb-167">Um novo projeto de API Web do ASP.NET Core direcionado ao .NET Core é gerado e aberto no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3fffb-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="3fffb-168">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' BooksApi '. Adicioná-los?** .</span><span class="sxs-lookup"><span data-stu-id="3fffb-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="3fffb-169">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="3fffb-169">Select **Yes**.</span></span>
1. <span data-ttu-id="3fffb-170">Visite a [Galeria do NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .net para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="3fffb-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="3fffb-171">Abra **Terminal Integrado** e navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="3fffb-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="3fffb-172">Execute o seguinte comando para instalar o driver .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="3fffb-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3fffb-173">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3fffb-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3fffb-174">Acesse **Arquivo** > **Nova Solução** > **.NET Core** > **Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="3fffb-175">Selecione o modelo de projeto C# da **API Web do ASP.NET Core** e, em seguida, **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="3fffb-176">Selecione **.NET Core 3.0** na lista suspensa **Estrutura de Destino** e, em seguida, **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-176">Select **.NET Core 3.0** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="3fffb-177">Insira *BooksApi* para o **Nome do Projeto** e selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="3fffb-178">No painel **Solução**, clique com o botão direito do mouse no nó **Dependências** do projeto e selecione **Adicionar Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="3fffb-179">Insira *MongoDB.Driver* na caixa de pesquisa, escolha o pacote *MongoDB.Driver* e selecione **Adicionar Pacote**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="3fffb-180">Selecione o botão **Aceitar** na caixa de diálogo **Aceitação da Licença**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="3fffb-181">Adicionar um modelo de entidade</span><span class="sxs-lookup"><span data-stu-id="3fffb-181">Add an entity model</span></span>

1. <span data-ttu-id="3fffb-182">Adicione um diretório *Modelos* à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="3fffb-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="3fffb-183">Adicione uma classe `Book` ao diretório *Modelos* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3fffb-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="3fffb-184">Na classe anterior, a propriedade `Id`:</span><span class="sxs-lookup"><span data-stu-id="3fffb-184">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="3fffb-185">É necessária para mapear o objeto CLR (Common Language Runtime) para a coleção do MongoDB.</span><span class="sxs-lookup"><span data-stu-id="3fffb-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="3fffb-186">É anotada com [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) para designar essa propriedade como a chave primária do documento.</span><span class="sxs-lookup"><span data-stu-id="3fffb-186">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="3fffb-187">É anotado com [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) para permitir a passagem do parâmetro como o tipo `string` em vez de uma estrutura [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="3fffb-187">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="3fffb-188">O Mongo processa a conversão de `string` para `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="3fffb-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="3fffb-189">A propriedade `BookName` é anotada com o atributo [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) .</span><span class="sxs-lookup"><span data-stu-id="3fffb-189">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="3fffb-190">O valor do atributo de `Name` representa o nome da propriedade da coleção do MongoDB.</span><span class="sxs-lookup"><span data-stu-id="3fffb-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="3fffb-191">Adicionar um modelo de configuração</span><span class="sxs-lookup"><span data-stu-id="3fffb-191">Add a configuration model</span></span>

1. <span data-ttu-id="3fffb-192">Adicione os seguintes valores de configuração de banco de dados no *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3fffb-192">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="3fffb-193">Adicione um arquivo *BookstoreDatabaseSettings.cs* no diretório *Modelos* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="3fffb-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="3fffb-194">A classe precedente `BookstoreDatabaseSettings` é usada para armazenar os valores de propriedade `BookstoreDatabaseSettings` do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3fffb-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="3fffb-195">Os nomes de propriedade JSON e C# são nomeados de forma idêntica para facilitar o processo de mapeamento.</span><span class="sxs-lookup"><span data-stu-id="3fffb-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="3fffb-196">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3fffb-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="3fffb-197">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="3fffb-197">In the preceding code:</span></span>

   * <span data-ttu-id="3fffb-198">A instância de configuração à qual a seção `BookstoreDatabaseSettings` do arquivo *appsettings.json* é associada é registrada no contêiner de DI (Injeção de Dependência).</span><span class="sxs-lookup"><span data-stu-id="3fffb-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="3fffb-199">Por exemplo, a propriedade `ConnectionString` de um objeto `BookstoreDatabaseSettings` é populada com a propriedade `BookstoreDatabaseSettings:ConnectionString` no *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3fffb-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="3fffb-200">A interface `IBookstoreDatabaseSettings` é registrada na DI com um [tempo de vida do serviço](xref:fundamentals/dependency-injection#service-lifetimes) singleton.</span><span class="sxs-lookup"><span data-stu-id="3fffb-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="3fffb-201">Quando inserida, a instância da interface é resolvida para um objeto `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="3fffb-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="3fffb-202">Adicione o seguinte código na parte superior do *Startup.cs* para resolver as referências `BookstoreDatabaseSettings` e `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="3fffb-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="3fffb-203">Adicionar um serviço de operações CRUD</span><span class="sxs-lookup"><span data-stu-id="3fffb-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="3fffb-204">Adicione um diretório *Serviços* à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="3fffb-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="3fffb-205">Adicione uma classe `BookService` ao diretório *Serviços* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3fffb-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="3fffb-206">No código anterior, uma instância `IBookstoreDatabaseSettings` é recuperada da DI por meio da injeção de construtor.</span><span class="sxs-lookup"><span data-stu-id="3fffb-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="3fffb-207">Essa técnica fornece acesso para os valores de configuração do *appsettings.json* que foram adicionados na seção [Adicionar um modelo de configuração](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="3fffb-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="3fffb-208">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3fffb-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="3fffb-209">No código anterior, a classe `BookService` é registrada com a DI para dar suporte à injeção de construtor nas classes consumidoras.</span><span class="sxs-lookup"><span data-stu-id="3fffb-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="3fffb-210">O tempo de vida do serviço singleton é mais apropriado porque `BookService` usa uma dependência direta de `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="3fffb-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="3fffb-211">De acordo com as [Diretrizes oficiais de reutilização do cliente Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), o `MongoClient` deve ser registrado na DI com um tempo de vida do serviço singleton.</span><span class="sxs-lookup"><span data-stu-id="3fffb-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="3fffb-212">Adicione o seguinte código na parte superior do *Startup.cs* para resolver a referências `BookService`:</span><span class="sxs-lookup"><span data-stu-id="3fffb-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="3fffb-213">A classe `BookService` usa os seguintes membros `MongoDB.Driver` para executar operações CRUD em relação ao banco de dados:</span><span class="sxs-lookup"><span data-stu-id="3fffb-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="3fffb-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; lê a instância do servidor para executar operações de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3fffb-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="3fffb-215">O construtor dessa classe é fornecido na cadeia de conexão do MongoDB:</span><span class="sxs-lookup"><span data-stu-id="3fffb-215">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="3fffb-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; representa o banco de dados Mongo para execução de operações.</span><span class="sxs-lookup"><span data-stu-id="3fffb-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="3fffb-217">Este tutorial usa o método [GetCollection\<TDocument>(coleção)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) genérico na interface para obter acesso aos dados em uma coleção específica.</span><span class="sxs-lookup"><span data-stu-id="3fffb-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="3fffb-218">Execute operações CRUD em relação à coleção depois que esse método for chamado.</span><span class="sxs-lookup"><span data-stu-id="3fffb-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="3fffb-219">Na chamada de método `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="3fffb-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="3fffb-220">`collection` representa o nome da coleção.</span><span class="sxs-lookup"><span data-stu-id="3fffb-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="3fffb-221">`TDocument` representa o tipo de objeto CLR armazenado na coleção.</span><span class="sxs-lookup"><span data-stu-id="3fffb-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="3fffb-222">`GetCollection<TDocument>(collection)` retorna um objeto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) que representa a coleção.</span><span class="sxs-lookup"><span data-stu-id="3fffb-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="3fffb-223">Neste tutorial, os seguintes métodos são invocados na coleção:</span><span class="sxs-lookup"><span data-stu-id="3fffb-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="3fffb-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; exclui um único documento que correspondem aos critérios de pesquisa fornecidos.</span><span class="sxs-lookup"><span data-stu-id="3fffb-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="3fffb-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; retorna todos os documentos na coleção que correspondem aos critérios de pesquisa fornecidos.</span><span class="sxs-lookup"><span data-stu-id="3fffb-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="3fffb-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; insere o objeto fornecido como um novo documento na coleção.</span><span class="sxs-lookup"><span data-stu-id="3fffb-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="3fffb-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; substitui o único documento que corresponde aos critérios de pesquisa fornecidos com o objeto fornecido.</span><span class="sxs-lookup"><span data-stu-id="3fffb-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="3fffb-228">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="3fffb-228">Add a controller</span></span>

<span data-ttu-id="3fffb-229">Adicione uma classe `BooksController` ao diretório *Controladores* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3fffb-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="3fffb-230">O controlador da API Web anterior:</span><span class="sxs-lookup"><span data-stu-id="3fffb-230">The preceding web API controller:</span></span>

* <span data-ttu-id="3fffb-231">Usa a classe `BookService` para executar operações CRUD.</span><span class="sxs-lookup"><span data-stu-id="3fffb-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="3fffb-232">Contém métodos de ação para dar suporte a solicitações GET, POST, PUT e DELETE HTTP.</span><span class="sxs-lookup"><span data-stu-id="3fffb-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="3fffb-233">Chama o <xref:System.Web.Http.ApiController.CreatedAtRoute*> no método de ação `Create` para retornar uma resposta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="3fffb-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="3fffb-234">O código de status 201 é a resposta padrão para um método HTTP POST que cria um recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="3fffb-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="3fffb-235">`CreatedAtRoute` também adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="3fffb-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="3fffb-236">O cabeçalho `Location` especifica o URI do livro recém-criado.</span><span class="sxs-lookup"><span data-stu-id="3fffb-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="3fffb-237">Testar a API Web</span><span class="sxs-lookup"><span data-stu-id="3fffb-237">Test the web API</span></span>

1. <span data-ttu-id="3fffb-238">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3fffb-238">Build and run the app.</span></span>

1. <span data-ttu-id="3fffb-239">Navegue até `http://localhost:<port>/api/books` para testar o método de ação `Get` sem parâmetros do controlador.</span><span class="sxs-lookup"><span data-stu-id="3fffb-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="3fffb-240">A seguinte resposta JSON é exibida:</span><span class="sxs-lookup"><span data-stu-id="3fffb-240">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="3fffb-241">Navegue até `http://localhost:<port>/api/books/{id here}` para testar o método de ação `Get` sobrecarregado do controlador.</span><span class="sxs-lookup"><span data-stu-id="3fffb-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="3fffb-242">A seguinte resposta JSON é exibida:</span><span class="sxs-lookup"><span data-stu-id="3fffb-242">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="3fffb-243">Configurar opções de serialização JSON</span><span class="sxs-lookup"><span data-stu-id="3fffb-243">Configure JSON serialization options</span></span>

<span data-ttu-id="3fffb-244">Há dois detalhes alterar sobre as respostas JSON retornadas na seção [Testar a API Web](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="3fffb-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="3fffb-245">O uso de maiúsculas e minúsculas padrão dos nomes da propriedade deve ser alterado para corresponder ao uso de maiúsculas e minúsculas Pascal dos nomes de propriedade do objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="3fffb-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="3fffb-246">A propriedade `bookName` deve ser retornada como `Name`.</span><span class="sxs-lookup"><span data-stu-id="3fffb-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="3fffb-247">Para cumprir os requisitos anteriores, faça as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="3fffb-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="3fffb-248">O JSON.NET foi removido da estrutura compartilhada do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="3fffb-248">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="3fffb-249">Adicione uma referência de pacote a [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="3fffb-249">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="3fffb-250">Em `Startup.ConfigureServices`, encadeie o seguinte código realçado para a chamada de método `AddMvc`:</span><span class="sxs-lookup"><span data-stu-id="3fffb-250">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="3fffb-251">Com a alteração anterior, os nomes de propriedade na resposta JSON serializada da API Web correspondem aos respectivos nomes de propriedade no tipo de objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="3fffb-251">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="3fffb-252">Por exemplo, a propriedade `Author` da classe `Book` é serializada como `Author`.</span><span class="sxs-lookup"><span data-stu-id="3fffb-252">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="3fffb-253">Em *Models/book. cs*, anote a propriedade `BookName` com o seguinte atributo [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) :</span><span class="sxs-lookup"><span data-stu-id="3fffb-253">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="3fffb-254">O valor do atributo `[JsonProperty]` de `Name` representa o nome da propriedade da resposta JSON serializada da API Web.</span><span class="sxs-lookup"><span data-stu-id="3fffb-254">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="3fffb-255">Adicione o seguinte código à parte superior de *Models/Book.cs* para resolver a referência de atributo `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="3fffb-255">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="3fffb-256">Repita as etapas definidas na seção [Testar a API Web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="3fffb-256">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="3fffb-257">Observe a diferença em nomes de propriedade JSON.</span><span class="sxs-lookup"><span data-stu-id="3fffb-257">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3fffb-258">Este tutorial cria uma API Web que executa as operações CRUD (criar, ler, atualizar e excluir) em um banco de dados NoSQL do [MongoDB](https://www.mongodb.com/what-is-mongodb).</span><span class="sxs-lookup"><span data-stu-id="3fffb-258">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="3fffb-259">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="3fffb-259">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3fffb-260">Configurar o MongoDB</span><span class="sxs-lookup"><span data-stu-id="3fffb-260">Configure MongoDB</span></span>
> * <span data-ttu-id="3fffb-261">Criar um banco de dados do MongoDB</span><span class="sxs-lookup"><span data-stu-id="3fffb-261">Create a MongoDB database</span></span>
> * <span data-ttu-id="3fffb-262">Definir uma coleção e um esquema do MongoDB</span><span class="sxs-lookup"><span data-stu-id="3fffb-262">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="3fffb-263">Executar operações CRUD do MongoDB a partir de uma API Web</span><span class="sxs-lookup"><span data-stu-id="3fffb-263">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="3fffb-264">Personalizar a serialização JSON</span><span class="sxs-lookup"><span data-stu-id="3fffb-264">Customize JSON serialization</span></span>

<span data-ttu-id="3fffb-265">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3fffb-265">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3fffb-266">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="3fffb-266">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3fffb-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3fffb-267">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="3fffb-268">SDK do .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="3fffb-268">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="3fffb-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) com a carga de trabalho de **desenvolvimento da Web e do ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="3fffb-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="3fffb-270">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3fffb-270">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3fffb-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3fffb-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="3fffb-272">SDK do .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="3fffb-272">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="3fffb-273">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3fffb-273">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="3fffb-274">C# para Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3fffb-274">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="3fffb-275">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3fffb-275">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3fffb-276">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3fffb-276">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="3fffb-277">SDK do .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="3fffb-277">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="3fffb-278">Visual Studio para Mac versão 7.7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="3fffb-278">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="3fffb-279">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3fffb-279">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="3fffb-280">Configurar o MongoDB</span><span class="sxs-lookup"><span data-stu-id="3fffb-280">Configure MongoDB</span></span>

<span data-ttu-id="3fffb-281">Se usar o Windows, o MongoDB será instalado em *C:\\Arquivos de Programas\\MongoDB* por padrão.</span><span class="sxs-lookup"><span data-stu-id="3fffb-281">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="3fffb-282">Adicione *C:\\Arquivos de Programas\\MongoDB\\Servidor\\\<número_de_versão>\\bin* à variável de ambiente `Path`.</span><span class="sxs-lookup"><span data-stu-id="3fffb-282">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="3fffb-283">Essa alteração possibilita o acesso ao MongoDB a partir de qualquer lugar em seu computador de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="3fffb-283">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="3fffb-284">Use o Shell do mongo nas etapas a seguir para criar um banco de dados, fazer coleções e armazenar documentos.</span><span class="sxs-lookup"><span data-stu-id="3fffb-284">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="3fffb-285">Para saber mais sobre os comandos de Shell do mongo, consulte [Como trabalhar com o Shell do mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="3fffb-285">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="3fffb-286">Escolha um diretório no seu computador de desenvolvimento para armazenar os dados.</span><span class="sxs-lookup"><span data-stu-id="3fffb-286">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="3fffb-287">Por exemplo, *C:\\BooksData* no Windows.</span><span class="sxs-lookup"><span data-stu-id="3fffb-287">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="3fffb-288">Crie o diretório se não houver um.</span><span class="sxs-lookup"><span data-stu-id="3fffb-288">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="3fffb-289">O Shell do mongo não cria novos diretórios.</span><span class="sxs-lookup"><span data-stu-id="3fffb-289">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="3fffb-290">Abra um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="3fffb-290">Open a command shell.</span></span> <span data-ttu-id="3fffb-291">Execute o comando a seguir para se conectar ao MongoDB na porta padrão 27017.</span><span class="sxs-lookup"><span data-stu-id="3fffb-291">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="3fffb-292">Lembre-se de substituir `<data_directory_path>` pelo diretório escolhido na etapa anterior.</span><span class="sxs-lookup"><span data-stu-id="3fffb-292">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="3fffb-293">Abra outra instância do shell de comando.</span><span class="sxs-lookup"><span data-stu-id="3fffb-293">Open another command shell instance.</span></span> <span data-ttu-id="3fffb-294">Conecte-se ao banco de dados de testes padrão executando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3fffb-294">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="3fffb-295">Execute o seguinte em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3fffb-295">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="3fffb-296">Se ele ainda não existir, um banco de dados chamado *BookstoreDb* será criado.</span><span class="sxs-lookup"><span data-stu-id="3fffb-296">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="3fffb-297">Se o banco de dados existir, a conexão dele será aberta para transações.</span><span class="sxs-lookup"><span data-stu-id="3fffb-297">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="3fffb-298">Crie uma coleção `Books` usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3fffb-298">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="3fffb-299">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="3fffb-299">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="3fffb-300">Defina um esquema para a coleção `Books` e insira dois documentos usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3fffb-300">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="3fffb-301">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="3fffb-301">The following result is displayed:</span></span>

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
   > <span data-ttu-id="3fffb-302">As IDs mostradas neste artigo não corresponderão às IDs de quando você executar esse exemplo.</span><span class="sxs-lookup"><span data-stu-id="3fffb-302">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="3fffb-303">Visualize os documentos no banco de dados usando o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3fffb-303">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="3fffb-304">O seguinte resultado é exibido:</span><span class="sxs-lookup"><span data-stu-id="3fffb-304">The following result is displayed:</span></span>

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

   <span data-ttu-id="3fffb-305">O esquema adiciona uma propriedade `_id` gerada automaticamente do tipo `ObjectId` para cada documento.</span><span class="sxs-lookup"><span data-stu-id="3fffb-305">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="3fffb-306">O banco de dados está pronto.</span><span class="sxs-lookup"><span data-stu-id="3fffb-306">The database is ready.</span></span> <span data-ttu-id="3fffb-307">Você pode começar a criar a API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3fffb-307">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="3fffb-308">Criar o projeto da API Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fffb-308">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3fffb-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3fffb-309">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3fffb-310">Acesse **Arquivo** > **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-310">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="3fffb-311">Selecione o tipo de projeto **aplicativo Web ASP.NET Core** e, em seguida, **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-311">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="3fffb-312">Nomeie o projeto como *BooksApi* e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-312">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="3fffb-313">Selecione a estrutura de destino **.NET Core** e **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-313">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="3fffb-314">Selecione o modelo de projeto **API** e, em seguida, **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-314">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="3fffb-315">Visite a [Galeria do NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .net para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="3fffb-315">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="3fffb-316">Na janela **Console do Gerenciador de Pacotes**, navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="3fffb-316">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="3fffb-317">Execute o seguinte comando para instalar o driver .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="3fffb-317">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3fffb-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3fffb-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3fffb-319">Execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="3fffb-319">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="3fffb-320">Um novo projeto de API Web do ASP.NET Core direcionado ao .NET Core é gerado e aberto no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3fffb-320">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="3fffb-321">Depois que o ícone de chama de OmniSharp da barra de status fica verde, uma caixa de diálogo solicita **que os ativos necessários compilem e depurem estão faltando em ' BooksApi '. Adicioná-los?** .</span><span class="sxs-lookup"><span data-stu-id="3fffb-321">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="3fffb-322">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="3fffb-322">Select **Yes**.</span></span>
1. <span data-ttu-id="3fffb-323">Visite a [Galeria do NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) para determinar a versão estável mais recente do driver .net para MongoDB.</span><span class="sxs-lookup"><span data-stu-id="3fffb-323">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="3fffb-324">Abra **Terminal Integrado** e navegue até a raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="3fffb-324">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="3fffb-325">Execute o seguinte comando para instalar o driver .NET para MongoDB:</span><span class="sxs-lookup"><span data-stu-id="3fffb-325">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3fffb-326">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="3fffb-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3fffb-327">Acesse **Arquivo** > **Nova Solução** > **.NET Core** > **Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-327">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="3fffb-328">Selecione o modelo de projeto C# da **API Web do ASP.NET Core** e, em seguida, **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-328">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="3fffb-329">Selecione **.NET Core 2.2** na lista suspensa **Estrutura de Destino** e, em seguida, **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-329">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="3fffb-330">Insira *BooksApi* para o **Nome do Projeto** e selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-330">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="3fffb-331">No painel **Solução**, clique com o botão direito do mouse no nó **Dependências** do projeto e selecione **Adicionar Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-331">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="3fffb-332">Insira *MongoDB.Driver* na caixa de pesquisa, escolha o pacote *MongoDB.Driver* e selecione **Adicionar Pacote**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-332">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="3fffb-333">Selecione o botão **Aceitar** na caixa de diálogo **Aceitação da Licença**.</span><span class="sxs-lookup"><span data-stu-id="3fffb-333">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="3fffb-334">Adicionar um modelo de entidade</span><span class="sxs-lookup"><span data-stu-id="3fffb-334">Add an entity model</span></span>

1. <span data-ttu-id="3fffb-335">Adicione um diretório *Modelos* à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="3fffb-335">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="3fffb-336">Adicione uma classe `Book` ao diretório *Modelos* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3fffb-336">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="3fffb-337">Na classe anterior, a propriedade `Id`:</span><span class="sxs-lookup"><span data-stu-id="3fffb-337">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="3fffb-338">É necessária para mapear o objeto CLR (Common Language Runtime) para a coleção do MongoDB.</span><span class="sxs-lookup"><span data-stu-id="3fffb-338">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="3fffb-339">É anotada com [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) para designar essa propriedade como a chave primária do documento.</span><span class="sxs-lookup"><span data-stu-id="3fffb-339">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="3fffb-340">É anotado com [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) para permitir a passagem do parâmetro como o tipo `string` em vez de uma estrutura [ObjectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="3fffb-340">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="3fffb-341">O Mongo processa a conversão de `string` para `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="3fffb-341">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="3fffb-342">A propriedade `BookName` é anotada com o atributo [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) .</span><span class="sxs-lookup"><span data-stu-id="3fffb-342">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="3fffb-343">O valor do atributo de `Name` representa o nome da propriedade da coleção do MongoDB.</span><span class="sxs-lookup"><span data-stu-id="3fffb-343">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="3fffb-344">Adicionar um modelo de configuração</span><span class="sxs-lookup"><span data-stu-id="3fffb-344">Add a configuration model</span></span>

1. <span data-ttu-id="3fffb-345">Adicione os seguintes valores de configuração de banco de dados no *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3fffb-345">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="3fffb-346">Adicione um arquivo *BookstoreDatabaseSettings.cs* no diretório *Modelos* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="3fffb-346">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="3fffb-347">A classe precedente `BookstoreDatabaseSettings` é usada para armazenar os valores de propriedade `BookstoreDatabaseSettings` do arquivo *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3fffb-347">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="3fffb-348">Os nomes de propriedade JSON e C# são nomeados de forma idêntica para facilitar o processo de mapeamento.</span><span class="sxs-lookup"><span data-stu-id="3fffb-348">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="3fffb-349">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3fffb-349">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="3fffb-350">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="3fffb-350">In the preceding code:</span></span>

   * <span data-ttu-id="3fffb-351">A instância de configuração à qual a seção `BookstoreDatabaseSettings` do arquivo *appsettings.json* é associada é registrada no contêiner de DI (Injeção de Dependência).</span><span class="sxs-lookup"><span data-stu-id="3fffb-351">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="3fffb-352">Por exemplo, a propriedade `ConnectionString` de um objeto `BookstoreDatabaseSettings` é populada com a propriedade `BookstoreDatabaseSettings:ConnectionString` no *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3fffb-352">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="3fffb-353">A interface `IBookstoreDatabaseSettings` é registrada na DI com um [tempo de vida do serviço](xref:fundamentals/dependency-injection#service-lifetimes) singleton.</span><span class="sxs-lookup"><span data-stu-id="3fffb-353">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="3fffb-354">Quando inserida, a instância da interface é resolvida para um objeto `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="3fffb-354">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="3fffb-355">Adicione o seguinte código na parte superior do *Startup.cs* para resolver as referências `BookstoreDatabaseSettings` e `IBookstoreDatabaseSettings`:</span><span class="sxs-lookup"><span data-stu-id="3fffb-355">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="3fffb-356">Adicionar um serviço de operações CRUD</span><span class="sxs-lookup"><span data-stu-id="3fffb-356">Add a CRUD operations service</span></span>

1. <span data-ttu-id="3fffb-357">Adicione um diretório *Serviços* à raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="3fffb-357">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="3fffb-358">Adicione uma classe `BookService` ao diretório *Serviços* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3fffb-358">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="3fffb-359">No código anterior, uma instância `IBookstoreDatabaseSettings` é recuperada da DI por meio da injeção de construtor.</span><span class="sxs-lookup"><span data-stu-id="3fffb-359">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="3fffb-360">Essa técnica fornece acesso para os valores de configuração do *appsettings.json* que foram adicionados na seção [Adicionar um modelo de configuração](#add-a-configuration-model).</span><span class="sxs-lookup"><span data-stu-id="3fffb-360">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="3fffb-361">Adicione o código realçado a seguir a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3fffb-361">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="3fffb-362">No código anterior, a classe `BookService` é registrada com a DI para dar suporte à injeção de construtor nas classes consumidoras.</span><span class="sxs-lookup"><span data-stu-id="3fffb-362">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="3fffb-363">O tempo de vida do serviço singleton é mais apropriado porque `BookService` usa uma dependência direta de `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="3fffb-363">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="3fffb-364">De acordo com as [Diretrizes oficiais de reutilização do cliente Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), o `MongoClient` deve ser registrado na DI com um tempo de vida do serviço singleton.</span><span class="sxs-lookup"><span data-stu-id="3fffb-364">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="3fffb-365">Adicione o seguinte código na parte superior do *Startup.cs* para resolver a referências `BookService`:</span><span class="sxs-lookup"><span data-stu-id="3fffb-365">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="3fffb-366">A classe `BookService` usa os seguintes membros `MongoDB.Driver` para executar operações CRUD em relação ao banco de dados:</span><span class="sxs-lookup"><span data-stu-id="3fffb-366">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="3fffb-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; lê a instância do servidor para executar operações de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3fffb-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="3fffb-368">O construtor dessa classe é fornecido na cadeia de conexão do MongoDB:</span><span class="sxs-lookup"><span data-stu-id="3fffb-368">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="3fffb-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; representa o banco de dados Mongo para execução de operações.</span><span class="sxs-lookup"><span data-stu-id="3fffb-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="3fffb-370">Este tutorial usa o método [GetCollection\<TDocument>(coleção)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) genérico na interface para obter acesso aos dados em uma coleção específica.</span><span class="sxs-lookup"><span data-stu-id="3fffb-370">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="3fffb-371">Execute operações CRUD em relação à coleção depois que esse método for chamado.</span><span class="sxs-lookup"><span data-stu-id="3fffb-371">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="3fffb-372">Na chamada de método `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="3fffb-372">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="3fffb-373">`collection` representa o nome da coleção.</span><span class="sxs-lookup"><span data-stu-id="3fffb-373">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="3fffb-374">`TDocument` representa o tipo de objeto CLR armazenado na coleção.</span><span class="sxs-lookup"><span data-stu-id="3fffb-374">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="3fffb-375">`GetCollection<TDocument>(collection)` retorna um objeto [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) que representa a coleção.</span><span class="sxs-lookup"><span data-stu-id="3fffb-375">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="3fffb-376">Neste tutorial, os seguintes métodos são invocados na coleção:</span><span class="sxs-lookup"><span data-stu-id="3fffb-376">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="3fffb-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; exclui um único documento que correspondem aos critérios de pesquisa fornecidos.</span><span class="sxs-lookup"><span data-stu-id="3fffb-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="3fffb-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; retorna todos os documentos na coleção que correspondem aos critérios de pesquisa fornecidos.</span><span class="sxs-lookup"><span data-stu-id="3fffb-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="3fffb-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; insere o objeto fornecido como um novo documento na coleção.</span><span class="sxs-lookup"><span data-stu-id="3fffb-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="3fffb-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; substitui o único documento que corresponde aos critérios de pesquisa fornecidos com o objeto fornecido.</span><span class="sxs-lookup"><span data-stu-id="3fffb-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="3fffb-381">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="3fffb-381">Add a controller</span></span>

<span data-ttu-id="3fffb-382">Adicione uma classe `BooksController` ao diretório *Controladores* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3fffb-382">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="3fffb-383">O controlador da API Web anterior:</span><span class="sxs-lookup"><span data-stu-id="3fffb-383">The preceding web API controller:</span></span>

* <span data-ttu-id="3fffb-384">Usa a classe `BookService` para executar operações CRUD.</span><span class="sxs-lookup"><span data-stu-id="3fffb-384">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="3fffb-385">Contém métodos de ação para dar suporte a solicitações GET, POST, PUT e DELETE HTTP.</span><span class="sxs-lookup"><span data-stu-id="3fffb-385">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="3fffb-386">Chama o <xref:System.Web.Http.ApiController.CreatedAtRoute*> no método de ação `Create` para retornar uma resposta [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="3fffb-386">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="3fffb-387">O código de status 201 é a resposta padrão para um método HTTP POST que cria um recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="3fffb-387">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="3fffb-388">`CreatedAtRoute` também adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="3fffb-388">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="3fffb-389">O cabeçalho `Location` especifica o URI do livro recém-criado.</span><span class="sxs-lookup"><span data-stu-id="3fffb-389">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="3fffb-390">Testar a API Web</span><span class="sxs-lookup"><span data-stu-id="3fffb-390">Test the web API</span></span>

1. <span data-ttu-id="3fffb-391">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3fffb-391">Build and run the app.</span></span>

1. <span data-ttu-id="3fffb-392">Navegue até `http://localhost:<port>/api/books` para testar o método de ação `Get` sem parâmetros do controlador.</span><span class="sxs-lookup"><span data-stu-id="3fffb-392">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="3fffb-393">A seguinte resposta JSON é exibida:</span><span class="sxs-lookup"><span data-stu-id="3fffb-393">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="3fffb-394">Navegue até `http://localhost:<port>/api/books/{id here}` para testar o método de ação `Get` sobrecarregado do controlador.</span><span class="sxs-lookup"><span data-stu-id="3fffb-394">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="3fffb-395">A seguinte resposta JSON é exibida:</span><span class="sxs-lookup"><span data-stu-id="3fffb-395">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="3fffb-396">Configurar opções de serialização JSON</span><span class="sxs-lookup"><span data-stu-id="3fffb-396">Configure JSON serialization options</span></span>

<span data-ttu-id="3fffb-397">Há dois detalhes alterar sobre as respostas JSON retornadas na seção [Testar a API Web](#test-the-web-api):</span><span class="sxs-lookup"><span data-stu-id="3fffb-397">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="3fffb-398">O uso de maiúsculas e minúsculas padrão dos nomes da propriedade deve ser alterado para corresponder ao uso de maiúsculas e minúsculas Pascal dos nomes de propriedade do objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="3fffb-398">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="3fffb-399">A propriedade `bookName` deve ser retornada como `Name`.</span><span class="sxs-lookup"><span data-stu-id="3fffb-399">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="3fffb-400">Para cumprir os requisitos anteriores, faça as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="3fffb-400">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="3fffb-401">Em `Startup.ConfigureServices`, encadeie o seguinte código realçado para a chamada de método `AddMvc`:</span><span class="sxs-lookup"><span data-stu-id="3fffb-401">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="3fffb-402">Com a alteração anterior, os nomes de propriedade na resposta JSON serializada da API Web correspondem aos respectivos nomes de propriedade no tipo de objeto CLR.</span><span class="sxs-lookup"><span data-stu-id="3fffb-402">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="3fffb-403">Por exemplo, a propriedade `Author` da classe `Book` é serializada como `Author`.</span><span class="sxs-lookup"><span data-stu-id="3fffb-403">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="3fffb-404">Em *Models/book. cs*, anote a propriedade `BookName` com o seguinte atributo [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) :</span><span class="sxs-lookup"><span data-stu-id="3fffb-404">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="3fffb-405">O valor do atributo `[JsonProperty]` de `Name` representa o nome da propriedade da resposta JSON serializada da API Web.</span><span class="sxs-lookup"><span data-stu-id="3fffb-405">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="3fffb-406">Adicione o seguinte código à parte superior de *Models/Book.cs* para resolver a referência de atributo `[JsonProperty]`:</span><span class="sxs-lookup"><span data-stu-id="3fffb-406">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="3fffb-407">Repita as etapas definidas na seção [Testar a API Web](#test-the-web-api).</span><span class="sxs-lookup"><span data-stu-id="3fffb-407">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="3fffb-408">Observe a diferença em nomes de propriedade JSON.</span><span class="sxs-lookup"><span data-stu-id="3fffb-408">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="3fffb-409">Adicionar suporte de autenticação a uma API da Web</span><span class="sxs-lookup"><span data-stu-id="3fffb-409">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="3fffb-410">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="3fffb-410">Next steps</span></span>

<span data-ttu-id="3fffb-411">Para saber mais sobre a criação de APIs Web do ASP.NET Core, confira os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="3fffb-411">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="3fffb-412">Versão deste artigo no YouTube</span><span class="sxs-lookup"><span data-stu-id="3fffb-412">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
