---
title: 'Tutorial: criar uma API Web com ASP.NET Core'
author: rick-anderson
description: Saiba como criar uma API Web com o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
uid: tutorials/first-web-api
ms.openlocfilehash: 55dfc05b5c96f7fa060d537745bac969e92daa9b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655585"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="a0113-103">Tutorial: criar uma API Web com ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0113-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="a0113-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5)e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="a0113-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5),  and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="a0113-105">Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a0113-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a0113-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="a0113-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a0113-107">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="a0113-107">Create a web API project.</span></span>
> * <span data-ttu-id="a0113-108">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a0113-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="a0113-109">Faça scaffold de um controlador com métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="a0113-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="a0113-110">Configure o roteamento, os caminhos de URL e os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="a0113-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="a0113-111">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="a0113-111">Call the web API with Postman.</span></span>

<span data-ttu-id="a0113-112">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a0113-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="a0113-113">Visão geral</span><span class="sxs-lookup"><span data-stu-id="a0113-113">Overview</span></span>

<span data-ttu-id="a0113-114">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="a0113-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="a0113-115">API</span><span class="sxs-lookup"><span data-stu-id="a0113-115">API</span></span> | <span data-ttu-id="a0113-116">Descrição</span><span class="sxs-lookup"><span data-stu-id="a0113-116">Description</span></span> | <span data-ttu-id="a0113-117">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="a0113-117">Request body</span></span> | <span data-ttu-id="a0113-118">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="a0113-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="a0113-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="a0113-119">GET /api/TodoItems</span></span> | <span data-ttu-id="a0113-120">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="a0113-120">Get all to-do items</span></span> | <span data-ttu-id="a0113-121">Nenhum</span><span class="sxs-lookup"><span data-stu-id="a0113-121">None</span></span> | <span data-ttu-id="a0113-122">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="a0113-122">Array of to-do items</span></span>|
|<span data-ttu-id="a0113-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="a0113-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="a0113-124">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="a0113-124">Get an item by ID</span></span> | <span data-ttu-id="a0113-125">Nenhum</span><span class="sxs-lookup"><span data-stu-id="a0113-125">None</span></span> | <span data-ttu-id="a0113-126">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="a0113-126">To-do item</span></span>|
|<span data-ttu-id="a0113-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="a0113-127">POST /api/TodoItems</span></span> | <span data-ttu-id="a0113-128">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="a0113-128">Add a new item</span></span> | <span data-ttu-id="a0113-129">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="a0113-129">To-do item</span></span> | <span data-ttu-id="a0113-130">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="a0113-130">To-do item</span></span> |
|<span data-ttu-id="a0113-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="a0113-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="a0113-132">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="a0113-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="a0113-133">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="a0113-133">To-do item</span></span> | <span data-ttu-id="a0113-134">Nenhum</span><span class="sxs-lookup"><span data-stu-id="a0113-134">None</span></span> |
|<span data-ttu-id="a0113-135">EXCLUIR/api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="a0113-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="a0113-136">Excluir um item &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="a0113-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="a0113-137">Nenhum</span><span class="sxs-lookup"><span data-stu-id="a0113-137">None</span></span> | <span data-ttu-id="a0113-138">Nenhum</span><span class="sxs-lookup"><span data-stu-id="a0113-138">None</span></span>|

<span data-ttu-id="a0113-139">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a0113-139">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="a0113-145">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="a0113-145">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0113-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0113-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0113-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0113-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0113-148">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a0113-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="a0113-149">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="a0113-149">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0113-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0113-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0113-151">No menu **arquivo** , selecione **novo** **projeto**de >.</span><span class="sxs-lookup"><span data-stu-id="a0113-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="a0113-152">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="a0113-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="a0113-153">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="a0113-154">Na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , confirme se o **.net Core** e **ASP.NET Core 3,1** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="a0113-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="a0113-155">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-155">Select the **API** template and click **Create**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0113-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0113-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a0113-158">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a0113-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="a0113-159">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="a0113-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="a0113-160">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="a0113-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="a0113-161">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="a0113-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="a0113-162">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="a0113-162">The preceding commands:</span></span>

  * <span data-ttu-id="a0113-163">Cria um novo projeto de API Web e o abre no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a0113-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="a0113-164">Adiciona os pacotes do NuGet que são exigidos na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="a0113-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0113-165">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a0113-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a0113-166">Selecione **arquivo** > **nova solução**.</span><span class="sxs-lookup"><span data-stu-id="a0113-166">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="a0113-168">Selecione **.NET Core** > **aplicativo** > **API** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="a0113-170">Na caixa de diálogo **configurar sua nova API Web do ASP.NET Core** , selecione **estrutura de destino** do \* *.NET Core 3,1*.</span><span class="sxs-lookup"><span data-stu-id="a0113-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="a0113-171">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="a0113-173">Abra um terminal de comando na pasta do projeto e execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="a0113-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="a0113-174">Testar a API</span><span class="sxs-lookup"><span data-stu-id="a0113-174">Test the API</span></span>

