---
title: 'Tutorial: Criar uma API Web com o ASP.NET Core'
author: rick-anderson
description: Saiba como criar uma API Web com o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2019
uid: tutorials/first-web-api
ms.openlocfilehash: a53f7019c1079296f073e743ddbf9d90fc5abad3
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555877"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="f3037-103">Tutorial: Criar uma API Web com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3037-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="f3037-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="f3037-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="f3037-105">Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f3037-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

<span data-ttu-id="f3037-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="f3037-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f3037-107">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="f3037-107">Create a web API project.</span></span>
> * <span data-ttu-id="f3037-108">Adicionar uma classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="f3037-108">Add a model class.</span></span>
> * <span data-ttu-id="f3037-109">Criar o contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f3037-109">Create the database context.</span></span>
> * <span data-ttu-id="f3037-110">Registrar o contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f3037-110">Register the database context.</span></span>
> * <span data-ttu-id="f3037-111">Adicionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="f3037-111">Add a controller.</span></span>
> * <span data-ttu-id="f3037-112">Adicionar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="f3037-112">Add CRUD methods.</span></span>
> * <span data-ttu-id="f3037-113">Configurar o roteamento e caminhos de URL.</span><span class="sxs-lookup"><span data-stu-id="f3037-113">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="f3037-114">Especificar os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="f3037-114">Specify return values.</span></span>
> * <span data-ttu-id="f3037-115">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="f3037-115">Call the web API with Postman.</span></span>
> * <span data-ttu-id="f3037-116">Chamar a API Web com o jQuery.</span><span class="sxs-lookup"><span data-stu-id="f3037-116">Call the web API with jQuery.</span></span>

<span data-ttu-id="f3037-117">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="f3037-117">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="f3037-118">Visão geral</span><span class="sxs-lookup"><span data-stu-id="f3037-118">Overview</span></span>

<span data-ttu-id="f3037-119">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="f3037-119">This tutorial creates the following API:</span></span>

|<span data-ttu-id="f3037-120">API</span><span class="sxs-lookup"><span data-stu-id="f3037-120">API</span></span> | <span data-ttu-id="f3037-121">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="f3037-121">Description</span></span> | <span data-ttu-id="f3037-122">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="f3037-122">Request body</span></span> | <span data-ttu-id="f3037-123">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="f3037-123">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="f3037-124">GET /api/todo</span><span class="sxs-lookup"><span data-stu-id="f3037-124">GET /api/todo</span></span> | <span data-ttu-id="f3037-125">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="f3037-125">Get all to-do items</span></span> | <span data-ttu-id="f3037-126">Nenhum</span><span class="sxs-lookup"><span data-stu-id="f3037-126">None</span></span> | <span data-ttu-id="f3037-127">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="f3037-127">Array of to-do items</span></span>|
|<span data-ttu-id="f3037-128">GET /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="f3037-128">GET /api/todo/{id}</span></span> | <span data-ttu-id="f3037-129">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="f3037-129">Get an item by ID</span></span> | <span data-ttu-id="f3037-130">Nenhum</span><span class="sxs-lookup"><span data-stu-id="f3037-130">None</span></span> | <span data-ttu-id="f3037-131">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="f3037-131">To-do item</span></span>|
|<span data-ttu-id="f3037-132">POST /api/todo</span><span class="sxs-lookup"><span data-stu-id="f3037-132">POST /api/todo</span></span> | <span data-ttu-id="f3037-133">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="f3037-133">Add a new item</span></span> | <span data-ttu-id="f3037-134">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="f3037-134">To-do item</span></span> | <span data-ttu-id="f3037-135">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="f3037-135">To-do item</span></span> |
|<span data-ttu-id="f3037-136">PUT /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="f3037-136">PUT /api/todo/{id}</span></span> | <span data-ttu-id="f3037-137">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="f3037-137">Update an existing item &nbsp;</span></span> | <span data-ttu-id="f3037-138">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="f3037-138">To-do item</span></span> | <span data-ttu-id="f3037-139">Nenhum</span><span class="sxs-lookup"><span data-stu-id="f3037-139">None</span></span> |
|<span data-ttu-id="f3037-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f3037-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="f3037-141">Excluir um item &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="f3037-141">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="f3037-142">Nenhum</span><span class="sxs-lookup"><span data-stu-id="f3037-142">None</span></span> | <span data-ttu-id="f3037-143">Nenhum</span><span class="sxs-lookup"><span data-stu-id="f3037-143">None</span></span>|

