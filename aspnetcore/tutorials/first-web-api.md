---
title: 'Tutorial: Criar uma API Web com o ASP.NET Core MVC'
author: rick-anderson
description: Criar uma API Web com o ASP.NET Core MVC
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2018
uid: tutorials/first-web-api
ms.openlocfilehash: c2b4dcddd5332330cd6e6abe7d3a12697cde845e
ms.sourcegitcommit: 4e87712029de2aceb1cf2c52e9e3dda8195a5b8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53381998"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core-mvc"></a><span data-ttu-id="27154-103">Tutorial: Criar uma API Web com o ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="27154-103">Tutorial: Create a web API with ASP.NET Core MVC</span></span>

<span data-ttu-id="27154-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="27154-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="27154-105">Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="27154-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

<span data-ttu-id="27154-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="27154-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="27154-107">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="27154-107">Create a web API project.</span></span>
> * <span data-ttu-id="27154-108">Adicionar uma classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="27154-108">Add a model class.</span></span>
> * <span data-ttu-id="27154-109">Criar o contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="27154-109">Create the database context.</span></span>
> * <span data-ttu-id="27154-110">Registrar o contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="27154-110">Register the database context.</span></span>
> * <span data-ttu-id="27154-111">Adicionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="27154-111">Add a controller.</span></span>
> * <span data-ttu-id="27154-112">Adicionar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="27154-112">Add CRUD methods.</span></span>
> * <span data-ttu-id="27154-113">Configurar o roteamento e caminhos de URL.</span><span class="sxs-lookup"><span data-stu-id="27154-113">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="27154-114">Especificar os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="27154-114">Specify return values.</span></span>
> * <span data-ttu-id="27154-115">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="27154-115">Call the web API with Postman.</span></span>
> * <span data-ttu-id="27154-116">Chamar a API Web com o jQuery.</span><span class="sxs-lookup"><span data-stu-id="27154-116">Call the web API with jQuery.</span></span>

<span data-ttu-id="27154-117">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="27154-117">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="27154-118">Visão geral</span><span class="sxs-lookup"><span data-stu-id="27154-118">Overview</span></span>

<span data-ttu-id="27154-119">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="27154-119">This tutorial creates the following API:</span></span>

|<span data-ttu-id="27154-120">API</span><span class="sxs-lookup"><span data-stu-id="27154-120">API</span></span> | <span data-ttu-id="27154-121">Descrição</span><span class="sxs-lookup"><span data-stu-id="27154-121">Description</span></span> | <span data-ttu-id="27154-122">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="27154-122">Request body</span></span> | <span data-ttu-id="27154-123">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="27154-123">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="27154-124">GET /api/todo</span><span class="sxs-lookup"><span data-stu-id="27154-124">GET /api/todo</span></span> | <span data-ttu-id="27154-125">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="27154-125">Get all to-do items</span></span> | <span data-ttu-id="27154-126">Nenhum</span><span class="sxs-lookup"><span data-stu-id="27154-126">None</span></span> | <span data-ttu-id="27154-127">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="27154-127">Array of to-do items</span></span>|
|<span data-ttu-id="27154-128">GET /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="27154-128">GET /api/todo/{id}</span></span> | <span data-ttu-id="27154-129">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="27154-129">Get an item by ID</span></span> | <span data-ttu-id="27154-130">Nenhum</span><span class="sxs-lookup"><span data-stu-id="27154-130">None</span></span> | <span data-ttu-id="27154-131">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="27154-131">To-do item</span></span>|
|<span data-ttu-id="27154-132">POST /api/todo</span><span class="sxs-lookup"><span data-stu-id="27154-132">POST /api/todo</span></span> | <span data-ttu-id="27154-133">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="27154-133">Add a new item</span></span> | <span data-ttu-id="27154-134">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="27154-134">To-do item</span></span> | <span data-ttu-id="27154-135">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="27154-135">To-do item</span></span> |
|<span data-ttu-id="27154-136">PUT /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="27154-136">PUT /api/todo/{id}</span></span> | <span data-ttu-id="27154-137">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="27154-137">Update an existing item &nbsp;</span></span> | <span data-ttu-id="27154-138">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="27154-138">To-do item</span></span> | <span data-ttu-id="27154-139">Nenhum</span><span class="sxs-lookup"><span data-stu-id="27154-139">None</span></span> |
|<span data-ttu-id="27154-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="27154-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="27154-141">Excluir um item &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="27154-141">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="27154-142">Nenhum</span><span class="sxs-lookup"><span data-stu-id="27154-142">None</span></span> | <span data-ttu-id="27154-143">Nenhum</span><span class="sxs-lookup"><span data-stu-id="27154-143">None</span></span>|

