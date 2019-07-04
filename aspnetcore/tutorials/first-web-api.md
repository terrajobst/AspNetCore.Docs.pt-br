---
title: 'Tutorial: Criar uma API Web com o ASP.NET Core'
author: rick-anderson
description: Saiba como criar uma API Web com o ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 06/18/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 17e8ee08fca775b8fccc3f2e6cd6067caca9c79f
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207775"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Tutorial: Criar uma API Web com o ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Mike Wasson](https://github.com/mikewasson)

Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto de API Web.
> * Adicionar uma classe de modelo.
> * Criar o contexto de banco de dados.
> * Registrar o contexto de banco de dados.
> * Adicionar um controlador.
> * Adicionar métodos CRUD.
> * Configurar o roteamento e caminhos de URL.
> * Especificar os valores retornados.
> * Chamar a API Web com o Postman.
> * Chamar a API Web com o jQuery.

No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados relacional.

## <a name="overview"></a>Visão geral

Este tutorial cria a seguinte API:

|API | DESCRIÇÃO | Corpo da solicitação | Corpo da resposta |
|--- | ---- | ---- | ---- |
|GET /api/todo | Obter todos os itens de tarefas pendentes | Nenhum | Matriz de itens de tarefas pendentes|
|GET /api/todo/{id} | Obter um item por ID | Nenhum | Item de tarefas pendentes|
|POST /api/todo | Adicionar um novo item | Item de tarefas pendentes | Item de tarefas pendentes |
|PUT /api/todo/{id} | Atualizar um item &nbsp; existente | Item de tarefas pendentes | Nenhum |
|DELETE /api/todo/{id} &nbsp; &nbsp; | Excluir um item &nbsp; &nbsp; | Nenhum | Nenhum|

O diagrama a seguir mostra o design do aplicativo.

![O cliente é representado por uma caixa à esquerda e envia uma solicitação e recebe uma resposta do aplicativo, uma caixa desenhada à direita. Dentro da caixa do aplicativo, três caixas representam o controlador, o modelo e a camada de acesso a dados. A solicitação é recebida no controlador do aplicativo e as operações de leitura/gravação ocorrem entre o controlador e a camada de acesso a dados. O modelo é serializado e retornado para o cliente na resposta.](first-web-api/_static/architecture.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-project"></a>Criar um projeto Web

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* No menu **Arquivo**, selecione **Novo** > **Projeto**.
* Selecione o modelo **Aplicativo Web ASP.NET Core**. Nomeie o projeto como *TodoApi* e clique em **OK**.
* Na caixa de diálogo **Novo aplicativo Web ASP.NET Core – TodoApi** e escolha a versão do ASP.NET Core. Selecione o modelo **API** e clique em **OK**. **Não** selecione **Habilitar Suporte ao Docker**.

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.
* Execute os seguintes comandos:

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  Esses comandos criam um novo projeto de API Web e abrem uma nova instância do Visual Studio Code na nova pasta do projeto.

* Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Selecione **Arquivo** > **Nova Solução**.

  ![Nova solução do macOS](first-web-api-mac/_static/sln.png)

* Selecione **Aplicativo .NET Core** > **API Web ASP.NET Core** > **Avançar**.

  ![Caixa de diálogo Novo projeto do macOS](first-web-api-mac/_static/1.png)
  
* Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, aceite a **Estrutura de Destino** padrão * *.NET Core 2.2*.

* Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a>Testar a API

O modelo de projeto cria uma API `values`. Chame o método `Get` em um navegador para testar o aplicativo.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Pressione CTRL+F5 para executar o aplicativo. O Visual Studio inicia um navegador e navega para `https://localhost:<port>/api/values`, em que `<port>` é um número de porta escolhido aleatoriamente.

Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**. Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pressione CTRL+F5 para executar o aplicativo. Em um navegador, acesse a seguinte URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Selecione **Executar** > **Iniciar com Depuração** para iniciar o aplicativo. O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente. Um erro HTTP 404 (Não Encontrado) será retornado. Acrescente `/api/values` à URL (altere a URL para `https://localhost:<port>/api/values`).

---

O seguinte JSON é retornado:

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a>Adicionar uma classe de modelo

Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo. O modelo para esse aplicativo é uma única classe `TodoItem`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto. Selecione **Adicionar** > **Nova Pasta**. Nomeie a pasta *Models*.

* Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**. Dê à classe o nome *TodoItem* e selecione **Adicionar**.

* Substitua o código do modelo pelo seguinte código:

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Adicione uma pasta chamada *Models*.

* Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Clique com o botão direito do mouse no projeto. Selecione **Adicionar** > **Nova Pasta**. Nomeie a pasta como *Modelos*.

  ![nova pasta](first-web-api-mac/_static/folder.png)

* Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.

* Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.

* Substitua o código do modelo pelo seguinte código:

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.

As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.

## <a name="add-a-database-context"></a>Adicionar um contexto de banco de dados

O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados. Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**. Nomeie a classe como *TodoContext* e clique em **Adicionar**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* Adicione uma classe denominada `TodoContext` à pasta *Modelos*.

---

* Substitua o código do modelo pelo seguinte código:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registrar o contexto de banco de dados

No ASP.NET Core, serviços como o contexto de BD precisam ser registrados no contêiner de [DI (injeção de dependência)](xref:fundamentals/dependency-injection). O contêiner fornece o serviço aos controladores.

Atualize *Startup.cs* com o seguinte código realçado:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

O código anterior:

* Remove as declarações `using` não utilizadas.
* Adiciona o contexto de banco de dados ao contêiner de DI.
* Especifica que o contexto de banco de dados usará um banco de dados em memória.

## <a name="add-a-controller"></a>Adicionar um controlador

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Clique com o botão direito do mouse na pasta *Controllers*.
* Selecione **Adicionar** > **Novo Item**.
* Na caixa de diálogo **Adicionar Novo Item**, selecione o modelo **Classe do Controlador de API**.
* Dê à classe o nome *TodoController* e selecione **Adicionar**.

  ![Caixa de diálogo Adicionar Novo Item com o controlador na caixa de pesquisa e o controlador da API Web selecionados](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* Na pasta *Controllers*, crie uma classe chamada `TodoController`.

---

* Substitua o código do modelo pelo seguinte código:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

O código anterior:

* Define uma classe de controlador de API sem métodos.
* Decora a classe com o atributo [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute). Esse atributo indica se o controlador responde às solicitações da API Web. Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.
* Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador. O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.
* Adiciona um item chamado `Item1` ao banco de dados se o banco de dados está vazio. Esse código está no construtor, de modo que ele seja executado sempre que há uma nova solicitação HTTP. Se você excluir todos os itens, o construtor criará `Item1` novamente na próxima vez que um método de API for chamado. Portanto, pode parecer que a exclusão não funcionou quando ela realmente funcionou.

## <a name="add-get-methods"></a>Adicionar métodos Get

Para fornecer uma API que recupera itens pendentes, adicione os seguintes métodos à classe `TodoController`:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

Esses métodos implementam dois pontos de extremidade GET:

* `GET /api/todo`
* `GET /api/todo/{id}`

Teste o aplicativo chamando os dois pontos de extremidade em um navegador. Por exemplo:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

A seguinte resposta HTTP é produzida pela chamada a `GetTodoItems`:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a>Roteamento e caminhos de URL

O atributo [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) indica um método que responde a uma solicitação HTTP GET. O caminho da URL de cada método é construído da seguinte maneira:

* Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção. Para esta amostra, o nome da classe do controlador é **Todo**Controller e, portanto, o nome do controlador é "todo". O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.
* Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho. Esta amostra não usa um modelo. Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente. Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido para o método no parâmetro `id`.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Valores de retorno

O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type). O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta. O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento. As exceções sem tratamento são convertidas em erros 5xx.

Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP. Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:

* Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound).
* Caso contrário, o método retornará 200 com um corpo de resposta JSON. Retornar `item` resulta em uma resposta HTTP 200.

