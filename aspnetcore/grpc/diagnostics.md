---
title: Registro em log e diagnóstico no gRPC no .NET
author: jamesnk
description: Saiba como coletar diagnósticos do seu aplicativo gRPC no .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 7194e91b40a08c4a7ee619b8f207900af2683aa1
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250730"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>Registro em log e diagnóstico no gRPC no .NET

Por [James Newton – King](https://twitter.com/jamesnk)

Este artigo fornece orientação para a coleta de diagnósticos do seu aplicativo gRPC para ajudar a solucionar problemas.

## <a name="grpc-services-logging"></a>log de serviços gRPCs

> [!WARNING]
> Os logs do lado do servidor podem conter informações confidenciais do seu aplicativo. **Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.

Como os serviços gRPCs são hospedados em ASP.NET Core, ele usa o sistema de registro em log de ASP.NET Core. Na configuração padrão, o gRPC registra muito pouca informação, mas isso pode ser configurado. Consulte a documentação em [log de ASP.NET Core](xref:fundamentals/logging/index#configuration) para obter detalhes sobre como configurar o log de ASP.NET Core.

gRPC adiciona logs na `Grpc` categoria. Para habilitar logs detalhados do gRPC, configure `Grpc` os prefixos `Debug` para o nível em seu arquivo *appSettings. JSON* adicionando os seguintes itens à `LogLevel` subseção em `Logging`:

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

Você também pode configurar isso em *Startup.cs* com `ConfigureLogging`:

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

Se você não estiver usando a configuração baseada em JSON, defina o seguinte valor de configuração em seu sistema de configuração:

* `Logging:LogLevel:Grpc` = `Debug`

Verifique a documentação do seu sistema de configuração para determinar como especificar valores de configuração aninhados. Por exemplo, ao usar variáveis de ambiente, `_` dois caracteres são usados em vez `:` do (por exemplo `Logging__LogLevel__Grpc`,).

É recomendável usar `Debug` o nível ao coletar diagnósticos mais detalhados para seu aplicativo. O `Trace` nível produz diagnósticos de nível muito baixo e raramente é necessário para diagnosticar problemas em seu aplicativo.

### <a name="sample-logging-output"></a>Exemplo de saída de registro em log

Aqui está um exemplo de saída do console no `Debug` nível de um serviço gRPC:

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

## <a name="access-server-side-logs"></a>Acessar logs do lado do servidor

A maneira como você acessa os logs do lado do servidor depende do ambiente no qual você está executando o.

### <a name="as-a-console-app"></a>Como um aplicativo de console

Se você estiver executando o em um aplicativo de console, o [agente de log do console](xref:fundamentals/logging/index#console-provider) deverá ser habilitado por padrão. os logs do gRPC serão exibidos no console do.

### <a name="other-environments"></a>Outros ambientes

Se o aplicativo for implantado em outro ambiente (por exemplo, Docker, kubernetes ou serviço do Windows) <xref:fundamentals/logging/index> , consulte para obter mais informações sobre como configurar provedores de log adequados para o ambiente.

## <a name="grpc-client-logging"></a>log de cliente do gRPC

> [!WARNING]
> Os logs do lado do cliente podem conter informações confidenciais do seu aplicativo. **Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.

Para obter logs do cliente .net, você pode definir a `GrpcChannelOptions.LoggerFactory` Propriedade quando o canal do cliente é criado. Se você estiver chamando um serviço gRPC de um aplicativo ASP.NET Core, a fábrica do agente poderá ser resolvida da injeção de dependência (DI):

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

Uma maneira alternativa de habilitar o log do cliente é usar a [fábrica do cliente do gRPC](xref:grpc/clientfactory) para criar o cliente. Um cliente gRPC registrado com a fábrica do cliente e resolvido a partir de DI usará automaticamente o log configurado do aplicativo.

Se seu aplicativo não estiver usando di, você poderá criar uma `ILoggerFactory` nova instância com [LoggerFactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*). Para acessar esse método, adicione o pacote [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) ao seu aplicativo.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

### <a name="sample-logging-output"></a>Exemplo de saída de registro em log

Aqui está um exemplo de saída do console no `Debug` nível de um cliente gRPC:

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

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