<span data-ttu-id="a0113-175">O modelo de projeto cria uma API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="a0113-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="a0113-176">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a0113-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0113-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0113-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a0113-178">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a0113-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="a0113-179">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/WeatherForecast`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="a0113-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="a0113-180">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="a0113-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="a0113-181">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="a0113-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0113-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0113-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a0113-183">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a0113-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="a0113-184">Em um navegador, acesse a seguinte URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="a0113-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0113-185">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a0113-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a0113-186">Selecione **Executar** > **Iniciar com Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a0113-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="a0113-187">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="a0113-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="a0113-188">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="a0113-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="a0113-189">Acrescente `/WeatherForecast` à URL (altere a URL para `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="a0113-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="a0113-190">Um JSON semelhante ao seguinte será retornado:</span><span class="sxs-lookup"><span data-stu-id="a0113-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="a0113-191">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="a0113-191">Add a model class</span></span>

<span data-ttu-id="a0113-192">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a0113-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="a0113-193">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="a0113-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0113-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0113-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0113-195">No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="a0113-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="a0113-196">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="a0113-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="a0113-197">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="a0113-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="a0113-198">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="a0113-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="a0113-199">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="a0113-200">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a0113-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0113-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0113-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a0113-202">Adicione uma pasta chamada *Models*.</span><span class="sxs-lookup"><span data-stu-id="a0113-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="a0113-203">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a0113-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0113-204">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a0113-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a0113-205">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="a0113-205">Right-click the project.</span></span> <span data-ttu-id="a0113-206">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="a0113-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="a0113-207">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="a0113-207">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="a0113-209">Clique com o botão direito do mouse na pasta *modelos* e selecione **Adicionar** > **novo arquivo** > **geral** > **classe vazia**.</span><span class="sxs-lookup"><span data-stu-id="a0113-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="a0113-210">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="a0113-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="a0113-211">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a0113-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="a0113-212">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="a0113-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="a0113-213">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="a0113-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="a0113-214">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="a0113-214">Add a database context</span></span>

<span data-ttu-id="a0113-215">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="a0113-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="a0113-216">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="a0113-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0113-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0113-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="a0113-218">Adicione Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="a0113-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="a0113-219">No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.</span><span class="sxs-lookup"><span data-stu-id="a0113-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="a0113-220">Selecione a guia **Procurar** e, em seguida, insira **Microsoft.EntityFrameworkCore.SqlServer** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="a0113-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="a0113-221">Selecione **Microsoft. EntityFrameworkCore. SqlServer** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="a0113-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="a0113-222">Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="a0113-223">Use as instruções anteriores para adicionar o pacote do NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="a0113-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Gerenciador de Pacotes NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="a0113-225">Adicione o contexto de banco de dados TodoContext</span><span class="sxs-lookup"><span data-stu-id="a0113-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="a0113-226">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="a0113-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="a0113-227">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a0113-228">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a0113-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="a0113-229">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="a0113-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="a0113-230">Insira o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a0113-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="a0113-231">Registrar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="a0113-231">Register the database context</span></span>

<span data-ttu-id="a0113-232">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a0113-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a0113-233">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="a0113-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="a0113-234">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="a0113-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="a0113-235">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="a0113-235">The preceding code:</span></span>

* <span data-ttu-id="a0113-236">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="a0113-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="a0113-237">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="a0113-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="a0113-238">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="a0113-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="a0113-239">Faça scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="a0113-239">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0113-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0113-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0113-241">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="a0113-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="a0113-242">Selecione **adicionar** > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="a0113-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="a0113-243">Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="a0113-244">Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework**:</span><span class="sxs-lookup"><span data-stu-id="a0113-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="a0113-245">Selecione **TodoItem (TodoApi. Models)** na **classe Model**.</span><span class="sxs-lookup"><span data-stu-id="a0113-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="a0113-246">Selecione **TodoContext (TodoApi. Models)** na **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="a0113-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="a0113-247">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a0113-248">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a0113-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="a0113-249">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="a0113-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="a0113-250">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="a0113-250">The preceding commands:</span></span>

