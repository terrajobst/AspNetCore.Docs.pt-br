---
title: 'Tutorial: Criar uma API Web com o ASP.NET Core'
author: rick-anderson
description: Saiba como criar uma API Web com o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 25bfccb136d875b454034bd011828c9f3b6cd3d8
ms.sourcegitcommit: de17150e5ec7507d7114dde0e5dbc2e45a66ef53
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70113295"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="71606-103">Tutorial: Criar uma API Web com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71606-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="71606-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="71606-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="71606-105">Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="71606-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71606-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="71606-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="71606-107">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="71606-107">Create a web API project.</span></span>
> * <span data-ttu-id="71606-108">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="71606-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="71606-109">Faça scaffold de um controlador com métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="71606-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="71606-110">Configure o roteamento, os caminhos de URL e os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="71606-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="71606-111">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="71606-111">Call the web API with Postman.</span></span>

<span data-ttu-id="71606-112">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="71606-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="71606-113">Visão geral</span><span class="sxs-lookup"><span data-stu-id="71606-113">Overview</span></span>

<span data-ttu-id="71606-114">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="71606-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="71606-115">API</span><span class="sxs-lookup"><span data-stu-id="71606-115">API</span></span> | <span data-ttu-id="71606-116">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="71606-116">Description</span></span> | <span data-ttu-id="71606-117">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="71606-117">Request body</span></span> | <span data-ttu-id="71606-118">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="71606-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="71606-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="71606-119">GET /api/TodoItems</span></span> | <span data-ttu-id="71606-120">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="71606-120">Get all to-do items</span></span> | <span data-ttu-id="71606-121">Nenhum</span><span class="sxs-lookup"><span data-stu-id="71606-121">None</span></span> | <span data-ttu-id="71606-122">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="71606-122">Array of to-do items</span></span>|
|<span data-ttu-id="71606-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="71606-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="71606-124">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="71606-124">Get an item by ID</span></span> | <span data-ttu-id="71606-125">Nenhum</span><span class="sxs-lookup"><span data-stu-id="71606-125">None</span></span> | <span data-ttu-id="71606-126">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="71606-126">To-do item</span></span>|
|<span data-ttu-id="71606-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="71606-127">POST /api/TodoItems</span></span> | <span data-ttu-id="71606-128">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="71606-128">Add a new item</span></span> | <span data-ttu-id="71606-129">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="71606-129">To-do item</span></span> | <span data-ttu-id="71606-130">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="71606-130">To-do item</span></span> |
|<span data-ttu-id="71606-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="71606-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="71606-132">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="71606-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="71606-133">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="71606-133">To-do item</span></span> | <span data-ttu-id="71606-134">Nenhum</span><span class="sxs-lookup"><span data-stu-id="71606-134">None</span></span> |
|<span data-ttu-id="71606-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="71606-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="71606-136">Excluir um item &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="71606-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="71606-137">Nenhum</span><span class="sxs-lookup"><span data-stu-id="71606-137">None</span></span> | <span data-ttu-id="71606-138">Nenhum</span><span class="sxs-lookup"><span data-stu-id="71606-138">None</span></span>|

