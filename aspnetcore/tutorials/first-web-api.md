---
title: 'Tutorial: criar uma API Web com ASP.NET Core'
author: rick-anderson
description: Saiba como criar uma API Web com o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: tutorials/first-web-api
ms.openlocfilehash: b4c88f5dc7853396448a2a6122f3652f92079e68
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72541823"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="5e02f-103">Tutorial: criar uma API Web com ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5e02f-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="5e02f-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="5e02f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="5e02f-105">Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5e02f-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

<span data-ttu-id="5e02f-106">Neste tutorial, você aprenderá como:</span><span class="sxs-lookup"><span data-stu-id="5e02f-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5e02f-107">Criar um projeto de API Web.</span><span class="sxs-lookup"><span data-stu-id="5e02f-107">Create a web API project.</span></span>
> * <span data-ttu-id="5e02f-108">Adicione uma classe de modelo e um contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5e02f-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="5e02f-109">Faça scaffold de um controlador com métodos CRUD.</span><span class="sxs-lookup"><span data-stu-id="5e02f-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="5e02f-110">Configure o roteamento, os caminhos de URL e os valores retornados.</span><span class="sxs-lookup"><span data-stu-id="5e02f-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="5e02f-111">Chamar a API Web com o Postman.</span><span class="sxs-lookup"><span data-stu-id="5e02f-111">Call the web API with Postman.</span></span>

<span data-ttu-id="5e02f-112">No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5e02f-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="5e02f-113">Visão Geral</span><span class="sxs-lookup"><span data-stu-id="5e02f-113">Overview</span></span>

<span data-ttu-id="5e02f-114">Este tutorial cria uma API Web que contém os seguintes pontos de extremidade:</span><span class="sxs-lookup"><span data-stu-id="5e02f-114">This tutorial creates a web API containing the following endpoints:</span></span>

|<span data-ttu-id="5e02f-115">Ponto de extremidade</span><span class="sxs-lookup"><span data-stu-id="5e02f-115">Endpoint</span></span>                  |<span data-ttu-id="5e02f-116">Descrição</span><span class="sxs-lookup"><span data-stu-id="5e02f-116">Description</span></span>            |<span data-ttu-id="5e02f-117">Corpo da solicitação</span><span class="sxs-lookup"><span data-stu-id="5e02f-117">Request body</span></span>|<span data-ttu-id="5e02f-118">Corpo da resposta</span><span class="sxs-lookup"><span data-stu-id="5e02f-118">Response body</span></span>       |
|--------------------------|-----------------------|------------|--------------------|
|<span data-ttu-id="5e02f-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="5e02f-119">GET /api/TodoItems</span></span>        |<span data-ttu-id="5e02f-120">Obter todos os itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="5e02f-120">Get all to-do items</span></span>    |<span data-ttu-id="5e02f-121">Nenhum</span><span class="sxs-lookup"><span data-stu-id="5e02f-121">None</span></span>        |<span data-ttu-id="5e02f-122">Matriz de itens de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="5e02f-122">Array of to-do items</span></span>|
|<span data-ttu-id="5e02f-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="5e02f-123">GET /api/TodoItems/{id}</span></span>   |<span data-ttu-id="5e02f-124">Obter um item por ID</span><span class="sxs-lookup"><span data-stu-id="5e02f-124">Get an item by ID</span></span>      |<span data-ttu-id="5e02f-125">Nenhum</span><span class="sxs-lookup"><span data-stu-id="5e02f-125">None</span></span>        |<span data-ttu-id="5e02f-126">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="5e02f-126">To-do item</span></span>          |
|<span data-ttu-id="5e02f-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="5e02f-127">POST /api/TodoItems</span></span>       |<span data-ttu-id="5e02f-128">Adicionar um novo item</span><span class="sxs-lookup"><span data-stu-id="5e02f-128">Add a new item</span></span>         |<span data-ttu-id="5e02f-129">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="5e02f-129">To-do item</span></span>  |<span data-ttu-id="5e02f-130">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="5e02f-130">To-do item</span></span>          |
|<span data-ttu-id="5e02f-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="5e02f-131">PUT /api/TodoItems/{id}</span></span>   |<span data-ttu-id="5e02f-132">Atualizar um item existente</span><span class="sxs-lookup"><span data-stu-id="5e02f-132">Update an existing item</span></span>|<span data-ttu-id="5e02f-133">Item de tarefas pendentes</span><span class="sxs-lookup"><span data-stu-id="5e02f-133">To-do item</span></span>  |<span data-ttu-id="5e02f-134">Nenhum</span><span class="sxs-lookup"><span data-stu-id="5e02f-134">None</span></span>                |
|<span data-ttu-id="5e02f-135">EXCLUIR/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="5e02f-135">DELETE /api/TodoItems/{id}</span></span>|<span data-ttu-id="5e02f-136">Excluir um item</span><span class="sxs-lookup"><span data-stu-id="5e02f-136">Delete an item</span></span>         |<span data-ttu-id="5e02f-137">Nenhum</span><span class="sxs-lookup"><span data-stu-id="5e02f-137">None</span></span>        |<span data-ttu-id="5e02f-138">Nenhum</span><span class="sxs-lookup"><span data-stu-id="5e02f-138">None</span></span>                |