## <a name="test-the-gettodoitems-method"></a>Testar o método GetTodoItems

Este tutorial usa o Postman para testar a API Web.

* Instale o [Postman](https://www.getpostman.com/downloads/)
* Inicie o aplicativo Web.
* Inicie o Postman.
* Desabilite a **Verificação do certificado SSL**
  
  * Em **Arquivo > Configurações** (guia **Geral*), desabilite **Verificação do certificado SSL**.
    > [!WARNING]
    > Habilite novamente a verificação do certificado SSL depois de testar o controlador.

* Crie uma solicitação.
  * Defina o método HTTP como **GET**.
  * Defina a URL de solicitação como `https://localhost:<port>/api/todo`. Por exemplo, `https://localhost:5001/api/todo`.
* Defina **Exibição de dois painéis** no Postman.
* Selecione **Enviar**.

![Postman com solicitação GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a>Adicionar um método Create

Adicione o seguinte método `PostTodoItem`:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

O código anterior é um método HTTP POST, conforme indicado pelo atributo [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute). O método obtém o valor do item pendente no corpo da solicitação HTTP.

O método `CreatedAtAction`:

* retorna um código de status HTTP 201 em caso de êxito. HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.
* Adiciona um cabeçalho `Location` à resposta. O cabeçalho `Location` especifica o URI do item de tarefas pendentes recém-criado. Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho. A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a>Testar o método PostTodoItem

* Compile o projeto.
* No Postman, defina o método HTTP como `POST`.
* Selecione a guia **Corpo**.
* Selecione o botão de opção **bruto**.
* Defina o tipo como **JSON (aplicativo/json)** .
* No corpo da solicitação, insira JSON para um item pendente:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* Selecione **Enviar**.

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

  Se você receber um erro 405 Método Não Permitido, provavelmente, esse será o resultado da não compilação do projeto após a adição do método `PostTodoItem`.

### <a name="test-the-location-header-uri"></a>Testar o URI do cabeçalho de local

* Selecione a guia **Cabeçalhos** no painel **Resposta**.
* Copie o valor do cabeçalho **Local**:

  ![Guia Cabeçalhos do console do Postman](first-web-api/_static/pmc2.png)

* Defina o método como GET.
* Cole o URI (por exemplo, `https://localhost:5001/api/Todo/2`)
* Selecione **Enviar**.

## <a name="add-a-puttodoitem-method"></a>Adicionar um método PutTodoItem

Adicione o seguinte método `PutTodoItem`:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT. A resposta é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações. Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.

### <a name="test-the-puttodoitem-method"></a>Testar o método PutTodoItem

Este exemplo usa um banco de dados em memória que deverá ser iniciado sempre que o aplicativo for iniciado. Deverá haver um item no banco de dados antes de você fazer uma chamada PUT. Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.

Atualize o item pendente que tem a ID = 1 e defina seu nome como "feed fish":

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

A seguinte imagem mostra a atualização do Postman:

![Console do Postman mostrando a resposta 204 (Sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a>Adicionar um método DeleteTodoItem

Adicione o seguinte método `DeleteTodoItem`:

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>Testar o método DeleteTodoItem

Use o Postman para excluir um item pendente:

* Defina o método como `DELETE`.
* Defina o URI do objeto a ser excluído, por exemplo, `https://localhost:5001/api/todo/1`
* Selecione **Enviar**

O aplicativo de exemplo permite que você exclua todos os itens, mas quando o último item é excluído, um novo é criado pelo construtor de classe de modelo na próxima vez que a API é chamada.

## <a name="call-the-api-with-jquery"></a>Chamar a API com o jQuery

Nesta seção, uma página HTML que usa o jQuery para chamar a API Web é adicionada. O jQuery inicia a solicitação e atualiza a página com os detalhes da resposta da API.

Configure o aplicativo para [fornecer arquivos estáticos](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) e [habilitar o mapeamento de arquivo padrão](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_):

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

::: moniker range=">= aspnetcore-2.2"
Crie uma pasta *wwwroot* no diretório do projeto.
::: moniker-end

Adicione um arquivo HTML chamado *index.html* ao diretório *wwwroot*. Substitua seu conteúdo pela seguinte marcação:

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

Adicione um arquivo JavaScript chamado *site.js* ao diretório *wwwroot*. Substitua seu conteúdo pelo código a seguir:

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

Uma alteração nas configurações de inicialização do projeto ASP.NET Core pode ser necessária para testar a página HTML localmente:

* Abra *Properties\launchSettings.json*.
* Remova a propriedade `launchUrl` para forçar o aplicativo a ser aberto em *index.html*, o arquivo padrão do projeto.

Há várias maneiras de obter o jQuery. No snippet anterior, a biblioteca é carregada de uma CDN.

Esta amostra chama todos os métodos CRUD da API. Veja a seguir explicações das chamadas à API.

### <a name="get-a-list-of-to-do-items"></a>Obter uma lista de itens pendentes

A função [ajax](https://api.jquery.com/jquery.ajax/) do jQuery envia uma solicitação `GET` para a API, que retorna o JSON que representa uma matriz de itens pendentes. A função de retorno de chamada `success` será invocada se a solicitação for bem-sucedida. No retorno de chamada, o DOM é atualizado com as informações do item pendente.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>Adicionar um item pendente

A função [ajax](https://api.jquery.com/jquery.ajax/) envia uma solicitação `POST` com o item pendente no corpo da solicitação. As opções `accepts` e `contentType` são definidas como `application/json` para especificar o tipo de mídia que está sendo recebido e enviado. O item pendente é convertido em JSON usando [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify). Quando a API retorna um código de status de êxito, a função `getData` é invocada para atualizar a tabela HTML.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>Atualizar um item pendente

A atualização de um item pendente é semelhante à adição de um. A `url` é alterada para adicionar o identificador exclusivo do item, e o `type` é `PUT`.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>Excluir um item pendente

A exclusão de um item pendente é feita definindo o `type` na chamada do AJAX como `DELETE` e especificando o identificador exclusivo do item na URL.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

## <a name="additional-resources"></a>Recursos adicionais

[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples). Consulte [como baixar](xref:index#how-to-download-a-sample).

Para obter mais informações, consulte os seguintes recursos:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Versão do YouTube deste tutorial](https://www.youtube.com/watch?v=TTkhEyGBfAk)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um projeto de aplicativo API Web.
> * Adicionar uma classe de modelo.
> * Criar o contexto de banco de dados.
> * Registrar o contexto de banco de dados.
> * Adicionar um controlador.
> * Adicionar métodos CRUD.
> * Configurar o roteamento e caminhos de URL.
> * Especificar os valores retornados.
> * Chamar a API Web com o Postman.
> * Chamar a API Web com o jQuery.

Avance para o próximo tutorial para saber como gerar páginas de ajuda da API:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
