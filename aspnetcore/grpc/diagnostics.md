---
title: Registro em log e diagnóstico no gRPC no .NET
author: jamesnk
description: Saiba como coletar diagnósticos do seu aplicativo gRPC no .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 17607b734e6d777de9516aa14e81c97f87b61023
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667338"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>Registro em log e diagnóstico no gRPC no .NET

Por [James Newton – King](https://twitter.com/jamesnk)

Este artigo fornece diretrizes para coletar diagnósticos de um aplicativo gRPC para ajudar a solucionar problemas. Os tópicos abordados incluem:

* Logs estruturados em **log** gravados no [log do .NET Core](xref:fundamentals/logging/index). <xref:Microsoft.Extensions.Logging.ILogger> é usado pelas estruturas de aplicativo para gravar logs e por usuários para seus próprios registros em um aplicativo.
* **Rastreamento** -eventos relacionados a uma operação escrita usando `DiaganosticSource` e `Activity`. Os rastreamentos da fonte de diagnóstico são comumente usados para coletar telemetria de aplicativo por bibliotecas como [Application insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) e [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).
* **Métricas** – representação de medidas de dados em intervalos de tempo, por exemplo, solicitações por segundo. As métricas são emitidas usando `EventCounter` e podem ser observadas usando [a ferramenta de linha de comando dotnet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) ou com [Application insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).

## <a name="logging"></a>Registro em log

os serviços gRPC e o cliente gRPC gravam logs usando o [log do .NET Core](xref:fundamentals/logging/index). Os logs são um bom lugar para começar quando você precisa depurar um comportamento inesperado em seus aplicativos.

### <a name="grpc-services-logging"></a>log de serviços gRPCs

> [!WARNING]
> Os logs do lado do servidor podem conter informações confidenciais do seu aplicativo. **Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.

Como os serviços gRPCs são hospedados em ASP.NET Core, ele usa o sistema de registro em log de ASP.NET Core. Na configuração padrão, o gRPC registra muito pouca informação, mas isso pode ser configurado. Consulte a documentação em [log de ASP.NET Core](xref:fundamentals/logging/index#configuration) para obter detalhes sobre como configurar o log de ASP.NET Core.

gRPC adiciona logs na categoria `Grpc`. Para habilitar logs detalhados do gRPC, configure os prefixos `Grpc` para o nível de `Debug` no arquivo *appSettings. JSON* adicionando os seguintes itens à subseção `LogLevel` no `Logging`:

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

Você também pode configurar isso em *Startup.cs* com `ConfigureLogging`:

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

Se você não estiver usando a configuração baseada em JSON, defina o seguinte valor de configuração em seu sistema de configuração:

* `Logging:LogLevel:Grpc` = `Debug`

Verifique a documentação do seu sistema de configuração para determinar como especificar valores de configuração aninhados. Por exemplo, ao usar variáveis de ambiente, são usados dois caracteres `_` em vez da `:` (por exemplo, `Logging__LogLevel__Grpc`).

É recomendável usar o nível de `Debug` ao coletar diagnósticos mais detalhados para seu aplicativo. O nível de `Trace` produz diagnósticos de nível muito baixo e raramente é necessário para diagnosticar problemas em seu aplicativo.

#### <a name="sample-logging-output"></a>Exemplo de saída de registro em log

Aqui está um exemplo de saída do console no nível de `Debug` de um serviço gRPC:

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a>Acessar logs do lado do servidor

A maneira como você acessa os logs do lado do servidor depende do ambiente no qual você está executando o.

#### <a name="as-a-console-app"></a>Como um aplicativo de console

Se você estiver executando o em um aplicativo de console, o [agente de log do console](xref:fundamentals/logging/index#console-provider) deverá ser habilitado por padrão. os logs do gRPC serão exibidos no console do.

#### <a name="other-environments"></a>Outros ambientes

Se o aplicativo for implantado em outro ambiente (por exemplo, Docker, kubernetes ou serviço do Windows), consulte <xref:fundamentals/logging/index> para obter mais informações sobre como configurar provedores de log adequados para o ambiente.

### <a name="grpc-client-logging"></a>log de cliente do gRPC

> [!WARNING]
> Os logs do lado do cliente podem conter informações confidenciais do seu aplicativo. **Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.

Para obter logs do cliente .NET, você pode definir a propriedade `GrpcChannelOptions.LoggerFactory` quando o canal do cliente é criado. Se você estiver chamando um serviço gRPC de um aplicativo ASP.NET Core, a fábrica do agente poderá ser resolvida da injeção de dependência (DI):

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

Uma maneira alternativa de habilitar o log do cliente é usar a [fábrica do cliente do gRPC](xref:grpc/clientfactory) para criar o cliente. Um cliente gRPC registrado com a fábrica do cliente e resolvido a partir de DI usará automaticamente o log configurado do aplicativo.

Se seu aplicativo não estiver usando DI, você poderá criar uma nova instância de `ILoggerFactory` com [LoggerFactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*). Para acessar esse método, adicione o pacote [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) ao seu aplicativo.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>escopos de log do cliente gRPC

O cliente gRPC adiciona um [escopo de log](https://docs.microsoft.com/aspnet/core/fundamentals/logginglog-scopes) aos logs feitos durante uma chamada gRPC. O escopo tem metadados relacionados à chamada gRPC:

* **GrpcMethodType** -o tipo de método gRPC. Os valores possíveis são nomes de `Grpc.Core.MethodType` enum, por exemplo, unário
* **GrpcUri** -o URI relativo do método gRPC, por exemplo,/Greet. Greeter/SayHellos

#### <a name="sample-logging-output"></a>Exemplo de saída de registro em log

Aqui está um exemplo de saída do console no nível de `Debug` de um cliente gRPC:

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a>Rastreamento

os serviços gRPC e o cliente gRPC fornecem informações sobre chamadas gRPC usando a [diagnóstico](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) e a [atividade](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).

* O .NET gRPC usa uma atividade para representar uma chamada gRPC.
* Os eventos de rastreamento são gravados na fonte de diagnóstico no início e na parada da atividade de chamada gRPC.
* O rastreamento não captura informações sobre quando as mensagens são enviadas durante o tempo de vida das chamadas de streaming gRPC.

### <a name="grpc-service-tracing"></a>rastreamento de serviço gRPC

os serviços gRPCs são hospedados em ASP.NET Core que relata eventos sobre solicitações HTTP de entrada. os metadados específicos do gRPC são adicionados ao diagnóstico de solicitação HTTP existente que o ASP.NET Core fornece.

* O nome de origem do diagnóstico é `Microsoft.AspNetCore`.
* O nome da atividade é `Microsoft.AspNetCore.Hosting.HttpRequestIn`.
  * O nome do método gRPC invocado pela chamada gRPC é adicionado como uma marca com o nome `grpc.method`.
  * O código de status da chamada gRPC quando ela é concluída é adicionado como uma marca com o nome `grpc.status_code`.

### <a name="grpc-client-tracing"></a>rastreamento de cliente gRPC

O cliente .NET gRPC usa `HttpClient` para fazer chamadas gRPC. Embora `HttpClient` grave eventos de diagnóstico, o cliente .NET gRPC fornece uma fonte de diagnóstico, atividade e eventos personalizados para que as informações completas sobre uma chamada gRPC possam ser coletadas.

* O nome de origem do diagnóstico é `Grpc.Net.Client`.
* O nome da atividade é `Grpc.Net.Client.GrpcOut`.
  * O nome do método gRPC invocado pela chamada gRPC é adicionado como uma marca com o nome `grpc.method`.
  * O código de status da chamada gRPC quando ela é concluída é adicionado como uma marca com o nome `grpc.status_code`.

### <a name="collecting-tracing"></a>Coletando rastreamento

A maneira mais fácil de usar o `DiagnosticSource` é configurar uma biblioteca de telemetria, como [Application insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) ou [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) em seu aplicativo. A biblioteca processará informações sobre chamadas gRPC em outras telemetrias do aplicativo.

O rastreamento pode ser exibido em um serviço gerenciado como Application Insights ou você pode optar por executar seu próprio sistema de rastreamento distribuído. O OpenTelemetry dá suporte à exportação de dados de rastreamento para [Jaeger](https://www.jaegertracing.io/) e [Zipkin](https://zipkin.io/).

`DiagnosticSource` pode consumir eventos de rastreamento no código usando `DiagnosticListener`. Para obter informações sobre como escutar uma fonte de diagnóstico com código, consulte o [Guia do usuário do diagnosticm](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).

> [!NOTE]
> As bibliotecas de telemetria não capturam a telemetria `Grpc.Net.Client.GrpcOut` específica do gRPC no momento. Trabalho para melhorar as bibliotecas de telemetria capturando esse rastreamento está em andamento.

## <a name="metrics"></a>Métricas

Métricas é uma representação de medidas de dados em intervalos de tempo, por exemplo, solicitações por segundo. Os dados de métricas permitem a observação do estado de um aplicativo em um alto nível. As métricas do .NET gRPC são emitidas usando `EventCounter`.

### <a name="grpc-service-metrics"></a>métricas do serviço gRPC

as métricas do servidor gRPC são relatadas na origem do evento `Grpc.AspNetCore.Server`.

| {1&gt;Nome&lt;1}                      | Descrição                   |
| --------------------------|-------------------------------|
| `total-calls`             | Total de Chamadas                   |
| `current-calls`           | Chamadas atuais                 |
| `calls-failed`            | Total de chamadas com falha            |
| `calls-deadline-exceeded` | Prazo total de chamadas excedido |
| `messages-sent`           | Total de Mensagens Enviadas           |
| `messages-received`       | Total de mensagens recebidas       |
| `calls-unimplemented`     | Total de chamadas não implementadas     |

ASP.NET Core também fornece suas próprias métricas em `Microsoft.AspNetCore.Hosting` origem do evento.

### <a name="grpc-client-metrics"></a>métricas do cliente gRPC

as métricas do cliente gRPC são relatadas na origem do evento `Grpc.Net.Client`.

| {1&gt;Nome&lt;1}                      | Descrição                   |
| --------------------------|-------------------------------|
| `total-calls`             | Total de Chamadas                   |
| `current-calls`           | Chamadas atuais                 |
| `calls-failed`            | Total de chamadas com falha            |
| `calls-deadline-exceeded` | Prazo total de chamadas excedido |
| `messages-sent`           | Total de Mensagens Enviadas           |
| `messages-received`       | Total de mensagens recebidas       |

### <a name="observe-metrics"></a>Observar métricas

[dotnet-os contadores](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) são uma ferramenta de monitoramento de desempenho para monitoramento de integridade ad hoc e investigação de desempenho de primeiro nível. Monitore um aplicativo .NET com `Grpc.AspNetCore.Server` ou `Grpc.Net.Client` como o nome do provedor.

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

Outra maneira de observar as métricas do gRPC é capturar dados do contador usando [o pacote Microsoft. ApplicationInsights. EventCounterCollector](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)da Application insights. Depois da instalação, o Application Insights coleta contadores comuns do .NET em tempo de execução. os contadores de gRPC não são coletados por padrão, mas o app insights pode ser [personalizado para incluir contadores adicionais](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).

Especifique os contadores de gRPC para o Application insights coletar em *Startup.cs*:

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