* <span data-ttu-id="a0113-251">Adicionam os pacotes do NuGet para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="a0113-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="a0113-252">Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="a0113-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="a0113-253">Fazem scaffold de `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="a0113-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="a0113-254">O código gerado:</span><span class="sxs-lookup"><span data-stu-id="a0113-254">The generated code:</span></span>

* <span data-ttu-id="a0113-255">Marca a classe com o atributo [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) .</span><span class="sxs-lookup"><span data-stu-id="a0113-255">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="a0113-256">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="a0113-256">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="a0113-257">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="a0113-257">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="a0113-258">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="a0113-258">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="a0113-259">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="a0113-259">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="a0113-260">Os modelos de ASP.NET Core para:</span><span class="sxs-lookup"><span data-stu-id="a0113-260">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="a0113-261">Os controladores com exibições incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="a0113-261">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="a0113-262">Os controladores de API não incluem `[action]` no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="a0113-262">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="a0113-263">Quando o token de `[action]` não está no modelo de rota, o nome da [ação](xref:mvc/controllers/routing#action) é excluído da rota.</span><span class="sxs-lookup"><span data-stu-id="a0113-263">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="a0113-264">Ou seja, o nome do método associado da ação não é usado na rota correspondente.</span><span class="sxs-lookup"><span data-stu-id="a0113-264">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="a0113-265">Examine o método criar do PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0113-265">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="a0113-266">Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="a0113-266">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="a0113-267">O código anterior é um método HTTP POST, conforme indicado pelo atributo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) .</span><span class="sxs-lookup"><span data-stu-id="a0113-267">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="a0113-268">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0113-268">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="a0113-269">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="a0113-269">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="a0113-270">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="a0113-270">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="a0113-271">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="a0113-271">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="a0113-272">Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta.</span><span class="sxs-lookup"><span data-stu-id="a0113-272">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="a0113-273">O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="a0113-273">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="a0113-274">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="a0113-274">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="a0113-275">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="a0113-275">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="a0113-276">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="a0113-276">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="a0113-277">Instale o Postman</span><span class="sxs-lookup"><span data-stu-id="a0113-277">Install Postman</span></span>

<span data-ttu-id="a0113-278">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="a0113-278">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="a0113-279">Instale o [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="a0113-279">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="a0113-280">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="a0113-280">Start the web app.</span></span>
* <span data-ttu-id="a0113-281">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="a0113-281">Start Postman.</span></span>
* <span data-ttu-id="a0113-282">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="a0113-282">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="a0113-283">Em **configurações** de > de **arquivo** (guia**geral** ), desabilite a **verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="a0113-283">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="a0113-284">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="a0113-284">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="a0113-285">Teste o PostTodoItem com o Postman</span><span class="sxs-lookup"><span data-stu-id="a0113-285">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="a0113-286">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="a0113-286">Create a new request.</span></span>
* <span data-ttu-id="a0113-287">Defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="a0113-287">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="a0113-288">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="a0113-288">Select the **Body** tab.</span></span>
* <span data-ttu-id="a0113-289">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="a0113-289">Select the **raw** radio button.</span></span>
* <span data-ttu-id="a0113-290">Defina o tipo como **JSON (aplicativo/json)** .</span><span class="sxs-lookup"><span data-stu-id="a0113-290">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="a0113-291">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="a0113-291">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="a0113-292">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-292">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="a0113-294">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="a0113-294">Test the location header URI</span></span>

* <span data-ttu-id="a0113-295">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="a0113-295">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="a0113-296">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="a0113-296">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="a0113-298">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="a0113-298">Set the method to GET.</span></span>
* <span data-ttu-id="a0113-299">Cole o URI (por exemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="a0113-299">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="a0113-300">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-300">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="a0113-301">Examine os métodos GET</span><span class="sxs-lookup"><span data-stu-id="a0113-301">Examine the GET methods</span></span>

<span data-ttu-id="a0113-302">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="a0113-302">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="a0113-303">Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman.</span><span class="sxs-lookup"><span data-stu-id="a0113-303">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="a0113-304">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="a0113-304">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="a0113-305">Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="a0113-305">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="a0113-306">Teste o GET com o Postman</span><span class="sxs-lookup"><span data-stu-id="a0113-306">Test Get with Postman</span></span>

* <span data-ttu-id="a0113-307">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="a0113-307">Create a new request.</span></span>
* <span data-ttu-id="a0113-308">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="a0113-308">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="a0113-309">Defina a URL de solicitação como `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="a0113-309">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="a0113-310">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="a0113-310">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="a0113-311">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="a0113-311">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="a0113-312">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-312">Select **Send**.</span></span>

<span data-ttu-id="a0113-313">Este aplicativo usa um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="a0113-313">This app uses an in-memory database.</span></span> <span data-ttu-id="a0113-314">Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado.</span><span class="sxs-lookup"><span data-stu-id="a0113-314">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="a0113-315">Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a0113-315">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="a0113-316">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="a0113-316">Routing and URL paths</span></span>

<span data-ttu-id="a0113-317">O atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica um método que responde a uma solicitação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="a0113-317">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="a0113-318">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="a0113-318">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="a0113-319">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="a0113-319">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="a0113-320">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="a0113-320">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="a0113-321">Para esta amostra, o nome da classe do controlador é **TodoItems**Controller. Portanto, o nome do controlador é "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="a0113-321">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="a0113-322">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="a0113-322">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="a0113-323">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="a0113-323">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="a0113-324">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="a0113-324">This sample doesn't use a template.</span></span> <span data-ttu-id="a0113-325">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="a0113-325">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="a0113-326">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="a0113-326">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="a0113-327">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido ao método em seu parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="a0113-327">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="a0113-328">Valores retornados</span><span class="sxs-lookup"><span data-stu-id="a0113-328">Return values</span></span>

<span data-ttu-id="a0113-329">O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="a0113-329">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="a0113-330">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="a0113-330">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="a0113-331">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="a0113-331">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="a0113-332">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="a0113-332">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="a0113-333">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0113-333">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="a0113-334">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="a0113-334">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="a0113-335">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="a0113-335">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="a0113-336">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="a0113-336">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="a0113-337">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="a0113-337">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="a0113-338">O método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0113-338">The PutTodoItem method</span></span>

