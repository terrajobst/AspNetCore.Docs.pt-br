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
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>Tutorial: criar uma API Web com ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Mike Wasson](https://github.com/mikewasson)

Este tutorial ensina os conceitos básicos da criação de uma API Web com o ASP.NET Core.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto de API Web.
> * Adicione uma classe de modelo e um contexto de banco de dados.
> * Faça scaffold de um controlador com métodos CRUD.
> * Configure o roteamento, os caminhos de URL e os valores retornados.
> * Chamar a API Web com o Postman.

No final, você terá uma API Web que pode gerenciar itens de "tarefas pendentes" armazenados em um banco de dados.

## <a name="overview"></a>Visão Geral

Este tutorial cria uma API Web que contém os seguintes pontos de extremidade:

|Ponto de extremidade                  |Descrição            |Corpo da solicitação|Corpo da resposta       |
|--------------------------|-----------------------|------------|--------------------|
|GET /api/TodoItems        |Obter todos os itens de tarefas pendentes    |Nenhum        |Matriz de itens de tarefas pendentes|
|GET /api/TodoItems/{id}   |Obter um item por ID      |Nenhum        |Item de tarefas pendentes          |
|POST /api/TodoItems       |Adicionar um novo item         |Item de tarefas pendentes  |Item de tarefas pendentes          |
|PUT /api/TodoItems/{id}   |Atualizar um item existente|Item de tarefas pendentes  |Nenhum                |
|EXCLUIR/api/TodoItems/{id}|Excluir um item         |Nenhum        |Nenhum                |

O diagrama a seguir mostra o design do aplicativo.

![O cliente é representado por uma caixa à esquerda. Ele envia uma solicitação e recebe uma resposta do aplicativo, uma caixa desenhada à direita. Dentro da caixa do aplicativo, três caixas representam o controlador, o modelo e a camada de acesso a dados. A solicitação é recebida no controlador do aplicativo e as operações de leitura/gravação ocorrem entre o controlador e a camada de acesso a dados. O modelo é serializado e retornado para o cliente na resposta.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>Prerequisites

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a>Criar um projeto Web

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. No menu **Arquivo**, selecione **Novo** > **Projeto**.
1. Selecione o modelo **Aplicativo Web ASP.NET Core** e clique em **Próximo**.
1. Nomeie o projeto como *TodoApi* e clique em **Criar**.
1. Na caixa de diálogo **Criar um aplicativo Web ASP.NET Core**, confirme se **.NET Core** e **ASP.NET Core 3.0** estão selecionados. Selecione o modelo **API** e clique em **Criar**.

![Caixa de diálogo Novo projeto do VS](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
1. Altere os diretórios (`cd`) para a pasta que conterá a pasta do projeto.
1. Execute os seguintes comandos:

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

1. Quando uma caixa de diálogo perguntar se você deseja adicionar os ativos necessários ao projeto, selecione **Sim**.

  Os comandos anteriores:

  * Crie um novo projeto de API Web e abra-o no Visual Studio Code.
  * Adicione os pacotes NuGet que são necessários na próxima seção.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Selecione **Arquivo** > **Nova Solução**.

    ![Nova Solução do macOS](first-web-api-mac/_static/sln.png)

1. Selecione **.NET Core** > **Aplicativo** > **API** > **Próximo**.

    ![Caixa de diálogo Novo Projeto do macOS](first-web-api-mac/_static/1.png)
  
1. Na caixa de diálogo **Configurar sua nova API Web do ASP.NET Core**, selecione a **Estrutura de Destino** do * *.NET Core 3.0*.
1. Insira *TodoApi* para o **Nome do Projeto** e, em seguida, selecione **Criar**.

  ![caixa de diálogo de configuração](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

Abra um terminal de comando na pasta do projeto e execute os seguintes comandos:

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a>Testar a API

O modelo de projeto cria uma API `WeatherForecast`. Chame o método `Get` em um navegador para testar o aplicativo.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Pressione <kbd>Ctrl + F5</kbd> para executar o aplicativo. O Visual Studio inicia um navegador e navega para `https://localhost:<port>/WeatherForecast`, em que `<port>` é um número de porta escolhido aleatoriamente.

Se você receber uma caixa de diálogo perguntando se você deve confiar no certificado do IIS Express, selecione **Sim**. Na caixa de diálogo **Aviso de Segurança** exibida em seguida, selecione **Sim**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pressione <kbd>Ctrl + F5</kbd> para executar o aplicativo. Em um navegador, acesse a seguinte URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Selecione **Executar** > **Iniciar com Depuração** para iniciar o aplicativo. O Visual Studio para Mac inicia um navegador e navega para `https://localhost:<port>`, em que `<port>` é um número de porta escolhido aleatoriamente. Um erro HTTP 404 (Não Encontrado) será retornado. Acrescente `/WeatherForecast` à URL (altere a URL para `https://localhost:<port>/WeatherForecast`).

---

Um JSON semelhante ao seguinte será retornado:

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

## <a name="add-a-model-class"></a>Adicionar uma classe de modelo

Um *modelo* é um conjunto de classes que representam os dados gerenciados pelo aplicativo. O modelo para esse aplicativo é uma única classe `TodoItem`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto. Selecione **Adicionar** > **Nova Pasta**. Nomeie a pasta como *Modelos*.
1. Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**. Dê à classe o nome *TodoItem* e selecione **Adicionar**.
1. Substitua o código do modelo pelo seguinte código:

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Adicione uma pasta denominada *Modelos*.
1. Adicione uma classe `TodoItem` à pasta *Models* com o seguinte código:

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Clique com o botão direito do mouse no projeto. Selecione **Adicionar** > **Nova Pasta**. Nomeie a pasta como *Modelos*.

    ![nova pasta](first-web-api-mac/_static/folder.png)

1. Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Novo Arquivo** > **Geral** > **Classe Vazia**.
1. Nomeie a classe como *TodoItem* e, em seguida, clique em **Novo**.
1. Substitua o código do modelo pelo seguinte código:

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

A propriedade `Id` funciona como a chave exclusiva em um banco de dados relacional.

As classes de modelo podem ser colocadas em qualquer lugar no projeto, mas a pasta *Models* é usada por convenção.

## <a name="add-a-database-context"></a>Adicionar um contexto de banco de dados

O *contexto de banco de dados* é a classe principal que coordena a funcionalidade do Entity Framework para um modelo de dados. Essa classe é criada derivando-a da classe `Microsoft.EntityFrameworkCore.DbContext`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a>Adicione Microsoft.EntityFrameworkCore.SqlServer

1. No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet > Gerenciar Pacotes do NuGet para a Solução**.
1. Selecione a guia **Procurar** e, em seguida, insira **Microsoft.EntityFrameworkCore.SqlServer** na caixa de pesquisa.
1. Selecione **Microsoft. EntityFrameworkCore. SqlServer** no painel esquerdo.
1. Selecione a caixa de seleção **Projeto** no painel direito e, em seguida, selecione **Instalar**.
1. Use as instruções anteriores para adicionar o pacote do NuGet `Microsoft.EntityFrameworkCore.InMemory`.

![Gerenciador de pacotes do NuGet](first-web-api/_static/vs3nuget.png)

## <a name="add-the-todocontext-database-context"></a>Adicione o contexto de banco de dados TodoContext

* Clique com o botão direito do mouse na pasta *Modelos* e selecione **Adicionar** > **Classe**. Nomeie a classe como *TodoContext* e clique em **Adicionar**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

* Adicione uma classe denominada `TodoContext` à pasta *Modelos*.

---

* Insira o seguinte código:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>Registrar o contexto do banco de dados

No ASP.NET Core, os serviços como o contexto do banco de dados devem ser registrados com o contêiner [injeção de dependência (di)](xref:fundamentals/dependency-injection) . O contêiner fornece o serviço aos controladores.

Atualize *Startup.cs* com o seguinte código realçado:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

O código anterior:

* Remove as declarações `using` não utilizadas.
* Adiciona o contexto de banco de dados ao contêiner de DI.
* Especifica que o contexto de banco de dados usará um banco de dados em memória.

## <a name="scaffold-a-controller"></a>Faça scaffold de um controlador

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Clique com o botão direito do mouse na pasta *Controllers*.
1. Selecione **adicionar**  > **novo item com Scaffold**.
1. Selecione **Controlador de API com ações, usando o Entity Framework** e, em seguida, selecione **Adicionar**.
1. Na caixa de diálogo **Adicionar Controlador de API com ações, usando o Entity Framework**:
    * Selecione **TodoItem (TodoApi. Models)** na **classe Model**.
    * Selecione **TodoContext (TodoApi. Models)** na **classe de contexto de dados**.
    * Selecione **Adicionar**.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code/Visual Studio para Mac](#tab/visual-studio-code+visual-studio-mac)

Execute os seguintes comandos:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

Os comandos anteriores:

* Adicionam os pacotes do NuGet para scaffolding.
* Instalam o mecanismo de scaffolding (`dotnet-aspnet-codegenerator`).
* Fazem scaffold de `TodoItemsController`.

---

O código gerado:

* Define uma classe de controlador de API sem métodos.
* Decora a classe com o atributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute). Esse atributo indica se o controlador responde às solicitações da API Web. Para saber mais sobre comportamentos específicos habilitados pelo atributo, consulte <xref:web-api/index>.
* Usa a DI para injetar o contexto de banco de dados (`TodoContext`) no controlador. O contexto de banco de dados é usado em cada um dos métodos [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) no controlador.

## <a name="examine-the-posttodoitem-create-method"></a>Examine o método criar do PostTodoItem

Substitua a instrução return no `PostTodoItem` para usar o operador [nameof](/dotnet/csharp/language-reference/operators/nameof):

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

O código anterior é um método HTTP POST, conforme indicado pelo atributo [[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute). O método obtém o valor do item pendente no corpo da solicitação HTTP.

O método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>:

* retorna um código de status HTTP 201 em caso de êxito. HTTP 201 é a resposta padrão para um método HTTP POST que cria um novo recurso no servidor.
* Adiciona um cabeçalho de [Local](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) à resposta. O cabeçalho `Location` especifica o [URI](https://developer.mozilla.org/docs/Glossary/URI) do item de tarefas pendentes recém-criado. Para obter mais informações, confira [10.2.2 201 Criado](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
* Faz referência à ação `GetTodoItem` para criar o URI de `Location` do cabeçalho. A palavra-chave `nameof` do C# é usada para evitar o hard-coding do nome da ação, na chamada `CreatedAtAction`.

### <a name="install-postman"></a>Instale o Postman

Este tutorial usa o Postman para testar a API Web.

1. Instale o [Postman](https://www.getpostman.com/downloads/)
1. Inicie o aplicativo Web.
1. Inicie o Postman.
1. Desabilite a **Verificação do certificado SSL**
1. Em**configurações** de  >  de **arquivo** (guia**geral** ), desabilite a **verificação de certificado SSL**.

    > [!WARNING]
    > Habilite novamente a verificação do certificado SSL depois de testar o controlador.

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a>Teste o PostTodoItem com o Postman

1. Crie uma solicitação.
1. Defina o método HTTP como `POST`.
1. Selecione a guia **Corpo**.
1. Selecione o botão de opção **bruto**.
1. Defina o tipo como **JSON (aplicativo/json)** .
1. No corpo da solicitação, insira JSON para um item de tarefas pendentes:

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

1. Selecione **Enviar**.

  ![Postman com a solicitação Create](first-web-api/_static/create.png)

### <a name="test-the-location-header-uri"></a>Testar o URI do cabeçalho de local

1. Selecione a guia **Cabeçalhos** no painel **Resposta**.
1. Copie o valor do cabeçalho **Local**:

    ![Guia Cabeçalhos do console do Postman](first-web-api/_static/create.png)

1. Defina o método como GET.
1. Cole o URI (por exemplo, `https://localhost:5001/api/TodoItems/1`).
1. Selecione **Enviar**.

## <a name="examine-the-get-methods"></a>Examine os métodos GET

Esses métodos implementam dois pontos de extremidade GET:

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

Teste o aplicativo chamando os dois pontos de extremidade de um navegador ou do Postman. Por exemplo:

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

Uma resposta semelhante à seguinte é produzida pela chamada a `GetTodoItems`:

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a>Teste o GET com o Postman

1. Crie uma solicitação.
1. Defina o método HTTP como **GET**.
1. Defina a URL de solicitação como `https://localhost:<port>/api/TodoItems`. Por exemplo, `https://localhost:5001/api/TodoItems`.
1. Defina **Exibição de dois painéis** no Postman.
1. Selecione **Enviar**.

Este aplicativo usa um banco de dados em memória. Se o aplicativo for interrompido e iniciado, a solicitação GET anterior não retornará nenhum dado. Se nenhum dado for retornado, execute [POST](#post) de dados no aplicativo.

## <a name="routing-and-url-paths"></a>Roteamento e caminhos de URL

O atributo [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) denota um método que responde a uma solicitação HTTP Get. O caminho da URL de cada método é construído da seguinte maneira:

* Comece com a cadeia de caracteres de modelo no atributo `Route` do controlador:

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* Substitua `[controller]` pelo nome do controlador, que é o nome de classe do controlador menos o sufixo "Controlador" por convenção. Para esta amostra, o nome da classe do controlador é **TodoItems**Controller. Portanto, o nome do controlador é "TodoItems". O [roteamento](xref:mvc/controllers/routing) do ASP.NET Core não diferencia maiúsculas de minúsculas.
* Se o atributo `[HttpGet]` tiver um modelo de rota (por exemplo, `[HttpGet("products")]`), acrescente isso ao caminho. Esta amostra não usa um modelo. Para obter mais informações, confira [Roteamento de atributo com atributos Http[Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).

No método `GetTodoItem` a seguir, `"{id}"` é uma variável de espaço reservado para o identificador exclusivo do item pendente. Quando `GetTodoItem` é invocado, o valor de `"{id}"` na URL é fornecido ao método em seu parâmetro `id`.

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>Valores de retorno

O tipo de retorno dos métodos `GetTodoItems` e `GetTodoItem` é o [tipo ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type). O ASP.NET Core serializa automaticamente o objeto em [JSON](https://www.json.org/) e grava o JSON no corpo da mensagem de resposta. O código de resposta para esse tipo de retorno é 200, supondo que não haja nenhuma exceção sem tratamento. As exceções sem tratamento são convertidas em erros 5xx.

Os tipos de retorno `ActionResult` podem representar uma ampla variedade de códigos de status HTTP. Por exemplo, `GetTodoItem` pode retornar dois valores de status diferentes:

* Se nenhum item corresponder à ID solicitada, o método retornará um código de erro 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound>.
* Caso contrário, o método retornará 200 com um corpo de resposta JSON. Retornar `item` resulta em uma resposta HTTP 200.

## <a name="the-puttodoitem-method"></a>O método PutTodoItem

Examine o método `PutTodoItem`:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

`PutTodoItem` é semelhante a `PostTodoItem`, exceto pelo uso de HTTP PUT. A resposta é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). De acordo com a especificação de HTTP, uma solicitação PUT exige que o cliente envie a entidade inteira atualizada, não apenas as alterações. Para dar suporte a atualizações parciais, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).

Se você vir um erro ao chamar `PutTodoItem`, chame `GET` para garantir que existe um item no banco de dados.

### <a name="test-the-puttodoitem-method"></a>Testar o método PutTodoItem

Este exemplo usa um banco de dados na memória que deve ser inicializado toda vez que o aplicativo é iniciado. Deverá haver um item no banco de dados antes de você fazer uma chamada PUT. Chame GET para garantir a existência de um item no banco de dados antes de fazer uma chamada PUT.

Atualize o item pendente que tem uma ID de 1. Defina seu nome como "alimentar feed":

```json
{
  "ID":1,
  "name":"feed fish",
  "isComplete":true
}
```

A seguinte imagem mostra a atualização do Postman:

![Console do Postman mostrando a resposta 204 (sem conteúdo)](first-web-api/_static/pmcput.png)

## <a name="the-deletetodoitem-method"></a>O método DeleteTodoItem

Examine o método `DeleteTodoItem`:

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

A resposta `DeleteTodoItem` é [204 (Sem conteúdo)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

### <a name="test-the-deletetodoitem-method"></a>Testar o método DeleteTodoItem

Use o Postman para excluir um item pendente:

1. Defina o método como `DELETE`.
1. Defina o URI do objeto a ser excluído (por exemplo, `https://localhost:5001/api/TodoItems/1`).
1. Selecione **Enviar**.

## <a name="call-the-web-api-with-javascript"></a>Chamar a API Web com o JavaScript

Consulte [o tutorial: chamar uma API Web do ASP.NET Core com JavaScript](xref:tutorials/web-api-javascript).

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a>Adicionar suporte de autenticação a uma API da Web

Consulte o tutorial do [IdentityServer4](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) .

## <a name="additional-resources"></a>Recursos adicionais

[Exibir ou baixar o código de exemplo para este tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples). Consulte [como baixar](xref:index#how-to-download-a-sample).

Para obter mais informações, consulte os seguintes recursos:

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [Versão do YouTube deste tutorial](https://www.youtube.com/watch?v=TTkhEyGBfAk)
