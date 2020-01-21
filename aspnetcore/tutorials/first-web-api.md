---
title: 'Tutorial: Create a web API with ASP.NET Core'
author: rick-anderson
description: Saiba como criar uma API Web com o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 73e547b014d78dcbcbf1c887ebec16e0743d10b9
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294744"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="6c186-103">Tutorial: Create a web API with ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6c186-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="6c186-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="6c186-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="6c186-105">Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6c186-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6c186-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="6c186-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6c186-107">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="6c186-107">Create a web API project.</span></span>
> * <span data-ttu-id="6c186-108">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c186-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="6c186-109">Faça scaffold de um controlador com métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="6c186-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="6c186-110">Configure o roteamento, os caminhos de URL e os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="6c186-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="6c186-111">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="6c186-111">Call the web API with Postman.</span></span>

<span data-ttu-id="6c186-112">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c186-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="6c186-113">{1&gt;Visão Geral&lt;1}</span><span class="sxs-lookup"><span data-stu-id="6c186-113">Overview</span></span>

<span data-ttu-id="6c186-114">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="6c186-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="6c186-115">API</span><span class="sxs-lookup"><span data-stu-id="6c186-115">API</span></span> | <span data-ttu-id="6c186-116">Descrição</span><span class="sxs-lookup"><span data-stu-id="6c186-116">Description</span></span> | <span data-ttu-id="6c186-117">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="6c186-117">Request body</span></span> | <span data-ttu-id="6c186-118">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="6c186-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="6c186-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="6c186-119">GET /api/TodoItems</span></span> | <span data-ttu-id="6c186-120">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6c186-120">Get all to-do items</span></span> | <span data-ttu-id="6c186-121">{1&gt;Nenhum&lt;1}</span><span class="sxs-lookup"><span data-stu-id="6c186-121">None</span></span> | <span data-ttu-id="6c186-122">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6c186-122">Array of to-do items</span></span>|
|<span data-ttu-id="6c186-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="6c186-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="6c186-124">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="6c186-124">Get an item by ID</span></span> | <span data-ttu-id="6c186-125">{1&gt;Nenhum&lt;1}</span><span class="sxs-lookup"><span data-stu-id="6c186-125">None</span></span> | <span data-ttu-id="6c186-126">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6c186-126">To-do item</span></span>|
|<span data-ttu-id="6c186-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="6c186-127">POST /api/TodoItems</span></span> | <span data-ttu-id="6c186-128">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="6c186-128">Add a new item</span></span> | <span data-ttu-id="6c186-129">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6c186-129">To-do item</span></span> | <span data-ttu-id="6c186-130">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6c186-130">To-do item</span></span> |
|<span data-ttu-id="6c186-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="6c186-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="6c186-132">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="6c186-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="6c186-133">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6c186-133">To-do item</span></span> | <span data-ttu-id="6c186-134">{1&gt;Nenhum&lt;1}</span><span class="sxs-lookup"><span data-stu-id="6c186-134">None</span></span> |
|<span data-ttu-id="6c186-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="6c186-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="6c186-136">Delete an item &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="6c186-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="6c186-137">{1&gt;Nenhum&lt;1}</span><span class="sxs-lookup"><span data-stu-id="6c186-137">None</span></span> | <span data-ttu-id="6c186-138">{1&gt;Nenhum&lt;1}</span><span class="sxs-lookup"><span data-stu-id="6c186-138">None</span></span>|

