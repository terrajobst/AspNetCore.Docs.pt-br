---
title: 'Tutorial: Criar uma API Web com o ASP.NET Core'
author: rick-anderson
description: Saiba como criar uma API Web com o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 08/05/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 855d05fa2b9c1a7572212c40adbe61bb396f4bac
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68819839"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="36bca-103">Tutorial: Criar uma API Web com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36bca-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="36bca-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="36bca-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="36bca-105">Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="36bca-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="36bca-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="36bca-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="36bca-107">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="36bca-107">Create a web API project.</span></span>
> * <span data-ttu-id="36bca-108">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="36bca-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="36bca-109">Faça scaffold de um controlador com métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="36bca-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="36bca-110">Configure o roteamento, os caminhos de URL e os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="36bca-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="36bca-111">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="36bca-111">Call the web API with Postman.</span></span>

<span data-ttu-id="36bca-112">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="36bca-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="36bca-113">Visão geral</span><span class="sxs-lookup"><span data-stu-id="36bca-113">Overview</span></span>

<span data-ttu-id="36bca-114">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="36bca-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="36bca-115">API</span><span class="sxs-lookup"><span data-stu-id="36bca-115">API</span></span> | <span data-ttu-id="36bca-116">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="36bca-116">Description</span></span> | <span data-ttu-id="36bca-117">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="36bca-117">Request body</span></span> | <span data-ttu-id="36bca-118">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="36bca-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="36bca-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="36bca-119">GET /api/TodoItems</span></span> | <span data-ttu-id="36bca-120">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="36bca-120">Get all to-do items</span></span> | <span data-ttu-id="36bca-121">Nenhum</span><span class="sxs-lookup"><span data-stu-id="36bca-121">None</span></span> | <span data-ttu-id="36bca-122">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="36bca-122">Array of to-do items</span></span>|
|<span data-ttu-id="36bca-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="36bca-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="36bca-124">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="36bca-124">Get an item by ID</span></span> | <span data-ttu-id="36bca-125">Nenhum</span><span class="sxs-lookup"><span data-stu-id="36bca-125">None</span></span> | <span data-ttu-id="36bca-126">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="36bca-126">To-do item</span></span>|
|<span data-ttu-id="36bca-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="36bca-127">POST /api/TodoItems</span></span> | <span data-ttu-id="36bca-128">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="36bca-128">Add a new item</span></span> | <span data-ttu-id="36bca-129">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="36bca-129">To-do item</span></span> | <span data-ttu-id="36bca-130">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="36bca-130">To-do item</span></span> |
|<span data-ttu-id="36bca-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="36bca-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="36bca-132">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="36bca-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="36bca-133">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="36bca-133">To-do item</span></span> | <span data-ttu-id="36bca-134">Nenhum</span><span class="sxs-lookup"><span data-stu-id="36bca-134">None</span></span> |
|<span data-ttu-id="36bca-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="36bca-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="36bca-136">Excluir um item &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="36bca-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="36bca-137">Nenhum</span><span class="sxs-lookup"><span data-stu-id="36bca-137">None</span></span> | <span data-ttu-id="36bca-138">Nenhum</span><span class="sxs-lookup"><span data-stu-id="36bca-138">None</span></span>|

<span data-ttu-id="36bca-139">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36bca-139">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="36bca-145">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="36bca-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36bca-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36bca-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="36bca-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36bca-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="36bca-148">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36bca-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="36bca-149">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="36bca-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36bca-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36bca-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36bca-151">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="36bca-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="36bca-152">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="36bca-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="36bca-153">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="36bca-154">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 3.0** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="36bca-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="36bca-155">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-155">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="36bca-156">**Não** selecione **Habilitar Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="36bca-156">**Don't** select **Enable Docker Support**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="36bca-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36bca-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="36bca-159">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="36bca-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="36bca-160">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="36bca-160">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="36bca-161">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="36bca-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* <span data-ttu-id="36bca-162">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="36bca-162">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="36bca-163">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="36bca-163">The preceding commands:</span></span>

  * <span data-ttu-id="36bca-164">Cria um novo projeto de API Web e o abre no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="36bca-164">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="36bca-165">Adiciona os pacotes do NuGet que são exigidos na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="36bca-165">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="36bca-166">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36bca-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="36bca-167">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="36bca-167">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="36bca-169">Selecione **.NET Core** > **Aplicativo** > **API** > **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="36bca-169">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="36bca-171">Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, selecione a **Estrutura de Destino** do \* *.NET Core 3.0*.</span><span class="sxs-lookup"><span data-stu-id="36bca-171">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="36bca-172">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-172">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="36bca-174">Testar a API</span><span class="sxs-lookup"><span data-stu-id="36bca-174">Test the API</span></span>