<span data-ttu-id="27154-144">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="27154-144">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda e envia uma solicitação e recebe uma resposta do aplicativo, uma caixa desenhada à direita.](first-web-api/_static/architecture.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-project"></a><span data-ttu-id="27154-149">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="27154-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="27154-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27154-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="27154-151">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="27154-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="27154-152">Selecione o modelo **Aplicativo Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="27154-152">Select the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="27154-153">Nomeie o projeto como *TodoApi* e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="27154-153">Name the project *TodoApi* and click **OK**.</span></span>
* <span data-ttu-id="27154-154">Na caixa de diálogo **Novo aplicativo Web ASP.NET Core – TodoApi** e escolha a versão do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="27154-154">In the **New ASP.NET Core Web Application - TodoApi** dialog, choose the ASP.NET Core version.</span></span> <span data-ttu-id="27154-155">Selecione o modelo **API** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="27154-155">Select the **API** template and click **OK**.</span></span> <span data-ttu-id="27154-156">**Não** selecione **Habilitar Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="27154-156">Do **not** select **Enable Docker Support**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="27154-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="27154-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="27154-159">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="27154-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="27154-160">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="27154-160">Change directories (`cd`) to the folder which will contain the project folder.</span></span>
* <span data-ttu-id="27154-161">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="27154-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="27154-162">Esses comandos criam um novo projeto de API Web e abrem uma nova instância do Visual Studio Code na nova pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="27154-162">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="27154-163">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="27154-163">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="27154-164">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="27154-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="27154-165">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="27154-165">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="27154-167">Selecione **Aplicativo .NET Core** > **API Web ASP.NET Core** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="27154-167">Select **.NET Core App** > **ASP.NET Core Web API** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="27154-169">Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, aceite a **Estrutura de Destino** padrão \**.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="27154-169">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="27154-170">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="27154-170">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="27154-172">Testar a API</span><span class="sxs-lookup"><span data-stu-id="27154-172">Test the API</span></span>

<span data-ttu-id="27154-173">O modelo de projeto cria uma API `values`.</span><span class="sxs-lookup"><span data-stu-id="27154-173">The project template creates a `values` API.</span></span> <span data-ttu-id="27154-174">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="27154-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="27154-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27154-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="27154-176">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="27154-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="27154-177">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/api/values`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="27154-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="27154-178">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="27154-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="27154-179">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="27154-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="27154-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="27154-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="27154-181">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="27154-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="27154-182">Em um navegador, acesse a seguinte URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="27154-182">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="27154-183">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="27154-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="27154-184">Selecione **Executar** > **Iniciar com Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="27154-184">Select **Run** > **Start With Debugging** to launch the app.</span></span> <span data-ttu-id="27154-185">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="27154-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="27154-186">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="27154-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="27154-187">Acrescente `/api/values` à URL (altere a URL para `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="27154-187">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="27154-188">O seguinte JSON é retornado:</span><span class="sxs-lookup"><span data-stu-id="27154-188">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="27154-189">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="27154-189">Add a model class</span></span>

<span data-ttu-id="27154-190">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="27154-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="27154-191">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="27154-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="27154-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27154-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="27154-193">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="27154-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="27154-194">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="27154-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="27154-195">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="27154-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="27154-196">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="27154-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="27154-197">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="27154-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="27154-198">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="27154-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="27154-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="27154-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="27154-200">Adicione uma pasta chamada *Models*.</span><span class="sxs-lookup"><span data-stu-id="27154-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="27154-201">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="27154-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="27154-202">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="27154-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="27154-203">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="27154-203">Right-click the project.</span></span> <span data-ttu-id="27154-204">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="27154-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="27154-205">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="27154-205">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="27154-207">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="27154-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="27154-208">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="27154-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="27154-209">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="27154-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="27154-210">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="27154-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="27154-211">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="27154-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="27154-212">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="27154-212">Add a database context</span></span>

<span data-ttu-id="27154-213">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="27154-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="27154-214">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="27154-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="27154-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27154-215">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="27154-216">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="27154-216">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="27154-217">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="27154-217">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="27154-218">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="27154-218">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="27154-219">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="27154-219">Add a `TodoContext` class to the *Models* folder.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="27154-220">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="27154-220">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="27154-221">Adicione uma classe `TodoContext` à pasta *Models*:</span><span class="sxs-lookup"><span data-stu-id="27154-221">Add a `TodoContext` class in the *Models* folder:</span></span>

---

* <span data-ttu-id="27154-222">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="27154-222">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="27154-223">Registrar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="27154-223">Register the database context</span></span>

<span data-ttu-id="27154-224">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="27154-224">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="27154-225">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="27154-225">The container provides the service to controllers.</span></span>

<span data-ttu-id="27154-226">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="27154-226">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="27154-227">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="27154-227">The preceding code:</span></span>

* <span data-ttu-id="27154-228">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="27154-228">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="27154-229">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="27154-229">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="27154-230">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="27154-230">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="27154-231">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="27154-231">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="27154-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="27154-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="27154-233">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="27154-233">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="27154-234">Selecione **Adicionar** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="27154-234">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="27154-235">Na caixa de diálogo **Adicionar Novo Item**, selecione o modelo **Classe do Controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="27154-235">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="27154-236">Dê à classe o nome *TodoController* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="27154-236">Name the class *TodoController*, and select **Add**.</span></span>

  ![Caixa de diálogo Adicionar Novo Item com o controlador na caixa de pesquisa e o controlador da API Web selecionados](first-web-api/_static/new_controller.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="27154-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="27154-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="27154-239">Na pasta *Controllers*, crie uma classe chamada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="27154-239">In the *Controllers* folder, create a class named `TodoController`.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="27154-240">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="27154-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="27154-241">Na pasta *Controllers*, adicione a classe `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="27154-241">In the *Controllers* folder, add the class `TodoController`.</span></span>

---

* <span data-ttu-id="27154-242">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="27154-242">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="27154-243">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="27154-243">The preceding code:</span></span>

* <span data-ttu-id="27154-244">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="27154-244">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="27154-245">Decore a classe com o atributo [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="27154-245">Decorates the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="27154-246">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="27154-246">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="27154-247">Para obter informações sobre comportamentos específicos habilitados pelo atributo, confira [Anotação com o atributo ApiController](xref:web-api/index#annotation-with-apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="27154-247">For information about specific behaviors that the attribute enables, see [Annotation with ApiController attribute](xref:web-api/index#annotation-with-apicontroller-attribute).</span></span>
* <span data-ttu-id="27154-248">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="27154-248">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="27154-249">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="27154-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="27154-250">Adiciona um item chamado `Item1` ao banco de dados se o banco de dados está vazio.</span><span class="sxs-lookup"><span data-stu-id="27154-250">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="27154-251">Esse código está no construtor, de modo que ele seja executado sempre que há uma nova solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="27154-251">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="27154-252">Se você excluir todos os itens, o construtor criará `Item1` novamente na próxima vez que um método de API for chamado.</span><span class="sxs-lookup"><span data-stu-id="27154-252">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="27154-253">Portanto, pode parecer que a exclusão não funcionou quando ela realmente funcionou.</span><span class="sxs-lookup"><span data-stu-id="27154-253">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="27154-254">Adicionar métodos Get</span><span class="sxs-lookup"><span data-stu-id="27154-254">Add Get methods</span></span>

<span data-ttu-id="27154-255">Para fornecer uma API que recupera itens pendentes, adicione os seguintes métodos à classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="27154-255">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="27154-256">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="27154-256">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="27154-257">Teste o aplicativo chamando os dois pontos de extremidade em um navegador.</span><span class="sxs-lookup"><span data-stu-id="27154-257">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="27154-258">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="27154-258">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="27154-259">A seguinte resposta HTTP é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="27154-259">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="27154-260">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="27154-260">Routing and URL paths</span></span>

<span data-ttu-id="27154-261">O atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica um método que responde a uma solicitação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="27154-261">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="27154-262">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="27154-262">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="27154-263">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="27154-263">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="27154-264">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="27154-264">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="27154-265">Para esta amostra, o nome da classe do controlador é **Todo**Controller e, portanto, o nome do controlador é "todo".</span><span class="sxs-lookup"><span data-stu-id="27154-265">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="27154-266">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="27154-266">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="27154-267">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("/products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="27154-267">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("/products")]`, append that to the path.</span></span> <span data-ttu-id="27154-268">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="27154-268">This sample doesn't use a template.</span></span> <span data-ttu-id="27154-269">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="27154-269">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="27154-270">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="27154-270">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="27154-271">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="27154-271">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

<span data-ttu-id="27154-272">O parâmetro `Name = "GetTodo"` cria uma rota nomeada.</span><span class="sxs-lookup"><span data-stu-id="27154-272">The `Name = "GetTodo"` parameter creates a named route.</span></span> <span data-ttu-id="27154-273">Você verá posteriormente como o aplicativo pode usar o nome para criar um link HTTP usando o nome da rota.</span><span class="sxs-lookup"><span data-stu-id="27154-273">You'll see later how the app can use the name to create an HTTP link using the route name.</span></span>

## <a name="return-values"></a><span data-ttu-id="27154-274">Valores de retorno</span><span class="sxs-lookup"><span data-stu-id="27154-274">Return values</span></span>

<span data-ttu-id="27154-275">O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="27154-275">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="27154-276">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="27154-276">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="27154-277">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="27154-277">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="27154-278">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="27154-278">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="27154-279">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="27154-279">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="27154-280">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="27154-280">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="27154-281">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="27154-281">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="27154-282">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="27154-282">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="27154-283">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="27154-283">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="27154-284">Testar o método GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="27154-284">Test the GetTodoItems method</span></span>

<span data-ttu-id="27154-285">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="27154-285">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="27154-286">Instale o [Postman](https://www.getpostman.com/apps)</span><span class="sxs-lookup"><span data-stu-id="27154-286">Install [Postman](https://www.getpostman.com/apps)</span></span>
* <span data-ttu-id="27154-287">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="27154-287">Start the web app.</span></span>
* <span data-ttu-id="27154-288">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="27154-288">Start Postman.</span></span>
* <span data-ttu-id="27154-289">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="27154-289">Disable **SSL certificate verification**</span></span>
  
  * <span data-ttu-id="27154-290">Em **Arquivo > Configurações** (guia \**Geral*), desabilite **Verificação do certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="27154-290">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="27154-291">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="27154-291">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="27154-292">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="27154-292">Create a new request.</span></span>
  * <span data-ttu-id="27154-293">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="27154-293">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="27154-294">Defina a URL de solicitação como `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="27154-294">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="27154-295">Por exemplo, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="27154-295">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="27154-296">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="27154-296">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="27154-297">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="27154-297">Select **Send**.</span></span>

![Postman com solicitação GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="27154-299">Adicionar um método Create</span><span class="sxs-lookup"><span data-stu-id="27154-299">Add a Create method</span></span>

<span data-ttu-id="27154-300">Adicione o seguinte método `PostTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="27154-300">Add the following `PostTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="27154-301">O código anterior é um método HTTP POST, conforme indicado pelo atributo [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="27154-301">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="27154-302">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="27154-302">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="27154-303">O método `CreatedAtRoute`:</span><span class="sxs-lookup"><span data-stu-id="27154-303">The `CreatedAtRoute` method:</span></span>

* <span data-ttu-id="27154-304">Retorna uma resposta 201.</span><span class="sxs-lookup"><span data-stu-id="27154-304">Returns a 201 response.</span></span> <span data-ttu-id="27154-305">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="27154-305">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="27154-306">Adiciona um cabeçalho Local à resposta.</span><span class="sxs-lookup"><span data-stu-id="27154-306">Adds a Location header to the response.</span></span> <span data-ttu-id="27154-307">O cabeçalho Location especifica o URI do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="27154-307">The Location header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="27154-308">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="27154-308">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="27154-309">Usa a rota chamada "GetTodo" para criar a URL.</span><span class="sxs-lookup"><span data-stu-id="27154-309">Uses the "GetTodo" named route to create the URL.</span></span> <span data-ttu-id="27154-310">A rota chamada "GetTodo" é definida em `GetTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="27154-310">The "GetTodo" named route is defined in `GetTodoItem`:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="27154-311">Testar o método PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="27154-311">Test the PostTodoItem method</span></span>

* <span data-ttu-id="27154-312">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="27154-312">Build the project.</span></span>
* <span data-ttu-id="27154-313">No Postman, defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="27154-313">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="27154-314">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="27154-314">Select the **Body** tab.</span></span>
* <span data-ttu-id="27154-315">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="27154-315">Select the **raw** radio button.</span></span>
* <span data-ttu-id="27154-316">Defina o tipo como **JSON (aplicativo/json)**.</span><span class="sxs-lookup"><span data-stu-id="27154-316">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="27154-317">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="27154-317">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="27154-318">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="27154-318">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

  <span data-ttu-id="27154-320">Se você receber um erro 405 Método Não Permitido, esse, provavelmente, será o resultado da não compilação do projeto após a adição do método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="27154-320">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="27154-321">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="27154-321">Test the location header URI</span></span>

* <span data-ttu-id="27154-322">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="27154-322">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="27154-323">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="27154-323">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="27154-325">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="27154-325">Set the method to GET.</span></span>
* <span data-ttu-id="27154-326">Cole o URI (por exemplo, `https://localhost:5001/api/Todo/2`)</span><span class="sxs-lookup"><span data-stu-id="27154-326">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="27154-327">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="27154-327">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="27154-328">Adicionar um método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="27154-328">Add a PutTodoItem method</span></span>

<span data-ttu-id="27154-329">Adicione o seguinte método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="27154-329">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="27154-330">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="27154-330">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="27154-331">A resposta é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="27154-331">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="27154-332">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="27154-332">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="27154-333">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="27154-333">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="27154-334">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="27154-334">Test the PutTodoItem method</span></span>

<span data-ttu-id="27154-335">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="27154-335">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="27154-336">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="27154-336">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="27154-338">Adicionar um método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="27154-338">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="27154-339">Adicione o seguinte método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="27154-339">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="27154-340">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="27154-340">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="27154-341">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="27154-341">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="27154-342">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="27154-342">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="27154-343">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="27154-343">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="27154-344">Defina o URI do objeto a ser excluído, por exemplo, `https://localhost:5001/api/todo/1`</span><span class="sxs-lookup"><span data-stu-id="27154-344">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="27154-345">Selecione **Enviar**</span><span class="sxs-lookup"><span data-stu-id="27154-345">Select **Send**</span></span>

<span data-ttu-id="27154-346">O aplicativo de exemplo permite que você exclua todos os itens, mas quando o último item é excluído, um novo é criado pelo construtor de classe de modelo na próxima vez que a API é chamada.</span><span class="sxs-lookup"><span data-stu-id="27154-346">The sample app allows you to delete all the items, but when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="27154-347">Chamar a API com o jQuery</span><span class="sxs-lookup"><span data-stu-id="27154-347">Call the API with jQuery</span></span>

<span data-ttu-id="27154-348">Nesta seção, uma página HTML que usa o jQuery para chamar a API Web é adicionada.</span><span class="sxs-lookup"><span data-stu-id="27154-348">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="27154-349">O jQuery inicia a solicitação e atualiza a página com os detalhes da resposta da API.</span><span class="sxs-lookup"><span data-stu-id="27154-349">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="27154-350">Configure o aplicativo para [fornecer arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_):</span><span class="sxs-lookup"><span data-stu-id="27154-350">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_):</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

::: moniker range=">= aspnetcore-2.2"
<span data-ttu-id="27154-351">Crie uma pasta *wwwroot* no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="27154-351">Create a *wwwroot* folder in the project directory.</span></span>
::: moniker-end

<span data-ttu-id="27154-352">Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="27154-352">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="27154-353">Substitua seu conteúdo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="27154-353">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="27154-354">Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="27154-354">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="27154-355">Substitua seu conteúdo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="27154-355">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="27154-356">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="27154-356">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="27154-357">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="27154-357">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="27154-358">Remova a propriedade `launchUrl` para forçar o aplicativo a ser aberto em *index.html*, o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="27154-358">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="27154-359">Há várias maneiras de obter o jQuery.</span><span class="sxs-lookup"><span data-stu-id="27154-359">There are several ways to get jQuery.</span></span> <span data-ttu-id="27154-360">No snippet anterior, a biblioteca é carregada de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="27154-360">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="27154-361">Esta amostra chama todos os métodos CRUD da API.</span><span class="sxs-lookup"><span data-stu-id="27154-361">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="27154-362">Veja a seguir explicações das chamadas à API.</span><span class="sxs-lookup"><span data-stu-id="27154-362">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="27154-363">Obter uma lista de itens pendentes</span><span class="sxs-lookup"><span data-stu-id="27154-363">Get a list of to-do items</span></span>

<span data-ttu-id="27154-364">A função [ajax](https://api.jquery.com/jquery.ajax/) do jQuery envia uma solicitação `GET` para a API, que retorna o JSON que representa uma matriz de itens pendentes.</span><span class="sxs-lookup"><span data-stu-id="27154-364">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="27154-365">A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="27154-365">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="27154-366">No retorno de chamada, o DOM é atualizado com as informações do item pendente.</span><span class="sxs-lookup"><span data-stu-id="27154-366">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="27154-367">Adicionar um item pendente</span><span class="sxs-lookup"><span data-stu-id="27154-367">Add a to-do item</span></span>

<span data-ttu-id="27154-368">A função [ajax](https://api.jquery.com/jquery.ajax/) envia uma solicitação `POST` com o item pendente no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="27154-368">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="27154-369">As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado.</span><span class="sxs-lookup"><span data-stu-id="27154-369">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="27154-370">O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="27154-370">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="27154-371">Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="27154-371">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="27154-372">Atualizar um item pendente</span><span class="sxs-lookup"><span data-stu-id="27154-372">Update a to-do item</span></span>

<span data-ttu-id="27154-373">A atualização de um item pendente é semelhante à adição de um.</span><span class="sxs-lookup"><span data-stu-id="27154-373">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="27154-374">A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.</span><span class="sxs-lookup"><span data-stu-id="27154-374">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="27154-375">Excluir um item pendente</span><span class="sxs-lookup"><span data-stu-id="27154-375">Delete a to-do item</span></span>

<span data-ttu-id="27154-376">A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="27154-376">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

## <a name="additional-resources"></a><span data-ttu-id="27154-377">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="27154-377">Additional resources</span></span>

<span data-ttu-id="27154-378">[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="27154-378">[View or download sample code for this tutorial](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="27154-379">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="27154-379">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="27154-380">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="27154-380">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>

## <a name="next-steps"></a><span data-ttu-id="27154-381">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="27154-381">Next steps</span></span>

<span data-ttu-id="27154-382">Neste tutorial, você aprendeu como:</span><span class="sxs-lookup"><span data-stu-id="27154-382">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="27154-383">Criar um projeto de aplicativo API Web.</span><span class="sxs-lookup"><span data-stu-id="27154-383">Create a web api project.</span></span>
> * <span data-ttu-id="27154-384">Adicionar uma classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="27154-384">Add a model class.</span></span>
> * <span data-ttu-id="27154-385">Criar o contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="27154-385">Create the database context.</span></span>
> * <span data-ttu-id="27154-386">Registrar o contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="27154-386">Register the database context.</span></span>
> * <span data-ttu-id="27154-387">Adicionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="27154-387">Add a controller.</span></span>
> * <span data-ttu-id="27154-388">Adicionar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="27154-388">Add CRUD methods.</span></span>
> * <span data-ttu-id="27154-389">Configurar o roteamento e caminhos de URL.</span><span class="sxs-lookup"><span data-stu-id="27154-389">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="27154-390">Especificar os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="27154-390">Specify return values.</span></span>
> * <span data-ttu-id="27154-391">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="27154-391">Call the web API with Postman.</span></span>
> * <span data-ttu-id="27154-392">Chamar a API Web com o jQuery.</span><span class="sxs-lookup"><span data-stu-id="27154-392">Call the web api with jQuery.</span></span>

<span data-ttu-id="27154-393">Avance para o próximo tutorial para saber como gerar páginas de ajuda da API:</span><span class="sxs-lookup"><span data-stu-id="27154-393">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
