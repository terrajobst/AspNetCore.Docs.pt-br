---
title: 'Tutorial: Criar uma API Web com o ASP.NET Core'
author: rick-anderson
description: Saiba como criar uma API Web com o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 5e5215f246c6c7a805a4c99f485d51a2fb3c712d
ms.sourcegitcommit: cf9ffcce4fe0b69fe795aae9ae06e99fdb18bdfc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71306673"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="db41e-103">Tutorial: Criar uma API Web com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="db41e-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="db41e-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="db41e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="db41e-105">Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="db41e-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="db41e-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="db41e-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="db41e-107">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="db41e-107">Create a web API project.</span></span>
> * <span data-ttu-id="db41e-108">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="db41e-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="db41e-109">Faça scaffold de um controlador com métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="db41e-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="db41e-110">Configure o roteamento, os caminhos de URL e os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="db41e-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="db41e-111">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="db41e-111">Call the web API with Postman.</span></span>

<span data-ttu-id="db41e-112">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="db41e-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="db41e-113">Visão geral</span><span class="sxs-lookup"><span data-stu-id="db41e-113">Overview</span></span>

<span data-ttu-id="db41e-114">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="db41e-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="db41e-115">API</span><span class="sxs-lookup"><span data-stu-id="db41e-115">API</span></span> | <span data-ttu-id="db41e-116">Descrição</span><span class="sxs-lookup"><span data-stu-id="db41e-116">Description</span></span> | <span data-ttu-id="db41e-117">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="db41e-117">Request body</span></span> | <span data-ttu-id="db41e-118">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="db41e-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="db41e-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="db41e-119">GET /api/TodoItems</span></span> | <span data-ttu-id="db41e-120">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="db41e-120">Get all to-do items</span></span> | <span data-ttu-id="db41e-121">Nenhum</span><span class="sxs-lookup"><span data-stu-id="db41e-121">None</span></span> | <span data-ttu-id="db41e-122">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="db41e-122">Array of to-do items</span></span>|
|<span data-ttu-id="db41e-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="db41e-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="db41e-124">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="db41e-124">Get an item by ID</span></span> | <span data-ttu-id="db41e-125">Nenhum</span><span class="sxs-lookup"><span data-stu-id="db41e-125">None</span></span> | <span data-ttu-id="db41e-126">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="db41e-126">To-do item</span></span>|
|<span data-ttu-id="db41e-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="db41e-127">POST /api/TodoItems</span></span> | <span data-ttu-id="db41e-128">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="db41e-128">Add a new item</span></span> | <span data-ttu-id="db41e-129">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="db41e-129">To-do item</span></span> | <span data-ttu-id="db41e-130">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="db41e-130">To-do item</span></span> |
|<span data-ttu-id="db41e-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="db41e-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="db41e-132">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="db41e-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="db41e-133">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="db41e-133">To-do item</span></span> | <span data-ttu-id="db41e-134">Nenhum</span><span class="sxs-lookup"><span data-stu-id="db41e-134">None</span></span> |
|<span data-ttu-id="db41e-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="db41e-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="db41e-136">Excluir um item &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="db41e-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="db41e-137">Nenhum</span><span class="sxs-lookup"><span data-stu-id="db41e-137">None</span></span> | <span data-ttu-id="db41e-138">Nenhum</span><span class="sxs-lookup"><span data-stu-id="db41e-138">None</span></span>|

<span data-ttu-id="db41e-139">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="db41e-139">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="db41e-145">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="db41e-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="db41e-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db41e-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="db41e-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="db41e-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="db41e-148">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="db41e-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="db41e-149">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="db41e-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="db41e-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db41e-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="db41e-151">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="db41e-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="db41e-152">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="db41e-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="db41e-153">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="db41e-154">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 3.0** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="db41e-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="db41e-155">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-155">Select the **API** template and click **Create**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="db41e-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="db41e-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="db41e-158">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="db41e-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="db41e-159">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="db41e-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="db41e-160">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="db41e-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="db41e-161">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="db41e-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="db41e-162">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="db41e-162">The preceding commands:</span></span>

  * <span data-ttu-id="db41e-163">Cria um novo projeto de API Web e o abre no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="db41e-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="db41e-164">Adiciona os pacotes do NuGet que são exigidos na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="db41e-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="db41e-165">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="db41e-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="db41e-166">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="db41e-166">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="db41e-168">Selecione **.NET Core** > **Aplicativo** > **API** > **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="db41e-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="db41e-170">Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, selecione a **Estrutura de Destino** do \* *.NET Core 3.0*.</span><span class="sxs-lookup"><span data-stu-id="db41e-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="db41e-171">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="db41e-173">Abra um terminal de comando na pasta do projeto e execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="db41e-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="db41e-174">Testar a API</span><span class="sxs-lookup"><span data-stu-id="db41e-174">Test the API</span></span>