<span data-ttu-id="a0113-339">Examine o método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="a0113-339">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="a0113-340">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="a0113-340">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="a0113-341">A resposta é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="a0113-341">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="a0113-342">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="a0113-342">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="a0113-343">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="a0113-343">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="a0113-344">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a0113-344">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="a0113-345">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0113-345">Test the PutTodoItem method</span></span>

<span data-ttu-id="a0113-346">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="a0113-346">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="a0113-347">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="a0113-347">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="a0113-348">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="a0113-348">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="a0113-349">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="a0113-349">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="a0113-350">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="a0113-350">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (sem conteúdo)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="a0113-352">O método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0113-352">The DeleteTodoItem method</span></span>

<span data-ttu-id="a0113-353">Examine o método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="a0113-353">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="a0113-354">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0113-354">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="a0113-355">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="a0113-355">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="a0113-356">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="a0113-356">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="a0113-357">Defina o URI do objeto a ser excluído (por exemplo `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="a0113-357">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="a0113-358">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-358">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="a0113-359">Evitar excesso de postagem</span><span class="sxs-lookup"><span data-stu-id="a0113-359">Prevent over-posting</span></span>

<span data-ttu-id="a0113-360">Atualmente, o aplicativo de exemplo expõe todo o objeto de `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="a0113-360">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="a0113-361">Os aplicativos de produções normalmente limitam os dados que são inseridos e retornados usando um subconjunto do modelo.</span><span class="sxs-lookup"><span data-stu-id="a0113-361">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="a0113-362">Há várias razões por trás disso e a segurança é uma importante.</span><span class="sxs-lookup"><span data-stu-id="a0113-362">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="a0113-363">O subconjunto de um modelo é geralmente conhecido como um objeto Transferência de Dados (DTO), um modelo de entrada ou um modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="a0113-363">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="a0113-364">O **dto** é usado neste artigo.</span><span class="sxs-lookup"><span data-stu-id="a0113-364">**DTO** is used in this article.</span></span>

<span data-ttu-id="a0113-365">Um DTO pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="a0113-365">A DTO may be used to:</span></span>

* <span data-ttu-id="a0113-366">Evitar sobrepostos.</span><span class="sxs-lookup"><span data-stu-id="a0113-366">Prevent over-posting.</span></span>
* <span data-ttu-id="a0113-367">Oculte as propriedades que os clientes não devem exibir.</span><span class="sxs-lookup"><span data-stu-id="a0113-367">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="a0113-368">Omita algumas propriedades para reduzir o tamanho da carga.</span><span class="sxs-lookup"><span data-stu-id="a0113-368">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="a0113-369">Mesclar grafos de objeto que contêm objetos aninhados.</span><span class="sxs-lookup"><span data-stu-id="a0113-369">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="a0113-370">Os gráficos de objeto achatados podem ser mais convenientes para os clientes.</span><span class="sxs-lookup"><span data-stu-id="a0113-370">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="a0113-371">Para demonstrar a abordagem do DTO, atualize a classe `TodoItem` para incluir um campo secreto:</span><span class="sxs-lookup"><span data-stu-id="a0113-371">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="a0113-372">O campo segredo precisa ser ocultado deste aplicativo, mas um aplicativo administrativo pode optar por expô-lo.</span><span class="sxs-lookup"><span data-stu-id="a0113-372">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="a0113-373">Verifique se você pode postar e obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="a0113-373">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="a0113-374">Criar um modelo de DTO:</span><span class="sxs-lookup"><span data-stu-id="a0113-374">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="a0113-375">Atualize o `TodoItemsController` para usar `TodoItemDTO`:</span><span class="sxs-lookup"><span data-stu-id="a0113-375">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="a0113-376">Verifique se você não pode postar ou obter o campo secreto.</span><span class="sxs-lookup"><span data-stu-id="a0113-376">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="a0113-377">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="a0113-377">Call the web API with JavaScript</span></span>

<span data-ttu-id="a0113-378">Consulte [o tutorial: chamar uma API Web do ASP.NET Core com JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="a0113-378">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a0113-379">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="a0113-379">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a0113-380">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="a0113-380">Create a web API project.</span></span>
> * <span data-ttu-id="a0113-381">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a0113-381">Add a model class and a database context.</span></span>
> * <span data-ttu-id="a0113-382">Adicionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="a0113-382">Add a controller.</span></span>
> * <span data-ttu-id="a0113-383">Adicionar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="a0113-383">Add CRUD methods.</span></span>
> * <span data-ttu-id="a0113-384">Configurar o roteamento e caminhos de URL.</span><span class="sxs-lookup"><span data-stu-id="a0113-384">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="a0113-385">Especificar os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="a0113-385">Specify return values.</span></span>
> * <span data-ttu-id="a0113-386">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="a0113-386">Call the web API with Postman.</span></span>
> * <span data-ttu-id="a0113-387">Chamar a API Web com o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a0113-387">Call the web API with JavaScript.</span></span>

<span data-ttu-id="a0113-388">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="a0113-388">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="a0113-389">Visão geral</span><span class="sxs-lookup"><span data-stu-id="a0113-389">Overview</span></span>

<span data-ttu-id="a0113-390">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="a0113-390">This tutorial creates the following API:</span></span>

|<span data-ttu-id="a0113-391">API</span><span class="sxs-lookup"><span data-stu-id="a0113-391">API</span></span> | <span data-ttu-id="a0113-392">Descrição</span><span class="sxs-lookup"><span data-stu-id="a0113-392">Description</span></span> | <span data-ttu-id="a0113-393">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="a0113-393">Request body</span></span> | <span data-ttu-id="a0113-394">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="a0113-394">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="a0113-395">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="a0113-395">GET /api/TodoItems</span></span> | <span data-ttu-id="a0113-396">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="a0113-396">Get all to-do items</span></span> | <span data-ttu-id="a0113-397">Nenhum</span><span class="sxs-lookup"><span data-stu-id="a0113-397">None</span></span> | <span data-ttu-id="a0113-398">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="a0113-398">Array of to-do items</span></span>|
|<span data-ttu-id="a0113-399">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="a0113-399">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="a0113-400">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="a0113-400">Get an item by ID</span></span> | <span data-ttu-id="a0113-401">Nenhum</span><span class="sxs-lookup"><span data-stu-id="a0113-401">None</span></span> | <span data-ttu-id="a0113-402">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="a0113-402">To-do item</span></span>|
|<span data-ttu-id="a0113-403">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="a0113-403">POST /api/TodoItems</span></span> | <span data-ttu-id="a0113-404">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="a0113-404">Add a new item</span></span> | <span data-ttu-id="a0113-405">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="a0113-405">To-do item</span></span> | <span data-ttu-id="a0113-406">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="a0113-406">To-do item</span></span> |
|<span data-ttu-id="a0113-407">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="a0113-407">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="a0113-408">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="a0113-408">Update an existing item &nbsp;</span></span> | <span data-ttu-id="a0113-409">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="a0113-409">To-do item</span></span> | <span data-ttu-id="a0113-410">Nenhum</span><span class="sxs-lookup"><span data-stu-id="a0113-410">None</span></span> |
|<span data-ttu-id="a0113-411">EXCLUIR/api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="a0113-411">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="a0113-412">Excluir um item &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="a0113-412">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="a0113-413">Nenhum</span><span class="sxs-lookup"><span data-stu-id="a0113-413">None</span></span> | <span data-ttu-id="a0113-414">Nenhum</span><span class="sxs-lookup"><span data-stu-id="a0113-414">None</span></span>|

<span data-ttu-id="a0113-415">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a0113-415">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="a0113-421">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="a0113-421">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0113-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0113-422">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0113-423">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0113-423">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0113-424">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a0113-424">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="a0113-425">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="a0113-425">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0113-426">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0113-426">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0113-427">No menu **arquivo** , selecione **novo** **projeto**de >.</span><span class="sxs-lookup"><span data-stu-id="a0113-427">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="a0113-428">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="a0113-428">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="a0113-429">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-429">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="a0113-430">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 2.2** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="a0113-430">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="a0113-431">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-431">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="a0113-432">**Não** selecione **Habilitar Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="a0113-432">**Don't** select **Enable Docker Support**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0113-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0113-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a0113-435">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="a0113-435">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="a0113-436">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="a0113-436">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="a0113-437">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="a0113-437">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="a0113-438">Esses comandos criam um novo projeto de API Web e abrem uma nova instância do Visual Studio Code na nova pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="a0113-438">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="a0113-439">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="a0113-439">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0113-440">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a0113-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a0113-441">Selecione **arquivo** > **nova solução**.</span><span class="sxs-lookup"><span data-stu-id="a0113-441">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="a0113-443">Selecione **.NET Core** > **aplicativo** > **API** > **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-443">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="a0113-445">Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, aceite a **Estrutura de Destino** padrão \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="a0113-445">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="a0113-446">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-446">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="a0113-448">Testar a API</span><span class="sxs-lookup"><span data-stu-id="a0113-448">Test the API</span></span>

<span data-ttu-id="a0113-449">O modelo de projeto cria uma API `values`.</span><span class="sxs-lookup"><span data-stu-id="a0113-449">The project template creates a `values` API.</span></span> <span data-ttu-id="a0113-450">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a0113-450">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0113-451">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0113-451">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a0113-452">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a0113-452">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="a0113-453">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/api/values`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="a0113-453">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="a0113-454">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="a0113-454">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="a0113-455">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="a0113-455">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0113-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0113-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a0113-457">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a0113-457">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="a0113-458">Em um navegador, acesse a seguinte URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="a0113-458">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0113-459">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a0113-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a0113-460">Selecione **Executar** > **Iniciar com Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a0113-460">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="a0113-461">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="a0113-461">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="a0113-462">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="a0113-462">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="a0113-463">Acrescente `/api/values` à URL (altere a URL para `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="a0113-463">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="a0113-464">O seguinte JSON é retornado:</span><span class="sxs-lookup"><span data-stu-id="a0113-464">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="a0113-465">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="a0113-465">Add a model class</span></span>