<span data-ttu-id="71606-139">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71606-139">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="71606-145">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="71606-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71606-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71606-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71606-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71606-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="71606-148">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="71606-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="71606-149">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="71606-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71606-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71606-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="71606-151">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="71606-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="71606-152">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="71606-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="71606-153">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="71606-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="71606-154">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 3.0** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="71606-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="71606-155">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="71606-155">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="71606-156">**Não** selecione **Habilitar Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="71606-156">**Don't** select **Enable Docker Support**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71606-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71606-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="71606-159">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="71606-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="71606-160">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="71606-160">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="71606-161">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="71606-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* <span data-ttu-id="71606-162">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="71606-162">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="71606-163">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="71606-163">The preceding commands:</span></span>

  * <span data-ttu-id="71606-164">Cria um novo projeto de API Web e o abre no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="71606-164">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="71606-165">Adiciona os pacotes do NuGet que são exigidos na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="71606-165">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="71606-166">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="71606-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="71606-167">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="71606-167">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="71606-169">Selecione **.NET Core** > **Aplicativo** > **API** > **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="71606-169">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="71606-171">Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, selecione a **Estrutura de Destino** do \* *.NET Core 3.0*.</span><span class="sxs-lookup"><span data-stu-id="71606-171">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="71606-172">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="71606-172">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="71606-174">Abra um terminal de comando na pasta do projeto e execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="71606-174">Open a command terminal in the project folder and run the following commands:</span></span>

   ```console
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="71606-175">Testar a API</span><span class="sxs-lookup"><span data-stu-id="71606-175">Test the API</span></span>

<span data-ttu-id="71606-176">O modelo de projeto cria uma API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="71606-176">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="71606-177">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71606-177">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71606-178">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71606-178">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="71606-179">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71606-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="71606-180">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/WeatherForecast`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="71606-180">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="71606-181">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="71606-181">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="71606-182">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="71606-182">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71606-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71606-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="71606-184">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71606-184">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="71606-185">Em um navegador, acesse a seguinte URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="71606-185">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="71606-186">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="71606-186">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="71606-187">Selecione **Executar** > **Iniciar com Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71606-187">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="71606-188">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="71606-188">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="71606-189">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="71606-189">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="71606-190">Acrescente `/WeatherForecast` à URL (altere a URL para `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="71606-190">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="71606-191">Um JSON semelhante ao seguinte será retornado:</span><span class="sxs-lookup"><span data-stu-id="71606-191">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="71606-192">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="71606-192">Add a model class</span></span>

<span data-ttu-id="71606-193">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71606-193">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="71606-194">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="71606-194">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71606-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71606-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="71606-196">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="71606-196">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="71606-197">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="71606-197">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="71606-198">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="71606-198">Name the folder *Models*.</span></span>

* <span data-ttu-id="71606-199">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="71606-199">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="71606-200">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="71606-200">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="71606-201">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="71606-201">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71606-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71606-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="71606-203">Adicione uma pasta chamada *Models*.</span><span class="sxs-lookup"><span data-stu-id="71606-203">Add a folder named *Models*.</span></span>

* <span data-ttu-id="71606-204">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="71606-204">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="71606-205">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="71606-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="71606-206">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="71606-206">Right-click the project.</span></span> <span data-ttu-id="71606-207">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="71606-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="71606-208">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="71606-208">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="71606-210">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="71606-210">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="71606-211">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="71606-211">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="71606-212">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="71606-212">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="71606-213">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="71606-213">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="71606-214">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="71606-214">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="71606-215">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="71606-215">Add a database context</span></span>

<span data-ttu-id="71606-216">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="71606-216">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="71606-217">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="71606-217">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71606-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71606-218">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="71606-219">Adicione Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="71606-219">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="71606-220">No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.</span><span class="sxs-lookup"><span data-stu-id="71606-220">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="71606-221">Selecione a caixa de seleção **Incluir Pré-lançamento**.</span><span class="sxs-lookup"><span data-stu-id="71606-221">Select the **Include prerelease** checkbox.</span></span>
* <span data-ttu-id="71606-222">Selecione a guia **Procurar** e, em seguida, insira **Microsoft.EntityFrameworkCore.SqlServer** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="71606-222">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="71606-223">Selecione **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="71606-223">Select  **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** in the left pane.</span></span>
* <span data-ttu-id="71606-224">Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="71606-224">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="71606-225">Use as instruções anteriores para adicionar o pacote do NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="71606-225">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Gerenciador de pacotes do NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="71606-227">Adicione o contexto de banco de dados TodoContext</span><span class="sxs-lookup"><span data-stu-id="71606-227">Add the TodoContext database context</span></span>

* <span data-ttu-id="71606-228">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="71606-228">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="71606-229">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="71606-229">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="71606-230">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="71606-230">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="71606-231">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="71606-231">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="71606-232">Insira o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="71606-232">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="71606-233">Registrar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="71606-233">Register the database context</span></span>

<span data-ttu-id="71606-234">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="71606-234">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="71606-235">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="71606-235">The container provides the service to controllers.</span></span>

<span data-ttu-id="71606-236">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="71606-236">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="71606-237">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="71606-237">The preceding code:</span></span>

* <span data-ttu-id="71606-238">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="71606-238">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="71606-239">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="71606-239">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="71606-240">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="71606-240">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="71606-241">Faça scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="71606-241">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71606-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71606-242">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="71606-243">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="71606-243">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="71606-244">Selecione **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="71606-244">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="71606-245">Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="71606-245">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="71606-246">Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework**:</span><span class="sxs-lookup"><span data-stu-id="71606-246">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="71606-247">Selecione **TodoItem (TodoAPI.Models**) na **classe Modelo**.</span><span class="sxs-lookup"><span data-stu-id="71606-247">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="71606-248">Selecione **TodoContext (TodoAPI.Models**) na **classe Contexto de Dados**.</span><span class="sxs-lookup"><span data-stu-id="71606-248">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="71606-249">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="71606-249">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="71606-250">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="71606-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="71606-251">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="71606-251">Run the following commands:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="71606-252">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="71606-252">The preceding commands:</span></span>

* <span data-ttu-id="71606-253">Adicionam os pacotes do NuGet para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="71606-253">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="71606-254">Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="71606-254">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="71606-255">Fazem scaffold de `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="71606-255">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="71606-256">O código gerado:</span><span class="sxs-lookup"><span data-stu-id="71606-256">The generated code:</span></span>

* <span data-ttu-id="71606-257">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="71606-257">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="71606-258">Decora a classe com o atributo [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="71606-258">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="71606-259">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="71606-259">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="71606-260">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="71606-260">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="71606-261">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="71606-261">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="71606-262">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="71606-262">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="71606-263">Examine o método criar do PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="71606-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="71606-264">Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="71606-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="71606-265">O código anterior é um método HTTP POST, conforme indicado pelo atributo [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="71606-265">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="71606-266">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="71606-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="71606-267">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="71606-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="71606-268">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="71606-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="71606-269">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="71606-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="71606-270">Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta.</span><span class="sxs-lookup"><span data-stu-id="71606-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="71606-271">O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="71606-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="71606-272">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="71606-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="71606-273">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="71606-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="71606-274">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="71606-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="71606-275">Instale o Postman</span><span class="sxs-lookup"><span data-stu-id="71606-275">Install Postman</span></span>

<span data-ttu-id="71606-276">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="71606-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="71606-277">Instale o [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="71606-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="71606-278">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="71606-278">Start the web app.</span></span>
* <span data-ttu-id="71606-279">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="71606-279">Start Postman.</span></span>
* <span data-ttu-id="71606-280">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="71606-280">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="71606-281">Em **Arquivo > Configurações** (guia \**Geral*), desabilite **Verificação do certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="71606-281">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="71606-282">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="71606-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="71606-283">Teste o PostTodoItem com o Postman</span><span class="sxs-lookup"><span data-stu-id="71606-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="71606-284">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="71606-284">Create a new request.</span></span>
* <span data-ttu-id="71606-285">Defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="71606-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="71606-286">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="71606-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="71606-287">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="71606-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="71606-288">Defina o tipo como **JSON (aplicativo/json)** .</span><span class="sxs-lookup"><span data-stu-id="71606-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="71606-289">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="71606-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="71606-290">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="71606-290">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="71606-292">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="71606-292">Test the location header URI</span></span>

* <span data-ttu-id="71606-293">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="71606-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="71606-294">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="71606-294">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="71606-296">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="71606-296">Set the method to GET.</span></span>
* <span data-ttu-id="71606-297">Cole o URI (por exemplo, `https://localhost:5001/api/TodoItems/1`)</span><span class="sxs-lookup"><span data-stu-id="71606-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="71606-298">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="71606-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="71606-299">Examine os métodos GET</span><span class="sxs-lookup"><span data-stu-id="71606-299">Examine the GET methods</span></span>

<span data-ttu-id="71606-300">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="71606-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="71606-301">Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman.</span><span class="sxs-lookup"><span data-stu-id="71606-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="71606-302">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="71606-302">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="71606-303">Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="71606-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="71606-304">Teste o GET com o Postman</span><span class="sxs-lookup"><span data-stu-id="71606-304">Test Get with Postman</span></span>

* <span data-ttu-id="71606-305">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="71606-305">Create a new request.</span></span>
* <span data-ttu-id="71606-306">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="71606-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="71606-307">Defina a URL de solicitação como `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="71606-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="71606-308">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="71606-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="71606-309">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="71606-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="71606-310">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="71606-310">Select **Send**.</span></span>

<span data-ttu-id="71606-311">Este aplicativo usa um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="71606-311">This app uses an in-memory database.</span></span> <span data-ttu-id="71606-312">Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado.</span><span class="sxs-lookup"><span data-stu-id="71606-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="71606-313">Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71606-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="71606-314">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="71606-314">Routing and URL paths</span></span>

<span data-ttu-id="71606-315">O atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica um método que responde a uma solicitação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="71606-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="71606-316">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="71606-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="71606-317">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="71606-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="71606-318">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="71606-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="71606-319">Para esta amostra, o nome da classe do controlador é **TodoItems**Controller. Portanto, o nome do controlador é "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="71606-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="71606-320">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="71606-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="71606-321">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="71606-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="71606-322">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="71606-322">This sample doesn't use a template.</span></span> <span data-ttu-id="71606-323">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="71606-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="71606-324">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="71606-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="71606-325">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="71606-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="71606-326">Valores de retorno</span><span class="sxs-lookup"><span data-stu-id="71606-326">Return values</span></span>

<span data-ttu-id="71606-327">O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="71606-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="71606-328">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="71606-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="71606-329">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="71606-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="71606-330">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="71606-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="71606-331">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="71606-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="71606-332">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="71606-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="71606-333">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="71606-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="71606-334">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="71606-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="71606-335">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="71606-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="71606-336">O método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="71606-336">The PutTodoItem method</span></span>

<span data-ttu-id="71606-337">Examine o método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="71606-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="71606-338">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="71606-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="71606-339">A resposta é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="71606-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="71606-340">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="71606-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="71606-341">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="71606-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="71606-342">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="71606-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="71606-343">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="71606-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="71606-344">Este exemplo usa um banco de dados em memória que deverá ser iniciado sempre que o aplicativo for iniciado.</span><span class="sxs-lookup"><span data-stu-id="71606-344">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="71606-345">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="71606-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="71606-346">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="71606-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="71606-347">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="71606-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="71606-348">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="71606-348">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="71606-350">O método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="71606-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="71606-351">Examine o método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="71606-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="71606-352">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="71606-352">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="71606-353">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="71606-353">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="71606-354">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="71606-354">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="71606-355">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="71606-355">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="71606-356">Defina o URI do objeto a ser excluído, por exemplo, `https://localhost:5001/api/TodoItems/1`</span><span class="sxs-lookup"><span data-stu-id="71606-356">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="71606-357">Selecione **Enviar**</span><span class="sxs-lookup"><span data-stu-id="71606-357">Select **Send**</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="71606-358">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="71606-358">Call the web API with JavaScript</span></span>

<span data-ttu-id="71606-359">Consulte [Tutorial: Chamar uma API Web do ASP.NET Core com o JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="71606-359">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71606-360">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="71606-360">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="71606-361">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="71606-361">Create a web API project.</span></span>
> * <span data-ttu-id="71606-362">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="71606-362">Add a model class and a database context.</span></span>
> * <span data-ttu-id="71606-363">Adicionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="71606-363">Add a controller.</span></span>
> * <span data-ttu-id="71606-364">Adicionar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="71606-364">Add CRUD methods.</span></span>
> * <span data-ttu-id="71606-365">Configurar o roteamento e caminhos de URL.</span><span class="sxs-lookup"><span data-stu-id="71606-365">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="71606-366">Especificar os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="71606-366">Specify return values.</span></span>
> * <span data-ttu-id="71606-367">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="71606-367">Call the web API with Postman.</span></span>
> * <span data-ttu-id="71606-368">Chamar a API Web com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="71606-368">Call the web API with JavaScript.</span></span>

<span data-ttu-id="71606-369">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="71606-369">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="71606-370">Visão geral</span><span class="sxs-lookup"><span data-stu-id="71606-370">Overview</span></span>

<span data-ttu-id="71606-371">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="71606-371">This tutorial creates the following API:</span></span>

|<span data-ttu-id="71606-372">API</span><span class="sxs-lookup"><span data-stu-id="71606-372">API</span></span> | <span data-ttu-id="71606-373">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="71606-373">Description</span></span> | <span data-ttu-id="71606-374">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="71606-374">Request body</span></span> | <span data-ttu-id="71606-375">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="71606-375">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="71606-376">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="71606-376">GET /api/TodoItems</span></span> | <span data-ttu-id="71606-377">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="71606-377">Get all to-do items</span></span> | <span data-ttu-id="71606-378">Nenhum</span><span class="sxs-lookup"><span data-stu-id="71606-378">None</span></span> | <span data-ttu-id="71606-379">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="71606-379">Array of to-do items</span></span>|
|<span data-ttu-id="71606-380">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="71606-380">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="71606-381">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="71606-381">Get an item by ID</span></span> | <span data-ttu-id="71606-382">Nenhum</span><span class="sxs-lookup"><span data-stu-id="71606-382">None</span></span> | <span data-ttu-id="71606-383">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="71606-383">To-do item</span></span>|
|<span data-ttu-id="71606-384">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="71606-384">POST /api/TodoItems</span></span> | <span data-ttu-id="71606-385">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="71606-385">Add a new item</span></span> | <span data-ttu-id="71606-386">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="71606-386">To-do item</span></span> | <span data-ttu-id="71606-387">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="71606-387">To-do item</span></span> |
|<span data-ttu-id="71606-388">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="71606-388">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="71606-389">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="71606-389">Update an existing item &nbsp;</span></span> | <span data-ttu-id="71606-390">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="71606-390">To-do item</span></span> | <span data-ttu-id="71606-391">Nenhum</span><span class="sxs-lookup"><span data-stu-id="71606-391">None</span></span> |
|<span data-ttu-id="71606-392">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="71606-392">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="71606-393">Excluir um item &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="71606-393">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="71606-394">Nenhum</span><span class="sxs-lookup"><span data-stu-id="71606-394">None</span></span> | <span data-ttu-id="71606-395">Nenhum</span><span class="sxs-lookup"><span data-stu-id="71606-395">None</span></span>|

<span data-ttu-id="71606-396">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71606-396">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="71606-402">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="71606-402">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71606-403">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71606-403">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71606-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71606-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="71606-405">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="71606-405">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="71606-406">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="71606-406">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71606-407">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71606-407">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="71606-408">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="71606-408">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="71606-409">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="71606-409">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="71606-410">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="71606-410">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="71606-411">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 2.2** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="71606-411">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="71606-412">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="71606-412">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="71606-413">**Não** selecione **Habilitar Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="71606-413">**Don't** select **Enable Docker Support**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71606-415">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71606-415">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="71606-416">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="71606-416">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="71606-417">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="71606-417">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="71606-418">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="71606-418">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="71606-419">Esses comandos criam um novo projeto de API Web e abrem uma nova instância do Visual Studio Code na nova pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="71606-419">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="71606-420">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="71606-420">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="71606-421">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="71606-421">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="71606-422">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="71606-422">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="71606-424">Selecione **.NET Core** > **Aplicativo** > **API** > **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="71606-424">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="71606-426">Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, aceite a **Estrutura de Destino** padrão \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="71606-426">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="71606-427">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="71606-427">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="71606-429">Testar a API</span><span class="sxs-lookup"><span data-stu-id="71606-429">Test the API</span></span>

<span data-ttu-id="71606-430">O modelo de projeto cria uma API `values`.</span><span class="sxs-lookup"><span data-stu-id="71606-430">The project template creates a `values` API.</span></span> <span data-ttu-id="71606-431">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71606-431">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71606-432">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71606-432">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="71606-433">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71606-433">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="71606-434">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/api/values`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="71606-434">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="71606-435">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="71606-435">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="71606-436">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="71606-436">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71606-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71606-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="71606-438">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71606-438">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="71606-439">Em um navegador, acesse a seguinte URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="71606-439">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="71606-440">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="71606-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="71606-441">Selecione **Executar** > **Iniciar com Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71606-441">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="71606-442">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="71606-442">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="71606-443">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="71606-443">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="71606-444">Acrescente `/api/values` à URL (altere a URL para `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="71606-444">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="71606-445">O seguinte JSON é retornado:</span><span class="sxs-lookup"><span data-stu-id="71606-445">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="71606-446">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="71606-446">Add a model class</span></span>

<span data-ttu-id="71606-447">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="71606-447">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="71606-448">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="71606-448">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71606-449">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71606-449">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="71606-450">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="71606-450">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="71606-451">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="71606-451">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="71606-452">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="71606-452">Name the folder *Models*.</span></span>

* <span data-ttu-id="71606-453">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="71606-453">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="71606-454">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="71606-454">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="71606-455">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="71606-455">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71606-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71606-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="71606-457">Adicione uma pasta chamada *Models*.</span><span class="sxs-lookup"><span data-stu-id="71606-457">Add a folder named *Models*.</span></span>

* <span data-ttu-id="71606-458">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="71606-458">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="71606-459">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="71606-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="71606-460">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="71606-460">Right-click the project.</span></span> <span data-ttu-id="71606-461">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="71606-461">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="71606-462">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="71606-462">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="71606-464">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="71606-464">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="71606-465">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="71606-465">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="71606-466">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="71606-466">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="71606-467">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="71606-467">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="71606-468">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="71606-468">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="71606-469">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="71606-469">Add a database context</span></span>

<span data-ttu-id="71606-470">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="71606-470">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="71606-471">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="71606-471">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71606-472">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71606-472">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="71606-473">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="71606-473">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="71606-474">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="71606-474">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="71606-475">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="71606-475">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="71606-476">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="71606-476">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="71606-477">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="71606-477">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="71606-478">Registrar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="71606-478">Register the database context</span></span>

<span data-ttu-id="71606-479">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="71606-479">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="71606-480">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="71606-480">The container provides the service to controllers.</span></span>

<span data-ttu-id="71606-481">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="71606-481">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="71606-482">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="71606-482">The preceding code:</span></span>

* <span data-ttu-id="71606-483">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="71606-483">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="71606-484">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="71606-484">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="71606-485">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="71606-485">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="71606-486">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="71606-486">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71606-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71606-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="71606-488">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="71606-488">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="71606-489">Selecione **Adicionar** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="71606-489">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="71606-490">Na caixa de diálogo **Adicionar Novo Item**, selecione o modelo **Classe do Controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="71606-490">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="71606-491">Dê à classe o nome *TodoController* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="71606-491">Name the class *TodoController*, and select **Add**.</span></span>

  ![Caixa de diálogo Adicionar Novo Item com o controlador na caixa de pesquisa e o controlador da API Web selecionados](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="71606-493">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="71606-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="71606-494">Na pasta *Controllers*, crie uma classe chamada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="71606-494">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="71606-495">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="71606-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="71606-496">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="71606-496">The preceding code:</span></span>

* <span data-ttu-id="71606-497">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="71606-497">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="71606-498">Decora a classe com o atributo [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="71606-498">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="71606-499">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="71606-499">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="71606-500">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="71606-500">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="71606-501">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="71606-501">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="71606-502">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="71606-502">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="71606-503">Adiciona um item chamado `Item1` ao banco de dados se o banco de dados está vazio.</span><span class="sxs-lookup"><span data-stu-id="71606-503">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="71606-504">Esse código está no construtor, de modo que ele seja executado sempre que há uma nova solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="71606-504">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="71606-505">Se você excluir todos os itens, o construtor criará `Item1` novamente na próxima vez que um método de API for chamado.</span><span class="sxs-lookup"><span data-stu-id="71606-505">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="71606-506">Portanto, pode parecer que a exclusão não funcionou quando ela realmente funcionou.</span><span class="sxs-lookup"><span data-stu-id="71606-506">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="71606-507">Adicionar métodos Get</span><span class="sxs-lookup"><span data-stu-id="71606-507">Add Get methods</span></span>

<span data-ttu-id="71606-508">Para fornecer uma API que recupera itens pendentes, adicione os seguintes métodos à classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="71606-508">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="71606-509">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="71606-509">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="71606-510">Interrompa o aplicativo se ele ainda estiver em execução.</span><span class="sxs-lookup"><span data-stu-id="71606-510">Stop the app if it's still running.</span></span> <span data-ttu-id="71606-511">Em seguida, execute-o novamente para incluir as alterações mais recentes.</span><span class="sxs-lookup"><span data-stu-id="71606-511">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="71606-512">Teste o aplicativo chamando os dois pontos de extremidade em um navegador.</span><span class="sxs-lookup"><span data-stu-id="71606-512">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="71606-513">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="71606-513">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="71606-514">A seguinte resposta HTTP é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="71606-514">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="71606-515">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="71606-515">Routing and URL paths</span></span>

<span data-ttu-id="71606-516">O atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica um método que responde a uma solicitação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="71606-516">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="71606-517">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="71606-517">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="71606-518">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="71606-518">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="71606-519">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="71606-519">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="71606-520">Para esta amostra, o nome da classe do controlador é **Todo**Controller e, portanto, o nome do controlador é "todo".</span><span class="sxs-lookup"><span data-stu-id="71606-520">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="71606-521">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="71606-521">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="71606-522">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="71606-522">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="71606-523">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="71606-523">This sample doesn't use a template.</span></span> <span data-ttu-id="71606-524">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="71606-524">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="71606-525">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="71606-525">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="71606-526">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="71606-526">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="71606-527">Valores de retorno</span><span class="sxs-lookup"><span data-stu-id="71606-527">Return values</span></span>

<span data-ttu-id="71606-528">O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="71606-528">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="71606-529">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="71606-529">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="71606-530">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="71606-530">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="71606-531">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="71606-531">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="71606-532">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="71606-532">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="71606-533">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="71606-533">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="71606-534">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="71606-534">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="71606-535">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="71606-535">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="71606-536">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="71606-536">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="71606-537">Testar o método GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="71606-537">Test the GetTodoItems method</span></span>

<span data-ttu-id="71606-538">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="71606-538">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="71606-539">Instale o [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="71606-539">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="71606-540">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="71606-540">Start the web app.</span></span>
* <span data-ttu-id="71606-541">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="71606-541">Start Postman.</span></span>
* <span data-ttu-id="71606-542">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="71606-542">Disable **SSL certificate verification**</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71606-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71606-543">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="71606-544">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="71606-544">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="71606-545">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="71606-545">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="71606-546">Em **Postman** > **Preferências** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="71606-546">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="71606-547">Como alternativa, selecione a chave inglesa, selecione **Configurações** e desabilite a verificação do certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="71606-547">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="71606-548">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="71606-548">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="71606-549">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="71606-549">Create a new request.</span></span>
  * <span data-ttu-id="71606-550">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="71606-550">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="71606-551">Defina a URL de solicitação como `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="71606-551">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="71606-552">Por exemplo, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="71606-552">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="71606-553">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="71606-553">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="71606-554">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="71606-554">Select **Send**.</span></span>

![Postman com solicitação GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="71606-556">Adicionar um método Create</span><span class="sxs-lookup"><span data-stu-id="71606-556">Add a Create method</span></span>

<span data-ttu-id="71606-557">Adicione o seguinte método `PostTodoItem` dentro de *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="71606-557">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="71606-558">O código anterior é um método HTTP POST, conforme indicado pelo atributo [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="71606-558">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="71606-559">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="71606-559">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="71606-560">O método `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="71606-560">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="71606-561">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="71606-561">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="71606-562">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="71606-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="71606-563">Adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="71606-563">Adds a `Location` header to the response.</span></span> <span data-ttu-id="71606-564">O cabeçalho `Location` especifica o URI do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="71606-564">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="71606-565">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="71606-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="71606-566">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="71606-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="71606-567">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="71606-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="71606-568">Testar o método PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="71606-568">Test the PostTodoItem method</span></span>

* <span data-ttu-id="71606-569">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="71606-569">Build the project.</span></span>
* <span data-ttu-id="71606-570">No Postman, defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="71606-570">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="71606-571">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="71606-571">Select the **Body** tab.</span></span>
* <span data-ttu-id="71606-572">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="71606-572">Select the **raw** radio button.</span></span>
* <span data-ttu-id="71606-573">Defina o tipo como **JSON (aplicativo/json)** .</span><span class="sxs-lookup"><span data-stu-id="71606-573">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="71606-574">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="71606-574">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="71606-575">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="71606-575">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

  <span data-ttu-id="71606-577">Se você receber um erro 405 Método Não Permitido, provavelmente, esse será o resultado da não compilação do projeto após a adição do método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="71606-577">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="71606-578">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="71606-578">Test the location header URI</span></span>

* <span data-ttu-id="71606-579">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="71606-579">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="71606-580">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="71606-580">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="71606-582">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="71606-582">Set the method to GET.</span></span>
* <span data-ttu-id="71606-583">Cole o URI (por exemplo, `https://localhost:5001/api/Todo/2`)</span><span class="sxs-lookup"><span data-stu-id="71606-583">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="71606-584">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="71606-584">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="71606-585">Adicionar um método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="71606-585">Add a PutTodoItem method</span></span>

<span data-ttu-id="71606-586">Adicione o seguinte método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="71606-586">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="71606-587">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="71606-587">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="71606-588">A resposta é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="71606-588">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="71606-589">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="71606-589">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="71606-590">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="71606-590">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="71606-591">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="71606-591">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="71606-592">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="71606-592">Test the PutTodoItem method</span></span>

<span data-ttu-id="71606-593">Este exemplo usa um banco de dados em memória que deverá ser iniciado sempre que o aplicativo for iniciado.</span><span class="sxs-lookup"><span data-stu-id="71606-593">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="71606-594">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="71606-594">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="71606-595">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="71606-595">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="71606-596">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="71606-596">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="71606-597">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="71606-597">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="71606-599">Adicionar um método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="71606-599">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="71606-600">Adicione o seguinte método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="71606-600">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="71606-601">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="71606-601">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="71606-602">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="71606-602">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="71606-603">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="71606-603">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="71606-604">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="71606-604">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="71606-605">Defina o URI do objeto a ser excluído, por exemplo, `https://localhost:5001/api/todo/1`</span><span class="sxs-lookup"><span data-stu-id="71606-605">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="71606-606">Selecione **Enviar**</span><span class="sxs-lookup"><span data-stu-id="71606-606">Select **Send**</span></span>

<span data-ttu-id="71606-607">O aplicativo de exemplo permite que você exclua todos os itens.</span><span class="sxs-lookup"><span data-stu-id="71606-607">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="71606-608">No entanto, quando o último item é excluído, um novo é criado pelo construtor de classe de modelo na próxima vez que a API for chamada.</span><span class="sxs-lookup"><span data-stu-id="71606-608">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="71606-609">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="71606-609">Call the web API with JavaScript</span></span>

<span data-ttu-id="71606-610">Nesta seção, é adicionada uma página HTML que usa o JavaScript para chamar a API Web.</span><span class="sxs-lookup"><span data-stu-id="71606-610">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="71606-611">A API Fetch inicia a solicitação.</span><span class="sxs-lookup"><span data-stu-id="71606-611">The Fetch API initiates the request.</span></span> <span data-ttu-id="71606-612">O JavaScript atualiza a página com os detalhes da resposta da API Web.</span><span class="sxs-lookup"><span data-stu-id="71606-612">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="71606-613">Configurar o aplicativo para [servir arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) atualizando *Startup.cs* com o código realçado a seguir:</span><span class="sxs-lookup"><span data-stu-id="71606-613">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="71606-614">Crie uma pasta *wwwroot* no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="71606-614">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="71606-615">Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="71606-615">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="71606-616">Substitua seu conteúdo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="71606-616">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="71606-617">Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="71606-617">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="71606-618">Substitua seu conteúdo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="71606-618">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="71606-619">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="71606-619">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="71606-620">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="71606-620">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="71606-621">Remova a propriedade `launchUrl` para forçar o aplicativo a ser aberto em *index.html*, o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="71606-621">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="71606-622">Esta amostra chama todos os métodos CRUD da API Web.</span><span class="sxs-lookup"><span data-stu-id="71606-622">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="71606-623">Veja a seguir explicações das chamadas à API.</span><span class="sxs-lookup"><span data-stu-id="71606-623">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="71606-624">Obter uma lista de itens pendentes</span><span class="sxs-lookup"><span data-stu-id="71606-624">Get a list of to-do items</span></span>

<span data-ttu-id="71606-625">Fetch envia uma solicitação HTTP GET para a API Web, que retorna JSON representando uma matriz de itens de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="71606-625">Fetch sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="71606-626">A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="71606-626">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="71606-627">No retorno de chamada, o DOM é atualizado com as informações do item pendente.</span><span class="sxs-lookup"><span data-stu-id="71606-627">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="71606-628">Adicionar um item pendente</span><span class="sxs-lookup"><span data-stu-id="71606-628">Add a to-do item</span></span>

<span data-ttu-id="71606-629">Fetch envia uma solicitação HTTP POST com o item de tarefas pendentes no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="71606-629">Fetch sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="71606-630">As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado.</span><span class="sxs-lookup"><span data-stu-id="71606-630">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="71606-631">O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="71606-631">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="71606-632">Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="71606-632">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="71606-633">Atualizar um item pendente</span><span class="sxs-lookup"><span data-stu-id="71606-633">Update a to-do item</span></span>

<span data-ttu-id="71606-634">A atualização de um item pendente é semelhante à adição de um.</span><span class="sxs-lookup"><span data-stu-id="71606-634">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="71606-635">A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.</span><span class="sxs-lookup"><span data-stu-id="71606-635">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="71606-636">Excluir um item pendente</span><span class="sxs-lookup"><span data-stu-id="71606-636">Delete a to-do item</span></span>

<span data-ttu-id="71606-637">A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="71606-637">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="71606-638">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="71606-638">Additional resources</span></span>

<span data-ttu-id="71606-639">[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="71606-639">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="71606-640">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="71606-640">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="71606-641">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="71606-641">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="71606-642">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="71606-642">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
