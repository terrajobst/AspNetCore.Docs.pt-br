---
title: Como fazer registro em log no .NET Core e no ASP.NET Core
author: rick-anderson
description: Saiba como usar a estrutura de registro em log fornecida pelo pacote do NuGet Microsoft.Extensions.Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 697e6cf0cd1b51ad6c2942e21bc084d1fe6bfa4e
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259741"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>Como fazer registro em log no .NET Core e no ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra) e [Steve Smith](https://ardalis.com/)

O .NET Core oferece suporte a uma API de registro em log que funciona com uma variedade de provedores de logs internos e terceirizados. Este artigo mostra como usar a API de registro em log com provedores internos.

::: moniker range=">= aspnetcore-3.0"

A maioria dos exemplos de código mostrados neste artigo é de aplicativos ASP.NET Core. As partes específicas de log desses trechos de código se aplicam a qualquer aplicativo .NET Core que usa o [host genérico](xref:fundamentals/host/generic-host). Para obter informações sobre como usar o host genérico em aplicativos de console não Web, confira [Serviços hospedados](xref:fundamentals/host/hosted-services).

O código de registro em log de aplicativos sem host genérico difere na maneira como os [provedores são adicionados](#add-providers) e como os [agentes são criados](#create-logs). Exemplos de código não host são mostrados nessas seções do artigo.

::: moniker-end

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Adicionar provedores

Um provedor de log exibe ou armazena logs. Por exemplo, o provedor de Console exibe os logs no console, e o provedor do Azure Application Insights armazena-os no Azure Application Insights. Os logs podem ser enviados para vários destinos por meio da adição de vários provedores.

::: moniker range=">= aspnetcore-3.0"

Para adicionar um provedor em um aplicativo que usa um host genérico, chame o método de extensão `Add{provider name}` do provedor no *Program.cs*:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

Em um aplicativo de console não host, chame o método de extensão `Add{provider name}` do provedor ao criar um `LoggerFactory`:

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

`LoggerFactory` e `AddConsole` exigem uma instrução `using` para `Microsoft.Extensions.Logging`.

Os modelos de projeto padrão do ASP.NET Core chamam <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, que adiciona os seguintes provedores de log:

* Console
* Depurar
* EventSource
* EventLog (somente quando em execução no Windows)

Você pode substituir os provedores padrão por aqueles de sua preferência. Chame <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> e adicione os provedores desejados.

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

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

Saiba mais sobre [provedores de log internos](#built-in-logging-providers) e [provedores de log de terceiros](#third-party-logging-providers) mais adiante no artigo.

## <a name="create-logs"></a>Criar logs

Para criar logs, use um objeto <xref:Microsoft.Extensions.Logging.ILogger%601>. Em um aplicativo Web ou serviço hospedado, obtenha um `ILogger` da DI (injeção de dependência). Em aplicativos de console não host, use o `LoggerFactory` para criar um `ILogger`.

O exemplo do ASP.NET Core a seguir cria um agente de categoria `TodoApiSample.Pages.AboutModel`. A *categoria* do log é uma cadeia de caracteres associada a cada log. A instância de `ILogger<T>` cria logs que têm como a categoria o nome totalmente qualificado do tipo `T`. 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

O exemplo do aplicativo de console não host a seguir cria um agente de categoria `LoggingConsoleApp.Program`.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

Nos exemplos de ASP.NET Core e aplicativo de console a seguir, o agente é usado para criar logs de nível `Information`. O *nível* de log indica a gravidade do evento registrado. 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

[Níveis](#log-level) e [categorias](#log-category) serão explicados com mais detalhes posteriormente neste artigo. 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a>Criar logs na classe Programa

Para gravar logs na classe `Program` de um aplicativo ASP.NET Core, obtenha uma instância de `ILogger` por meio da DI após compilar o host:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

### <a name="create-logs-in-the-startup-class"></a>Criar logs na classe Startup

Para gravar logs no método `Startup.Configure` de um aplicativo ASP.NET Core, inclua um parâmetro `ILogger` na assinatura do método:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

A gravação de logs antes da conclusão da configuração do contêiner de DI no método `Startup.ConfigureServices` não é uma ação compatível:

* A injeção de agente no construtor `Startup` não é uma ação compatível.
* A injeção de agente na assinatura do método `Startup.ConfigureServices` não é uma ação compatível

O motivo para essa restrição é que o registro em log depende da DI e da configuração, a qual por sua vez depende da DI. O contêiner de DI não é configurado até `ConfigureServices` ser concluído.

A injeção de construtor de um agente em `Startup` funciona em versões anteriores do ASP.NET Core porque um contêiner de DI separado é criado para o host da Web. Para obter informações sobre por que apenas um contêiner é criado para o host genérico, consulte o [anúncio de alteração da falha](https://github.com/aspnet/Announcements/issues/353).

Se precisar configurar um serviço que dependa de `ILogger<T>`, você ainda poderá fazer isso usando a injeção de construtor ou fornecendo um método de fábrica. A abordagem do método de fábrica é recomendada somente se não há nenhuma outra opção. Por exemplo, suponha que você precise preencher uma propriedade com um serviço da DI:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

O código realçado anterior é um `Func` que é executado na primeira vez que o contêiner de DI precisa construir uma instância de `MyService`. Você pode acessar qualquer um dos serviços registrados dessa maneira.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a>Criar logs na inicialização

Para gravar logs na classe `Startup`, inclua um parâmetro `ILogger` na assinatura de construtor:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Criar logs na classe Programa

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

Saiba mais sobre como implementar provedores de configuração em <xref:fundamentals/configuration/index>.

## <a name="sample-logging-output"></a>Exemplo de saída de registro em log

Com o código de exemplo mostrado na seção anterior, os logs serão exibidos no console quando o aplicativo for executado pela linha de comando. Aqui está um exemplo da saída do console:

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

Os logs anteriores foram gerados por meio de uma solicitação HTTP Get para o aplicativo de exemplo em `http://localhost:5000/api/todo/0`.

Veja um exemplo de como os mesmos logs aparecem na janela Depuração quando você executa o aplicativo de exemplo no Visual Studio:

::: moniker range=">= aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

Os logs criados pelas chamadas `ILogger` mostradas na seção anterior começam com "TodoApiSample". Os logs que começam com categorias "Microsoft" são de código da estrutura ASP.NET Core. O ASP.NET Core e o código do aplicativo estão usando a mesma API de registro em log e os mesmos provedores.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

Os logs criados pelas chamadas `ILogger` mostradas na seção anterior começam com "TodoApi". Os logs que começam com categorias "Microsoft" são de código da estrutura ASP.NET Core. O ASP.NET Core e o código do aplicativo estão usando a mesma API de registro em log e os mesmos provedores.

::: moniker-end

O restante deste artigo explica alguns detalhes e opções para registro em log.

## <a name="nuget-packages"></a>Pacotes NuGet

As interfaces `ILogger` e `ILoggerFactory` estão em [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) e as implementações padrão para elas estão em [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Categoria de log

Quando um objeto `ILogger` é criado, uma *categoria* é especificada para ele. Essa categoria é incluída em cada mensagem de log criada por essa instância de `ILogger`. A categoria pode ser qualquer cadeia de caracteres, mas a convenção é usar o nome da classe, como "TodoApi.Controllers.TodoController".

Use `ILogger<T>` para obter uma instância `ILogger` que usa o nome de tipo totalmente qualificado do `T` como a categoria:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

Para especificar explicitamente a categoria, chame `ILoggerFactory.CreateLogger`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

`ILogger<T>` é equivalente a chamar `CreateLogger` com o nome de tipo totalmente qualificado de `T`.

## <a name="log-level"></a>Nível de log

Todo log especifica um valor <xref:Microsoft.Extensions.Logging.LogLevel>. O nível de log indica a gravidade ou importância. Por exemplo, você pode gravar um log `Information` quando um método é finalizado normalmente e um log `Warning` quando um método retorna um código de status *404 Não Encontrado*.

O código a seguir cria os logs `Information` e `Warning`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

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

* Em produção:
  * O registro em log no `Trace` por meio de níveis `Information` produz um alto volume de mensagens de log detalhadas. Para controlar os custos e não exceder os limites de armazenamento de dados, faça o log `Trace` por meio de mensagens de nível `Information` para um armazenamento de dados de alto volume e baixo custo.
  * O registro em log em `Warning` em níveis `Critical` normalmente produz menos mensagens de log menores. Portanto, os custos e os limites de armazenamento geralmente não são uma preocupação, o que resulta em maior flexibilidade de escolha de armazenamento de dados.
* Durante o desenvolvimento:
  * Log `Warning` através de mensagens `Critical` para o console.
  * Adicione `Trace` por meio de mensagens `Information` ao solucionar problemas.

A seção [Filtragem de log](#log-filtering) mais adiante neste artigo explicará como controlar os níveis de log que um provedor manipula.

O ASP.NET Core grava logs para eventos de estrutura. Os exemplos de log anteriores neste artigo excluíram logs abaixo do nível `Information`, portanto, logs de nível `Debug` ou `Trace` não foram criados. Veja um exemplo de logs de console produzidos por meio da execução do aplicativo de exemplo configurado para mostrar logs `Debug`:

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

## <a name="log-event-id"></a>ID de evento de log

Cada log pode especificar uma *ID do evento*. O aplicativo de exemplo faz isso usando uma classe `LoggingEvents` definida localmente:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

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

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

A ordem dos espaços reservados e não de seus nomes, determina quais parâmetros serão usados para fornecer seus valores. No código a seguir, observe que os nomes de parâmetro estão fora de sequência no modelo de mensagem:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Esse código cria uma mensagem de log com os valores de parâmetro na sequência:

```text
Parameter values: parm1, parm2
```

A estrutura de registros funciona dessa maneira para que os provedores de logs possam implementar [registro em log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Os próprios argumentos são passados para o sistema de registro em log, não apenas o modelo de mensagem formatado. Essas informações permitem que os provedores de log armazenem os valores de parâmetro como campos. Por exemplo, suponha que as chamadas de método do agente sejam assim:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Se você estiver enviando os logs para o Armazenamento de Tabelas do Azure, cada entidade da Tabela do Azure poderá ter propriedades `ID` e `RequestTime`, o que simplificará as consultas nos dados de log. Uma consulta pode encontrar todos os logs em determinado intervalo de `RequestTime` sem analisar o tempo limite da mensagem de texto.

## <a name="logging-exceptions"></a>Exceções de registro em log

Os métodos de agente têm sobrecargas que permitem que você passe uma exceção, como no exemplo a seguir:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

Provedores diferentes manipulam as informações de exceção de maneiras diferentes. Aqui está um exemplo da saída do provedor Depuração do código mostrado acima.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtragem de log

Você pode especificar um nível de log mínimo para um provedor e uma categoria específicos ou para todos os provedores ou todas as categorias. Os logs abaixo do nível mínimo não serão passados para esse provedor, para que não sejam exibidos ou armazenados.

Para suprimir todos os logs, especifique `LogLevel.None` como o nível de log mínimo. O valor inteiro de `LogLevel.None` é 6, que é maior do que `LogLevel.Critical` (5).

### <a name="create-filter-rules-in-configuration"></a>Criar regras de filtro na configuração

O código do modelo de projeto chama `CreateDefaultBuilder` para configurar o registro em log para os provedores Console e Depuração. O método `CreateDefaultBuilder` configura o registro em log para procurar a configuração em uma seção `Logging`, conforme explicado [anteriormente neste artigo](#configuration).

Os dados de configuração especificam níveis de log mínimo por provedor e por categoria, como no exemplo a seguir:

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

Este JSON cria seis regras de filtro, uma para o provedor Depuração, quatro para o provedor Console e uma para todos os provedores. Apenas uma regra é escolhida para cada provedor quando um objeto `ILogger` é criado.

### <a name="filter-rules-in-code"></a>Regras de filtro no código

O exemplo a seguir mostra como registrar regras de filtro no código:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

O segundo `AddFilter` especifica o provedor Depuração usando seu nome de tipo. O primeiro `AddFilter` se aplica a todos os provedores porque ele não especifica um tipo de provedor.

### <a name="how-filtering-rules-are-applied"></a>Como as regras de filtragem são aplicadas

Os dados de configuração e o código `AddFilter`, mostrados nos exemplos anteriores, criam as regras mostradas na tabela a seguir. As primeiras seis vêm do exemplo de configuração e as últimas duas vêm do exemplo de código.

| Number | Provider      | Categorias que começam com...          | Nível de log mínimo |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Depurar         | Todas as categorias                          | Informações       |
| 2      | Console       | Microsoft.AspNetCore.Mvc.Razor.Internal | Aviso           |
| 3      | Console       | Microsoft.AspNetCore.Mvc.Razor.Razor    | Depurar             |
| 4      | Console       | Microsoft.AspNetCore.Mvc.Razor          | Erro             |
| 5      | Console       | Todas as categorias                          | Informações       |
| 6      | Todos os provedores | Todas as categorias                          | Depurar             |
| 7      | Todos os provedores | Sistema                                  | Depurar             |
| 8      | Depurar         | Microsoft                               | Rastreamento             |

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

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

Se você não definir explicitamente o nível mínimo, o valor padrão será `Information`, o que significa que logs `Trace` e `Debug` serão ignorados.

### <a name="filter-functions"></a>Funções de filtro

Uma função de filtro é invocada para todos os provedores e categorias que não têm regras atribuídas a eles por configuração ou código. O código na função tem acesso ao tipo de provedor, à categoria e ao nível de log. Por exemplo:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

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

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

O código a seguir habilita os escopos para o provedor de console:

*Program.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> A configuração da opção de agente de console `IncludeScopes` é necessária para habilitar o registro em log baseado em escopo.
>
> Saiba mais sobre como configurar na seção [Configuração](#configuration).

Cada mensagem de log inclui as informações com escopo definido:

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
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

```csharp
logging.AddConsole();
```

Para ver a saída de registro em log de console, abra um prompt de comando na pasta do projeto e execute o seguinte comando:

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Depurar provedor

O pacote de provedor [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) grava a saída de log usando a classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (chamadas de método `Debug.WriteLine`).

No Linux, esse provedor grava logs em */var/log/message*.

```csharp
logging.AddDebug();
```

### <a name="eventsource-provider"></a>Provedor EventSource

Para aplicativos que se destinam ao ASP.NET Core 1.1.0 ou posterior, o pacote de provedor [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) pode implementar o rastreamento de eventos. No Windows, ele usa [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803). O provedor é multiplataforma, mas ainda não há ferramentas de coleta e exibição de eventos para Linux ou macOS.

```csharp
logging.AddEventSourceLogger();
```

Uma boa maneira de coletar e exibir logs é usar o [utilitário PerfView](https://github.com/Microsoft/perfview). Há outras ferramentas para exibir os logs do ETW, mas o PerfView proporciona a melhor experiência para trabalhar com os eventos de ETW emitidos pelo ASP.NET Core.

Para configurar o PerfView para coletar eventos registrados por esse provedor, adicione a cadeia de caracteres `*Microsoft-Extensions-Logging` à lista **Provedores Adicionais**. (Não se esqueça do asterisco no início da cadeia de caracteres).

![Outros provedores de Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Provedor EventLog do Windows

O pacote de provedor [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envia a saída de log para o Log de Eventos do Windows.

```csharp
logging.AddEventLog();
```

As [sobrecargas de AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) permitem que você passe <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.

### <a name="tracesource-provider"></a>Provedor TraceSource

O pacote de provedor [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) usa as bibliotecas e provedores de <xref:System.Diagnostics.TraceSource>.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

As [sobrecargas de AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) permitem que você passe um comutador de fonte e um ouvinte de rastreamento.

Para usar esse provedor, o aplicativo deve ser executado no .NET Framework (em vez do .NET Core). O provedor pode rotear mensagens a uma variedade de [ouvintes](/dotnet/framework/debug-trace-profile/trace-listeners), como o <xref:System.Diagnostics.TextWriterTraceListener> usado no aplicativo de exemplo.

### <a name="azure-app-service-provider"></a>Provedor do Serviço de Aplicativo do Azure

O pacote de provedor [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) grava logs em arquivos de texto no sistema de arquivos de um aplicativo do Serviço de Aplicativo do Azure e no [armazenamento de blobs](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) em uma conta de Armazenamento do Azure.

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

O pacote do provedor não está incluído na estrutura compartilhada. Para usar o provedor, adicione o pacote do provedor ao projeto.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

O pacote de provedor não está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Se você estiver direcionando para o .NET Framework ou referenciando o metapacote `Microsoft.AspNetCore.App`, adicione o pacote do provedor ao projeto. 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Para definir as configurações do provedor, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> e <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, conforme mostrado no exemplo a seguir:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Para definir as configurações do provedor, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> e <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, conforme mostrado no exemplo a seguir:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Uma sobrecarga <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> permite passar <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>. O objeto settings pode substituir as configurações padrão, como o modelo de saída de registro em log, o nome do blob e o limite do tamanho do arquivo. (O *modelo Output* é um modelo de mensagem aplicado a todos os logs, além daquele fornecido com uma chamada ao método `ILogger`).

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

## <a name="third-party-logging-providers"></a>Provedores de log de terceiros

Estruturas de log de terceiros que funcionam com o ASP.NET Core:

* [elmah.io](https://elmah.io/) ([repositório GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repositório do GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([repositório GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([Repositório do GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4net](https://logging.apache.org/log4net/) ([repositório GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([repositório GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([repositório GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([repositório GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([repositório GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repositório Github](https://github.com/googleapis/google-cloud-dotnet))

Algumas estruturas de terceiros podem fazer o [log semântico, também conhecido como registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Usar uma estrutura de terceiros é semelhante ao uso de um dos provedores internos:

1. Adicione um pacote NuGet ao projeto.
1. Chame um método de extensão `ILoggerFactory` fornecido pela estrutura de log.

Para saber mais, consulte a documentação de cada provedor. Não há suporte para provedores de log de terceiros na Microsoft.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/logging/loggermessage>