<span data-ttu-id="a0113-466">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a0113-466">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="a0113-467">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="a0113-467">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0113-468">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0113-468">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0113-469">No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="a0113-469">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="a0113-470">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="a0113-470">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="a0113-471">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="a0113-471">Name the folder *Models*.</span></span>

* <span data-ttu-id="a0113-472">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="a0113-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="a0113-473">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-473">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="a0113-474">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a0113-474">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a0113-475">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0113-475">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a0113-476">Adicione uma pasta chamada *Models*.</span><span class="sxs-lookup"><span data-stu-id="a0113-476">Add a folder named *Models*.</span></span>

* <span data-ttu-id="a0113-477">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a0113-477">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a0113-478">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a0113-478">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a0113-479">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="a0113-479">Right-click the project.</span></span> <span data-ttu-id="a0113-480">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="a0113-480">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="a0113-481">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="a0113-481">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="a0113-483">Clique com o botão direito do mouse na pasta *modelos* e selecione **Adicionar** > **novo arquivo** > **geral** > **classe vazia**.</span><span class="sxs-lookup"><span data-stu-id="a0113-483">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="a0113-484">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="a0113-484">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="a0113-485">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a0113-485">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="a0113-486">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="a0113-486">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="a0113-487">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="a0113-487">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="a0113-488">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="a0113-488">Add a database context</span></span>