<span data-ttu-id="db41e-175">O modelo de projeto cria uma API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="db41e-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="db41e-176">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="db41e-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="db41e-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db41e-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="db41e-178">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="db41e-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="db41e-179">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/WeatherForecast`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="db41e-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="db41e-180">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="db41e-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="db41e-181">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="db41e-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="db41e-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="db41e-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="db41e-183">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="db41e-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="db41e-184">Em um navegador, acesse a seguinte URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="db41e-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="db41e-185">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="db41e-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="db41e-186">Selecione **Executar** > **Iniciar com Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="db41e-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="db41e-187">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="db41e-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="db41e-188">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="db41e-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="db41e-189">Acrescente `/WeatherForecast` à URL (altere a URL para `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="db41e-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="db41e-190">Um JSON semelhante ao seguinte será retornado:</span><span class="sxs-lookup"><span data-stu-id="db41e-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="db41e-191">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="db41e-191">Add a model class</span></span>

<span data-ttu-id="db41e-192">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="db41e-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="db41e-193">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="db41e-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="db41e-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db41e-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="db41e-195">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="db41e-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="db41e-196">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="db41e-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="db41e-197">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="db41e-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="db41e-198">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="db41e-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="db41e-199">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="db41e-200">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="db41e-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="db41e-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="db41e-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="db41e-202">Adicione uma pasta chamada *Models*.</span><span class="sxs-lookup"><span data-stu-id="db41e-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="db41e-203">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="db41e-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="db41e-204">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="db41e-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="db41e-205">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="db41e-205">Right-click the project.</span></span> <span data-ttu-id="db41e-206">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="db41e-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="db41e-207">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="db41e-207">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="db41e-209">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="db41e-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="db41e-210">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="db41e-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="db41e-211">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="db41e-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="db41e-212">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="db41e-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="db41e-213">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="db41e-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="db41e-214">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="db41e-214">Add a database context</span></span>

<span data-ttu-id="db41e-215">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="db41e-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="db41e-216">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="db41e-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="db41e-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db41e-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="db41e-218">Adicione Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="db41e-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="db41e-219">No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.</span><span class="sxs-lookup"><span data-stu-id="db41e-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="db41e-220">Selecione a guia **Procurar** e, em seguida, insira **Microsoft.EntityFrameworkCore.SqlServer** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="db41e-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="db41e-221">Selecione **Microsoft. EntityFrameworkCore. SqlServer** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="db41e-221">Select  **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="db41e-222">Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="db41e-223">Use as instruções anteriores para adicionar o pacote do NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="db41e-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Gerenciador de pacotes do NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="db41e-225">Adicione o contexto de banco de dados TodoContext</span><span class="sxs-lookup"><span data-stu-id="db41e-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="db41e-226">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="db41e-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="db41e-227">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="db41e-228">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="db41e-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="db41e-229">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="db41e-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="db41e-230">Insira o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="db41e-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="db41e-231">Registrar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="db41e-231">Register the database context</span></span>

<span data-ttu-id="db41e-232">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="db41e-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="db41e-233">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="db41e-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="db41e-234">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="db41e-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="db41e-235">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="db41e-235">The preceding code:</span></span>

* <span data-ttu-id="db41e-236">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="db41e-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="db41e-237">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="db41e-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="db41e-238">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="db41e-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="db41e-239">Faça scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="db41e-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="db41e-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db41e-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="db41e-241">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="db41e-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="db41e-242">Selecione **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="db41e-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="db41e-243">Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="db41e-244">Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework**:</span><span class="sxs-lookup"><span data-stu-id="db41e-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="db41e-245">Selecione **TodoItem (TodoAPI.Models**) na **classe Modelo**.</span><span class="sxs-lookup"><span data-stu-id="db41e-245">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="db41e-246">Selecione **TodoContext (TodoAPI.Models**) na **classe Contexto de Dados**.</span><span class="sxs-lookup"><span data-stu-id="db41e-246">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="db41e-247">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="db41e-247">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="db41e-248">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="db41e-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="db41e-249">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="db41e-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="db41e-250">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="db41e-250">The preceding commands:</span></span>

