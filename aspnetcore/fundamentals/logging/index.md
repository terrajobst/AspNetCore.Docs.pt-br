---
title: Registro em log no ASP.NET Core
author: tdykstra
description: Saiba mais sobre a estrutura de registros no ASP.NET Core. Descubra os provedores de log internos e saiba mais sobre os provedores de terceiros populares.
ms.author: tdykstra
ms.custom: mvc
ms.date: 07/11/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 4fe677e69478284db2ccab655c35b5744b6f63f9
ms.sourcegitcommit: 059ab380744fa3be3b69aa90d431b563c57092cf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68410915"
---
# <a name="logging-in-aspnet-core"></a>Registro em log no ASP.NET Core

Por [Steve Smith](https://ardalis.com/) e [Tom Dykstra](https://github.com/tdykstra)

O ASP.NET Core oferece suporte a uma API de registro em log que funciona com uma variedade de provedores de logs internos e terceirizados. Este artigo mostra como usar a API de registro em log com provedores internos.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Adicionar provedores

Um provedor de log exibe ou armazena logs. Por exemplo, o provedor de Console exibe os logs no console, e o provedor do Azure Application Insights armazena-os no Azure Application Insights. Os logs podem ser enviados para vários destinos por meio da adição de vários provedores.

::: moniker range=">= aspnetcore-2.0"

Para adicionar um provedor, chame o método de extensão `Add{provider name}` do provedor no *Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

O código anterior requer referências a `Microsoft.Extensions.Logging` e `Microsoft.Extensions.Configuration`.

O modelo de projeto padrão chama o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, que adiciona os seguintes provedores de log:

* Console
* Depurar
* EventSource (a partir do ASP.NET Core 2.2)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Se você usar `CreateDefaultBuilder`, poderá substituir os provedores padrão por aqueles que preferir. Chame <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e adicione os provedores desejados.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Para usar um provedor, instale o pacote NuGet e chame o método de extensão do provedor em uma instância de <xref:Microsoft.Extensions.Logging.ILoggerFactory>:

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

A [DI (injeção de dependência)](xref:fundamentals/dependency-injection) do ASP.NET Core fornece a instância de `ILoggerFactory`. Os métodos de extensão `AddConsole` e `AddDebug` são definidos nos pacotes [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) e [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/). Cada método de extensão chama o método `ILoggerFactory.AddProvider`, passando uma instância do provedor.

> [!NOTE]
> O [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x) adiciona provedores de log no método `Startup.Configure`. Para obter saída de log do código que é executado anteriormente, adicione provedores de log no construtor de classe `Startup`.

::: moniker-end

Saiba mais sobre [provedores de log internos](#built-in-logging-providers) e [provedores de log de terceiros](#third-party-logging-providers) mais adiante no artigo.

## <a name="create-logs"></a>Criar logs

Obtenha um objeto <xref:Microsoft.Extensions.Logging.ILogger%601> da DI.

::: moniker range=">= aspnetcore-2.0"

O exemplo de controlador a seguir cria os logs `Information` e `Warning`. A *categoria* é `TodoApiSample.Controllers.TodoController` (o nome de classe totalmente qualificado do `TodoController` no aplicativo de exemplo):

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=4,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

O exemplo do Razor Pages a seguir cria logs com `Information` como o *nível* e `TodoApiSample.Pages.AboutModel` como a *categoria*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3, 7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

O exemplo acima cria logs com `Information` e `Warning` como o *nível* e a classe `TodoController` como a *categoria*. 

::: moniker-end

O *nível* de log indica a gravidade do evento registrado. A *categoria* do log é uma cadeia de caracteres associada a cada log. A instância `ILogger<T>` cria logs que têm o nome totalmente qualificado do tipo `T` como a categoria. [Níveis](#log-level) e [categorias](#log-category) serão explicados com mais detalhes posteriormente neste artigo. 

::: moniker range=">= aspnetcore-2.0"

### <a name="create-logs-in-startup"></a>Criar logs na inicialização

Para gravar logs na classe `Startup`, inclua um parâmetro `ILogger` na assinatura de construtor:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-program"></a>Criar logs no programa

Para gravar logs na classe `Program`, obtenha uma instância `ILogger` da DI:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a>Sem métodos de agente assíncronos

O registro em log deve ser tão rápido que não justifique o custo de desempenho de código assíncrono. Se o armazenamento de dados em log estiver lento, não grave diretamente nele. Grave as mensagens de log em um repositório rápido primeiro e, depois, mova-as para um repositório lento. Por exemplo, se você estiver enviado logs para o SQL Server, convém não fazer isso diretamente em um método `Log`, uma vez que os métodos `Log` são síncronos. Em vez disso, adicione mensagens de log de forma síncrona a uma fila na memória e faça com que uma função de trabalho de plano de fundo efetue pull das mensagens para fora da fila para fazer o trabalho assíncrono de envio de dados por push para o SQL Server.

## <a name="configuration"></a>Configuração

A configuração do provedor de logs é fornecida por um ou mais provedores de sincronização:

* Formatos de arquivo (INI, JSON e XML).
* Argumentos de linha de comando.
* Variáveis de ambiente.
* Objetos do .NET na memória.
* O armazenamento do [Secret Manager](xref:security/app-secrets) não criptografado.
* Um repositório de usuário criptografado, como o [Azure Key Vault](xref:security/key-vault-configuration).
* Provedores personalizados (instalados ou criados).

Por exemplo, a configuração de log geralmente é fornecida pela seção `Logging` dos arquivos de configurações do aplicativo. O exemplo a seguir mostra o conteúdo de um típico arquivo *appsettings.Development.json*:

::: moniker range=">= aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

A propriedade `Logging` pode ter `LogLevel` e propriedades do provedor de logs (o Console é mostrado).

A propriedade `LogLevel` em `Logging` especifica o [nível](#log-level) mínimo para log nas categorias selecionadas. No exemplo, as categorias `System` e `Microsoft` têm log no nível `Information`, e todas as outras no nível `Debug`.

Outras propriedades em `Logging` especificam provedores de logs. O exemplo se refere ao provedor de Console. Se um provedor oferecer suporte a [escopos de log](#log-scopes), `IncludeScopes` indicará se eles estão habilitados. Uma propriedade de provedor (como `Console`, no exemplo) também pode especificar uma propriedade `LogLevel`. `LogLevel` em um provedor especifica os níveis de log para esse provedor.

Se os níveis forem especificados em `Logging.{providername}.LogLevel`, eles substituirão o que estiver definido em `Logging.LogLevel`.

::: moniker-end

::: moniker range="< aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  }
}
```

Chaves `LogLevel` representam nomes de log. A chave `Default` aplica-se a logs não listados de forma explícita. O valor representa o [nível de log](#log-level) aplicado ao log fornecido.

::: moniker-end

Saiba mais sobre como implementar provedores de configuração em <xref:fundamentals/configuration/index>.

## <a name="sample-logging-output"></a>Exemplo de saída de registro em log

Com o código de exemplo mostrado na seção anterior, os logs serão exibidos no console quando o aplicativo for executado pela linha de comando. Aqui está um exemplo da saída do console:

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

Os logs anteriores foram gerados por meio de uma solicitação HTTP Get para o aplicativo de exemplo em `http://localhost:5000/api/todo/0`.

Veja um exemplo de como os mesmos logs aparecem na janela Depuração quando você executa o aplicativo de exemplo no Visual Studio:

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

Os logs criados pelas chamadas `ILogger` mostradas na seção anterior começam com "TodoApi.Controllers.TodoController". Os logs que começam com categorias "Microsoft" são de código da estrutura ASP.NET Core. O ASP.NET Core e o código do aplicativo estão usando a mesma API de registro em log e os mesmos provedores.

O restante deste artigo explica alguns detalhes e opções para registro em log.

## <a name="nuget-packages"></a>Pacotes NuGet

As interfaces `ILogger` e `ILoggerFactory` estão em [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e as implementações padrão para elas estão em [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Categoria de log

Quando um objeto `ILogger` é criado, uma *categoria* é especificada para ele. Essa categoria é incluída em cada mensagem de log criada por essa instância de `ILogger`. A categoria pode ser qualquer cadeia de caracteres, mas a convenção é usar o nome da classe, como "TodoApi.Controllers.TodoController".

Use `ILogger<T>` para obter uma instância `ILogger` que usa o nome de tipo totalmente qualificado do `T` como a categoria:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

Para especificar explicitamente a categoria, chame `ILoggerFactory.CreateLogger`:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

`ILogger<T>` é equivalente a chamar `CreateLogger` com o nome de tipo totalmente qualificado de `T`.

## <a name="log-level"></a>Nível de log

Todo log especifica um valor <xref:Microsoft.Extensions.Logging.LogLevel>. O nível de log indica a gravidade ou importância. Por exemplo, você pode gravar um log `Information` quando um método é finalizado normalmente e um log `Warning` quando um método retorna um código de status *404 Não Encontrado*.

O código a seguir cria os logs `Information` e `Warning`:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

No código anterior, o primeiro parâmetro é a [ID de evento de log](#log-event-id). O segundo parâmetro é um modelo de mensagem com espaços reservados para valores de argumento fornecidos pelos parâmetros de método restantes. Os parâmetros de método serão explicados com posteriormente neste artigo, na [seção de modelos de mensagem](#log-message-template).

Os métodos de log que incluem o nível no nome do método (por exemplo, `LogInformation` e `LogWarning`) são [métodos de extensão para ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Esses métodos chamam um método `Log` que recebe um parâmetro `LogLevel`. Você pode chamar o método `Log` diretamente em vez de um desses métodos de extensão, mas a sintaxe é relativamente complicada. Para saber mais, veja <xref:Microsoft.Extensions.Logging.ILogger> e o [código-fonte de extensões de agente](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

O ASP.NET Core define os seguintes níveis de log, ordenados aqui da menor para a maior gravidade.

* Trace = 0

  Para obter informações que normalmente são valiosas somente para depuração. Essas mensagens podem conter dados confidenciais de aplicativos e, portanto, não devem ser habilitadas em um ambiente de produção. *Desabilitado por padrão.*

* Debug = 1

  Para obter informações que possam ser úteis durante o desenvolvimento e a depuração. Exemplo: `Entering method Configure with flag set to true.` habilite logs de nível `Debug` em produção somente ao solucionar problemas, devido ao alto volume de logs.

* Information = 2

  Para rastrear o fluxo geral do aplicativo. Esses logs normalmente têm algum valor a longo prazo. Exemplo: `Request received for path /api/todo`

* Warning = 3

  Para eventos anormais ou inesperados no fluxo de aplicativo. Eles podem incluir erros ou outras condições que não fazem com que o aplicativo pare, mas que talvez precisem ser investigados. Exceções manipuladas são um local comum para usar o nível de log `Warning`. Exemplo: `FileNotFoundException for file quotes.txt.`

* Error = 4

  Para erros e exceções que não podem ser manipulados. Essas mensagens indicam uma falha na atividade ou na operação atual (como a solicitação HTTP atual) e não uma falha em todo o aplicativo. Mensagem de log de exemplo:`Cannot insert record due to duplicate key violation.`

* Critical = 5

  Para falhas que exigem atenção imediata. Exemplos: cenários de perda de dados, espaço em disco insuficiente.

Use o nível de log para controlar a quantidade de saída de log que é gravada em uma mídia de armazenamento específica ou em uma janela de exibição. Por exemplo:

* Em produção, envie `Trace` por meio do nível `Information` para um armazenamento de dados com volume. Envie `Warning` por meio de `Critical` para um armazenamento de dados com valor.
* Durante o desenvolvimento, envie `Warning` por meio `Critical` para o console e adicione `Trace` por meio de `Information` ao solucionar problemas.

A seção [Filtragem de log](#log-filtering) mais adiante neste artigo explicará como controlar os níveis de log que um provedor manipula.

O ASP.NET Core grava logs para eventos de estrutura. Os exemplos de log anteriores neste artigo excluíram logs abaixo do nível `Information`, portanto, logs de nível `Debug` ou `Trace` não foram criados. Veja um exemplo de logs de console produzidos por meio da execução do aplicativo de exemplo configurado para mostrar logs `Debug`:

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a>ID de evento de log

Cada log pode especificar uma *ID do evento*. O aplicativo de exemplo faz isso usando uma classe `LoggingEvents` definida localmente:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

Uma ID de evento associa um conjunto de eventos. Por exemplo, todos os logs relacionados à exibição de uma lista de itens em uma página podem ser 1001.

O provedor de logs pode armazenar a ID do evento em um campo de ID na mensagem de log ou não armazenar. O provedor de Depuração não mostra IDs de eventos. O provedor de console mostra IDs de evento entre colchetes após a categoria:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Modelo de mensagem de log

Cada log especifica um modelo de mensagem. O modelo de mensagem pode conter espaços reservados para os quais são fornecidos argumentos. Use nomes para os espaços reservados, não números.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

A ordem dos espaços reservados e não de seus nomes, determina quais parâmetros serão usados para fornecer seus valores. No código a seguir, observe que os nomes de parâmetro estão fora de sequência no modelo de mensagem:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Esse código cria uma mensagem de log com os valores de parâmetro na sequência:

```
Parameter values: parm1, parm2
```

A estrutura de registros funciona dessa maneira para que os provedores de logs possam implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Os próprios argumentos são passados para o sistema de registro em log, não apenas o modelo de mensagem formatado. Essas informações permitem que os provedores de log armazenem os valores de parâmetro como campos. Por exemplo, suponha que as chamadas de método do agente sejam assim:

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

Se você estiver enviando os logs para o Armazenamento de Tabelas do Azure, cada entidade da Tabela do Azure poderá ter propriedades `ID` e `RequestTime`, o que simplificará as consultas nos dados de log. Uma consulta pode encontrar todos os logs em determinado intervalo de `RequestTime` sem analisar o tempo limite da mensagem de texto.

## <a name="logging-exceptions"></a>Exceções de registro em log

Os métodos de agente têm sobrecargas que permitem que você passe uma exceção, como no exemplo a seguir:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

Provedores diferentes manipulam as informações de exceção de maneiras diferentes. Aqui está um exemplo da saída do provedor Depuração do código mostrado acima.

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtragem de log

::: moniker range=">= aspnetcore-2.0"

Você pode especificar um nível de log mínimo para um provedor e uma categoria específicos ou para todos os provedores ou todas as categorias. Os logs abaixo do nível mínimo não serão passados para esse provedor, para que não sejam exibidos ou armazenados.

Para suprimir todos os logs, especifique `LogLevel.None` como o nível de log mínimo. O valor inteiro de `LogLevel.None` é 6, que é maior do que `LogLevel.Critical` (5).

### <a name="create-filter-rules-in-configuration"></a>Criar regras de filtro na configuração

O código do modelo de projeto chama `CreateDefaultBuilder` para configurar o registro em log para os provedores Console e Depuração. O método `CreateDefaultBuilder` também configura o registro em log para procurar a configuração em uma seção `Logging`, usando código semelhante ao seguinte:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=17)]

Os dados de configuração especificam níveis de log mínimo por provedor e por categoria, como no exemplo a seguir:

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

Este JSON cria seis regras de filtro, uma para o provedor Depuração, quatro para o provedor Console e uma para todos os provedores. Apenas uma regra é escolhida para cada provedor quando um objeto `ILogger` é criado.

### <a name="filter-rules-in-code"></a>Regras de filtro no código

O exemplo a seguir mostra como registrar regras de filtro no código:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

O segundo `AddFilter` especifica o provedor Depuração usando seu nome de tipo. O primeiro `AddFilter` se aplica a todos os provedores porque ele não especifica um tipo de provedor.

### <a name="how-filtering-rules-are-applied"></a>Como as regras de filtragem são aplicadas

Os dados de configuração e o código `AddFilter`, mostrados nos exemplos anteriores, criam as regras mostradas na tabela a seguir. As primeiras seis vêm do exemplo de configuração e as últimas duas vêm do exemplo de código.

| Número | Provider      | Categorias que começam com...          | Nível de log mínimo |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Depurar         | Todas as categorias                          | Informações       |
| 2      | Console       | Microsoft.AspNetCore.Mvc.Razor.Internal | Aviso           |
| 3      | Console       | Microsoft.AspNetCore.Mvc.Razor.Razor    | Depurar             |
| 4      | Console       | Microsoft.AspNetCore.Mvc.Razor          | Erro             |
| 5      | Console       | Todas as categorias                          | Informações       |
| 6      | Todos os provedores | Todas as categorias                          | Depurar             |
| 7      | Todos os provedores | Sistema                                  | Depurar             |
| 8      | Depurar         | Microsoft                               | Rastrear             |

Quando um objeto `ILogger` é criado, o objeto `ILoggerFactory` seleciona uma única regra por provedor para aplicar a esse agente. Todas as mensagens gravadas pela instância `ILogger` são filtradas com base nas regras selecionadas. A regra mais específica possível para cada par de categoria e provedor é selecionada dentre as regras disponíveis.

O algoritmo a seguir é usado para cada provedor quando um `ILogger` é criado para uma determinada categoria:

* Selecione todas as regras que correspondem ao provedor ou seu alias. Se nenhuma correspondência for encontrada, selecione todas as regras com um provedor vazio.
* Do resultado da etapa anterior, selecione as regras com o prefixo de categoria de maior correspondência. Se nenhuma correspondência for encontrada, selecione todas as regras que não especificam uma categoria.
* Se várias regras forem selecionadas, use a **última**.
* Se nenhuma regra for selecionada, use `MinimumLevel`.

Com a lista anterior de regras, suponha que você crie um objeto `ILogger` para a categoria "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":

* Para o provedor Depuração as regras 1, 6 e 8 se aplicam. A regra 8 é mais específica, portanto é a que será selecionada.
* Para o provedor Console as regras 3, 4, 5 e 6 se aplicam. A regra 3 é a mais específica.

A instância `ILogger` resultante envia logs de nível `Trace` e superior para o provedor Depuração. Logs de nível `Debug` e superior são enviados para o provedor Console.

### <a name="provider-aliases"></a>Aliases de provedor

Cada provedor define um *alias* que pode ser usado na configuração no lugar do nome de tipo totalmente qualificado.  Para os provedores internos, use os seguintes aliases:

* Console
* Depurar
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Nível mínimo padrão

Há uma configuração de nível mínimo que entra em vigor somente se nenhuma regra de código ou de configuração se aplicar a um provedor e uma categoria determinados. O exemplo a seguir mostra como definir o nível mínimo:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

Se você não definir explicitamente o nível mínimo, o valor padrão será `Information`, o que significa que logs `Trace` e `Debug` serão ignorados.

### <a name="filter-functions"></a>Funções de filtro

Uma função de filtro é invocada para todos os provedores e categorias que não têm regras atribuídas a eles por configuração ou código. O código na função tem acesso ao tipo de provedor, à categoria e ao nível de log. Por exemplo:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Alguns provedores de log permitem especificar quando os logs devem ser gravados em uma mídia de armazenamento ou ignorados, com base no nível de log e na categoria.

Os métodos de extensão `AddConsole` e `AddDebug` fornecem sobrecargas que aceitam critérios de filtragem. O código de exemplo a seguir faz com que o provedor de console ignore os logs abaixo do nível `Warning`, enquanto o provedor Depuração ignora os logs criados pela estrutura.

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

O método `AddEventLog` tem uma sobrecarga que recebe uma instância `EventLogSettings`, que pode conter uma função de filtragem em sua propriedade `Filter`. O provedor TraceSource não fornece nenhuma dessas sobrecargas, porque seu nível de registro em log e outros parâmetros são baseados no `SourceSwitch` e no `TraceListener` que ele usa.

Para definir regras de filtragem para todos os provedores registrados com uma instância `ILoggerFactory`, use o método de extensão `WithFilter`. O exemplo abaixo limita os logs de estrutura (categoria começa com "Microsoft" ou "Sistema") a avisos, enquanto registra em nível de depuração os logs criados por código de aplicativo.

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

Para impedir a gravação de logs, especifique `LogLevel.None` como o nível de log mínimo. O valor inteiro de `LogLevel.None` é 6, que é maior do que `LogLevel.Critical` (5).

O método de extensão `WithFilter` é fornecido pelo pacote NuGet [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter). O método retorna um nova instância `ILoggerFactory`, que filtrará as mensagens de log passadas para todos os provedores de agente registrados com ela. Isso não afeta nenhuma outra instância de `ILoggerFactory`, incluindo a instância de `ILoggerFactory` original.

::: moniker-end

## <a name="system-categories-and-levels"></a>Categorias e níveis de sistema

Veja algumas categorias usadas pelo ASP.NET Core e Entity Framework Core, com anotações sobre quais logs esperar delas:

| Categoria                            | Observações |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Diagnóstico geral de ASP.NET Core. |
| Microsoft.AspNetCore.DataProtection | Quais chaves foram consideradas, encontradas e usadas. |
| Microsoft.AspNetCore.HostFiltering  | Hosts permitidos. |
| Microsoft.AspNetCore.Hosting        | Quanto tempo levou para que as solicitações de HTTP fossem concluídas e em que horário foram iniciadas. Quais assemblies de inicialização de hospedagem foram carregados. |
| Microsoft.AspNetCore.Mvc            | Diagnóstico do MVC e Razor. Model binding, execução de filtro, compilação de exibição, seleção de ação. |
| Microsoft.AspNetCore.Routing        | Informações de correspondência de rotas. |
| Microsoft.AspNetCore.Server         | Respostas de início, parada e atividade da conexão. Informações sobre o certificado HTTPS. |
| Microsoft.AspNetCore.StaticFiles    | Arquivos atendidos. |
| Microsoft.EntityFrameworkCore       | Diagnóstico geral do Entity Framework Core. Atividade e configuração do banco de dados, detecção de alterações, migrações. |

## <a name="log-scopes"></a>Escopos de log

 Um *escopo* pode agrupar um conjunto de operações lógicas. Esse agrupamento pode ser usado para anexar os mesmos dados para cada log criado como parte de um conjunto. Por exemplo, todo log criado como parte do processamento de uma transação pode incluir a ID da transação.

Um escopo é um tipo `IDisposable` retornado pelo método <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> e que dura até que seja descartado. Use um escopo por meio do encapsulamento de chamadas de agente em um bloco `using`:

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

O código a seguir habilita os escopos para o provedor de console:

::: moniker range="> aspnetcore-2.0"

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> A configuração da opção de agente de console `IncludeScopes` é necessária para habilitar o registro em log baseado em escopo.
>
> Saiba mais sobre como configurar na seção [Configuração](#configuration).

::: moniker-end

::: moniker range="= aspnetcore-2.0"

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> A configuração da opção de agente de console `IncludeScopes` é necessária para habilitar o registro em log baseado em escopo.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

*Startup.cs*:

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

Cada mensagem de log inclui as informações com escopo definido:

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Provedores de log internos

O ASP.NET Core vem com os seguintes provedores:

* [Console](#console-provider)
* [Depurar](#debug-provider)
* [EventSource](#eventsource-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Para obter informações sobre StdOut e o log de depuração com o Módulo do ASP.NET Core, consulte <xref:test/troubleshoot-azure-iis> e <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

### <a name="console-provider"></a>Provedor do console

O pacote de provedor [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envia a saída de log para o console. 

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddConsole();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddConsole();
```

As [sobrecargas do AddConsole](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions) permitem que você passe um nível de log mínimo, uma função de filtro e um valor booliano que indica se escopos são compatíveis. Outra opção é passar um objeto `IConfiguration`, que pode especificar suporte de escopos e níveis de log.

Para opções de provedor do console, confira <xref:Microsoft.Extensions.Logging.Console.ConsoleLoggerOptions>.

O provedor de console tem um impacto significativo no desempenho e geralmente não é adequado para uso em produção.

Quando você cria um novo projeto no Visual Studio, o método `AddConsole` tem essa aparência:

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

Esse código se refere à seção `Logging` do arquivo *appSettings.json*:

[!code-json[](index/samples/1.x/TodoApiSample/appsettings.json)]

As configurações mostradas limitam os logs de estrutura a avisos, permitindo que o aplicativo para faça registros no nível de depuração, conforme explicado na [Filtragem de log](#log-filtering) seção. Para obter mais informações, consulte [Configuração](xref:fundamentals/configuration/index).

::: moniker-end

Para ver a saída de registro em log de console, abra um prompt de comando na pasta do projeto e execute o seguinte comando:

```console
dotnet run
```

### <a name="debug-provider"></a>Depurar provedor

O pacote de provedor [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) grava a saída de log usando a classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chamadas de método `Debug.WriteLine`).

No Linux, esse provedor grava logs em */var/log/message*.

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddDebug();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddDebug();
```

As [sobrecargas de AddDebug](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions) permitem que você passe um nível de log mínimo ou uma função de filtro.

::: moniker-end

### <a name="eventsource-provider"></a>Provedor EventSource

Para aplicativos que se destinam ao ASP.NET Core 1.1.0 ou posterior, o pacote de provedor [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) pode implementar o rastreamento de eventos. No Windows, ele usa [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803). O provedor é multiplataforma, mas ainda não há ferramentas de coleta e exibição de eventos para Linux ou macOS.

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventSourceLogger();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventSourceLogger();
```

::: moniker-end

Uma boa maneira de coletar e exibir logs é usar o [utilitário PerfView](https://github.com/Microsoft/perfview). Há outras ferramentas para exibir os logs do ETW, mas o PerfView proporciona a melhor experiência para trabalhar com os eventos de ETW emitidos pelo ASP.NET.

Para configurar o PerfView para coletar eventos registrados por esse provedor, adicione a cadeia de caracteres `*Microsoft-Extensions-Logging` à lista **Provedores Adicionais**. (Não se esqueça do asterisco no início da cadeia de caracteres).

![Outros provedores de Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Provedor EventLog do Windows

O pacote de provedor [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envia a saída de log para o Log de Eventos do Windows.

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventLog();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventLog();
```

As [sobrecargas de AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) permitem que você passe `EventLogSettings` ou um nível de log mínimo.

::: moniker-end

### <a name="tracesource-provider"></a>Provedor TraceSource

O pacote de provedor [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa as bibliotecas e provedores de <xref:System.Diagnostics.TraceSource>.

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddTraceSource(sourceSwitchName);
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

::: moniker-end

As [sobrecargas de AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) permitem que você passe um comutador de fonte e um ouvinte de rastreamento.

Para usar esse provedor, o aplicativo deve ser executado no .NET Framework (em vez do .NET Core). O provedor pode rotear mensagens a uma variedade de [ouvintes](/dotnet/framework/debug-trace-profile/trace-listeners), como o <xref:System.Diagnostics.TextWriterTraceListener> usado no aplicativo de exemplo.

::: moniker range="< aspnetcore-2.0"

O exemplo a seguir configura um provedor `TraceSource` que registra mensagens `Warning` e superiores na janela de console.

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

::: moniker-end

### <a name="azure-app-service-provider"></a>Provedor do Serviço de Aplicativo do Azure

O pacote de provedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) grava logs em arquivos de texto no sistema de arquivos de um aplicativo do Serviço de Aplicativo do Azure e no [armazenamento de blobs](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) em uma conta de Armazenamento do Azure. O pacote de provedor está disponível para aplicativos destinados ao .NET Core 1.1 ou posterior.

::: moniker range=">= aspnetcore-2.0"

Se você estiver direcionando para o .NET Core, observe os seguintes pontos:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* O pacote de provedor está incluído no [metapacote Microsoft.AspNetCore.All](xref:fundamentals/metapackage) do ASP.NET Core.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* O pacote de provedor não está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Para usar o provedor, instale o pacote.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

Se você estiver direcionando para o .NET Framework ou referenciando o metapacote `Microsoft.AspNetCore.App`, adicione o pacote do provedor ao projeto. Chame `AddAzureWebAppDiagnostics`:

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="= aspnetcore-1.1"

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

Uma sobrecarga <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> permite passar <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>. O objeto settings pode substituir as configurações padrão, como o modelo de saída de registro em log, o nome do blob e o limite do tamanho do arquivo. (O *modelo Output* é um modelo de mensagem aplicado a todos os logs, além daquele fornecido com uma chamada ao método `ILogger`).

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Para definir as configurações do provedor, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> e <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, conforme mostrado no exemplo a seguir:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

Quando você implanta em um aplicativo do Serviço de Aplicativo, o aplicativo respeita as configurações na seção [Logs do Serviço de Aplicativo](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) da página **Serviço de Aplicativo** do portal do Azure. Quando as configurações a seguir são atualizadas, as alterações entram em vigor imediatamente sem a necessidade de uma reinicialização ou reimplantação do aplicativo.

* **Log de aplicativo (Sistema de Arquivos)**
* **Log de aplicativo (Blob)**

O local padrão para arquivos de log é na pasta *D:\\home\\LogFiles\\Application* e o nome de arquivo padrão é *diagnostics-aaaammdd.txt*. O limite padrão de tamanho do arquivo é 10 MB e o número padrão máximo de arquivos mantidos é 2. O nome de blob padrão é *{app-name}{timestamp}/aaaa/mm/dd/hh/{guid}-applicationLog.txt*.

O provedor funciona somente quando o projeto é executado no ambiente do Azure. Ele não tem nenhum efeito quando o projeto é executado localmente&mdash;ele não grava em arquivos locais ou no armazenamento de desenvolvimento local para blobs.

#### <a name="azure-log-streaming"></a>Fluxo de log do Azure

O fluxo de log do Azure permite que você exiba a atividade de log em tempo real:

* O servidor de aplicativos
* Do servidor Web
* De uma solicitação de rastreio com falha

Para configurar o fluxo de log do Azure:

* Navegue até a página **Logs do Serviço de Aplicativo** da página do portal do seu aplicativo.
* Defina o **Log de aplicativo (Sistema de Arquivos)** como **Ativado**.
* Escolha o **Nível** de log.

Navegue até a página **Fluxo de Log** para exibir as mensagens de aplicativo. Elas são registradas pelo aplicativo por meio da interface `ILogger`.

::: moniker range=">= aspnetcore-1.1"

### <a name="azure-application-insights-trace-logging"></a>Log de rastreamento do Azure Application Insights

O pacote de provedor [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) grava os logs no Azure Application Insights. O Application Insights é um serviço que monitora um aplicativo web e fornece ferramentas para consultar e analisar os dados de telemetria. Se você usar esse provedor, poderá consultar e analisar os logs usando as ferramentas do Application Insights.

O provedor de registro em log está incluído como uma dependência de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), que é o pacote que fornece toda a telemetria disponível para o ASP.NET Core. Se você usar esse pacote, não precisará instalar o pacote de provedor.

Não use o pacote [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) &mdash;que é para o ASP.NET 4.x.

Para obter mais informações, consulte os seguintes recursos:

* [Visão geral do Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights para aplicativos ASP.NET Core](/azure/azure-monitor/app/asp-net-core) – Comece aqui se você deseja implementar toda a gama de telemetria do Application Insights junto com o registro em log.
* [ApplicationInsightsLoggerProvider para logs do .NET Core ILogger](/azure/azure-monitor/app/ilogger) – Comece aqui se você quiser implementar o provedor de log sem o restante da telemetria do Application Insights.
* [Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs) (Adaptadores de registro em log do Application Insights).
* [Instalar, configurar e inicializar o SDK do Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) – Tutorial interativo no site da Microsoft Learn.
::: moniker-end

## <a name="third-party-logging-providers"></a>Provedores de log de terceiros

Estruturas de log de terceiros que funcionam com o ASP.NET Core:

* [elmah.io](https://elmah.io/) ([repositório GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repositório do GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([repositório GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([Repositório do GitHub](https://github.com/catalingavan/KissLog-net))
* [Loggr](https://loggr.net/) ([repositório GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([repositório GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([repositório GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([repositório GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repositório Github](https://github.com/googleapis/google-cloud-dotnet))

Algumas estruturas de terceiros podem fazer o [log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Usar uma estrutura de terceiros é semelhante ao uso de um dos provedores internos:

1. Adicione um pacote NuGet ao projeto.
1. Chame um `ILoggerFactory`.

Para saber mais, consulte a documentação de cada provedor. Não há suporte para provedores de log de terceiros na Microsoft.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/logging/loggermessage>
