---
title: 'Tutorial: Chamar uma API Web com o jQuery usando o ASP.NET Core'
author: rick-anderson
description: Saiba como chamar uma API Web do ASP.NET Core com o jQuery.
ms.author: riande
ms.custom: mvc
ms.date: 07/20/2019
uid: tutorials/web-api-jquery
ms.openlocfilehash: eb8b2453fd037170a49f531fea4c3ef1c056292d
ms.sourcegitcommit: 0efb9e219fef481dee35f7b763165e488aa6cf9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602565"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-jquery"></a>Tutorial: Chamar uma API Web do ASP.NET Core com o jQuery

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Este tutorial mostra como chamar uma API Web do ASP.NET Core com o jQuery

::: moniker range="< aspnetcore-3.0"

Para o ASP.NET Core 2.2, consulte a versão 2.2 de [Chamar a API Web com o jQuery](xref:tutorials/first-web-api#call-the-api-with-jquery).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Pré-requisitos

Conclua o [Tutorial: Criar uma API Web](xref:tutorials/first-web-api)

## <a name="call-the-api-with-jquery"></a>Chamar a API com o jQuery

Nesta seção, uma página HTML que usa o jQuery para chamar a API Web é adicionada. O jQuery inicia a solicitação e atualiza a página com os detalhes da resposta da API.

Configurar o aplicativo para [servir arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) atualizando *Startup.cs* com o código realçado a seguir:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJquery.cs?highlight=8-9&name=snippet_configure)]

Crie uma pasta *wwwroot* no diretório do projeto.

Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*. Substitua seu conteúdo pela seguinte marcação:

[!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*. Substitua seu conteúdo pelo código a seguir:

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:

* Abra *Properties\launchSettings.json*.
* Remova a propriedade `launchUrl` para forçar o aplicativo a ser aberto em *index.html*, o arquivo padrão do projeto.

Há várias maneiras de obter o jQuery. No snippet anterior, a biblioteca é carregada de uma CDN.

Esta amostra chama todos os métodos CRUD da API. Veja a seguir explicações das chamadas à API.

### <a name="get-a-list-of-to-do-items"></a>Obter uma lista de itens pendentes

A função [ajax](https://api.jquery.com/jquery.ajax/) do jQuery envia uma solicitação `GET` para a API, que retorna o JSON que representa uma matriz de itens pendentes. A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida. No retorno de chamada, o DOM é atualizado com as informações do item pendente.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>Adicionar um item pendente

A função [ajax](https://api.jquery.com/jquery.ajax/) envia uma solicitação `POST` com o item pendente no corpo da solicitação. As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado. O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>Atualizar um item pendente

A atualização de um item pendente é semelhante à adição de um. A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>Excluir um item pendente

A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

Avance para o próximo tutorial para saber como gerar páginas de ajuda da API:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
::: moniker-end