<span data-ttu-id="6c186-139">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c186-139">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="6c186-145">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="6c186-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c186-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c186-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6c186-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c186-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6c186-148">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c186-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="6c186-149">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="6c186-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c186-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c186-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6c186-151">From the **File** menu, select **New** > **Project**.</span><span class="sxs-lookup"><span data-stu-id="6c186-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="6c186-152">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="6c186-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="6c186-153">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="6c186-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span><span class="sxs-lookup"><span data-stu-id="6c186-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="6c186-155">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-155">Select the **API** template and click **Create**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6c186-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c186-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6c186-158">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6c186-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="6c186-159">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="6c186-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="6c186-160">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6c186-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="6c186-161">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="6c186-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="6c186-162">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="6c186-162">The preceding commands:</span></span>

  * <span data-ttu-id="6c186-163">Cria um novo projeto de API Web e o abre no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="6c186-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="6c186-164">Adiciona os pacotes do NuGet que são exigidos na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="6c186-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6c186-165">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c186-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6c186-166">Select **File** > **New Solution**.</span><span class="sxs-lookup"><span data-stu-id="6c186-166">Select **File** > **New Solution**.</span></span>

  ![Nova Solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="6c186-168">Select **.NET Core** > **App** > **API** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="6c186-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Caixa de diálogo Novo Projeto do macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="6c186-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \* *.NET Core 3.1*.</span><span class="sxs-lookup"><span data-stu-id="6c186-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="6c186-171">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="6c186-173">Abra um terminal de comando na pasta do projeto e execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6c186-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="6c186-174">Testar a API</span><span class="sxs-lookup"><span data-stu-id="6c186-174">Test the API</span></span>

<span data-ttu-id="6c186-175">O modelo de projeto cria uma API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="6c186-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="6c186-176">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c186-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c186-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c186-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c186-178">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c186-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="6c186-179">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/WeatherForecast`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="6c186-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="6c186-180">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="6c186-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="6c186-181">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="6c186-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6c186-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c186-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6c186-183">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c186-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="6c186-184">Em um navegador, acesse a seguinte URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="6c186-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6c186-185">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c186-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6c186-186">Selecione **Executar** > **Iniciar com Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c186-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="6c186-187">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="6c186-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="6c186-188">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="6c186-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="6c186-189">Acrescente `/WeatherForecast` à URL (altere a URL para `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="6c186-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="6c186-190">Um JSON semelhante ao seguinte será retornado:</span><span class="sxs-lookup"><span data-stu-id="6c186-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="6c186-191">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="6c186-191">Add a model class</span></span>

<span data-ttu-id="6c186-192">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c186-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="6c186-193">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="6c186-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c186-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c186-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6c186-195">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="6c186-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="6c186-196">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="6c186-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="6c186-197">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6c186-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="6c186-198">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6c186-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="6c186-199">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="6c186-200">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6c186-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6c186-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c186-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6c186-202">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6c186-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="6c186-203">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6c186-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6c186-204">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c186-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6c186-205">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="6c186-205">Right-click the project.</span></span> <span data-ttu-id="6c186-206">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="6c186-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="6c186-207">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6c186-207">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="6c186-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span><span class="sxs-lookup"><span data-stu-id="6c186-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="6c186-210">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="6c186-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="6c186-211">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6c186-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="6c186-212">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="6c186-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="6c186-213">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="6c186-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="6c186-214">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="6c186-214">Add a database context</span></span>

<span data-ttu-id="6c186-215">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="6c186-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="6c186-216">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="6c186-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c186-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c186-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="6c186-218">Adicione Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="6c186-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="6c186-219">No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.</span><span class="sxs-lookup"><span data-stu-id="6c186-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="6c186-220">Selecione a guia **Procurar** e, em seguida, insira **Microsoft.EntityFrameworkCore.SqlServer** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="6c186-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="6c186-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span><span class="sxs-lookup"><span data-stu-id="6c186-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="6c186-222">Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="6c186-223">Use as instruções anteriores para adicionar o pacote do NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="6c186-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Gerenciador de pacotes do NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="6c186-225">Adicione o contexto de banco de dados TodoContext</span><span class="sxs-lookup"><span data-stu-id="6c186-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="6c186-226">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6c186-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="6c186-227">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6c186-228">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c186-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6c186-229">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6c186-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="6c186-230">Insira o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6c186-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="6c186-231">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="6c186-231">Register the database context</span></span>

<span data-ttu-id="6c186-232">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6c186-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="6c186-233">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="6c186-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="6c186-234">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="6c186-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="6c186-235">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="6c186-235">The preceding code:</span></span>

* <span data-ttu-id="6c186-236">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="6c186-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="6c186-237">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="6c186-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="6c186-238">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="6c186-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="6c186-239">Faça scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="6c186-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c186-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c186-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6c186-241">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="6c186-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="6c186-242">Select **Add** > **New Scaffolded Item**.</span><span class="sxs-lookup"><span data-stu-id="6c186-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="6c186-243">Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="6c186-244">Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework**:</span><span class="sxs-lookup"><span data-stu-id="6c186-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="6c186-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span><span class="sxs-lookup"><span data-stu-id="6c186-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="6c186-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span><span class="sxs-lookup"><span data-stu-id="6c186-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="6c186-247">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6c186-248">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c186-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="6c186-249">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6c186-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="6c186-250">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="6c186-250">The preceding commands:</span></span>

* <span data-ttu-id="6c186-251">Adicionam os pacotes do NuGet para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="6c186-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="6c186-252">Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="6c186-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="6c186-253">Fazem scaffold de `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="6c186-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="6c186-254">O código gerado:</span><span class="sxs-lookup"><span data-stu-id="6c186-254">The generated code:</span></span>

* <span data-ttu-id="6c186-255">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span><span class="sxs-lookup"><span data-stu-id="6c186-255">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="6c186-256">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="6c186-256">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="6c186-257">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="6c186-257">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="6c186-258">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6c186-258">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="6c186-259">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6c186-259">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="6c186-260">Examine o método criar do PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="6c186-260">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="6c186-261">Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="6c186-261">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="6c186-262">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span><span class="sxs-lookup"><span data-stu-id="6c186-262">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="6c186-263">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6c186-263">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="6c186-264">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="6c186-264">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="6c186-265">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="6c186-265">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="6c186-266">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="6c186-266">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="6c186-267">Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta.</span><span class="sxs-lookup"><span data-stu-id="6c186-267">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="6c186-268">O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="6c186-268">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="6c186-269">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="6c186-269">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="6c186-270">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="6c186-270">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="6c186-271">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="6c186-271">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="6c186-272">Instale o Postman</span><span class="sxs-lookup"><span data-stu-id="6c186-272">Install Postman</span></span>

<span data-ttu-id="6c186-273">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="6c186-273">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="6c186-274">Instale o [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="6c186-274">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="6c186-275">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="6c186-275">Start the web app.</span></span>
* <span data-ttu-id="6c186-276">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="6c186-276">Start Postman.</span></span>
* <span data-ttu-id="6c186-277">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="6c186-277">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="6c186-278">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span><span class="sxs-lookup"><span data-stu-id="6c186-278">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="6c186-279">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="6c186-279">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="6c186-280">Teste o PostTodoItem com o Postman</span><span class="sxs-lookup"><span data-stu-id="6c186-280">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="6c186-281">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="6c186-281">Create a new request.</span></span>
* <span data-ttu-id="6c186-282">Defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="6c186-282">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="6c186-283">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="6c186-283">Select the **Body** tab.</span></span>
* <span data-ttu-id="6c186-284">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="6c186-284">Select the **raw** radio button.</span></span>
* <span data-ttu-id="6c186-285">Defina o tipo como **JSON (aplicativo/json)** .</span><span class="sxs-lookup"><span data-stu-id="6c186-285">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="6c186-286">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="6c186-286">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="6c186-287">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-287">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="6c186-289">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="6c186-289">Test the location header URI</span></span>

* <span data-ttu-id="6c186-290">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="6c186-290">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="6c186-291">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="6c186-291">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="6c186-293">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="6c186-293">Set the method to GET.</span></span>
* <span data-ttu-id="6c186-294">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="6c186-294">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="6c186-295">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-295">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="6c186-296">Examine os métodos GET</span><span class="sxs-lookup"><span data-stu-id="6c186-296">Examine the GET methods</span></span>

<span data-ttu-id="6c186-297">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="6c186-297">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="6c186-298">Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman.</span><span class="sxs-lookup"><span data-stu-id="6c186-298">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="6c186-299">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="6c186-299">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="6c186-300">Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="6c186-300">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="6c186-301">Teste o GET com o Postman</span><span class="sxs-lookup"><span data-stu-id="6c186-301">Test Get with Postman</span></span>

* <span data-ttu-id="6c186-302">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="6c186-302">Create a new request.</span></span>
* <span data-ttu-id="6c186-303">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="6c186-303">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="6c186-304">Defina a URL de solicitação como `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="6c186-304">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="6c186-305">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="6c186-305">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="6c186-306">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="6c186-306">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="6c186-307">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-307">Select **Send**.</span></span>

<span data-ttu-id="6c186-308">Este aplicativo usa um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="6c186-308">This app uses an in-memory database.</span></span> <span data-ttu-id="6c186-309">Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado.</span><span class="sxs-lookup"><span data-stu-id="6c186-309">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="6c186-310">Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c186-310">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="6c186-311">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="6c186-311">Routing and URL paths</span></span>

<span data-ttu-id="6c186-312">O atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica um método que responde a uma solicitação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="6c186-312">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="6c186-313">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="6c186-313">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="6c186-314">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="6c186-314">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="6c186-315">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="6c186-315">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="6c186-316">Para esta amostra, o nome da classe do controlador é **TodoItems**Controller. Portanto, o nome do controlador é "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="6c186-316">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="6c186-317">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6c186-317">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="6c186-318">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="6c186-318">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="6c186-319">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="6c186-319">This sample doesn't use a template.</span></span> <span data-ttu-id="6c186-320">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="6c186-320">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="6c186-321">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="6c186-321">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="6c186-322">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span><span class="sxs-lookup"><span data-stu-id="6c186-322">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="6c186-323">Valores de retorno</span><span class="sxs-lookup"><span data-stu-id="6c186-323">Return values</span></span>

<span data-ttu-id="6c186-324">O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="6c186-324">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="6c186-325">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="6c186-325">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="6c186-326">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="6c186-326">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="6c186-327">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="6c186-327">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="6c186-328">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="6c186-328">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="6c186-329">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="6c186-329">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="6c186-330">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="6c186-330">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="6c186-331">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="6c186-331">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="6c186-332">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="6c186-332">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="6c186-333">O método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="6c186-333">The PutTodoItem method</span></span>

<span data-ttu-id="6c186-334">Examine o método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="6c186-334">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="6c186-335">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="6c186-335">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="6c186-336">A resposta é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="6c186-336">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="6c186-337">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="6c186-337">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="6c186-338">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="6c186-338">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="6c186-339">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c186-339">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="6c186-340">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="6c186-340">Test the PutTodoItem method</span></span>

<span data-ttu-id="6c186-341">This sample uses an in-memory database that must be initialized each time the app is started.</span><span class="sxs-lookup"><span data-stu-id="6c186-341">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="6c186-342">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="6c186-342">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="6c186-343">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="6c186-343">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="6c186-344">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="6c186-344">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="6c186-345">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="6c186-345">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="6c186-347">O método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="6c186-347">The DeleteTodoItem method</span></span>

<span data-ttu-id="6c186-348">Examine o método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="6c186-348">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="6c186-349">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="6c186-349">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="6c186-350">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="6c186-350">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="6c186-351">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="6c186-351">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="6c186-352">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="6c186-352">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="6c186-353">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-353">Select **Send**.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="6c186-354">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="6c186-354">Call the web API with JavaScript</span></span>

<span data-ttu-id="6c186-355">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="6c186-355">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6c186-356">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="6c186-356">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6c186-357">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="6c186-357">Create a web API project.</span></span>
> * <span data-ttu-id="6c186-358">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c186-358">Add a model class and a database context.</span></span>
> * <span data-ttu-id="6c186-359">Adicionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="6c186-359">Add a controller.</span></span>
> * <span data-ttu-id="6c186-360">Adicionar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="6c186-360">Add CRUD methods.</span></span>
> * <span data-ttu-id="6c186-361">Configurar o roteamento e caminhos de URL.</span><span class="sxs-lookup"><span data-stu-id="6c186-361">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="6c186-362">Especificar os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="6c186-362">Specify return values.</span></span>
> * <span data-ttu-id="6c186-363">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="6c186-363">Call the web API with Postman.</span></span>
> * <span data-ttu-id="6c186-364">Chamar a API Web com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6c186-364">Call the web API with JavaScript.</span></span>

<span data-ttu-id="6c186-365">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="6c186-365">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="6c186-366">{1&gt;Visão Geral&lt;1}</span><span class="sxs-lookup"><span data-stu-id="6c186-366">Overview</span></span>

<span data-ttu-id="6c186-367">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="6c186-367">This tutorial creates the following API:</span></span>

|<span data-ttu-id="6c186-368">API</span><span class="sxs-lookup"><span data-stu-id="6c186-368">API</span></span> | <span data-ttu-id="6c186-369">Descrição</span><span class="sxs-lookup"><span data-stu-id="6c186-369">Description</span></span> | <span data-ttu-id="6c186-370">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="6c186-370">Request body</span></span> | <span data-ttu-id="6c186-371">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="6c186-371">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="6c186-372">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="6c186-372">GET /api/TodoItems</span></span> | <span data-ttu-id="6c186-373">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6c186-373">Get all to-do items</span></span> | <span data-ttu-id="6c186-374">{1&gt;Nenhum&lt;1}</span><span class="sxs-lookup"><span data-stu-id="6c186-374">None</span></span> | <span data-ttu-id="6c186-375">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6c186-375">Array of to-do items</span></span>|
|<span data-ttu-id="6c186-376">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="6c186-376">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="6c186-377">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="6c186-377">Get an item by ID</span></span> | <span data-ttu-id="6c186-378">{1&gt;Nenhum&lt;1}</span><span class="sxs-lookup"><span data-stu-id="6c186-378">None</span></span> | <span data-ttu-id="6c186-379">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6c186-379">To-do item</span></span>|
|<span data-ttu-id="6c186-380">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="6c186-380">POST /api/TodoItems</span></span> | <span data-ttu-id="6c186-381">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="6c186-381">Add a new item</span></span> | <span data-ttu-id="6c186-382">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6c186-382">To-do item</span></span> | <span data-ttu-id="6c186-383">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6c186-383">To-do item</span></span> |
|<span data-ttu-id="6c186-384">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="6c186-384">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="6c186-385">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="6c186-385">Update an existing item &nbsp;</span></span> | <span data-ttu-id="6c186-386">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="6c186-386">To-do item</span></span> | <span data-ttu-id="6c186-387">{1&gt;Nenhum&lt;1}</span><span class="sxs-lookup"><span data-stu-id="6c186-387">None</span></span> |
|<span data-ttu-id="6c186-388">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="6c186-388">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="6c186-389">Delete an item &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="6c186-389">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="6c186-390">{1&gt;Nenhum&lt;1}</span><span class="sxs-lookup"><span data-stu-id="6c186-390">None</span></span> | <span data-ttu-id="6c186-391">{1&gt;Nenhum&lt;1}</span><span class="sxs-lookup"><span data-stu-id="6c186-391">None</span></span>|

<span data-ttu-id="6c186-392">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c186-392">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="6c186-398">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="6c186-398">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c186-399">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c186-399">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6c186-400">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c186-400">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6c186-401">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c186-401">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="6c186-402">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="6c186-402">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c186-403">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c186-403">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6c186-404">From the **File** menu, select **New** > **Project**.</span><span class="sxs-lookup"><span data-stu-id="6c186-404">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="6c186-405">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="6c186-405">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="6c186-406">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-406">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="6c186-407">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 2.2** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="6c186-407">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="6c186-408">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-408">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="6c186-409">**Não** selecione **Habilitar Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="6c186-409">**Don't** select **Enable Docker Support**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6c186-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c186-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6c186-412">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6c186-412">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="6c186-413">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="6c186-413">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="6c186-414">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="6c186-414">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="6c186-415">Esses comandos criam um novo projeto de API Web e abrem uma nova instância do Visual Studio Code na nova pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="6c186-415">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="6c186-416">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="6c186-416">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6c186-417">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c186-417">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6c186-418">Select **File** > **New Solution**.</span><span class="sxs-lookup"><span data-stu-id="6c186-418">Select **File** > **New Solution**.</span></span>

  ![Nova Solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="6c186-420">Select **.NET Core** > **App** > **API** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="6c186-420">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Caixa de diálogo Novo Projeto do macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="6c186-422">Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, aceite a **Estrutura de Destino** padrão \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="6c186-422">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="6c186-423">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-423">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="6c186-425">Testar a API</span><span class="sxs-lookup"><span data-stu-id="6c186-425">Test the API</span></span>

<span data-ttu-id="6c186-426">O modelo de projeto cria uma API `values`.</span><span class="sxs-lookup"><span data-stu-id="6c186-426">The project template creates a `values` API.</span></span> <span data-ttu-id="6c186-427">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c186-427">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c186-428">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c186-428">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6c186-429">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c186-429">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="6c186-430">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/api/values`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="6c186-430">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="6c186-431">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="6c186-431">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="6c186-432">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="6c186-432">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6c186-433">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c186-433">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6c186-434">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c186-434">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="6c186-435">Em um navegador, acesse a seguinte URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="6c186-435">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6c186-436">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c186-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6c186-437">Selecione **Executar** > **Iniciar com Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c186-437">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="6c186-438">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="6c186-438">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="6c186-439">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="6c186-439">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="6c186-440">Acrescente `/api/values` à URL (altere a URL para `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="6c186-440">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="6c186-441">O seguinte JSON é retornado:</span><span class="sxs-lookup"><span data-stu-id="6c186-441">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="6c186-442">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="6c186-442">Add a model class</span></span>

<span data-ttu-id="6c186-443">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="6c186-443">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="6c186-444">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="6c186-444">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c186-445">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c186-445">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6c186-446">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="6c186-446">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="6c186-447">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="6c186-447">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="6c186-448">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6c186-448">Name the folder *Models*.</span></span>

* <span data-ttu-id="6c186-449">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6c186-449">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="6c186-450">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-450">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="6c186-451">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6c186-451">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6c186-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6c186-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6c186-453">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6c186-453">Add a folder named *Models*.</span></span>

* <span data-ttu-id="6c186-454">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6c186-454">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="6c186-455">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c186-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6c186-456">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="6c186-456">Right-click the project.</span></span> <span data-ttu-id="6c186-457">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="6c186-457">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="6c186-458">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6c186-458">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="6c186-460">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span><span class="sxs-lookup"><span data-stu-id="6c186-460">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="6c186-461">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="6c186-461">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="6c186-462">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6c186-462">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="6c186-463">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="6c186-463">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="6c186-464">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="6c186-464">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="6c186-465">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="6c186-465">Add a database context</span></span>

<span data-ttu-id="6c186-466">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="6c186-466">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="6c186-467">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="6c186-467">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c186-468">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c186-468">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6c186-469">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="6c186-469">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="6c186-470">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-470">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6c186-471">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c186-471">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6c186-472">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="6c186-472">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="6c186-473">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6c186-473">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="6c186-474">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="6c186-474">Register the database context</span></span>

<span data-ttu-id="6c186-475">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6c186-475">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="6c186-476">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="6c186-476">The container provides the service to controllers.</span></span>

<span data-ttu-id="6c186-477">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="6c186-477">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="6c186-478">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="6c186-478">The preceding code:</span></span>

* <span data-ttu-id="6c186-479">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="6c186-479">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="6c186-480">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="6c186-480">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="6c186-481">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="6c186-481">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="6c186-482">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="6c186-482">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c186-483">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c186-483">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6c186-484">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="6c186-484">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="6c186-485">Select **Add** > **New Item**.</span><span class="sxs-lookup"><span data-stu-id="6c186-485">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="6c186-486">Na caixa de diálogo **Adicionar Novo Item**, selecione o modelo **Classe do Controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="6c186-486">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="6c186-487">Dê à classe o nome *TodoController* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-487">Name the class *TodoController*, and select **Add**.</span></span>

  ![Caixa de diálogo Adicionar Novo Item com o controlador na caixa de pesquisa e o controlador da API Web selecionados](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6c186-489">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c186-489">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6c186-490">Na pasta *Controllers*, crie uma classe chamada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="6c186-490">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="6c186-491">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6c186-491">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="6c186-492">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="6c186-492">The preceding code:</span></span>

* <span data-ttu-id="6c186-493">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="6c186-493">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="6c186-494">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span><span class="sxs-lookup"><span data-stu-id="6c186-494">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="6c186-495">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="6c186-495">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="6c186-496">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="6c186-496">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="6c186-497">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6c186-497">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="6c186-498">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="6c186-498">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="6c186-499">Adiciona um item chamado `Item1` ao banco de dados se o banco de dados está vazio.</span><span class="sxs-lookup"><span data-stu-id="6c186-499">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="6c186-500">Esse código está no construtor, de modo que ele seja executado sempre que há uma nova solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6c186-500">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="6c186-501">Se você excluir todos os itens, o construtor criará `Item1` novamente na próxima vez que um método de API for chamado.</span><span class="sxs-lookup"><span data-stu-id="6c186-501">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="6c186-502">Portanto, pode parecer que a exclusão não funcionou quando ela realmente funcionou.</span><span class="sxs-lookup"><span data-stu-id="6c186-502">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="6c186-503">Adicionar métodos Get</span><span class="sxs-lookup"><span data-stu-id="6c186-503">Add Get methods</span></span>

<span data-ttu-id="6c186-504">Para fornecer uma API que recupera itens pendentes, adicione os seguintes métodos à classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="6c186-504">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="6c186-505">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="6c186-505">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="6c186-506">Interrompa o aplicativo se ele ainda estiver em execução.</span><span class="sxs-lookup"><span data-stu-id="6c186-506">Stop the app if it's still running.</span></span> <span data-ttu-id="6c186-507">Em seguida, execute-o novamente para incluir as alterações mais recentes.</span><span class="sxs-lookup"><span data-stu-id="6c186-507">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="6c186-508">Teste o aplicativo chamando os dois pontos de extremidade em um navegador.</span><span class="sxs-lookup"><span data-stu-id="6c186-508">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="6c186-509">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="6c186-509">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="6c186-510">A seguinte resposta HTTP é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="6c186-510">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="6c186-511">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="6c186-511">Routing and URL paths</span></span>

<span data-ttu-id="6c186-512">O atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica um método que responde a uma solicitação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="6c186-512">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="6c186-513">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="6c186-513">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="6c186-514">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="6c186-514">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="6c186-515">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="6c186-515">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="6c186-516">Para esta amostra, o nome da classe do controlador é **Todo**Controller e, portanto, o nome do controlador é "todo".</span><span class="sxs-lookup"><span data-stu-id="6c186-516">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="6c186-517">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="6c186-517">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="6c186-518">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="6c186-518">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="6c186-519">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="6c186-519">This sample doesn't use a template.</span></span> <span data-ttu-id="6c186-520">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="6c186-520">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="6c186-521">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="6c186-521">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="6c186-522">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="6c186-522">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="6c186-523">Valores de retorno</span><span class="sxs-lookup"><span data-stu-id="6c186-523">Return values</span></span>

<span data-ttu-id="6c186-524">O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="6c186-524">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="6c186-525">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="6c186-525">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="6c186-526">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="6c186-526">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="6c186-527">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="6c186-527">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="6c186-528">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="6c186-528">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="6c186-529">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="6c186-529">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="6c186-530">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="6c186-530">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="6c186-531">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="6c186-531">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="6c186-532">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="6c186-532">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="6c186-533">Testar o método GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="6c186-533">Test the GetTodoItems method</span></span>

<span data-ttu-id="6c186-534">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="6c186-534">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="6c186-535">Install [Postman](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="6c186-535">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="6c186-536">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="6c186-536">Start the web app.</span></span>
* <span data-ttu-id="6c186-537">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="6c186-537">Start Postman.</span></span>
* <span data-ttu-id="6c186-538">Disable **SSL certificate verification**.</span><span class="sxs-lookup"><span data-stu-id="6c186-538">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6c186-539">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c186-539">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6c186-540">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span><span class="sxs-lookup"><span data-stu-id="6c186-540">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="6c186-541">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="6c186-541">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6c186-542">Em **Postman** > **Preferências** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="6c186-542">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="6c186-543">Como alternativa, selecione a chave inglesa, selecione **Configurações** e desabilite a verificação do certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="6c186-543">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="6c186-544">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="6c186-544">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="6c186-545">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="6c186-545">Create a new request.</span></span>
  * <span data-ttu-id="6c186-546">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="6c186-546">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="6c186-547">Defina a URL de solicitação como `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="6c186-547">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="6c186-548">Por exemplo, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="6c186-548">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="6c186-549">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="6c186-549">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="6c186-550">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-550">Select **Send**.</span></span>

![Postman com solicitação GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="6c186-552">Adicionar um método Create</span><span class="sxs-lookup"><span data-stu-id="6c186-552">Add a Create method</span></span>

<span data-ttu-id="6c186-553">Adicione o seguinte método `PostTodoItem` dentro de *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="6c186-553">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="6c186-554">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span><span class="sxs-lookup"><span data-stu-id="6c186-554">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="6c186-555">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6c186-555">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="6c186-556">O método `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="6c186-556">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="6c186-557">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="6c186-557">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="6c186-558">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="6c186-558">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="6c186-559">Adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="6c186-559">Adds a `Location` header to the response.</span></span> <span data-ttu-id="6c186-560">O cabeçalho `Location` especifica o URI do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="6c186-560">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="6c186-561">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="6c186-561">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="6c186-562">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="6c186-562">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="6c186-563">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="6c186-563">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="6c186-564">Testar o método PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="6c186-564">Test the PostTodoItem method</span></span>

* <span data-ttu-id="6c186-565">Crie o projeto.</span><span class="sxs-lookup"><span data-stu-id="6c186-565">Build the project.</span></span>
* <span data-ttu-id="6c186-566">No Postman, defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="6c186-566">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="6c186-567">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="6c186-567">Select the **Body** tab.</span></span>
* <span data-ttu-id="6c186-568">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="6c186-568">Select the **raw** radio button.</span></span>
* <span data-ttu-id="6c186-569">Defina o tipo como **JSON (aplicativo/json)** .</span><span class="sxs-lookup"><span data-stu-id="6c186-569">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="6c186-570">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="6c186-570">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="6c186-571">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-571">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

  <span data-ttu-id="6c186-573">Se você receber um erro 405 Método Não Permitido, provavelmente, esse será o resultado da não compilação do projeto após a adição do método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="6c186-573">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="6c186-574">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="6c186-574">Test the location header URI</span></span>

* <span data-ttu-id="6c186-575">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="6c186-575">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="6c186-576">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="6c186-576">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="6c186-578">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="6c186-578">Set the method to GET.</span></span>
* <span data-ttu-id="6c186-579">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="6c186-579">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="6c186-580">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-580">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="6c186-581">Adicionar um método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="6c186-581">Add a PutTodoItem method</span></span>

<span data-ttu-id="6c186-582">Adicione o seguinte método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="6c186-582">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="6c186-583">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="6c186-583">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="6c186-584">A resposta é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="6c186-584">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="6c186-585">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="6c186-585">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="6c186-586">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="6c186-586">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="6c186-587">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="6c186-587">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="6c186-588">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="6c186-588">Test the PutTodoItem method</span></span>

<span data-ttu-id="6c186-589">This sample uses an in-memory database that must be initialized each time the app is started.</span><span class="sxs-lookup"><span data-stu-id="6c186-589">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="6c186-590">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="6c186-590">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="6c186-591">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="6c186-591">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="6c186-592">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="6c186-592">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="6c186-593">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="6c186-593">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="6c186-595">Adicionar um método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="6c186-595">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="6c186-596">Adicione o seguinte método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="6c186-596">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="6c186-597">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="6c186-597">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="6c186-598">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="6c186-598">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="6c186-599">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="6c186-599">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="6c186-600">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="6c186-600">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="6c186-601">Set the URI of the object to delete (for example `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="6c186-601">Set the URI of the object to delete (for example `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="6c186-602">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="6c186-602">Select **Send**.</span></span>

<span data-ttu-id="6c186-603">O aplicativo de exemplo permite que você exclua todos os itens.</span><span class="sxs-lookup"><span data-stu-id="6c186-603">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="6c186-604">No entanto, quando o último item é excluído, um novo é criado pelo construtor de classe de modelo na próxima vez que a API for chamada.</span><span class="sxs-lookup"><span data-stu-id="6c186-604">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="6c186-605">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="6c186-605">Call the web API with JavaScript</span></span>

<span data-ttu-id="6c186-606">Nesta seção, é adicionada uma página HTML que usa o JavaScript para chamar a API Web.</span><span class="sxs-lookup"><span data-stu-id="6c186-606">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="6c186-607">jQuery initiates the request.</span><span class="sxs-lookup"><span data-stu-id="6c186-607">jQuery initiates the request.</span></span> <span data-ttu-id="6c186-608">O JavaScript atualiza a página com os detalhes da resposta da API Web.</span><span class="sxs-lookup"><span data-stu-id="6c186-608">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="6c186-609">Configurar o aplicativo para [servir arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) atualizando *Startup.cs* com o código realçado a seguir:</span><span class="sxs-lookup"><span data-stu-id="6c186-609">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="6c186-610">Crie uma pasta *wwwroot* no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="6c186-610">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="6c186-611">Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="6c186-611">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="6c186-612">Substitua seu conteúdo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="6c186-612">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="6c186-613">Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="6c186-613">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="6c186-614">Substitua seu conteúdo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="6c186-614">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="6c186-615">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="6c186-615">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="6c186-616">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6c186-616">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="6c186-617">Remova a propriedade `launchUrl` para forçar o aplicativo a ser aberto em *index.html*, o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="6c186-617">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="6c186-618">Esta amostra chama todos os métodos CRUD da API Web.</span><span class="sxs-lookup"><span data-stu-id="6c186-618">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="6c186-619">Veja a seguir explicações das chamadas à API.</span><span class="sxs-lookup"><span data-stu-id="6c186-619">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="6c186-620">Obter uma lista de itens pendentes</span><span class="sxs-lookup"><span data-stu-id="6c186-620">Get a list of to-do items</span></span>

<span data-ttu-id="6c186-621">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span><span class="sxs-lookup"><span data-stu-id="6c186-621">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="6c186-622">A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="6c186-622">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="6c186-623">No retorno de chamada, o DOM é atualizado com as informações do item pendente.</span><span class="sxs-lookup"><span data-stu-id="6c186-623">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="6c186-624">Adicionar um item pendente</span><span class="sxs-lookup"><span data-stu-id="6c186-624">Add a to-do item</span></span>

<span data-ttu-id="6c186-625">jQuery sends an HTTP POST request with the to-do item in the request body.</span><span class="sxs-lookup"><span data-stu-id="6c186-625">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="6c186-626">As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado.</span><span class="sxs-lookup"><span data-stu-id="6c186-626">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="6c186-627">O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="6c186-627">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="6c186-628">Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="6c186-628">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="6c186-629">Atualizar um item pendente</span><span class="sxs-lookup"><span data-stu-id="6c186-629">Update a to-do item</span></span>

<span data-ttu-id="6c186-630">A atualização de um item pendente é semelhante à adição de um.</span><span class="sxs-lookup"><span data-stu-id="6c186-630">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="6c186-631">A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.</span><span class="sxs-lookup"><span data-stu-id="6c186-631">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="6c186-632">Excluir um item pendente</span><span class="sxs-lookup"><span data-stu-id="6c186-632">Delete a to-do item</span></span>

<span data-ttu-id="6c186-633">A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="6c186-633">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="6c186-634">Add authentication support to a web API</span><span class="sxs-lookup"><span data-stu-id="6c186-634">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="6c186-635">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6c186-635">Additional resources</span></span>

<span data-ttu-id="6c186-636">[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="6c186-636">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="6c186-637">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="6c186-637">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="6c186-638">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="6c186-638">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="6c186-639">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="6c186-639">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
