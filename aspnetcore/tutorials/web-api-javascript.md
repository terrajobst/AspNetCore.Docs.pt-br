---
title: 'Tutorial: chamar uma API Web do ASP.NET Core com JavaScript'
author: rick-anderson
description: Saiba como chamar uma API Web do ASP.NET Core com o JavaScript.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: 5a31aa2974eb41938db89f97c070c352a26290fd
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681169"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a>Tutorial: chamar uma API Web do ASP.NET Core com JavaScript

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Este tutorial mostra como chamar uma API Web do ASP.NET Core com JavaScript, usando a [API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API).

::: moniker range="< aspnetcore-3.0"

Para o ASP.NET Core 2.2, confira a versão 2.2 de [Chamar a API Web com o JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* [Tutorial completo: criar uma API Web](xref:tutorials/first-web-api)
* Familiaridade com CSS, HTML e JavaScript

## <a name="call-the-web-api-with-javascript"></a>Chamar a API Web com o JavaScript

Nesta seção, você adicionará uma página HTML que contém formulários para criar e gerenciar itens de tarefas pendentes. Manipuladores de eventos são anexados aos elementos na página. Os manipuladores de eventos resultam em solicitações HTTP para os métodos de ação da API Web. A função `fetch` da API Fetch inicia cada solicitação HTTP.

A função `fetch` retorna um objeto `Promise`, que contém uma resposta HTTP representada como um objeto `Response`. Um padrão comum é extrair o corpo da resposta JSON invocando a função `json` no objeto `Response`. O JavaScript atualiza a página com os detalhes da resposta da API Web.

A chamada `fetch` mais simples aceita um parâmetro único que representa a rota. Um segundo parâmetro, conhecido como objeto `init`, é opcional. `init` é usado para configurar a solicitação HTTP.

1. Configure o aplicativo para [fornecer arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_). O seguinte código destacado é necessário no método `Configure` de *Startup.cs*:

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. Crie uma pasta *wwwroot* na raiz do projeto.

1. Crie uma pasta *js* dentro da pasta *wwwroot* .

1. Adicione um arquivo HTML chamado *index. html* à pasta *wwwroot* . Substitua o conteúdo de *index. html* pela seguinte marcação:

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. Adicione um arquivo JavaScript chamado *site. js* à pasta *wwwroot/js* . Substitua o conteúdo de *site. js* pelo código a seguir:

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:

1. Abra *Properties\launchSettings.json*.
1. Remova a propriedade `launchUrl` para forçar o aplicativo a ser aberto em *index.html*, o arquivo padrão do projeto.

Esta amostra chama todos os métodos CRUD da API Web. A seguir, são apresentadas explicações sobre as solicitações de API Web.

### <a name="get-a-list-of-to-do-items"></a>Obter uma lista de itens pendentes

No código a seguir, uma solicitação HTTP GET é enviada para a rota de *api/TodoItems*:

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

Quando a API Web retorna um código de status bem-sucedido, a função `_displayItems` é invocada. Cada item de tarefas pendentes no parâmetro de matriz aceito por `_displayItems` é adicionado a uma tabela com os botões **Editar** e **Excluir**. Se a solicitação da API Web falhar, um erro será registrado no console do navegador.

### <a name="add-a-to-do-item"></a>Adicionar um item pendente

No seguinte código:

* Uma variável `item` é declarada para construir uma representação literal de objeto do item de tarefas pendentes.
* Uma solicitação Fetch é configurada com as seguintes opções:
  * `method` &mdash; especifica o verbo de ação HTTP POST.
  * `body` &mdash; especifica a representação JSON do corpo da solicitação. O JSON é produzido passando o literal de objeto armazenado em `item` para a função [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).
  * `headers` &mdash; especifica os cabeçalhos de solicitação HTTP `Accept` e `Content-Type`. Ambos os cabeçalhos são definidos como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado, respectivamente.
* Uma solicitação HTTP POST é enviada para a rota de *api/TodoItems*.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

Quando a API Web retorna um código de status de êxito, a função `getItems` é invocada para atualizar a tabela HTML. Se a solicitação da API Web falhar, um erro será registrado no console do navegador.

### <a name="update-a-to-do-item"></a>Atualizar um item pendente

A atualização de um item de tarefas pendentes é semelhante à adição de um; no entanto, há duas diferenças significativas:

* A rota é sufixada com o identificador exclusivo do item a ser atualizado. Por exemplo, *api/TodoItems/1*.
* O verbo de ação HTTP é PUT, conforme indicado pela opção `method`.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a>Excluir um item pendente

Para excluir um item de tarefas pendentes, defina a opção `method` da solicitação como `DELETE` e especifique o identificador exclusivo do item na URL.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

Avance para o próximo tutorial para saber como gerar páginas de ajuda da API Web:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