* <span data-ttu-id="db41e-251">Adicionam os pacotes do NuGet para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="db41e-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="db41e-252">Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="db41e-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="db41e-253">Fazem scaffold de `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="db41e-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="db41e-254">O código gerado:</span><span class="sxs-lookup"><span data-stu-id="db41e-254">The generated code:</span></span>

* <span data-ttu-id="db41e-255">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="db41e-255">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="db41e-256">Decora a classe com o atributo [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="db41e-256">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="db41e-257">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="db41e-257">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="db41e-258">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="db41e-258">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="db41e-259">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="db41e-259">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="db41e-260">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="db41e-260">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="db41e-261">Examine o método criar do PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="db41e-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="db41e-262">Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="db41e-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="db41e-263">O código anterior é um método HTTP POST, conforme indicado pelo atributo [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="db41e-263">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="db41e-264">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="db41e-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="db41e-265">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="db41e-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="db41e-266">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="db41e-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="db41e-267">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="db41e-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="db41e-268">Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta.</span><span class="sxs-lookup"><span data-stu-id="db41e-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="db41e-269">O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="db41e-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="db41e-270">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="db41e-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="db41e-271">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="db41e-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="db41e-272">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="db41e-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="db41e-273">Instale o Postman</span><span class="sxs-lookup"><span data-stu-id="db41e-273">Install Postman</span></span>

<span data-ttu-id="db41e-274">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="db41e-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="db41e-275">Instale o [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="db41e-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="db41e-276">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="db41e-276">Start the web app.</span></span>
* <span data-ttu-id="db41e-277">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="db41e-277">Start Postman.</span></span>
* <span data-ttu-id="db41e-278">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="db41e-278">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="db41e-279">Em **Arquivo > Configurações** (guia \**Geral*), desabilite **Verificação do certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="db41e-279">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="db41e-280">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="db41e-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="db41e-281">Teste o PostTodoItem com o Postman</span><span class="sxs-lookup"><span data-stu-id="db41e-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="db41e-282">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="db41e-282">Create a new request.</span></span>
* <span data-ttu-id="db41e-283">Defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="db41e-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="db41e-284">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="db41e-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="db41e-285">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="db41e-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="db41e-286">Defina o tipo como **JSON (aplicativo/json)** .</span><span class="sxs-lookup"><span data-stu-id="db41e-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="db41e-287">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="db41e-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="db41e-288">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-288">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="db41e-290">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="db41e-290">Test the location header URI</span></span>

* <span data-ttu-id="db41e-291">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="db41e-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="db41e-292">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="db41e-292">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="db41e-294">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="db41e-294">Set the method to GET.</span></span>
* <span data-ttu-id="db41e-295">Cole o URI (por exemplo, `https://localhost:5001/api/TodoItems/1`)</span><span class="sxs-lookup"><span data-stu-id="db41e-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="db41e-296">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="db41e-297">Examine os métodos GET</span><span class="sxs-lookup"><span data-stu-id="db41e-297">Examine the GET methods</span></span>

<span data-ttu-id="db41e-298">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="db41e-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="db41e-299">Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman.</span><span class="sxs-lookup"><span data-stu-id="db41e-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="db41e-300">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="db41e-300">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="db41e-301">Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="db41e-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="db41e-302">Teste o GET com o Postman</span><span class="sxs-lookup"><span data-stu-id="db41e-302">Test Get with Postman</span></span>

* <span data-ttu-id="db41e-303">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="db41e-303">Create a new request.</span></span>
* <span data-ttu-id="db41e-304">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="db41e-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="db41e-305">Defina a URL de solicitação como `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="db41e-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="db41e-306">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="db41e-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="db41e-307">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="db41e-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="db41e-308">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-308">Select **Send**.</span></span>

<span data-ttu-id="db41e-309">Este aplicativo usa um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="db41e-309">This app uses an in-memory database.</span></span> <span data-ttu-id="db41e-310">Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado.</span><span class="sxs-lookup"><span data-stu-id="db41e-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="db41e-311">Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="db41e-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="db41e-312">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="db41e-312">Routing and URL paths</span></span>

<span data-ttu-id="db41e-313">O atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica um método que responde a uma solicitação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="db41e-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="db41e-314">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="db41e-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="db41e-315">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="db41e-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="db41e-316">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="db41e-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="db41e-317">Para esta amostra, o nome da classe do controlador é **TodoItems**Controller. Portanto, o nome do controlador é "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="db41e-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="db41e-318">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="db41e-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="db41e-319">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="db41e-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="db41e-320">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="db41e-320">This sample doesn't use a template.</span></span> <span data-ttu-id="db41e-321">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="db41e-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="db41e-322">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="db41e-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="db41e-323">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="db41e-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="db41e-324">Valores de retorno</span><span class="sxs-lookup"><span data-stu-id="db41e-324">Return values</span></span>