<span data-ttu-id="5e02f-139">O diagrama a seguir mostra o design do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5e02f-139">The following diagram shows the design of the app.</span></span>

![O cliente é representado por uma caixa à esquerda.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="5e02f-145">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="5e02f-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5e02f-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e02f-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5e02f-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e02f-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5e02f-148">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e02f-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="5e02f-149">Criar um projeto Web</span><span class="sxs-lookup"><span data-stu-id="5e02f-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5e02f-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e02f-150">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="5e02f-151">No menu **Arquivo**, selecione **Novo** > **Projeto**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-151">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="5e02f-152">Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
1. <span data-ttu-id="5e02f-153">Nomeie o projeto como *TodoApi* e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-153">Name the project *TodoApi* and click **Create**.</span></span>
1. <span data-ttu-id="5e02f-154">Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 3.0** estão selecionados.</span><span class="sxs-lookup"><span data-stu-id="5e02f-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="5e02f-155">Selecione o modelo **API** e clique em **Criar**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-155">Select the **API** template and click **Create**.</span></span>

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5e02f-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e02f-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="5e02f-158">Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="5e02f-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
1. <span data-ttu-id="5e02f-159">Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="5e02f-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
1. <span data-ttu-id="5e02f-160">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="5e02f-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

1. <span data-ttu-id="5e02f-161">Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="5e02f-162">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="5e02f-162">The preceding commands:</span></span>

  * <span data-ttu-id="5e02f-163">Crie um novo projeto de API Web e abra-o no Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5e02f-163">Create a new web API project and open it in Visual Studio Code.</span></span>
  * <span data-ttu-id="5e02f-164">Adicione os pacotes NuGet que são necessários na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="5e02f-164">Add the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5e02f-165">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e02f-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5e02f-166">Selecione **Arquivo** > **Nova Solução**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-166">Select **File** > **New Solution**.</span></span>

    ![Nova Solução do macOS](first-web-api-mac/_static/sln.png)

1. <span data-ttu-id="5e02f-168">Selecione **.NET Core** > **Aplicativo** > **API** > **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

    ![Caixa de diálogo Novo Projeto do macOS](first-web-api-mac/_static/1.png)
  
1. <span data-ttu-id="5e02f-170">Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, selecione a **Estrutura de Destino** do \* *.NET Core 3.0*.</span><span class="sxs-lookup"><span data-stu-id="5e02f-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>
1. <span data-ttu-id="5e02f-171">Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="5e02f-173">Abra um terminal de comando na pasta do projeto e execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="5e02f-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="5e02f-174">Testar a API</span><span class="sxs-lookup"><span data-stu-id="5e02f-174">Test the API</span></span>

<span data-ttu-id="5e02f-175">O modelo de projeto cria uma API `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="5e02f-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="5e02f-176">Chame o método `Get` em um navegador para testar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5e02f-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5e02f-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e02f-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5e02f-178">Pressione <kbd>Ctrl + F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5e02f-178">Press <kbd>Ctrl+F5</kbd> to run the app.</span></span> <span data-ttu-id="5e02f-179">O Visual Studio inicia um navegador e navega para `https://localhost:<port>/WeatherForecast`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="5e02f-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="5e02f-180">Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="5e02f-181">Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5e02f-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e02f-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5e02f-183">Pressione <kbd>Ctrl + F5</kbd> para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5e02f-183">Press <kbd>Ctrl+F5</kbd> to run the app.</span></span> <span data-ttu-id="5e02f-184">Em um navegador, acesse a seguinte URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span><span class="sxs-lookup"><span data-stu-id="5e02f-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5e02f-185">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e02f-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5e02f-186">Selecione **Executar** > **Iniciar com Depuração** para iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5e02f-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="5e02f-187">O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente.</span><span class="sxs-lookup"><span data-stu-id="5e02f-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="5e02f-188">Um erro HTTP 404 (Não Encontrado) será retornado.</span><span class="sxs-lookup"><span data-stu-id="5e02f-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="5e02f-189">Acrescente `/WeatherForecast` à URL (altere a URL para `https://localhost:<port>/WeatherForecast`).</span><span class="sxs-lookup"><span data-stu-id="5e02f-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="5e02f-190">Um JSON semelhante ao seguinte será retornado:</span><span class="sxs-lookup"><span data-stu-id="5e02f-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="5e02f-191">Adicionar uma classe de modelo</span><span class="sxs-lookup"><span data-stu-id="5e02f-191">Add a model class</span></span>

<span data-ttu-id="5e02f-192">Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5e02f-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="5e02f-193">O modelo para esse aplicativo é uma única classe `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="5e02f-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5e02f-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e02f-194">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="5e02f-195">No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="5e02f-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="5e02f-196">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="5e02f-197">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="5e02f-197">Name the folder *Models*.</span></span>
1. <span data-ttu-id="5e02f-198">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5e02f-199">Dê à classe o nome *TodoItem* e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-199">Name the class *TodoItem* and select **Add**.</span></span>
1. <span data-ttu-id="5e02f-200">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="5e02f-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5e02f-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e02f-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="5e02f-202">Adicione uma pasta denominada *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="5e02f-202">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="5e02f-203">Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="5e02f-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5e02f-204">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e02f-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5e02f-205">Clique com o botão direito do mouse no projeto.</span><span class="sxs-lookup"><span data-stu-id="5e02f-205">Right-click the project.</span></span> <span data-ttu-id="5e02f-206">Selecione **Adicionar** > **Nova Pasta**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="5e02f-207">Nomeie a pasta como *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="5e02f-207">Name the folder *Models*.</span></span>

    ![nova pasta](first-web-api-mac/_static/folder.png)

1. <span data-ttu-id="5e02f-209">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>
1. <span data-ttu-id="5e02f-210">Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-210">Name the class *TodoItem*, and then click **New**.</span></span>
1. <span data-ttu-id="5e02f-211">Substitua o código do modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="5e02f-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="5e02f-212">A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.</span><span class="sxs-lookup"><span data-stu-id="5e02f-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="5e02f-213">As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.</span><span class="sxs-lookup"><span data-stu-id="5e02f-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="5e02f-214">Adicionar um contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="5e02f-214">Add a database context</span></span>

<span data-ttu-id="5e02f-215">O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="5e02f-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="5e02f-216">Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.</span><span class="sxs-lookup"><span data-stu-id="5e02f-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5e02f-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e02f-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="5e02f-218">Adicione Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="5e02f-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

1. <span data-ttu-id="5e02f-219">No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
1. <span data-ttu-id="5e02f-220">Selecione a guia **Procurar** e, em seguida, insira **Microsoft.EntityFrameworkCore.SqlServer** na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="5e02f-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
1. <span data-ttu-id="5e02f-221">Selecione **Microsoft. EntityFrameworkCore. SqlServer** no painel esquerdo.</span><span class="sxs-lookup"><span data-stu-id="5e02f-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
1. <span data-ttu-id="5e02f-222">Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
1. <span data-ttu-id="5e02f-223">Use as instruções anteriores para adicionar o pacote do NuGet `Microsoft.EntityFrameworkCore.InMemory`.</span><span class="sxs-lookup"><span data-stu-id="5e02f-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![Gerenciador de pacotes do NuGet](first-web-api/_static/vs3nuget.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="5e02f-225">Adicione o contexto de banco de dados TodoContext</span><span class="sxs-lookup"><span data-stu-id="5e02f-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="5e02f-226">Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="5e02f-227">Nomeie a classe como *TodoContext* e clique em **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="5e02f-228">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e02f-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="5e02f-229">Adicione uma classe denominada `TodoContext` à pasta *Modelos*.</span><span class="sxs-lookup"><span data-stu-id="5e02f-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="5e02f-230">Insira o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="5e02f-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="5e02f-231">Registrar o contexto do banco de dados</span><span class="sxs-lookup"><span data-stu-id="5e02f-231">Register the database context</span></span>

<span data-ttu-id="5e02f-232">No ASP.NET Core, os serviços como o contexto do banco de dados devem ser registrados com o contêiner [injeção de dependência (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="5e02f-232">In ASP.NET Core, services such as the database context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5e02f-233">O contêiner fornece o serviço aos controladores.</span><span class="sxs-lookup"><span data-stu-id="5e02f-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="5e02f-234">Atualize *Startup.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="5e02f-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="5e02f-235">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="5e02f-235">The preceding code:</span></span>

* <span data-ttu-id="5e02f-236">Remove as declarações `using` não utilizadas.</span><span class="sxs-lookup"><span data-stu-id="5e02f-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="5e02f-237">Adiciona o contexto de banco de dados ao contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="5e02f-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="5e02f-238">Especifica que o contexto de banco de dados usará um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="5e02f-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="5e02f-239">Faça scaffold de um controlador</span><span class="sxs-lookup"><span data-stu-id="5e02f-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5e02f-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e02f-240">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="5e02f-241">Clique com o botão direito do mouse na pasta *Controllers*.</span><span class="sxs-lookup"><span data-stu-id="5e02f-241">Right-click the *Controllers* folder.</span></span>
1. <span data-ttu-id="5e02f-242">Selecione **adicionar**  > **novo item com Scaffold**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-242">Select **Add** > **New Scaffolded Item**.</span></span>
1. <span data-ttu-id="5e02f-243">Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
1. <span data-ttu-id="5e02f-244">Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework**:</span><span class="sxs-lookup"><span data-stu-id="5e02f-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>
    * <span data-ttu-id="5e02f-245">Selecione **TodoItem (TodoApi. Models)** na **classe Model**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
    * <span data-ttu-id="5e02f-246">Selecione **TodoContext (TodoApi. Models)** na **classe de contexto de dados**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
    * <span data-ttu-id="5e02f-247">Selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="5e02f-248">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="5e02f-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="5e02f-249">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="5e02f-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="5e02f-250">Os comandos anteriores:</span><span class="sxs-lookup"><span data-stu-id="5e02f-250">The preceding commands:</span></span>

* <span data-ttu-id="5e02f-251">Adicionam os pacotes do NuGet para scaffolding.</span><span class="sxs-lookup"><span data-stu-id="5e02f-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="5e02f-252">Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).</span><span class="sxs-lookup"><span data-stu-id="5e02f-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="5e02f-253">Fazem scaffold de `TodoItemsController`.</span><span class="sxs-lookup"><span data-stu-id="5e02f-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="5e02f-254">O código gerado:</span><span class="sxs-lookup"><span data-stu-id="5e02f-254">The generated code:</span></span>

* <span data-ttu-id="5e02f-255">Define uma classe de controlador de API sem métodos.</span><span class="sxs-lookup"><span data-stu-id="5e02f-255">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="5e02f-256">Decora a classe com o atributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute).</span><span class="sxs-lookup"><span data-stu-id="5e02f-256">Decorates the class with the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="5e02f-257">Esse atributo indica se o controlador responde às solicitações da API Web.</span><span class="sxs-lookup"><span data-stu-id="5e02f-257">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="5e02f-258">Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.</span><span class="sxs-lookup"><span data-stu-id="5e02f-258">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="5e02f-259">Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador.</span><span class="sxs-lookup"><span data-stu-id="5e02f-259">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="5e02f-260">O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.</span><span class="sxs-lookup"><span data-stu-id="5e02f-260">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="5e02f-261">Examine o método criar do PostTodoItem</span><span class="sxs-lookup"><span data-stu-id="5e02f-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="5e02f-262">Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):</span><span class="sxs-lookup"><span data-stu-id="5e02f-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="5e02f-263">O código anterior é um método HTTP POST, conforme indicado pelo atributo [[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute).</span><span class="sxs-lookup"><span data-stu-id="5e02f-263">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="5e02f-264">O método obtém o valor do item pendente no corpo da solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="5e02f-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="5e02f-265">O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:</span><span class="sxs-lookup"><span data-stu-id="5e02f-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="5e02f-266">retorna um código de status HTTP 201 em caso de êxito.</span><span class="sxs-lookup"><span data-stu-id="5e02f-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="5e02f-267">HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.</span><span class="sxs-lookup"><span data-stu-id="5e02f-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="5e02f-268">Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta.</span><span class="sxs-lookup"><span data-stu-id="5e02f-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="5e02f-269">O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado.</span><span class="sxs-lookup"><span data-stu-id="5e02f-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="5e02f-270">Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="5e02f-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="5e02f-271">Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="5e02f-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="5e02f-272">A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.</span><span class="sxs-lookup"><span data-stu-id="5e02f-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="5e02f-273">Instale o Postman</span><span class="sxs-lookup"><span data-stu-id="5e02f-273">Install Postman</span></span>

<span data-ttu-id="5e02f-274">Este tutorial usa o Postman para testar a API Web.</span><span class="sxs-lookup"><span data-stu-id="5e02f-274">This tutorial uses Postman to test the web API.</span></span>

1. <span data-ttu-id="5e02f-275">Instale o [Postman](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="5e02f-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
1. <span data-ttu-id="5e02f-276">Inicie o aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="5e02f-276">Start the web app.</span></span>
1. <span data-ttu-id="5e02f-277">Inicie o Postman.</span><span class="sxs-lookup"><span data-stu-id="5e02f-277">Start Postman.</span></span>
1. <span data-ttu-id="5e02f-278">Desabilite a **Verificação do certificado SSL**</span><span class="sxs-lookup"><span data-stu-id="5e02f-278">Disable **SSL certificate verification**</span></span>
1. <span data-ttu-id="5e02f-279">Em**configurações** de  >  de **arquivo** (guia**geral** ), desabilite a **verificação de certificado SSL**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

    > [!WARNING]
    > <span data-ttu-id="5e02f-280">Habilite novamente a verificação do certificado SSL depois de testar o controlador.</span><span class="sxs-lookup"><span data-stu-id="5e02f-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="5e02f-281">Teste o PostTodoItem com o Postman</span><span class="sxs-lookup"><span data-stu-id="5e02f-281">Test PostTodoItem with Postman</span></span>

1. <span data-ttu-id="5e02f-282">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="5e02f-282">Create a new request.</span></span>
1. <span data-ttu-id="5e02f-283">Defina o método HTTP como `POST`.</span><span class="sxs-lookup"><span data-stu-id="5e02f-283">Set the HTTP method to `POST`.</span></span>
1. <span data-ttu-id="5e02f-284">Selecione a guia **Corpo**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-284">Select the **Body** tab.</span></span>
1. <span data-ttu-id="5e02f-285">Selecione o botão de opção **bruto**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-285">Select the **raw** radio button.</span></span>
1. <span data-ttu-id="5e02f-286">Defina o tipo como **JSON (aplicativo/json)** .</span><span class="sxs-lookup"><span data-stu-id="5e02f-286">Set the type to **JSON (application/json)**.</span></span>
1. <span data-ttu-id="5e02f-287">No corpo da solicitação, insira JSON para um item de tarefas pendentes:</span><span class="sxs-lookup"><span data-stu-id="5e02f-287">In the request body, enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

1. <span data-ttu-id="5e02f-288">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-288">Select **Send**.</span></span>

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="5e02f-290">Testar o URI do cabeçalho de local</span><span class="sxs-lookup"><span data-stu-id="5e02f-290">Test the location header URI</span></span>

1. <span data-ttu-id="5e02f-291">Selecione a guia **Cabeçalhos** no painel **Resposta**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-291">Select the **Headers** tab in the **Response** pane.</span></span>
1. <span data-ttu-id="5e02f-292">Copie o valor do cabeçalho **Local**:</span><span class="sxs-lookup"><span data-stu-id="5e02f-292">Copy the **Location** header value:</span></span>

    ![Guia Cabeçalhos do console do Postman](first-web-api/_static/create.png)

1. <span data-ttu-id="5e02f-294">Defina o método como GET.</span><span class="sxs-lookup"><span data-stu-id="5e02f-294">Set the method to GET.</span></span>
1. <span data-ttu-id="5e02f-295">Cole o URI (por exemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="5e02f-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
1. <span data-ttu-id="5e02f-296">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="5e02f-297">Examine os métodos GET</span><span class="sxs-lookup"><span data-stu-id="5e02f-297">Examine the GET methods</span></span>

<span data-ttu-id="5e02f-298">Esses métodos implementam dois pontos de extremidade GET:</span><span class="sxs-lookup"><span data-stu-id="5e02f-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="5e02f-299">Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman.</span><span class="sxs-lookup"><span data-stu-id="5e02f-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="5e02f-300">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="5e02f-300">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="5e02f-301">Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:</span><span class="sxs-lookup"><span data-stu-id="5e02f-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="5e02f-302">Teste o GET com o Postman</span><span class="sxs-lookup"><span data-stu-id="5e02f-302">Test Get with Postman</span></span>

1. <span data-ttu-id="5e02f-303">Crie uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="5e02f-303">Create a new request.</span></span>
1. <span data-ttu-id="5e02f-304">Defina o método HTTP como **GET**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-304">Set the HTTP method to **GET**.</span></span>
1. <span data-ttu-id="5e02f-305">Defina a URL de solicitação como `https://localhost:<port>/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="5e02f-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="5e02f-306">Por exemplo, `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="5e02f-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
1. <span data-ttu-id="5e02f-307">Defina **Exibição de dois painéis** no Postman.</span><span class="sxs-lookup"><span data-stu-id="5e02f-307">Set **Two pane view** in Postman.</span></span>
1. <span data-ttu-id="5e02f-308">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-308">Select **Send**.</span></span>

<span data-ttu-id="5e02f-309">Este aplicativo usa um banco de dados em memória.</span><span class="sxs-lookup"><span data-stu-id="5e02f-309">This app uses an in-memory database.</span></span> <span data-ttu-id="5e02f-310">Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado.</span><span class="sxs-lookup"><span data-stu-id="5e02f-310">If the app is stopped and started, the preceding GET request won't return any data.</span></span> <span data-ttu-id="5e02f-311">Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="5e02f-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="5e02f-312">Roteamento e caminhos de URL</span><span class="sxs-lookup"><span data-stu-id="5e02f-312">Routing and URL paths</span></span>

<span data-ttu-id="5e02f-313">O atributo [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) denota um método que responde a uma solicitação HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="5e02f-313">The [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="5e02f-314">O caminho da URL de cada método é construído da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="5e02f-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="5e02f-315">Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:</span><span class="sxs-lookup"><span data-stu-id="5e02f-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="5e02f-316">Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção.</span><span class="sxs-lookup"><span data-stu-id="5e02f-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="5e02f-317">Para esta amostra, o nome da classe do controlador é **TodoItems**Controller. Portanto, o nome do controlador é "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="5e02f-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="5e02f-318">O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="5e02f-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="5e02f-319">Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho.</span><span class="sxs-lookup"><span data-stu-id="5e02f-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="5e02f-320">Esta amostra não usa um modelo.</span><span class="sxs-lookup"><span data-stu-id="5e02f-320">This sample doesn't use a template.</span></span> <span data-ttu-id="5e02f-321">Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="5e02f-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="5e02f-322">No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente.</span><span class="sxs-lookup"><span data-stu-id="5e02f-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="5e02f-323">Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido ao método em seu parâmetro `id`.</span><span class="sxs-lookup"><span data-stu-id="5e02f-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="5e02f-324">Valores de retorno</span><span class="sxs-lookup"><span data-stu-id="5e02f-324">Return values</span></span>

<span data-ttu-id="5e02f-325">O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="5e02f-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="5e02f-326">O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta.</span><span class="sxs-lookup"><span data-stu-id="5e02f-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="5e02f-327">O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="5e02f-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="5e02f-328">As exceções sem tratamento são convertidas em erros 5xx.</span><span class="sxs-lookup"><span data-stu-id="5e02f-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="5e02f-329">Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="5e02f-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="5e02f-330">Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:</span><span class="sxs-lookup"><span data-stu-id="5e02f-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="5e02f-331">Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound>.</span><span class="sxs-lookup"><span data-stu-id="5e02f-331">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound> error code.</span></span>
* <span data-ttu-id="5e02f-332">Caso contrário, o método retornará 200 com um corpo de resposta JSON.</span><span class="sxs-lookup"><span data-stu-id="5e02f-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="5e02f-333">Retornar `item` resulta em uma resposta HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="5e02f-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="5e02f-334">O método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="5e02f-334">The PutTodoItem method</span></span>

<span data-ttu-id="5e02f-335">Examine o método `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="5e02f-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="5e02f-336">`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="5e02f-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="5e02f-337">A resposta é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="5e02f-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="5e02f-338">De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="5e02f-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="5e02f-339">Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="5e02f-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="5e02f-340">Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5e02f-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="5e02f-341">Testar o método PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="5e02f-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="5e02f-342">Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="5e02f-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="5e02f-343">Deverá haver um item no banco de dados antes de você fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="5e02f-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="5e02f-344">Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.</span><span class="sxs-lookup"><span data-stu-id="5e02f-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="5e02f-345">Atualize o item pendente que tem uma ID de 1.</span><span class="sxs-lookup"><span data-stu-id="5e02f-345">Update the to-do item that has an ID of 1.</span></span> <span data-ttu-id="5e02f-346">Defina seu nome como "alimentar feed":</span><span class="sxs-lookup"><span data-stu-id="5e02f-346">Set its name to "feed fish":</span></span>

```json
{
  "ID":1,
  "name":"feed fish",
  "isComplete":true
}
```

<span data-ttu-id="5e02f-347">A seguinte imagem mostra a atualização do Postman:</span><span class="sxs-lookup"><span data-stu-id="5e02f-347">The following image shows the Postman update:</span></span>

![Console do Postman mostrando a resposta 204 (sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="5e02f-349">O método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="5e02f-349">The DeleteTodoItem method</span></span>

<span data-ttu-id="5e02f-350">Examine o método `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="5e02f-350">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="5e02f-351">A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="5e02f-351">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="5e02f-352">Testar o método DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="5e02f-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="5e02f-353">Use o Postman para excluir um item pendente:</span><span class="sxs-lookup"><span data-stu-id="5e02f-353">Use Postman to delete a to-do item:</span></span>

1. <span data-ttu-id="5e02f-354">Defina o método como `DELETE`.</span><span class="sxs-lookup"><span data-stu-id="5e02f-354">Set the method to `DELETE`.</span></span>
1. <span data-ttu-id="5e02f-355">Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/TodoItems/1`).</span><span class="sxs-lookup"><span data-stu-id="5e02f-355">Set the URI of the object to delete (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
1. <span data-ttu-id="5e02f-356">Selecione **Enviar**.</span><span class="sxs-lookup"><span data-stu-id="5e02f-356">Select **Send**.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="5e02f-357">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="5e02f-357">Call the web API with JavaScript</span></span>

<span data-ttu-id="5e02f-358">Consulte [o tutorial: chamar uma API Web do ASP.NET Core com JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="5e02f-358">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="5e02f-359">Adicionar suporte de autenticação a uma API da Web</span><span class="sxs-lookup"><span data-stu-id="5e02f-359">Add authentication support to a web API</span></span>

<span data-ttu-id="5e02f-360">Consulte o tutorial do [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) .</span><span class="sxs-lookup"><span data-stu-id="5e02f-360">See the [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5e02f-361">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5e02f-361">Additional resources</span></span>

<span data-ttu-id="5e02f-362">[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="5e02f-362">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="5e02f-363">Consulte [como baixar](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="5e02f-363">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="5e02f-364">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="5e02f-364">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="5e02f-365">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="5e02f-365">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