<span data-ttu-id="36bca-175">O modelo de projeto cria uma API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="36bca-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="36bca-176">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36bca-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36bca-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36bca-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="36bca-178">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36bca-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="36bca-179">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/WeatherForecast`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="36bca-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="36bca-180">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="36bca-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="36bca-181">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="36bca-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="36bca-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36bca-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="36bca-183">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36bca-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="36bca-184">Em um navegador, acesse a seguinte URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="36bca-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="36bca-185">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36bca-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="36bca-186">Selecione **Executar** > **Iniciar com Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36bca-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="36bca-187">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="36bca-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="36bca-188">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="36bca-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="36bca-189">Acrescente `/WeatherForecast` à URL (altere a URL para `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="36bca-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="36bca-190">Um JSON semelhante ao seguinte será retornado:</span><span class="sxs-lookup"><span data-stu-id="36bca-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="36bca-191">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="36bca-191">Add a model class</span></span>

<span data-ttu-id="36bca-192">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36bca-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="36bca-193">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="36bca-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36bca-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36bca-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36bca-195">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="36bca-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="36bca-196">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="36bca-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="36bca-197">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="36bca-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="36bca-198">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="36bca-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="36bca-199">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="36bca-200">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="36bca-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="36bca-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36bca-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="36bca-202">Adicione uma pasta chamada *Models*.</span><span class="sxs-lookup"><span data-stu-id="36bca-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="36bca-203">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="36bca-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="36bca-204">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36bca-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="36bca-205">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="36bca-205">Right-click the project.</span></span> <span data-ttu-id="36bca-206">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="36bca-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="36bca-207">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="36bca-207">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="36bca-209">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="36bca-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="36bca-210">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="36bca-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="36bca-211">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="36bca-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="36bca-212">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="36bca-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="36bca-213">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="36bca-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="36bca-214">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="36bca-214">Add a database context</span></span>

<span data-ttu-id="36bca-215">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="36bca-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="36bca-216">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="36bca-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36bca-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36bca-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="36bca-218">Adicione Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="36bca-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="36bca-219">No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.</span><span class="sxs-lookup"><span data-stu-id="36bca-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="36bca-220">Selecione a caixa de seleção **Incluir Pré-lançamento**.</span><span class="sxs-lookup"><span data-stu-id="36bca-220">Select the **Include prerelease** checkbox.</span></span>
* <span data-ttu-id="36bca-221">Selecione a guia **Procurar** e, em seguida, insira **Microsoft.EntityFrameworkCore.SqlServer** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="36bca-221">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="36bca-222">Selecione **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="36bca-222">Select  **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** in the left pane.</span></span>
* <span data-ttu-id="36bca-223">Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-223">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="36bca-224">Use as instruções anteriores para adicionar o pacote do NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="36bca-224">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Gerenciador de pacotes do NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="36bca-226">Adicione o contexto de banco de dados TodoContext</span><span class="sxs-lookup"><span data-stu-id="36bca-226">Add the TodoContext database context</span></span>

* <span data-ttu-id="36bca-227">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="36bca-227">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="36bca-228">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-228">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="36bca-229">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36bca-229">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="36bca-230">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="36bca-230">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="36bca-231">Insira o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="36bca-231">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="36bca-232">Registrar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="36bca-232">Register the database context</span></span>

<span data-ttu-id="36bca-233">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="36bca-233">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="36bca-234">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="36bca-234">The container provides the service to controllers.</span></span>

<span data-ttu-id="36bca-235">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="36bca-235">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="36bca-236">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="36bca-236">The preceding code:</span></span>

* <span data-ttu-id="36bca-237">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="36bca-237">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="36bca-238">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="36bca-238">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="36bca-239">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="36bca-239">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="36bca-240">Faça scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="36bca-240">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36bca-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36bca-241">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36bca-242">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="36bca-242">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="36bca-243">Selecione **Adicionar** > **Novo item com scaffold**.</span><span class="sxs-lookup"><span data-stu-id="36bca-243">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="36bca-244">Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-244">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="36bca-245">Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework**:</span><span class="sxs-lookup"><span data-stu-id="36bca-245">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="36bca-246">Selecione **TodoItem (TodoAPI.Models**) na **classe Modelo**.</span><span class="sxs-lookup"><span data-stu-id="36bca-246">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="36bca-247">Selecione **TodoContext (TodoAPI.Models**) na **classe Contexto de Dados**.</span><span class="sxs-lookup"><span data-stu-id="36bca-247">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="36bca-248">Selecione **Adicionar**</span><span class="sxs-lookup"><span data-stu-id="36bca-248">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="36bca-249">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36bca-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="36bca-250">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="36bca-250">Run the following commands:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="36bca-251">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="36bca-251">The preceding commands:</span></span>

* <span data-ttu-id="36bca-252">Adicionam os pacotes do NuGet para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="36bca-252">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="36bca-253">Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="36bca-253">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="36bca-254">Fazem scaffold de `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="36bca-254">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="36bca-255">O código gerado:</span><span class="sxs-lookup"><span data-stu-id="36bca-255">The generated code:</span></span>

* <span data-ttu-id="36bca-256">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="36bca-256">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="36bca-257">Decora a classe com o atributo [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="36bca-257">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="36bca-258">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="36bca-258">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="36bca-259">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="36bca-259">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="36bca-260">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="36bca-260">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="36bca-261">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="36bca-261">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="36bca-262">Examine o método criar do PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="36bca-262">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="36bca-263">Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="36bca-263">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="36bca-264">O código anterior é um método HTTP POST, conforme indicado pelo atributo [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="36bca-264">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="36bca-265">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="36bca-265">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="36bca-266">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="36bca-266">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="36bca-267">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="36bca-267">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="36bca-268">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="36bca-268">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="36bca-269">Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta.</span><span class="sxs-lookup"><span data-stu-id="36bca-269">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="36bca-270">O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="36bca-270">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="36bca-271">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="36bca-271">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="36bca-272">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="36bca-272">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="36bca-273">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="36bca-273">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="36bca-274">Instale o Postman</span><span class="sxs-lookup"><span data-stu-id="36bca-274">Install Postman</span></span>

<span data-ttu-id="36bca-275">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="36bca-275">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="36bca-276">Instale o [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="36bca-276">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="36bca-277">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="36bca-277">Start the web app.</span></span>
* <span data-ttu-id="36bca-278">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="36bca-278">Start Postman.</span></span>
* <span data-ttu-id="36bca-279">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="36bca-279">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="36bca-280">Em **Arquivo > Configurações** (guia \**Geral*), desabilite **Verificação do certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="36bca-280">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="36bca-281">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="36bca-281">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="36bca-282">Teste o PostTodoItem com o Postman</span><span class="sxs-lookup"><span data-stu-id="36bca-282">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="36bca-283">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="36bca-283">Create a new request.</span></span>
* <span data-ttu-id="36bca-284">Defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="36bca-284">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="36bca-285">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="36bca-285">Select the **Body** tab.</span></span>
* <span data-ttu-id="36bca-286">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="36bca-286">Select the **raw** radio button.</span></span>
* <span data-ttu-id="36bca-287">Defina o tipo como **JSON (aplicativo/json)** .</span><span class="sxs-lookup"><span data-stu-id="36bca-287">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="36bca-288">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="36bca-288">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="36bca-289">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-289">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="36bca-291">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="36bca-291">Test the location header URI</span></span>

* <span data-ttu-id="36bca-292">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="36bca-292">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="36bca-293">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="36bca-293">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/3/create.png)

* <span data-ttu-id="36bca-295">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="36bca-295">Set the method to GET.</span></span>
* <span data-ttu-id="36bca-296">Cole o URI (por exemplo, `https://localhost:5001/api/TodoItems/1`)</span><span class="sxs-lookup"><span data-stu-id="36bca-296">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="36bca-297">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-297">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="36bca-298">Examine os métodos GET</span><span class="sxs-lookup"><span data-stu-id="36bca-298">Examine the GET methods</span></span>

<span data-ttu-id="36bca-299">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="36bca-299">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="36bca-300">Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman.</span><span class="sxs-lookup"><span data-stu-id="36bca-300">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="36bca-301">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="36bca-301">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="36bca-302">Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="36bca-302">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="36bca-303">Teste o GET com o Postman</span><span class="sxs-lookup"><span data-stu-id="36bca-303">Test Get with Postman</span></span>

* <span data-ttu-id="36bca-304">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="36bca-304">Create a new request.</span></span>
* <span data-ttu-id="36bca-305">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="36bca-305">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="36bca-306">Defina a URL de solicitação como `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="36bca-306">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="36bca-307">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="36bca-307">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="36bca-308">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="36bca-308">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="36bca-309">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-309">Select **Send**.</span></span>

<span data-ttu-id="36bca-310">Este aplicativo usa um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="36bca-310">This app uses an in-memory database.</span></span> <span data-ttu-id="36bca-311">Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado.</span><span class="sxs-lookup"><span data-stu-id="36bca-311">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="36bca-312">Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36bca-312">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="36bca-313">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="36bca-313">Routing and URL paths</span></span>

<span data-ttu-id="36bca-314">O atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica um método que responde a uma solicitação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="36bca-314">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="36bca-315">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="36bca-315">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="36bca-316">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="36bca-316">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="36bca-317">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="36bca-317">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="36bca-318">Para esta amostra, o nome da classe do controlador é **TodoItems**Controller. Portanto, o nome do controlador é "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="36bca-318">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="36bca-319">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="36bca-319">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="36bca-320">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="36bca-320">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="36bca-321">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="36bca-321">This sample doesn't use a template.</span></span> <span data-ttu-id="36bca-322">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="36bca-322">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="36bca-323">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="36bca-323">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="36bca-324">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="36bca-324">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="36bca-325">Valores de retorno</span><span class="sxs-lookup"><span data-stu-id="36bca-325">Return values</span></span>

<span data-ttu-id="36bca-326">O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="36bca-326">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="36bca-327">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="36bca-327">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="36bca-328">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="36bca-328">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="36bca-329">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="36bca-329">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="36bca-330">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="36bca-330">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="36bca-331">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="36bca-331">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="36bca-332">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="36bca-332">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="36bca-333">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="36bca-333">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="36bca-334">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="36bca-334">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="36bca-335">O método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="36bca-335">The PutTodoItem method</span></span>

<span data-ttu-id="36bca-336">Examine o método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="36bca-336">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="36bca-337">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="36bca-337">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="36bca-338">A resposta é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="36bca-338">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="36bca-339">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="36bca-339">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="36bca-340">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="36bca-340">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="36bca-341">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="36bca-341">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="36bca-342">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="36bca-342">Test the PutTodoItem method</span></span>

<span data-ttu-id="36bca-343">Este exemplo usa um banco de dados em memória que deverá ser iniciado sempre que o aplicativo for iniciado.</span><span class="sxs-lookup"><span data-stu-id="36bca-343">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="36bca-344">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="36bca-344">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="36bca-345">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="36bca-345">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="36bca-346">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="36bca-346">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="36bca-347">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="36bca-347">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="36bca-349">O método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="36bca-349">The DeleteTodoItem method</span></span>

<span data-ttu-id="36bca-350">Examine o método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="36bca-350">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="36bca-351">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="36bca-351">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="36bca-352">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="36bca-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="36bca-353">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="36bca-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="36bca-354">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="36bca-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="36bca-355">Defina o URI do objeto a ser excluído, por exemplo, `https://localhost:5001/api/TodoItems/1`</span><span class="sxs-lookup"><span data-stu-id="36bca-355">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="36bca-356">Selecione **Enviar**</span><span class="sxs-lookup"><span data-stu-id="36bca-356">Select **Send**</span></span>

## <a name="call-the-api-from-jquery"></a><span data-ttu-id="36bca-357">Chame a API por meio do jQuery</span><span class="sxs-lookup"><span data-stu-id="36bca-357">Call the API from jQuery</span></span>

<span data-ttu-id="36bca-358">Consulte [Tutorial: Chamar uma API Web do ASP.NET Core com o jQuery](xref:tutorials/web-api-jquery).</span><span class="sxs-lookup"><span data-stu-id="36bca-358">See [Tutorial: Call an ASP.NET Core web API with jQuery](xref:tutorials/web-api-jquery).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="36bca-359">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="36bca-359">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="36bca-360">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="36bca-360">Create a web API project.</span></span>
> * <span data-ttu-id="36bca-361">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="36bca-361">Add a model class and a database context.</span></span>
> * <span data-ttu-id="36bca-362">Adicionar um controlador.</span><span class="sxs-lookup"><span data-stu-id="36bca-362">Add a controller.</span></span>
> * <span data-ttu-id="36bca-363">Adicionar métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="36bca-363">Add CRUD methods.</span></span>
> * <span data-ttu-id="36bca-364">Configurar o roteamento e caminhos de URL.</span><span class="sxs-lookup"><span data-stu-id="36bca-364">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="36bca-365">Especificar os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="36bca-365">Specify return values.</span></span>
> * <span data-ttu-id="36bca-366">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="36bca-366">Call the web API with Postman.</span></span>
> * <span data-ttu-id="36bca-367">Chamar a API Web com o jQuery.</span><span class="sxs-lookup"><span data-stu-id="36bca-367">Call the web API with jQuery.</span></span>

<span data-ttu-id="36bca-368">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="36bca-368">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>
## <a name="overview"></a><span data-ttu-id="36bca-369">Visão geral</span><span class="sxs-lookup"><span data-stu-id="36bca-369">Overview</span></span>

<span data-ttu-id="36bca-370">Este tutorial cria a seguinte API:</span><span class="sxs-lookup"><span data-stu-id="36bca-370">This tutorial creates the following API:</span></span>

|<span data-ttu-id="36bca-371">API</span><span class="sxs-lookup"><span data-stu-id="36bca-371">API</span></span> | <span data-ttu-id="36bca-372">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="36bca-372">Description</span></span> | <span data-ttu-id="36bca-373">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="36bca-373">Request body</span></span> | <span data-ttu-id="36bca-374">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="36bca-374">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="36bca-375">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="36bca-375">GET /api/TodoItems</span></span> | <span data-ttu-id="36bca-376">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="36bca-376">Get all to-do items</span></span> | <span data-ttu-id="36bca-377">Nenhum</span><span class="sxs-lookup"><span data-stu-id="36bca-377">None</span></span> | <span data-ttu-id="36bca-378">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="36bca-378">Array of to-do items</span></span>|
|<span data-ttu-id="36bca-379">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="36bca-379">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="36bca-380">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="36bca-380">Get an item by ID</span></span> | <span data-ttu-id="36bca-381">Nenhum</span><span class="sxs-lookup"><span data-stu-id="36bca-381">None</span></span> | <span data-ttu-id="36bca-382">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="36bca-382">To-do item</span></span>|
|<span data-ttu-id="36bca-383">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="36bca-383">POST /api/TodoItems</span></span> | <span data-ttu-id="36bca-384">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="36bca-384">Add a new item</span></span> | <span data-ttu-id="36bca-385">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="36bca-385">To-do item</span></span> | <span data-ttu-id="36bca-386">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="36bca-386">To-do item</span></span> |
|<span data-ttu-id="36bca-387">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="36bca-387">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="36bca-388">Atualizar um item &nbsp; existente</span><span class="sxs-lookup"><span data-stu-id="36bca-388">Update an existing item &nbsp;</span></span> | <span data-ttu-id="36bca-389">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="36bca-389">To-do item</span></span> | <span data-ttu-id="36bca-390">Nenhum</span><span class="sxs-lookup"><span data-stu-id="36bca-390">None</span></span> |
|<span data-ttu-id="36bca-391">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="36bca-391">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="36bca-392">Excluir um item &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="36bca-392">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="36bca-393">Nenhum</span><span class="sxs-lookup"><span data-stu-id="36bca-393">None</span></span> | <span data-ttu-id="36bca-394">Nenhum</span><span class="sxs-lookup"><span data-stu-id="36bca-394">None</span></span>|

<span data-ttu-id="36bca-395">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36bca-395">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="36bca-401">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="36bca-401">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36bca-402">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36bca-402">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="36bca-403">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36bca-403">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="36bca-404">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36bca-404">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="36bca-405">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="36bca-405">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36bca-406">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36bca-406">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36bca-407">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="36bca-407">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="36bca-408">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="36bca-408">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="36bca-409">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-409">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="36bca-410">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 2.2** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="36bca-410">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="36bca-411">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-411">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="36bca-412">**Não** selecione **Habilitar Suporte ao Docker**.</span><span class="sxs-lookup"><span data-stu-id="36bca-412">**Don't** select **Enable Docker Support**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="36bca-414">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36bca-414">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="36bca-415">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="36bca-415">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="36bca-416">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="36bca-416">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="36bca-417">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="36bca-417">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="36bca-418">Esses comandos criam um novo projeto de API Web e abrem uma nova instância do Visual Studio Code na nova pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="36bca-418">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="36bca-419">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="36bca-419">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="36bca-420">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36bca-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="36bca-421">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="36bca-421">Select **File** > **New Solution**.</span></span>

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="36bca-423">Selecione **.NET Core** > **Aplicativo** > **API** > **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="36bca-423">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![Caixa de diálogo Novo projeto do macOS](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="36bca-425">Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, aceite a **Estrutura de Destino** padrão \* *.NET Core 2.2*.</span><span class="sxs-lookup"><span data-stu-id="36bca-425">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="36bca-426">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-426">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="36bca-428">Testar a API</span><span class="sxs-lookup"><span data-stu-id="36bca-428">Test the API</span></span>

<span data-ttu-id="36bca-429">O modelo de projeto cria uma API `values`.</span><span class="sxs-lookup"><span data-stu-id="36bca-429">The project template creates a `values` API.</span></span> <span data-ttu-id="36bca-430">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36bca-430">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36bca-431">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36bca-431">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="36bca-432">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36bca-432">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="36bca-433">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/api/values`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="36bca-433">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="36bca-434">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="36bca-434">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="36bca-435">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="36bca-435">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="36bca-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36bca-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="36bca-437">Pressione CTRL+F5 para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36bca-437">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="36bca-438">Em um navegador, acesse a seguinte URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span><span class="sxs-lookup"><span data-stu-id="36bca-438">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="36bca-439">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36bca-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="36bca-440">Selecione **Executar** > **Iniciar com Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36bca-440">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="36bca-441">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="36bca-441">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="36bca-442">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="36bca-442">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="36bca-443">Acrescente `/api/values` à URL (altere a URL para `https://localhost:<port>/api/values`).</span><span class="sxs-lookup"><span data-stu-id="36bca-443">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="36bca-444">O seguinte JSON é retornado:</span><span class="sxs-lookup"><span data-stu-id="36bca-444">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="36bca-445">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="36bca-445">Add a model class</span></span>

<span data-ttu-id="36bca-446">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="36bca-446">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="36bca-447">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="36bca-447">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36bca-448">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36bca-448">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36bca-449">No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="36bca-449">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="36bca-450">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="36bca-450">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="36bca-451">Nomeie a pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="36bca-451">Name the folder *Models*.</span></span>

* <span data-ttu-id="36bca-452">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="36bca-452">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="36bca-453">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-453">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="36bca-454">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="36bca-454">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="36bca-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="36bca-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="36bca-456">Adicione uma pasta chamada *Models*.</span><span class="sxs-lookup"><span data-stu-id="36bca-456">Add a folder named *Models*.</span></span>

* <span data-ttu-id="36bca-457">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="36bca-457">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="36bca-458">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36bca-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="36bca-459">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="36bca-459">Right-click the project.</span></span> <span data-ttu-id="36bca-460">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="36bca-460">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="36bca-461">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="36bca-461">Name the folder *Models*.</span></span>

  ![nova pasta](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="36bca-463">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="36bca-463">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="36bca-464">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="36bca-464">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="36bca-465">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="36bca-465">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="36bca-466">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="36bca-466">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="36bca-467">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="36bca-467">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="36bca-468">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="36bca-468">Add a database context</span></span>

<span data-ttu-id="36bca-469">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="36bca-469">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="36bca-470">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="36bca-470">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36bca-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36bca-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36bca-472">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="36bca-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="36bca-473">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-473">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="36bca-474">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36bca-474">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="36bca-475">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="36bca-475">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="36bca-476">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="36bca-476">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="36bca-477">Registrar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="36bca-477">Register the database context</span></span>

<span data-ttu-id="36bca-478">No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="36bca-478">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="36bca-479">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="36bca-479">The container provides the service to controllers.</span></span>

<span data-ttu-id="36bca-480">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="36bca-480">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="36bca-481">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="36bca-481">The preceding code:</span></span>

* <span data-ttu-id="36bca-482">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="36bca-482">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="36bca-483">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="36bca-483">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="36bca-484">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="36bca-484">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="36bca-485">Adicionar um controlador</span><span class="sxs-lookup"><span data-stu-id="36bca-485">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36bca-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36bca-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36bca-487">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="36bca-487">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="36bca-488">Selecione **Adicionar** > **Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="36bca-488">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="36bca-489">Na caixa de diálogo **Adicionar Novo Item**, selecione o modelo **Classe do Controlador de API**.</span><span class="sxs-lookup"><span data-stu-id="36bca-489">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="36bca-490">Dê à classe o nome *TodoController* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-490">Name the class *TodoController*, and select **Add**.</span></span>

  ![Caixa de diálogo Adicionar Novo Item com o controlador na caixa de pesquisa e o controlador da API Web selecionados](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="36bca-492">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36bca-492">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="36bca-493">Na pasta *Controllers*, crie uma classe chamada `TodoController`.</span><span class="sxs-lookup"><span data-stu-id="36bca-493">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="36bca-494">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="36bca-494">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="36bca-495">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="36bca-495">The preceding code:</span></span>

* <span data-ttu-id="36bca-496">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="36bca-496">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="36bca-497">Decora a classe com o atributo [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute).</span><span class="sxs-lookup"><span data-stu-id="36bca-497">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="36bca-498">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="36bca-498">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="36bca-499">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="36bca-499">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="36bca-500">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="36bca-500">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="36bca-501">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="36bca-501">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="36bca-502">Adiciona um item chamado `Item1` ao banco de dados se o banco de dados está vazio.</span><span class="sxs-lookup"><span data-stu-id="36bca-502">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="36bca-503">Esse código está no construtor, de modo que ele seja executado sempre que há uma nova solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="36bca-503">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="36bca-504">Se você excluir todos os itens, o construtor criará `Item1` novamente na próxima vez que um método de API for chamado.</span><span class="sxs-lookup"><span data-stu-id="36bca-504">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="36bca-505">Portanto, pode parecer que a exclusão não funcionou quando ela realmente funcionou.</span><span class="sxs-lookup"><span data-stu-id="36bca-505">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="36bca-506">Adicionar métodos Get</span><span class="sxs-lookup"><span data-stu-id="36bca-506">Add Get methods</span></span>

<span data-ttu-id="36bca-507">Para fornecer uma API que recupera itens pendentes, adicione os seguintes métodos à classe `TodoController`:</span><span class="sxs-lookup"><span data-stu-id="36bca-507">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="36bca-508">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="36bca-508">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="36bca-509">Interrompa o aplicativo se ele ainda estiver em execução.</span><span class="sxs-lookup"><span data-stu-id="36bca-509">Stop the app if it's still running.</span></span> <span data-ttu-id="36bca-510">Em seguida, execute-o novamente para incluir as alterações mais recentes.</span><span class="sxs-lookup"><span data-stu-id="36bca-510">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="36bca-511">Teste o aplicativo chamando os dois pontos de extremidade em um navegador.</span><span class="sxs-lookup"><span data-stu-id="36bca-511">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="36bca-512">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="36bca-512">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="36bca-513">A seguinte resposta HTTP é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="36bca-513">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="36bca-514">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="36bca-514">Routing and URL paths</span></span>

<span data-ttu-id="36bca-515">O atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica um método que responde a uma solicitação HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="36bca-515">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="36bca-516">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="36bca-516">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="36bca-517">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="36bca-517">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="36bca-518">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="36bca-518">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="36bca-519">Para esta amostra, o nome da classe do controlador é **Todo**Controller e, portanto, o nome do controlador é "todo".</span><span class="sxs-lookup"><span data-stu-id="36bca-519">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="36bca-520">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="36bca-520">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="36bca-521">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="36bca-521">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="36bca-522">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="36bca-522">This sample doesn't use a template.</span></span> <span data-ttu-id="36bca-523">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="36bca-523">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="36bca-524">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="36bca-524">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="36bca-525">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="36bca-525">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="36bca-526">Valores de retorno</span><span class="sxs-lookup"><span data-stu-id="36bca-526">Return values</span></span>

<span data-ttu-id="36bca-527">O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="36bca-527">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="36bca-528">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="36bca-528">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="36bca-529">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="36bca-529">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="36bca-530">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="36bca-530">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="36bca-531">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="36bca-531">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="36bca-532">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="36bca-532">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="36bca-533">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).</span><span class="sxs-lookup"><span data-stu-id="36bca-533">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="36bca-534">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="36bca-534">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="36bca-535">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="36bca-535">Returning `item` results in an HTTP 200 response.</span></span>


## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="36bca-536">Testar o método GetTodoItems</span><span class="sxs-lookup"><span data-stu-id="36bca-536">Test the GetTodoItems method</span></span>

<span data-ttu-id="36bca-537">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="36bca-537">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="36bca-538">Instale o [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="36bca-538">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="36bca-539">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="36bca-539">Start the web app.</span></span>
* <span data-ttu-id="36bca-540">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="36bca-540">Start Postman.</span></span>
* <span data-ttu-id="36bca-541">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="36bca-541">Disable **SSL certificate verification**</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="36bca-542">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="36bca-542">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="36bca-543">Em **Arquivo** > **Configurações** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="36bca-543">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="36bca-544">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="36bca-544">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="36bca-545">Em **Postman** > **Preferências** (guia **Geral**), desabilite **Verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="36bca-545">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="36bca-546">Como alternativa, selecione a chave inglesa, selecione **Configurações** e desabilite a verificação do certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="36bca-546">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="36bca-547">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="36bca-547">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="36bca-548">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="36bca-548">Create a new request.</span></span>
  * <span data-ttu-id="36bca-549">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="36bca-549">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="36bca-550">Defina a URL de solicitação como `https://localhost:<port>/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="36bca-550">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="36bca-551">Por exemplo, `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="36bca-551">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="36bca-552">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="36bca-552">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="36bca-553">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-553">Select **Send**.</span></span>

![Postman com solicitação GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="36bca-555">Adicionar um método Create</span><span class="sxs-lookup"><span data-stu-id="36bca-555">Add a Create method</span></span>

<span data-ttu-id="36bca-556">Adicione o seguinte método `PostTodoItem` dentro de *Controllers/TodoController.cs*:</span><span class="sxs-lookup"><span data-stu-id="36bca-556">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="36bca-557">O código anterior é um método HTTP POST, conforme indicado pelo atributo [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute).</span><span class="sxs-lookup"><span data-stu-id="36bca-557">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="36bca-558">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="36bca-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="36bca-559">O método `CreatedAtAction`:</span><span class="sxs-lookup"><span data-stu-id="36bca-559">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="36bca-560">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="36bca-560">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="36bca-561">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="36bca-561">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="36bca-562">Adiciona um cabeçalho `Location` à resposta.</span><span class="sxs-lookup"><span data-stu-id="36bca-562">Adds a `Location` header to the response.</span></span> <span data-ttu-id="36bca-563">O cabeçalho `Location` especifica o URI do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="36bca-563">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="36bca-564">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="36bca-564">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="36bca-565">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="36bca-565">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="36bca-566">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="36bca-566">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="36bca-567">Testar o método PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="36bca-567">Test the PostTodoItem method</span></span>

* <span data-ttu-id="36bca-568">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="36bca-568">Build the project.</span></span>
* <span data-ttu-id="36bca-569">No Postman, defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="36bca-569">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="36bca-570">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="36bca-570">Select the **Body** tab.</span></span>
* <span data-ttu-id="36bca-571">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="36bca-571">Select the **raw** radio button.</span></span>
* <span data-ttu-id="36bca-572">Defina o tipo como **JSON (aplicativo/json)** .</span><span class="sxs-lookup"><span data-stu-id="36bca-572">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="36bca-573">No corpo da solicitação, insira JSON para um item pendente:</span><span class="sxs-lookup"><span data-stu-id="36bca-573">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="36bca-574">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-574">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

  <span data-ttu-id="36bca-576">Se você receber um erro 405 Método Não Permitido, provavelmente, esse será o resultado da não compilação do projeto após a adição do método `PostTodoItem`.</span><span class="sxs-lookup"><span data-stu-id="36bca-576">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="36bca-577">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="36bca-577">Test the location header URI</span></span>

* <span data-ttu-id="36bca-578">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="36bca-578">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="36bca-579">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="36bca-579">Copy the **Location** header value:</span></span>

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/pmc2.png)

* <span data-ttu-id="36bca-581">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="36bca-581">Set the method to GET.</span></span>
* <span data-ttu-id="36bca-582">Cole o URI (por exemplo, `https://localhost:5001/api/Todo/2`)</span><span class="sxs-lookup"><span data-stu-id="36bca-582">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="36bca-583">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="36bca-583">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="36bca-584">Adicionar um método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="36bca-584">Add a PutTodoItem method</span></span>

<span data-ttu-id="36bca-585">Adicione o seguinte método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="36bca-585">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="36bca-586">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="36bca-586">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="36bca-587">A resposta é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="36bca-587">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="36bca-588">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="36bca-588">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="36bca-589">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="36bca-589">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="36bca-590">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="36bca-590">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="36bca-591">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="36bca-591">Test the PutTodoItem method</span></span>

<span data-ttu-id="36bca-592">Este exemplo usa um banco de dados em memória que deverá ser iniciado sempre que o aplicativo for iniciado.</span><span class="sxs-lookup"><span data-stu-id="36bca-592">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="36bca-593">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="36bca-593">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="36bca-594">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="36bca-594">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="36bca-595">Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":</span><span class="sxs-lookup"><span data-stu-id="36bca-595">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="36bca-596">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="36bca-596">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="36bca-598">Adicionar um método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="36bca-598">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="36bca-599">Adicione o seguinte método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="36bca-599">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="36bca-600">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="36bca-600">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="36bca-601">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="36bca-601">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="36bca-602">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="36bca-602">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="36bca-603">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="36bca-603">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="36bca-604">Defina o URI do objeto a ser excluído, por exemplo, `https://localhost:5001/api/todo/1`</span><span class="sxs-lookup"><span data-stu-id="36bca-604">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="36bca-605">Selecione **Enviar**</span><span class="sxs-lookup"><span data-stu-id="36bca-605">Select **Send**</span></span>

<span data-ttu-id="36bca-606">O aplicativo de exemplo permite que você exclua todos os itens.</span><span class="sxs-lookup"><span data-stu-id="36bca-606">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="36bca-607">No entanto, quando o último item é excluído, um novo é criado pelo construtor de classe de modelo na próxima vez que a API for chamada.</span><span class="sxs-lookup"><span data-stu-id="36bca-607">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="36bca-608">Chamar a API com o jQuery</span><span class="sxs-lookup"><span data-stu-id="36bca-608">Call the API with jQuery</span></span>

<span data-ttu-id="36bca-609">Nesta seção, uma página HTML que usa o jQuery para chamar a API Web é adicionada.</span><span class="sxs-lookup"><span data-stu-id="36bca-609">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="36bca-610">O jQuery inicia a solicitação e atualiza a página com os detalhes da resposta da API.</span><span class="sxs-lookup"><span data-stu-id="36bca-610">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="36bca-611">Configurar o aplicativo para [servir arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) atualizando *Startup.cs* com o código realçado a seguir:</span><span class="sxs-lookup"><span data-stu-id="36bca-611">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="36bca-612">Crie uma pasta *wwwroot* no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="36bca-612">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="36bca-613">Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="36bca-613">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="36bca-614">Substitua seu conteúdo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="36bca-614">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="36bca-615">Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="36bca-615">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="36bca-616">Substitua seu conteúdo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="36bca-616">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="36bca-617">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="36bca-617">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="36bca-618">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="36bca-618">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="36bca-619">Remova a propriedade `launchUrl` para forçar o aplicativo a ser aberto em *index.html*, o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="36bca-619">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="36bca-620">Há várias maneiras de obter o jQuery.</span><span class="sxs-lookup"><span data-stu-id="36bca-620">There are several ways to get jQuery.</span></span> <span data-ttu-id="36bca-621">No snippet anterior, a biblioteca é carregada de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="36bca-621">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="36bca-622">Esta amostra chama todos os métodos CRUD da API.</span><span class="sxs-lookup"><span data-stu-id="36bca-622">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="36bca-623">Veja a seguir explicações das chamadas à API.</span><span class="sxs-lookup"><span data-stu-id="36bca-623">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="36bca-624">Obter uma lista de itens pendentes</span><span class="sxs-lookup"><span data-stu-id="36bca-624">Get a list of to-do items</span></span>

<span data-ttu-id="36bca-625">A função [ajax](https://api.jquery.com/jquery.ajax/) do jQuery envia uma solicitação `GET` para a API, que retorna o JSON que representa uma matriz de itens pendentes.</span><span class="sxs-lookup"><span data-stu-id="36bca-625">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="36bca-626">A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="36bca-626">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="36bca-627">No retorno de chamada, o DOM é atualizado com as informações do item pendente.</span><span class="sxs-lookup"><span data-stu-id="36bca-627">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="36bca-628">Adicionar um item pendente</span><span class="sxs-lookup"><span data-stu-id="36bca-628">Add a to-do item</span></span>

<span data-ttu-id="36bca-629">A função [ajax](https://api.jquery.com/jquery.ajax/) envia uma solicitação `POST` com o item pendente no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="36bca-629">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="36bca-630">As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado.</span><span class="sxs-lookup"><span data-stu-id="36bca-630">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="36bca-631">O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="36bca-631">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="36bca-632">Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="36bca-632">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="36bca-633">Atualizar um item pendente</span><span class="sxs-lookup"><span data-stu-id="36bca-633">Update a to-do item</span></span>

<span data-ttu-id="36bca-634">A atualização de um item pendente é semelhante à adição de um.</span><span class="sxs-lookup"><span data-stu-id="36bca-634">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="36bca-635">A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.</span><span class="sxs-lookup"><span data-stu-id="36bca-635">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="36bca-636">Excluir um item pendente</span><span class="sxs-lookup"><span data-stu-id="36bca-636">Delete a to-do item</span></span>

<span data-ttu-id="36bca-637">A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="36bca-637">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="36bca-638">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="36bca-638">Additional resources</span></span>

<span data-ttu-id="36bca-639">[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="36bca-639">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="36bca-640">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="36bca-640">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="36bca-641">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="36bca-641">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="36bca-642">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="36bca-642">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