<span data-ttu-id="db41e-325">O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="db41e-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="db41e-326">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="db41e-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="db41e-327">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="db41e-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="db41e-328">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="db41e-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="db41e-329">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="db41e-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="db41e-330">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="db41e-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="db41e-331">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="db41e-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="db41e-332">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="db41e-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="db41e-333">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="db41e-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="db41e-334">O método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="db41e-334">The PutTodoItem method</span></span>

<span data-ttu-id="db41e-335">Examine o método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="db41e-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="db41e-336">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="db41e-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="db41e-337">A resposta é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="db41e-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="db41e-338">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="db41e-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="db41e-339">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="db41e-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="db41e-340">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="db41e-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="db41e-341">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="db41e-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="db41e-342">Este exemplo usa um banco de dados em memória que deverá ser iniciado sempre que o aplicativo for iniciado.</span><span class="sxs-lookup"><span data-stu-id="db41e-342">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="db41e-343">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="db41e-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="db41e-344">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="db41e-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="db41e-345">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="db41e-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="db41e-346">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="db41e-346">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="db41e-348">O método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="db41e-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="db41e-349">Examine o método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="db41e-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="db41e-350">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="db41e-350">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="db41e-351">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="db41e-351">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="db41e-352">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="db41e-352">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="db41e-353">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="db41e-353">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="db41e-354">Defina o URI do objeto a ser excluído, por exemplo, `https://localhost:5001/api/TodoItems/1`</span><span class="sxs-lookup"><span data-stu-id="db41e-354">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="db41e-355">Selecione **Enviar**</span><span class="sxs-lookup"><span data-stu-id="db41e-355">Select **Send**</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="db41e-356">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="db41e-356">Call the web API with JavaScript</span></span>

