---
title: 'Tutorial: Chamar uma API Web do ASP.NET Core com o JavaScript'
author: rick-anderson
description: Saiba como chamar uma API Web do ASP.NET Core com o JavaScript.
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: 0070816149d64fc1d71d453eb0f135050c78597a
ms.sourcegitcommit: de17150e5ec7507d7114dde0e5dbc2e45a66ef53
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70116635"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="8084a-103">Tutorial: Chamar uma API Web do ASP.NET Core com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="8084a-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="8084a-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8084a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8084a-105">Este tutorial mostra como chamar uma API Web do ASP.NET Core com JavaScript, usando a [API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span><span class="sxs-lookup"><span data-stu-id="8084a-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8084a-106">Para o ASP.NET Core 2.2, confira a versão 2.2 de [Chamar a API Web com o JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span><span class="sxs-lookup"><span data-stu-id="8084a-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="8084a-107">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="8084a-107">Prerequisites</span></span>

* <span data-ttu-id="8084a-108">Conclua o [Tutorial: Criar uma API Web](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="8084a-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="8084a-109">Familiaridade com CSS, HTML e JavaScript</span><span class="sxs-lookup"><span data-stu-id="8084a-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="8084a-110">Chamar a API Web com o JavaScript</span><span class="sxs-lookup"><span data-stu-id="8084a-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="8084a-111">Nesta seção, você adicionará uma página HTML que contém formulários para criar e gerenciar itens de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="8084a-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="8084a-112">Manipuladores de eventos são anexados aos elementos na página.</span><span class="sxs-lookup"><span data-stu-id="8084a-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="8084a-113">Os manipuladores de eventos resultam em solicitações HTTP para os métodos de ação da API Web.</span><span class="sxs-lookup"><span data-stu-id="8084a-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="8084a-114">A função `fetch` da API Fetch inicia cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="8084a-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="8084a-115">A função `fetch` retorna um objeto `Promise`, que contém uma resposta HTTP representada como um objeto `Response`.</span><span class="sxs-lookup"><span data-stu-id="8084a-115">The `fetch` function returns a `Promise` object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="8084a-116">Um padrão comum é extrair o corpo da resposta JSON invocando a função `json` no objeto `Response`.</span><span class="sxs-lookup"><span data-stu-id="8084a-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="8084a-117">O JavaScript atualiza a página com os detalhes da resposta da API Web.</span><span class="sxs-lookup"><span data-stu-id="8084a-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="8084a-118">A chamada `fetch` mais simples aceita um parâmetro único que representa a rota.</span><span class="sxs-lookup"><span data-stu-id="8084a-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="8084a-119">Um segundo parâmetro, conhecido como objeto `init`, é opcional.</span><span class="sxs-lookup"><span data-stu-id="8084a-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="8084a-120">`init` é usado para configurar a solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="8084a-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="8084a-121">Configure o aplicativo para [fornecer arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="8084a-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="8084a-122">O seguinte código destacado é necessário no método `Configure` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8084a-122">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="8084a-123">Crie um novo diretório *wwwroot* na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="8084a-123">Create a *wwwroot* directory in the project root.</span></span>

1. <span data-ttu-id="8084a-124">Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="8084a-124">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="8084a-125">Substitua seu conteúdo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="8084a-125">Replace its contents with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="8084a-126">Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="8084a-126">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="8084a-127">Substitua seu conteúdo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="8084a-127">Replace its contents with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="8084a-128">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="8084a-128">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="8084a-129">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8084a-129">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="8084a-130">Remova a propriedade `launchUrl` para forçar o aplicativo a ser aberto em *index.html*, o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="8084a-130">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="8084a-131">Esta amostra chama todos os métodos CRUD da API Web.</span><span class="sxs-lookup"><span data-stu-id="8084a-131">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="8084a-132">A seguir, são apresentadas explicações sobre as solicitações de API Web.</span><span class="sxs-lookup"><span data-stu-id="8084a-132">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="8084a-133">Obter uma lista de itens pendentes</span><span class="sxs-lookup"><span data-stu-id="8084a-133">Get a list of to-do items</span></span>

<span data-ttu-id="8084a-134">No código a seguir, uma solicitação HTTP GET é enviada para a rota de *api/TodoItems*:</span><span class="sxs-lookup"><span data-stu-id="8084a-134">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="8084a-135">Quando a API Web retorna um código de status bem-sucedido, a função `_displayItems` é invocada.</span><span class="sxs-lookup"><span data-stu-id="8084a-135">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="8084a-136">Cada item de tarefas pendentes no parâmetro de matriz aceito por `_displayItems` é adicionado a uma tabela com os botões **Editar** e **Excluir**.</span><span class="sxs-lookup"><span data-stu-id="8084a-136">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="8084a-137">Se a solicitação da API Web falhar, um erro será registrado no console do navegador.</span><span class="sxs-lookup"><span data-stu-id="8084a-137">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="8084a-138">Adicionar um item pendente</span><span class="sxs-lookup"><span data-stu-id="8084a-138">Add a to-do item</span></span>

<span data-ttu-id="8084a-139">No seguinte código:</span><span class="sxs-lookup"><span data-stu-id="8084a-139">In the following code:</span></span>

* <span data-ttu-id="8084a-140">Uma variável `item` é declarada para construir uma representação literal de objeto do item de tarefas pendentes.</span><span class="sxs-lookup"><span data-stu-id="8084a-140">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="8084a-141">Uma solicitação Fetch é configurada com as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="8084a-141">A Fetch request is configured with the following options:</span></span>
    * <span data-ttu-id="8084a-142">`method` &mdash; especifica o verbo de ação HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="8084a-142">`method`&mdash;specifies the POST HTTP action verb.</span></span>
    * <span data-ttu-id="8084a-143">`body` &mdash; especifica a representação JSON do corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="8084a-143">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="8084a-144">O JSON é produzido passando o literal de objeto armazenado em `item` para a função [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="8084a-144">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
    * <span data-ttu-id="8084a-145">`headers` &mdash; especifica os cabeçalhos de solicitação HTTP `Accept` e `Content-Type`.</span><span class="sxs-lookup"><span data-stu-id="8084a-145">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="8084a-146">Ambos os cabeçalhos são definidos como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="8084a-146">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="8084a-147">Uma solicitação HTTP POST é enviada para a rota de *api/TodoItems*.</span><span class="sxs-lookup"><span data-stu-id="8084a-147">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="8084a-148">Quando a API Web retorna um código de status de êxito, a função `getItems` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="8084a-148">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="8084a-149">Se a solicitação da API Web falhar, um erro será registrado no console do navegador.</span><span class="sxs-lookup"><span data-stu-id="8084a-149">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="8084a-150">Atualizar um item pendente</span><span class="sxs-lookup"><span data-stu-id="8084a-150">Update a to-do item</span></span>

<span data-ttu-id="8084a-151">A atualização de um item de tarefas pendentes é semelhante à adição de um; no entanto, há duas diferenças significativas:</span><span class="sxs-lookup"><span data-stu-id="8084a-151">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="8084a-152">A rota é sufixada com o identificador exclusivo do item a ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="8084a-152">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="8084a-153">Por exemplo, *api/TodoItems/1*.</span><span class="sxs-lookup"><span data-stu-id="8084a-153">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="8084a-154">O verbo de ação HTTP é PUT, conforme indicado pela opção `method`.</span><span class="sxs-lookup"><span data-stu-id="8084a-154">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="8084a-155">Excluir um item pendente</span><span class="sxs-lookup"><span data-stu-id="8084a-155">Delete a to-do item</span></span>

<span data-ttu-id="8084a-156">Para excluir um item de tarefas pendentes, defina a opção `method` da solicitação como `DELETE` e especifique o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="8084a-156">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="8084a-157">Avance para o próximo tutorial para saber como gerar páginas de ajuda da API Web:</span><span class="sxs-lookup"><span data-stu-id="8084a-157">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