<span data-ttu-id="a0113-489">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="a0113-489">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="a0113-490">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="a0113-490">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0113-491">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0113-491">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0113-492">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="a0113-492">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="a0113-493">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-493">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a0113-494">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a0113-494">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="a0113-495">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="a0113-495">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="a0113-496">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a0113-496">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="a0113-497">Registrar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="a0113-497">Register the database context</span></span>

<span data-ttu-id="a0113-498">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a0113-498">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a0113-499">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="a0113-499">The container provides the service to controllers.</span></span>

<span data-ttu-id="a0113-500">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="a0113-500">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="a0113-501">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="a0113-501">The preceding code:</span></span>

* <span data-ttu-id="a0113-502">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="a0113-502">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="a0113-503">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="a0113-503">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="a0113-504">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="a0113-504">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="a0113-505">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="a0113-505">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0113-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0113-506">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0113-507">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="a0113-507">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="a0113-508">Selecione **adicionar** > **novo item**.</span><span class="sxs-lookup"><span data-stu-id="a0113-508">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="a0113-509">Na caixa de diálogo **Adicionar Novo Item**, selecione o modelo **Classe do Controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="a0113-509">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="a0113-510">Dê à classe o nome *TodoController* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-510">Name the class *TodoController*, and select **Add**.</span></span>

  ![Caixa de diálogo Adicionar Novo Item com o controlador na caixa de pesquisa e o controlador da API Web selecionados](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a0113-512">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a0113-512">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="a0113-513">Na pasta *Controllers*, crie uma classe chamada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="a0113-513">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="a0113-514">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="a0113-514">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="a0113-515">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="a0113-515">The preceding code:</span></span>

* <span data-ttu-id="a0113-516">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="a0113-516">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="a0113-517">Marca a classe com o atributo [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) .</span><span class="sxs-lookup"><span data-stu-id="a0113-517">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="a0113-518">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="a0113-518">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="a0113-519">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="a0113-519">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="a0113-520">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="a0113-520">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="a0113-521">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="a0113-521">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="a0113-522">Adiciona um item chamado `Item1` ao banco de dados se o banco de dados está vazio.</span><span class="sxs-lookup"><span data-stu-id="a0113-522">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="a0113-523">Esse código está no construtor, de modo que ele seja executado sempre que há uma nova solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0113-523">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="a0113-524">Se você excluir todos os itens, o construtor criará `Item1` novamente na próxima vez que um método de API for chamado.</span><span class="sxs-lookup"><span data-stu-id="a0113-524">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="a0113-525">Portanto, pode parecer que a exclusão não funcionou quando ela realmente funcionou.</span><span class="sxs-lookup"><span data-stu-id="a0113-525">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="a0113-526">Adicionar métodos Get</span><span class="sxs-lookup"><span data-stu-id="a0113-526">Add Get methods</span></span>

<span data-ttu-id="a0113-527">Para fornecer uma API que recupera itens pendentes, adicione os seguintes métodos à classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="a0113-527">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="a0113-528">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="a0113-528">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="a0113-529">Interrompa o aplicativo se ele ainda estiver em execução.</span><span class="sxs-lookup"><span data-stu-id="a0113-529">Stop the app if it's still running.</span></span> <span data-ttu-id="a0113-530">Em seguida, execute-o novamente para incluir as alterações mais recentes.</span><span class="sxs-lookup"><span data-stu-id="a0113-530">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="a0113-531">Teste o aplicativo chamando os dois pontos de extremidade em um navegador.</span><span class="sxs-lookup"><span data-stu-id="a0113-531">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="a0113-532">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="a0113-532">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="a0113-533">A seguinte resposta HTTP é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="a0113-533">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="a0113-534">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="a0113-534">Routing and URL paths</span></span>

<span data-ttu-id="a0113-535">O atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica um método que responde a uma solicitação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="a0113-535">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="a0113-536">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="a0113-536">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="a0113-537">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="a0113-537">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="a0113-538">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="a0113-538">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="a0113-539">Para esta amostra, o nome da classe do controlador é **Todo**Controller e, portanto, o nome do controlador é "todo".</span><span class="sxs-lookup"><span data-stu-id="a0113-539">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="a0113-540">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="a0113-540">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="a0113-541">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="a0113-541">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="a0113-542">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="a0113-542">This sample doesn't use a template.</span></span> <span data-ttu-id="a0113-543">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="a0113-543">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="a0113-544">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="a0113-544">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="a0113-545">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="a0113-545">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="a0113-546">Valores retornados</span><span class="sxs-lookup"><span data-stu-id="a0113-546">Return values</span></span>

<span data-ttu-id="a0113-547">O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="a0113-547">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="a0113-548">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="a0113-548">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="a0113-549">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="a0113-549">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="a0113-550">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="a0113-550">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="a0113-551">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0113-551">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="a0113-552">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="a0113-552">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="a0113-553">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="a0113-553">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="a0113-554">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="a0113-554">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="a0113-555">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="a0113-555">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="a0113-556">Testar o método GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="a0113-556">Test the GetTodoItems method</span></span>

<span data-ttu-id="a0113-557">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="a0113-557">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="a0113-558">Instalar o [postmaster](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="a0113-558">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="a0113-559">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="a0113-559">Start the web app.</span></span>
* <span data-ttu-id="a0113-560">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="a0113-560">Start Postman.</span></span>
* <span data-ttu-id="a0113-561">Desabilite a **verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="a0113-561">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a0113-562">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0113-562">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a0113-563">Em **configurações** de > de **arquivo** (guia**geral** ), desabilite a **verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="a0113-563">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="a0113-564">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="a0113-564">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="a0113-565">Em **Postman** > **Preferências** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="a0113-565">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="a0113-566">Como alternativa, selecione a chave inglesa, selecione **Configurações** e desabilite a verificação do certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="a0113-566">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="a0113-567">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="a0113-567">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="a0113-568">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="a0113-568">Create a new request.</span></span>
  * <span data-ttu-id="a0113-569">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="a0113-569">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="a0113-570">Defina a URL de solicitação como `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="a0113-570">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="a0113-571">Por exemplo, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="a0113-571">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="a0113-572">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="a0113-572">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="a0113-573">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-573">Select **Send**.</span></span>

![Postman com solicitação GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="a0113-575">Adicionar um método Create</span><span class="sxs-lookup"><span data-stu-id="a0113-575">Add a Create method</span></span>

<span data-ttu-id="a0113-576">Adicione o seguinte método `PostTodoItem` dentro de *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="a0113-576">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="a0113-577">O código anterior é um método HTTP POST, conforme indicado pelo atributo [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) .</span><span class="sxs-lookup"><span data-stu-id="a0113-577">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="a0113-578">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0113-578">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="a0113-579">O método `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="a0113-579">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="a0113-580">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="a0113-580">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="a0113-581">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="a0113-581">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="a0113-582">Adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="a0113-582">Adds a `Location` header to the response.</span></span> <span data-ttu-id="a0113-583">O cabeçalho `Location` especifica o URI do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="a0113-583">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="a0113-584">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="a0113-584">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="a0113-585">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="a0113-585">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="a0113-586">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="a0113-586">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="a0113-587">Testar o método PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0113-587">Test the PostTodoItem method</span></span>

* <span data-ttu-id="a0113-588">Crie o projeto.</span><span class="sxs-lookup"><span data-stu-id="a0113-588">Build the project.</span></span>
* <span data-ttu-id="a0113-589">No Postman, defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="a0113-589">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="a0113-590">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="a0113-590">Select the **Body** tab.</span></span>
* <span data-ttu-id="a0113-591">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="a0113-591">Select the **raw** radio button.</span></span>
* <span data-ttu-id="a0113-592">Defina o tipo como **JSON (aplicativo/json)** .</span><span class="sxs-lookup"><span data-stu-id="a0113-592">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="a0113-593">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="a0113-593">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="a0113-594">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-594">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

  <span data-ttu-id="a0113-596">Se você receber um erro 405 Método Não Permitido, provavelmente, esse será o resultado da não compilação do projeto após a adição do método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="a0113-596">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="a0113-597">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="a0113-597">Test the location header URI</span></span>

* <span data-ttu-id="a0113-598">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="a0113-598">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="a0113-599">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="a0113-599">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="a0113-601">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="a0113-601">Set the method to GET.</span></span>
* <span data-ttu-id="a0113-602">Cole o URI (por exemplo, `https://localhost:5001/api/Todo/2`).</span><span class="sxs-lookup"><span data-stu-id="a0113-602">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="a0113-603">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-603">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="a0113-604">Adicionar um método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0113-604">Add a PutTodoItem method</span></span>

<span data-ttu-id="a0113-605">Em seguida, adicione o método `PutTodoItem` a seguir:</span><span class="sxs-lookup"><span data-stu-id="a0113-605">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="a0113-606">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="a0113-606">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="a0113-607">A resposta é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="a0113-607">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="a0113-608">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="a0113-608">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="a0113-609">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="a0113-609">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="a0113-610">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="a0113-610">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="a0113-611">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0113-611">Test the PutTodoItem method</span></span>

<span data-ttu-id="a0113-612">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="a0113-612">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="a0113-613">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="a0113-613">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="a0113-614">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="a0113-614">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="a0113-615">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="a0113-615">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="a0113-616">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="a0113-616">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="a0113-618">Adicionar um método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0113-618">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="a0113-619">Em seguida, adicione o método `DeleteTodoItem` a seguir:</span><span class="sxs-lookup"><span data-stu-id="a0113-619">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="a0113-620">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="a0113-620">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="a0113-621">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="a0113-621">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="a0113-622">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="a0113-622">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="a0113-623">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="a0113-623">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="a0113-624">Defina o URI do objeto a ser excluído (por exemplo `https://localhost:5001/api/todo/1`).</span><span class="sxs-lookup"><span data-stu-id="a0113-624">Set the URI of the object to delete (for example `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="a0113-625">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="a0113-625">Select **Send**.</span></span>

<span data-ttu-id="a0113-626">O aplicativo de exemplo permite que você exclua todos os itens.</span><span class="sxs-lookup"><span data-stu-id="a0113-626">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="a0113-627">No entanto, quando o último item é excluído, um novo é criado pelo construtor de classe de modelo na próxima vez que a API for chamada.</span><span class="sxs-lookup"><span data-stu-id="a0113-627">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="a0113-628">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="a0113-628">Call the web API with JavaScript</span></span>

<span data-ttu-id="a0113-629">Nesta seção, é adicionada uma página HTML que usa o JavaScript para chamar a API Web.</span><span class="sxs-lookup"><span data-stu-id="a0113-629">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="a0113-630">o jQuery inicia a solicitação.</span><span class="sxs-lookup"><span data-stu-id="a0113-630">jQuery initiates the request.</span></span> <span data-ttu-id="a0113-631">O JavaScript atualiza a página com os detalhes da resposta da API Web.</span><span class="sxs-lookup"><span data-stu-id="a0113-631">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="a0113-632">Configurar o aplicativo para [servir arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) atualizando *Startup.cs* com o código realçado a seguir:</span><span class="sxs-lookup"><span data-stu-id="a0113-632">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="a0113-633">Crie uma pasta *wwwroot* no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="a0113-633">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="a0113-634">Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="a0113-634">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="a0113-635">Substitua seu conteúdo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="a0113-635">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="a0113-636">Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="a0113-636">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="a0113-637">Substitua o conteúdo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="a0113-637">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="a0113-638">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="a0113-638">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="a0113-639">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a0113-639">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="a0113-640">Remova a propriedade `launchUrl` para forçar o aplicativo a ser aberto em *index.html*, o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="a0113-640">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="a0113-641">Esta amostra chama todos os métodos CRUD da API Web.</span><span class="sxs-lookup"><span data-stu-id="a0113-641">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="a0113-642">Veja a seguir explicações das chamadas à API.</span><span class="sxs-lookup"><span data-stu-id="a0113-642">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="a0113-643">Obter uma lista de itens pendentes</span><span class="sxs-lookup"><span data-stu-id="a0113-643">Get a list of to-do items</span></span>

<span data-ttu-id="a0113-644">o jQuery envia uma solicitação HTTP GET para a API da Web, que retorna JSON representando uma matriz de itens de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="a0113-644">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="a0113-645">A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="a0113-645">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="a0113-646">No retorno de chamada, o DOM é atualizado com as informações do item pendente.</span><span class="sxs-lookup"><span data-stu-id="a0113-646">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="a0113-647">Adicionar um item pendente</span><span class="sxs-lookup"><span data-stu-id="a0113-647">Add a to-do item</span></span>

<span data-ttu-id="a0113-648">o jQuery envia uma solicitação HTTP POST com o item de tarefas pendentes no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="a0113-648">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="a0113-649">As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado.</span><span class="sxs-lookup"><span data-stu-id="a0113-649">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="a0113-650">O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="a0113-650">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="a0113-651">Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="a0113-651">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="a0113-652">Atualizar um item pendente</span><span class="sxs-lookup"><span data-stu-id="a0113-652">Update a to-do item</span></span>

<span data-ttu-id="a0113-653">A atualização de um item pendente é semelhante à adição de um.</span><span class="sxs-lookup"><span data-stu-id="a0113-653">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="a0113-654">A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.</span><span class="sxs-lookup"><span data-stu-id="a0113-654">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="a0113-655">Excluir um item pendente</span><span class="sxs-lookup"><span data-stu-id="a0113-655">Delete a to-do item</span></span>

<span data-ttu-id="a0113-656">A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="a0113-656">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="a0113-657">Adicionar suporte de autenticação a uma API da Web</span><span class="sxs-lookup"><span data-stu-id="a0113-657">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="a0113-658">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a0113-658">Additional resources</span></span>

<span data-ttu-id="a0113-659">[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="a0113-659">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="a0113-660">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="a0113-660">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="a0113-661">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="a0113-661">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="a0113-662">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="a0113-662">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