<span data-ttu-id="db41e-357">Consulte [o tutorial: Chamar uma API Web do ASP.NET Core com o JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="db41e-357">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="db41e-358">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="db41e-358">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="db41e-359">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="db41e-359">Create a web API project.</span></span>
> * <span data-ttu-id="db41e-360">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="db41e-360">Add a model class and a database context.</span></span>
> * <span data-ttu-id="db41e-361">Adicionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="db41e-361">Add a controller.</span></span>
> * <span data-ttu-id="db41e-362">Adicionar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="db41e-362">Add CRUD methods.</span></span>
> * <span data-ttu-id="db41e-363">Configurar o roteamento e caminhos de URL.</span><span class="sxs-lookup"><span data-stu-id="db41e-363">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="db41e-364">Especificar os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="db41e-364">Specify return values.</span></span>
> * <span data-ttu-id="db41e-365">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="db41e-365">Call the web API with Postman.</span></span>
> * <span data-ttu-id="db41e-366">Chamar a API Web com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="db41e-366">Call the web API with JavaScript.</span></span>

<span data-ttu-id="db41e-367">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="db41e-367">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="db41e-368">Visão geral</span><span class="sxs-lookup"><span data-stu-id="db41e-368">Overview</span></span>

<span data-ttu-id="db41e-369">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="db41e-369">This tutorial creates the following API:</span></span>

|<span data-ttu-id="db41e-370">API</span><span class="sxs-lookup"><span data-stu-id="db41e-370">API</span></span> | <span data-ttu-id="db41e-371">Descrição</span><span class="sxs-lookup"><span data-stu-id="db41e-371">Description</span></span> | <span data-ttu-id="db41e-372">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="db41e-372">Request body</span></span> | <span data-ttu-id="db41e-373">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="db41e-373">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="db41e-374">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="db41e-374">GET /api/TodoItems</span></span> | <span data-ttu-id="db41e-375">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="db41e-375">Get all to-do items</span></span> | <span data-ttu-id="db41e-376">Nenhum</span><span class="sxs-lookup"><span data-stu-id="db41e-376">None</span></span> | <span data-ttu-id="db41e-377">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="db41e-377">Array of to-do items</span></span>|
|<span data-ttu-id="db41e-378">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="db41e-378">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="db41e-379">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="db41e-379">Get an item by ID</span></span> | <span data-ttu-id="db41e-380">Nenhum</span><span class="sxs-lookup"><span data-stu-id="db41e-380">None</span></span> | <span data-ttu-id="db41e-381">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="db41e-381">To-do item</span></span>|
|<span data-ttu-id="db41e-382">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="db41e-382">POST /api/TodoItems</span></span> | <span data-ttu-id="db41e-383">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="db41e-383">Add a new item</span></span> | <span data-ttu-id="db41e-384">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="db41e-384">To-do item</span></span> | <span data-ttu-id="db41e-385">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="db41e-385">To-do item</span></span> |
|<span data-ttu-id="db41e-386">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="db41e-386">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="db41e-387">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="db41e-387">Update an existing item &nbsp;</span></span> | <span data-ttu-id="db41e-388">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="db41e-388">To-do item</span></span> | <span data-ttu-id="db41e-389">Nenhum</span><span class="sxs-lookup"><span data-stu-id="db41e-389">None</span></span> |
|<span data-ttu-id="db41e-390">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="db41e-390">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="db41e-391">Excluir um item &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="db41e-391">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="db41e-392">Nenhum</span><span class="sxs-lookup"><span data-stu-id="db41e-392">None</span></span> | <span data-ttu-id="db41e-393">Nenhum</span><span class="sxs-lookup"><span data-stu-id="db41e-393">None</span></span>|

<span data-ttu-id="db41e-394">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="db41e-394">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="db41e-400">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="db41e-400">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="db41e-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db41e-401">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="db41e-402">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="db41e-402">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="db41e-403">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="db41e-403">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="db41e-404">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="db41e-404">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="db41e-405">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db41e-405">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="db41e-406">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="db41e-406">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="db41e-407">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="db41e-407">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="db41e-408">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-408">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="db41e-409">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 2.2** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="db41e-409">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="db41e-410">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-410">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="db41e-411">**Não** selecione **Habilitar Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="db41e-411">**Don't** select **Enable Docker Support**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="db41e-413">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="db41e-413">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="db41e-414">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="db41e-414">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="db41e-415">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="db41e-415">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="db41e-416">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="db41e-416">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="db41e-417">Esses comandos criam um novo projeto de API Web e abrem uma nova instância do Visual Studio Code na nova pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="db41e-417">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="db41e-418">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="db41e-418">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="db41e-419">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="db41e-419">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="db41e-420">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="db41e-420">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="db41e-422">Selecione **.NET Core** > **Aplicativo** > **API** > **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="db41e-422">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="db41e-424">Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, aceite a **Estrutura de Destino** padrão \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="db41e-424">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="db41e-425">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-425">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="db41e-427">Testar a API</span><span class="sxs-lookup"><span data-stu-id="db41e-427">Test the API</span></span>

<span data-ttu-id="db41e-428">O modelo de projeto cria uma API `values`.</span><span class="sxs-lookup"><span data-stu-id="db41e-428">The project template creates a `values` API.</span></span> <span data-ttu-id="db41e-429">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="db41e-429">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="db41e-430">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db41e-430">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="db41e-431">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="db41e-431">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="db41e-432">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/api/values`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="db41e-432">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="db41e-433">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="db41e-433">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="db41e-434">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="db41e-434">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="db41e-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="db41e-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="db41e-436">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="db41e-436">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="db41e-437">Em um navegador, acesse a seguinte URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="db41e-437">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="db41e-438">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="db41e-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="db41e-439">Selecione **Executar** > **Iniciar com Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="db41e-439">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="db41e-440">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="db41e-440">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="db41e-441">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="db41e-441">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="db41e-442">Acrescente `/api/values` à URL (altere a URL para `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="db41e-442">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="db41e-443">O seguinte JSON é retornado:</span><span class="sxs-lookup"><span data-stu-id="db41e-443">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="db41e-444">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="db41e-444">Add a model class</span></span>

<span data-ttu-id="db41e-445">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="db41e-445">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="db41e-446">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="db41e-446">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="db41e-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db41e-447">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="db41e-448">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="db41e-448">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="db41e-449">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="db41e-449">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="db41e-450">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="db41e-450">Name the folder *Models*.</span></span>

* <span data-ttu-id="db41e-451">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="db41e-451">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="db41e-452">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-452">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="db41e-453">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="db41e-453">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="db41e-454">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="db41e-454">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="db41e-455">Adicione uma pasta chamada *Models*.</span><span class="sxs-lookup"><span data-stu-id="db41e-455">Add a folder named *Models*.</span></span>

* <span data-ttu-id="db41e-456">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="db41e-456">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="db41e-457">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="db41e-457">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="db41e-458">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="db41e-458">Right-click the project.</span></span> <span data-ttu-id="db41e-459">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="db41e-459">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="db41e-460">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="db41e-460">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="db41e-462">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="db41e-462">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="db41e-463">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="db41e-463">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="db41e-464">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="db41e-464">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="db41e-465">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="db41e-465">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="db41e-466">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="db41e-466">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="db41e-467">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="db41e-467">Add a database context</span></span>

<span data-ttu-id="db41e-468">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="db41e-468">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="db41e-469">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="db41e-469">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="db41e-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db41e-470">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="db41e-471">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="db41e-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="db41e-472">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-472">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="db41e-473">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="db41e-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="db41e-474">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="db41e-474">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="db41e-475">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="db41e-475">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="db41e-476">Registrar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="db41e-476">Register the database context</span></span>

<span data-ttu-id="db41e-477">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="db41e-477">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="db41e-478">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="db41e-478">The container provides the service to controllers.</span></span>

<span data-ttu-id="db41e-479">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="db41e-479">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="db41e-480">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="db41e-480">The preceding code:</span></span>

* <span data-ttu-id="db41e-481">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="db41e-481">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="db41e-482">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="db41e-482">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="db41e-483">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="db41e-483">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="db41e-484">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="db41e-484">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="db41e-485">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db41e-485">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="db41e-486">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="db41e-486">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="db41e-487">Selecione **Adicionar** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="db41e-487">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="db41e-488">Na caixa de diálogo **Adicionar Novo Item**, selecione o modelo **Classe do Controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="db41e-488">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="db41e-489">Dê à classe o nome *TodoController* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-489">Name the class *TodoController*, and select **Add**.</span></span>

  ![Caixa de diálogo Adicionar Novo Item com o controlador na caixa de pesquisa e o controlador da API Web selecionados](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="db41e-491">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="db41e-491">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="db41e-492">Na pasta *Controllers*, crie uma classe chamada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="db41e-492">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="db41e-493">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="db41e-493">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="db41e-494">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="db41e-494">The preceding code:</span></span>

* <span data-ttu-id="db41e-495">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="db41e-495">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="db41e-496">Decora a classe com o atributo [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="db41e-496">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="db41e-497">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="db41e-497">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="db41e-498">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="db41e-498">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="db41e-499">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="db41e-499">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="db41e-500">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="db41e-500">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="db41e-501">Adiciona um item chamado `Item1` ao banco de dados se o banco de dados está vazio.</span><span class="sxs-lookup"><span data-stu-id="db41e-501">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="db41e-502">Esse código está no construtor, de modo que ele seja executado sempre que há uma nova solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="db41e-502">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="db41e-503">Se você excluir todos os itens, o construtor criará `Item1` novamente na próxima vez que um método de API for chamado.</span><span class="sxs-lookup"><span data-stu-id="db41e-503">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="db41e-504">Portanto, pode parecer que a exclusão não funcionou quando ela realmente funcionou.</span><span class="sxs-lookup"><span data-stu-id="db41e-504">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="db41e-505">Adicionar métodos Get</span><span class="sxs-lookup"><span data-stu-id="db41e-505">Add Get methods</span></span>

<span data-ttu-id="db41e-506">Para fornecer uma API que recupera itens pendentes, adicione os seguintes métodos à classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="db41e-506">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="db41e-507">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="db41e-507">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="db41e-508">Interrompa o aplicativo se ele ainda estiver em execução.</span><span class="sxs-lookup"><span data-stu-id="db41e-508">Stop the app if it's still running.</span></span> <span data-ttu-id="db41e-509">Em seguida, execute-o novamente para incluir as alterações mais recentes.</span><span class="sxs-lookup"><span data-stu-id="db41e-509">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="db41e-510">Teste o aplicativo chamando os dois pontos de extremidade em um navegador.</span><span class="sxs-lookup"><span data-stu-id="db41e-510">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="db41e-511">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="db41e-511">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="db41e-512">A seguinte resposta HTTP é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="db41e-512">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="db41e-513">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="db41e-513">Routing and URL paths</span></span>

<span data-ttu-id="db41e-514">O atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica um método que responde a uma solicitação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="db41e-514">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="db41e-515">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="db41e-515">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="db41e-516">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="db41e-516">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="db41e-517">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="db41e-517">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="db41e-518">Para esta amostra, o nome da classe do controlador é **Todo**Controller e, portanto, o nome do controlador é "todo".</span><span class="sxs-lookup"><span data-stu-id="db41e-518">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="db41e-519">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="db41e-519">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="db41e-520">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="db41e-520">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="db41e-521">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="db41e-521">This sample doesn't use a template.</span></span> <span data-ttu-id="db41e-522">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="db41e-522">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="db41e-523">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="db41e-523">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="db41e-524">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="db41e-524">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="db41e-525">Valores de retorno</span><span class="sxs-lookup"><span data-stu-id="db41e-525">Return values</span></span>

<span data-ttu-id="db41e-526">O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="db41e-526">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="db41e-527">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="db41e-527">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="db41e-528">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="db41e-528">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="db41e-529">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="db41e-529">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="db41e-530">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="db41e-530">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="db41e-531">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="db41e-531">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="db41e-532">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="db41e-532">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="db41e-533">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="db41e-533">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="db41e-534">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="db41e-534">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="db41e-535">Testar o método GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="db41e-535">Test the GetTodoItems method</span></span>

<span data-ttu-id="db41e-536">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="db41e-536">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="db41e-537">Instale o [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="db41e-537">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="db41e-538">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="db41e-538">Start the web app.</span></span>
* <span data-ttu-id="db41e-539">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="db41e-539">Start Postman.</span></span>
* <span data-ttu-id="db41e-540">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="db41e-540">Disable **SSL certificate verification**</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="db41e-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db41e-541">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="db41e-542">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="db41e-542">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="db41e-543">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="db41e-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="db41e-544">Em **Postman** > **Preferências** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="db41e-544">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="db41e-545">Como alternativa, selecione a chave inglesa, selecione **Configurações** e desabilite a verificação do certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="db41e-545">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="db41e-546">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="db41e-546">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="db41e-547">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="db41e-547">Create a new request.</span></span>
  * <span data-ttu-id="db41e-548">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="db41e-548">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="db41e-549">Defina a URL de solicitação como `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="db41e-549">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="db41e-550">Por exemplo, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="db41e-550">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="db41e-551">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="db41e-551">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="db41e-552">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-552">Select **Send**.</span></span>

![Postman com solicitação GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="db41e-554">Adicionar um método Create</span><span class="sxs-lookup"><span data-stu-id="db41e-554">Add a Create method</span></span>

<span data-ttu-id="db41e-555">Adicione o seguinte método `PostTodoItem` dentro de *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="db41e-555">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="db41e-556">O código anterior é um método HTTP POST, conforme indicado pelo atributo [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="db41e-556">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="db41e-557">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="db41e-557">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="db41e-558">O método `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="db41e-558">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="db41e-559">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="db41e-559">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="db41e-560">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="db41e-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="db41e-561">Adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="db41e-561">Adds a `Location` header to the response.</span></span> <span data-ttu-id="db41e-562">O cabeçalho `Location` especifica o URI do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="db41e-562">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="db41e-563">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="db41e-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="db41e-564">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="db41e-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="db41e-565">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="db41e-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="db41e-566">Testar o método PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="db41e-566">Test the PostTodoItem method</span></span>

* <span data-ttu-id="db41e-567">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="db41e-567">Build the project.</span></span>
* <span data-ttu-id="db41e-568">No Postman, defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="db41e-568">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="db41e-569">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="db41e-569">Select the **Body** tab.</span></span>
* <span data-ttu-id="db41e-570">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="db41e-570">Select the **raw** radio button.</span></span>
* <span data-ttu-id="db41e-571">Defina o tipo como **JSON (aplicativo/json)** .</span><span class="sxs-lookup"><span data-stu-id="db41e-571">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="db41e-572">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="db41e-572">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="db41e-573">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-573">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

  <span data-ttu-id="db41e-575">Se você receber um erro 405 Método Não Permitido, provavelmente, esse será o resultado da não compilação do projeto após a adição do método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="db41e-575">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="db41e-576">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="db41e-576">Test the location header URI</span></span>

* <span data-ttu-id="db41e-577">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="db41e-577">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="db41e-578">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="db41e-578">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="db41e-580">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="db41e-580">Set the method to GET.</span></span>
* <span data-ttu-id="db41e-581">Cole o URI (por exemplo, `https://localhost:5001/api/Todo/2`)</span><span class="sxs-lookup"><span data-stu-id="db41e-581">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="db41e-582">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="db41e-582">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="db41e-583">Adicionar um método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="db41e-583">Add a PutTodoItem method</span></span>

<span data-ttu-id="db41e-584">Adicione o seguinte método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="db41e-584">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="db41e-585">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="db41e-585">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="db41e-586">A resposta é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="db41e-586">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="db41e-587">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="db41e-587">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="db41e-588">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="db41e-588">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="db41e-589">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="db41e-589">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="db41e-590">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="db41e-590">Test the PutTodoItem method</span></span>

<span data-ttu-id="db41e-591">Este exemplo usa um banco de dados em memória que deverá ser iniciado sempre que o aplicativo for iniciado.</span><span class="sxs-lookup"><span data-stu-id="db41e-591">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="db41e-592">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="db41e-592">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="db41e-593">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="db41e-593">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="db41e-594">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="db41e-594">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="db41e-595">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="db41e-595">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="db41e-597">Adicionar um método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="db41e-597">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="db41e-598">Adicione o seguinte método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="db41e-598">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="db41e-599">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="db41e-599">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="db41e-600">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="db41e-600">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="db41e-601">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="db41e-601">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="db41e-602">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="db41e-602">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="db41e-603">Defina o URI do objeto a ser excluído, por exemplo, `https://localhost:5001/api/todo/1`</span><span class="sxs-lookup"><span data-stu-id="db41e-603">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="db41e-604">Selecione **Enviar**</span><span class="sxs-lookup"><span data-stu-id="db41e-604">Select **Send**</span></span>

<span data-ttu-id="db41e-605">O aplicativo de exemplo permite que você exclua todos os itens.</span><span class="sxs-lookup"><span data-stu-id="db41e-605">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="db41e-606">No entanto, quando o último item é excluído, um novo é criado pelo construtor de classe de modelo na próxima vez que a API for chamada.</span><span class="sxs-lookup"><span data-stu-id="db41e-606">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="db41e-607">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="db41e-607">Call the web API with JavaScript</span></span>

<span data-ttu-id="db41e-608">Nesta seção, é adicionada uma página HTML que usa o JavaScript para chamar a API Web.</span><span class="sxs-lookup"><span data-stu-id="db41e-608">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="db41e-609">A API Fetch inicia a solicitação.</span><span class="sxs-lookup"><span data-stu-id="db41e-609">The Fetch API initiates the request.</span></span> <span data-ttu-id="db41e-610">O JavaScript atualiza a página com os detalhes da resposta da API Web.</span><span class="sxs-lookup"><span data-stu-id="db41e-610">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="db41e-611">Configurar o aplicativo para [servir arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) atualizando *Startup.cs* com o código realçado a seguir:</span><span class="sxs-lookup"><span data-stu-id="db41e-611">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="db41e-612">Crie uma pasta *wwwroot* no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="db41e-612">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="db41e-613">Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="db41e-613">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="db41e-614">Substitua seu conteúdo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="db41e-614">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="db41e-615">Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="db41e-615">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="db41e-616">Substitua seu conteúdo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="db41e-616">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="db41e-617">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="db41e-617">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="db41e-618">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="db41e-618">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="db41e-619">Remova a propriedade `launchUrl` para forçar o aplicativo a ser aberto em *index.html*, o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="db41e-619">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="db41e-620">Esta amostra chama todos os métodos CRUD da API Web.</span><span class="sxs-lookup"><span data-stu-id="db41e-620">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="db41e-621">Veja a seguir explicações das chamadas à API.</span><span class="sxs-lookup"><span data-stu-id="db41e-621">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="db41e-622">Obter uma lista de itens pendentes</span><span class="sxs-lookup"><span data-stu-id="db41e-622">Get a list of to-do items</span></span>

<span data-ttu-id="db41e-623">Fetch envia uma solicitação HTTP GET para a API Web, que retorna JSON representando uma matriz de itens de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="db41e-623">Fetch sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="db41e-624">A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="db41e-624">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="db41e-625">No retorno de chamada, o DOM é atualizado com as informações do item pendente.</span><span class="sxs-lookup"><span data-stu-id="db41e-625">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="db41e-626">Adicionar um item pendente</span><span class="sxs-lookup"><span data-stu-id="db41e-626">Add a to-do item</span></span>

<span data-ttu-id="db41e-627">Fetch envia uma solicitação HTTP POST com o item de tarefas pendentes no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="db41e-627">Fetch sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="db41e-628">As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado.</span><span class="sxs-lookup"><span data-stu-id="db41e-628">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="db41e-629">O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="db41e-629">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="db41e-630">Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="db41e-630">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="db41e-631">Atualizar um item pendente</span><span class="sxs-lookup"><span data-stu-id="db41e-631">Update a to-do item</span></span>

<span data-ttu-id="db41e-632">A atualização de um item pendente é semelhante à adição de um.</span><span class="sxs-lookup"><span data-stu-id="db41e-632">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="db41e-633">A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.</span><span class="sxs-lookup"><span data-stu-id="db41e-633">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="db41e-634">Excluir um item pendente</span><span class="sxs-lookup"><span data-stu-id="db41e-634">Delete a to-do item</span></span>

<span data-ttu-id="db41e-635">A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="db41e-635">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="db41e-636">Adicionar suporte de autenticação a uma API da Web</span><span class="sxs-lookup"><span data-stu-id="db41e-636">Add authentication support to a web API</span></span>

<span data-ttu-id="db41e-637">Consulte o tutorial do [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) .</span><span class="sxs-lookup"><span data-stu-id="db41e-637">See the [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="db41e-638">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="db41e-638">Additional resources</span></span>

<span data-ttu-id="db41e-639">[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="db41e-639">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="db41e-640">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="db41e-640">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="db41e-641">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="db41e-641">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="db41e-642">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="db41e-642">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
