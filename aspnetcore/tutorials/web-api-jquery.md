---
title: 'Tutorial: Chamar uma API Web com o jQuery usando o ASP.NET Core'
author: rick-anderson
description: Saiba como chamar uma API Web do ASP.NET Core com o jQuery.
ms.author: riande
ms.custom: mvc
ms.date: 07/20/2019
uid: tutorials/web-api-jquery
ms.openlocfilehash: a319e4b4ce09e9b09afeaff065d5740276deb115
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022557"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-jquery"></a><span data-ttu-id="e8508-103">Tutorial: Chamar uma API Web do ASP.NET Core com o jQuery</span><span class="sxs-lookup"><span data-stu-id="e8508-103">Tutorial: Call an ASP.NET Core web API with jQuery</span></span>

<span data-ttu-id="e8508-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e8508-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e8508-105">Este tutorial mostra como chamar uma API Web do ASP.NET Core com o jQuery</span><span class="sxs-lookup"><span data-stu-id="e8508-105">This tutorial shows how to call an ASP.NET Core web API with jQuery</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e8508-106">Para o ASP.NET Core 2.2, consulte a versão 2.2 de [Chamar a API Web com o jQuery](xref:tutorials/first-web-api#call-the-api-with-jquery).</span><span class="sxs-lookup"><span data-stu-id="e8508-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the Web API with jQuery](xref:tutorials/first-web-api#call-the-api-with-jquery).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="e8508-107">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e8508-107">Prerequisites</span></span>

* <span data-ttu-id="e8508-108">Conclua o [Tutorial: Criar uma API Web](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="e8508-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="e8508-109">Familiaridade com CSS, HTML, JavaScript e jQuery</span><span class="sxs-lookup"><span data-stu-id="e8508-109">Familiarity with CSS, HTML, JavaScript, and jQuery</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="e8508-110">Chamar a API com o jQuery</span><span class="sxs-lookup"><span data-stu-id="e8508-110">Call the API with jQuery</span></span>

<span data-ttu-id="e8508-111">Nesta seção, uma página HTML que usa o jQuery para chamar a API Web é adicionada.</span><span class="sxs-lookup"><span data-stu-id="e8508-111">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="e8508-112">O jQuery inicia a solicitação e atualiza a página com os detalhes da resposta da API.</span><span class="sxs-lookup"><span data-stu-id="e8508-112">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="e8508-113">Configurar o aplicativo para [servir arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) atualizando *Startup.cs* com o código realçado a seguir:</span><span class="sxs-lookup"><span data-stu-id="e8508-113">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJquery.cs?highlight=8-9&name=snippet_configure)]

<span data-ttu-id="e8508-114">Crie uma pasta *wwwroot* no diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="e8508-114">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="e8508-115">Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="e8508-115">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="e8508-116">Substitua seu conteúdo pela seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="e8508-116">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

<span data-ttu-id="e8508-117">Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="e8508-117">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="e8508-118">Substitua seu conteúdo pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="e8508-118">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="e8508-119">Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:</span><span class="sxs-lookup"><span data-stu-id="e8508-119">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="e8508-120">Abra *Properties\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="e8508-120">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="e8508-121">Remova a propriedade `launchUrl` para forçar o aplicativo a ser aberto em *index.html*, o arquivo padrão do projeto.</span><span class="sxs-lookup"><span data-stu-id="e8508-121">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="e8508-122">Há várias maneiras de obter o jQuery.</span><span class="sxs-lookup"><span data-stu-id="e8508-122">There are several ways to get jQuery.</span></span> <span data-ttu-id="e8508-123">No snippet anterior, a biblioteca é carregada de uma CDN.</span><span class="sxs-lookup"><span data-stu-id="e8508-123">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="e8508-124">Esta amostra chama todos os métodos CRUD da API.</span><span class="sxs-lookup"><span data-stu-id="e8508-124">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="e8508-125">Veja a seguir explicações das chamadas à API.</span><span class="sxs-lookup"><span data-stu-id="e8508-125">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="e8508-126">Obter uma lista de itens pendentes</span><span class="sxs-lookup"><span data-stu-id="e8508-126">Get a list of to-do items</span></span>

<span data-ttu-id="e8508-127">A função [ajax](https://api.jquery.com/jquery.ajax/) do jQuery envia uma solicitação `GET` para a API, que retorna o JSON que representa uma matriz de itens pendentes.</span><span class="sxs-lookup"><span data-stu-id="e8508-127">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="e8508-128">A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="e8508-128">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="e8508-129">No retorno de chamada, o DOM é atualizado com as informações do item pendente.</span><span class="sxs-lookup"><span data-stu-id="e8508-129">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="e8508-130">Adicionar um item pendente</span><span class="sxs-lookup"><span data-stu-id="e8508-130">Add a to-do item</span></span>

<span data-ttu-id="e8508-131">A função [ajax](https://api.jquery.com/jquery.ajax/) envia uma solicitação `POST` com o item pendente no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="e8508-131">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="e8508-132">As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado.</span><span class="sxs-lookup"><span data-stu-id="e8508-132">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="e8508-133">O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="e8508-133">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="e8508-134">Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.</span><span class="sxs-lookup"><span data-stu-id="e8508-134">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="e8508-135">Atualizar um item pendente</span><span class="sxs-lookup"><span data-stu-id="e8508-135">Update a to-do item</span></span>

<span data-ttu-id="e8508-136">A atualização de um item pendente é semelhante à adição de um.</span><span class="sxs-lookup"><span data-stu-id="e8508-136">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="e8508-137">A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.</span><span class="sxs-lookup"><span data-stu-id="e8508-137">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="e8508-138">Excluir um item pendente</span><span class="sxs-lookup"><span data-stu-id="e8508-138">Delete a to-do item</span></span>

<span data-ttu-id="e8508-139">A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.</span><span class="sxs-lookup"><span data-stu-id="e8508-139">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

<span data-ttu-id="e8508-140">Avance para o próximo tutorial para saber como gerar páginas de ajuda da API:</span><span class="sxs-lookup"><span data-stu-id="e8508-140">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
::: moniker-end