<span data-ttu-id="f3037-144">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f3037-144">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="f3037-150">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="f3037-150">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f3037-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3037-151">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f3037-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3037-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f3037-153">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f3037-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="f3037-154">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="f3037-154">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f3037-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3037-155">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f3037-156">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="f3037-156">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f3037-157">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="f3037-157">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="f3037-158">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f3037-158">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="f3037-159">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 2.2** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="f3037-159">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="f3037-160">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f3037-160">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="f3037-161">**Não** selecione **Habilitar Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="f3037-161">**Don't** select **Enable Docker Support**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f3037-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3037-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f3037-164">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="f3037-164">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="f3037-165">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="f3037-165">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="f3037-166">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="f3037-166">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="f3037-167">Esses comandos criam um novo projeto de API Web e abrem uma nova instância do Visual Studio Code na nova pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="f3037-167">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="f3037-168">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="f3037-168">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f3037-169">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f3037-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f3037-170">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="f3037-170">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="f3037-172">Selecione **Aplicativo .NET Core** > **API Web ASP.NET Core** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="f3037-172">Select **.NET Core App** > **ASP.NET Core Web API** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="f3037-174">Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, aceite a **Estrutura de Destino** padrão \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="f3037-174">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="f3037-175">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="f3037-175">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="f3037-177">Testar a API</span><span class="sxs-lookup"><span data-stu-id="f3037-177">Test the API</span></span>

<span data-ttu-id="f3037-178">O modelo de projeto cria uma API `values`.</span><span class="sxs-lookup"><span data-stu-id="f3037-178">The project template creates a `values` API.</span></span> <span data-ttu-id="f3037-179">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f3037-179">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f3037-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3037-180">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f3037-181">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f3037-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f3037-182">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/api/values`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="f3037-182">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="f3037-183">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="f3037-183">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="f3037-184">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="f3037-184">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f3037-185">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3037-185">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f3037-186">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f3037-186">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="f3037-187">Em um navegador, acesse a seguinte URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="f3037-187">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f3037-188">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f3037-188">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f3037-189">Selecione **Executar** > **Iniciar com Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f3037-189">Select **Run** > **Start With Debugging** to launch the app.</span></span> <span data-ttu-id="f3037-190">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="f3037-190">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="f3037-191">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="f3037-191">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="f3037-192">Acrescente `/api/values` à URL (altere a URL para `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="f3037-192">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="f3037-193">O seguinte JSON é retornado:</span><span class="sxs-lookup"><span data-stu-id="f3037-193">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="f3037-194">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="f3037-194">Add a model class</span></span>

<span data-ttu-id="f3037-195">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f3037-195">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="f3037-196">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="f3037-196">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f3037-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3037-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f3037-198">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="f3037-198">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="f3037-199">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="f3037-199">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f3037-200">Nomeie a pasta como *Models*.</span><span class="sxs-lookup"><span data-stu-id="f3037-200">Name the folder *Models*.</span></span>

* <span data-ttu-id="f3037-201">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="f3037-201">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f3037-202">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="f3037-202">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="f3037-203">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f3037-203">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f3037-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f3037-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f3037-205">Adicione uma pasta chamada *Models*.</span><span class="sxs-lookup"><span data-stu-id="f3037-205">Add a folder named *Models*.</span></span>

* <span data-ttu-id="f3037-206">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f3037-206">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f3037-207">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f3037-207">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f3037-208">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="f3037-208">Right-click the project.</span></span> <span data-ttu-id="f3037-209">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="f3037-209">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="f3037-210">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="f3037-210">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="f3037-212">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="f3037-212">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="f3037-213">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="f3037-213">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="f3037-214">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f3037-214">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="f3037-215">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="f3037-215">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="f3037-216">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="f3037-216">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="f3037-217">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="f3037-217">Add a database context</span></span>

<span data-ttu-id="f3037-218">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="f3037-218">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="f3037-219">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="f3037-219">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f3037-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3037-220">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f3037-221">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="f3037-221">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="f3037-222">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="f3037-222">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f3037-223">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f3037-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f3037-224">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="f3037-224">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="f3037-225">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f3037-225">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="f3037-226">Registrar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="f3037-226">Register the database context</span></span>

<span data-ttu-id="f3037-227">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f3037-227">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="f3037-228">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="f3037-228">The container provides the service to controllers.</span></span>

<span data-ttu-id="f3037-229">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="f3037-229">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="f3037-230">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="f3037-230">The preceding code:</span></span>

* <span data-ttu-id="f3037-231">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="f3037-231">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="f3037-232">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="f3037-232">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="f3037-233">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="f3037-233">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="f3037-234">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="f3037-234">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f3037-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3037-235">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f3037-236">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="f3037-236">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="f3037-237">Selecione **Adicionar** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="f3037-237">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="f3037-238">Na caixa de diálogo **Adicionar Novo Item**, selecione o modelo **Classe do Controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="f3037-238">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="f3037-239">Dê à classe o nome *TodoController* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="f3037-239">Name the class *TodoController*, and select **Add**.</span></span>

  ![Caixa de diálogo Adicionar Novo Item com o controlador na caixa de pesquisa e o controlador da API Web selecionados](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f3037-241">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="f3037-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f3037-242">Na pasta *Controllers*, crie uma classe chamada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="f3037-242">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="f3037-243">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f3037-243">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="f3037-244">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="f3037-244">The preceding code:</span></span>

* <span data-ttu-id="f3037-245">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="f3037-245">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="f3037-246">Decora a classe com o atributo [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="f3037-246">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="f3037-247">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="f3037-247">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="f3037-248">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="f3037-248">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="f3037-249">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="f3037-249">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="f3037-250">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="f3037-250">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="f3037-251">Adiciona um item chamado `Item1` ao banco de dados se o banco de dados está vazio.</span><span class="sxs-lookup"><span data-stu-id="f3037-251">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="f3037-252">Esse código está no construtor, de modo que ele seja executado sempre que há uma nova solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3037-252">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="f3037-253">Se você excluir todos os itens, o construtor criará `Item1` novamente na próxima vez que um método de API for chamado.</span><span class="sxs-lookup"><span data-stu-id="f3037-253">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="f3037-254">Portanto, pode parecer que a exclusão não funcionou quando ela realmente funcionou.</span><span class="sxs-lookup"><span data-stu-id="f3037-254">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="f3037-255">Adicionar métodos Get</span><span class="sxs-lookup"><span data-stu-id="f3037-255">Add Get methods</span></span>

<span data-ttu-id="f3037-256">Para fornecer uma API que recupera itens pendentes, adicione os seguintes métodos à classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="f3037-256">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="f3037-257">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="f3037-257">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="f3037-258">Teste o aplicativo chamando os dois pontos de extremidade em um navegador.</span><span class="sxs-lookup"><span data-stu-id="f3037-258">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="f3037-259">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="f3037-259">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="f3037-260">A seguinte resposta HTTP é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="f3037-260">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="f3037-261">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="f3037-261">Routing and URL paths</span></span>

<span data-ttu-id="f3037-262">O atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica um método que responde a uma solicitação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="f3037-262">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="f3037-263">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="f3037-263">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="f3037-264">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="f3037-264">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="f3037-265">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="f3037-265">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="f3037-266">Para esta amostra, o nome da classe do controlador é **Todo**Controller e, portanto, o nome do controlador é "todo".</span><span class="sxs-lookup"><span data-stu-id="f3037-266">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="f3037-267">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="f3037-267">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="f3037-268">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="f3037-268">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="f3037-269">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="f3037-269">This sample doesn't use a template.</span></span> <span data-ttu-id="f3037-270">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="f3037-270">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="f3037-271">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="f3037-271">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="f3037-272">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="f3037-272">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="f3037-273">Valores de retorno</span><span class="sxs-lookup"><span data-stu-id="f3037-273">Return values</span></span>

<span data-ttu-id="f3037-274">O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="f3037-274">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="f3037-275">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="f3037-275">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="f3037-276">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="f3037-276">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="f3037-277">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="f3037-277">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="f3037-278">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3037-278">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="f3037-279">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="f3037-279">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="f3037-280">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="f3037-280">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="f3037-281">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="f3037-281">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="f3037-282">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="f3037-282">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="f3037-283">Testar o método GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="f3037-283">Test the GetTodoItems method</span></span>

<span data-ttu-id="f3037-284">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="f3037-284">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="f3037-285">Instale o [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="f3037-285">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="f3037-286">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="f3037-286">Start the web app.</span></span>
* <span data-ttu-id="f3037-287">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="f3037-287">Start Postman.</span></span>
* <span data-ttu-id="f3037-288">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="f3037-288">Disable **SSL certificate verification**</span></span>
  
  * <span data-ttu-id="f3037-289">Em **Arquivo > Configurações** (guia \**Geral*), desabilite **Verificação do certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="f3037-289">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="f3037-290">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="f3037-290">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="f3037-291">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="f3037-291">Create a new request.</span></span>
  * <span data-ttu-id="f3037-292">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="f3037-292">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="f3037-293">Defina a URL de solicitação como `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="f3037-293">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="f3037-294">Por exemplo, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="f3037-294">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="f3037-295">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="f3037-295">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="f3037-296">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="f3037-296">Select **Send**.</span></span>

![Postman com solicitação GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="f3037-298">Adicionar um método Create</span><span class="sxs-lookup"><span data-stu-id="f3037-298">Add a Create method</span></span>

<span data-ttu-id="f3037-299">Adicione o seguinte método `PostTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="f3037-299">Add the following `PostTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="f3037-300">O código anterior é um método HTTP POST, conforme indicado pelo atributo [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="f3037-300">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="f3037-301">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3037-301">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="f3037-302">O método `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="f3037-302">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="f3037-303">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="f3037-303">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="f3037-304">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="f3037-304">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="f3037-305">Adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="f3037-305">Adds a `Location` header to the response.</span></span> <span data-ttu-id="f3037-306">O cabeçalho `Location` especifica o URI do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="f3037-306">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="f3037-307">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="f3037-307">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="f3037-308">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="f3037-308">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="f3037-309">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="f3037-309">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="f3037-310">Testar o método PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="f3037-310">Test the PostTodoItem method</span></span>

* <span data-ttu-id="f3037-311">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="f3037-311">Build the project.</span></span>
* <span data-ttu-id="f3037-312">No Postman, defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="f3037-312">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="f3037-313">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="f3037-313">Select the **Body** tab.</span></span>
* <span data-ttu-id="f3037-314">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="f3037-314">Select the **raw** radio button.</span></span>
* <span data-ttu-id="f3037-315">Defina o tipo como **JSON (aplicativo/json)** .</span><span class="sxs-lookup"><span data-stu-id="f3037-315">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="f3037-316">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="f3037-316">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="f3037-317">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="f3037-317">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

  <span data-ttu-id="f3037-319">Se você receber um erro 405 Método Não Permitido, provavelmente, esse será o resultado da não compilação do projeto após a adição do método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="f3037-319">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="f3037-320">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="f3037-320">Test the location header URI</span></span>

* <span data-ttu-id="f3037-321">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="f3037-321">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="f3037-322">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="f3037-322">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="f3037-324">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="f3037-324">Set the method to GET.</span></span>
* <span data-ttu-id="f3037-325">Cole o URI (por exemplo, `https://localhost:5001/api/Todo/2`)</span><span class="sxs-lookup"><span data-stu-id="f3037-325">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="f3037-326">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="f3037-326">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="f3037-327">Adicionar um método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="f3037-327">Add a PutTodoItem method</span></span>

<span data-ttu-id="f3037-328">Adicione o seguinte método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="f3037-328">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="f3037-329">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="f3037-329">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="f3037-330">A resposta é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f3037-330">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="f3037-331">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="f3037-331">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="f3037-332">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="f3037-332">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="f3037-333">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f3037-333">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="f3037-334">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="f3037-334">Test the PutTodoItem method</span></span>

<span data-ttu-id="f3037-335">Este exemplo usa um banco de dados em memória que deverá ser iniciado sempre que o aplicativo for iniciado.</span><span class="sxs-lookup"><span data-stu-id="f3037-335">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="f3037-336">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="f3037-336">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="f3037-337">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="f3037-337">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="f3037-338">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="f3037-338">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="f3037-339">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="f3037-339">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="f3037-341">Adicionar um método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="f3037-341">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="f3037-342">Adicione o seguinte método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="f3037-342">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="f3037-343">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="f3037-343">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="f3037-344">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="f3037-344">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="f3037-345">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="f3037-345">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="f3037-346">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="f3037-346">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="f3037-347">Defina o URI do objeto a ser excluído, por exemplo, `https://localhost:5001/api/todo/1`</span><span class="sxs-lookup"><span data-stu-id="f3037-347">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="f3037-348">Selecione **Enviar**</span><span class="sxs-lookup"><span data-stu-id="f3037-348">Select **Send**</span></span>

<span data-ttu-id="f3037-349">O aplicativo de exemplo permite que você exclua todos os itens.</span><span class="sxs-lookup"><span data-stu-id="f3037-349">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="f3037-350">No entanto, quando o último item é excluído, um novo é criado pelo construtor de classe de modelo na próxima vez que a API for chamada.</span><span class="sxs-lookup"><span data-stu-id="f3037-350">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="f3037-351">Chamar a API com o jQuery</span><span class="sxs-lookup"><span data-stu-id="f3037-351">Call the API with jQuery</span></span>

<span data-ttu-id="f3037-352">Nesta seção, uma página HTML que usa o jQuery para chamar a API Web é adicionada.</span><span class="sxs-lookup"><span data-stu-id="f3037-352">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="f3037-353">O jQuery inicia a solicitação e atualiza a página com os detalhes da resposta da API.</span><span class="sxs-lookup"><span data-stu-id="f3037-353">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="f3037-354">Configurar o aplicativo para [servir arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) atualizando *Startup.cs* com o código realçado a seguir:</span><span class="sxs-lookup"><span data-stu-id="f3037-354">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

::: moniker range=">= aspnetcore-2.2"
<span data-ttu-id="f3037-355">Crie uma pasta *wwwroot* no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="f3037-355">Create a *wwwroot* folder in the project directory.</span></span>
::: moniker-end

<span data-ttu-id="f3037-356">Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="f3037-356">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="f3037-357">Substitua seu conteúdo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="f3037-357">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="f3037-358">Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="f3037-358">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="f3037-359">Substitua seu conteúdo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="f3037-359">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="f3037-360">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="f3037-360">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="f3037-361">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f3037-361">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="f3037-362">Remova a propriedade `launchUrl` para forçar o aplicativo a ser aberto em *index.html*, o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="f3037-362">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="f3037-363">Há várias maneiras de obter o jQuery.</span><span class="sxs-lookup"><span data-stu-id="f3037-363">There are several ways to get jQuery.</span></span> <span data-ttu-id="f3037-364">No snippet anterior, a biblioteca é carregada de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="f3037-364">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="f3037-365">Esta amostra chama todos os métodos CRUD da API.</span><span class="sxs-lookup"><span data-stu-id="f3037-365">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="f3037-366">Veja a seguir explicações das chamadas à API.</span><span class="sxs-lookup"><span data-stu-id="f3037-366">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="f3037-367">Obter uma lista de itens pendentes</span><span class="sxs-lookup"><span data-stu-id="f3037-367">Get a list of to-do items</span></span>

<span data-ttu-id="f3037-368">A função [ajax](https://api.jquery.com/jquery.ajax/) do jQuery envia uma solicitação `GET` para a API, que retorna o JSON que representa uma matriz de itens pendentes.</span><span class="sxs-lookup"><span data-stu-id="f3037-368">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="f3037-369">A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="f3037-369">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="f3037-370">No retorno de chamada, o DOM é atualizado com as informações do item pendente.</span><span class="sxs-lookup"><span data-stu-id="f3037-370">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="f3037-371">Adicionar um item pendente</span><span class="sxs-lookup"><span data-stu-id="f3037-371">Add a to-do item</span></span>

<span data-ttu-id="f3037-372">A função [ajax](https://api.jquery.com/jquery.ajax/) envia uma solicitação `POST` com o item pendente no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="f3037-372">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="f3037-373">As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado.</span><span class="sxs-lookup"><span data-stu-id="f3037-373">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="f3037-374">O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="f3037-374">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="f3037-375">Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="f3037-375">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="f3037-376">Atualizar um item pendente</span><span class="sxs-lookup"><span data-stu-id="f3037-376">Update a to-do item</span></span>

<span data-ttu-id="f3037-377">A atualização de um item pendente é semelhante à adição de um.</span><span class="sxs-lookup"><span data-stu-id="f3037-377">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="f3037-378">A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.</span><span class="sxs-lookup"><span data-stu-id="f3037-378">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="f3037-379">Excluir um item pendente</span><span class="sxs-lookup"><span data-stu-id="f3037-379">Delete a to-do item</span></span>

<span data-ttu-id="f3037-380">A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="f3037-380">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

## <a name="additional-resources"></a><span data-ttu-id="f3037-381">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f3037-381">Additional resources</span></span>

<span data-ttu-id="f3037-382">[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="f3037-382">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="f3037-383">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="f3037-383">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="f3037-384">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="f3037-384">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="f3037-385">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="f3037-385">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

## <a name="next-steps"></a><span data-ttu-id="f3037-386">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="f3037-386">Next steps</span></span>

<span data-ttu-id="f3037-387">Neste tutorial, você aprendeu como:</span><span class="sxs-lookup"><span data-stu-id="f3037-387">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f3037-388">Criar um projeto de aplicativo API Web.</span><span class="sxs-lookup"><span data-stu-id="f3037-388">Create a web api project.</span></span>
> * <span data-ttu-id="f3037-389">Adicionar uma classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="f3037-389">Add a model class.</span></span>
> * <span data-ttu-id="f3037-390">Criar o contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f3037-390">Create the database context.</span></span>
> * <span data-ttu-id="f3037-391">Registrar o contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f3037-391">Register the database context.</span></span>
> * <span data-ttu-id="f3037-392">Adicionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="f3037-392">Add a controller.</span></span>
> * <span data-ttu-id="f3037-393">Adicionar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="f3037-393">Add CRUD methods.</span></span>
> * <span data-ttu-id="f3037-394">Configurar o roteamento e caminhos de URL.</span><span class="sxs-lookup"><span data-stu-id="f3037-394">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="f3037-395">Especificar os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="f3037-395">Specify return values.</span></span>
> * <span data-ttu-id="f3037-396">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="f3037-396">Call the web API with Postman.</span></span>
> * <span data-ttu-id="f3037-397">Chamar a API Web com o jQuery.</span><span class="sxs-lookup"><span data-stu-id="f3037-397">Call the web api with jQuery.</span></span>

<span data-ttu-id="f3037-398">Avance para o próximo tutorial para saber como gerar páginas de ajuda da API:</span><span class="sxs-lookup"><span data-stu-id="f3037-398">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
