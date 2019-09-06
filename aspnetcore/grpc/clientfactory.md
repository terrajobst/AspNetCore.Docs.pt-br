---
title: integração do gRPC Client Factory no .NET Core
author: jamesnk
description: Saiba como criar clientes do gRPC usando a fábrica de cliente.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/clientfactory
ms.openlocfilehash: a52fd397a7ed3e327938b0847af7f4e6a4a79400
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310642"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>integração do gRPC Client Factory no .NET Core

a integração do `HttpClientFactory` gRPC com o oferece uma maneira centralizada de criar clientes do gRPC. Ele pode ser usado como uma alternativa para [configurar instâncias de cliente gRPC](xref:grpc/client)autônomas. A integração de fábrica está disponível no pacote NuGet do [.net. ClientFactory do Grpc](https://www.nuget.org/packages/Grpc.Net.ClientFactory) .

A fábrica oferece os seguintes benefícios:

* Fornece um local central para configurar instâncias de cliente gRPC lógicas
* Gerencia o tempo de vida do subjacente`HttpClientMessageHandler`
* Propagação automática de prazo e cancelamento em um serviço de ASP.NET Core gRPC

## <a name="register-grpc-clients"></a>Registrar clientes gRPC

Para registrar um cliente gRPC, o método `AddGrpcClient` de extensão genérico pode ser usado `Startup.ConfigureServices`no, especificando a classe de cliente tipada gRPC e o endereço de serviço:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("http://localhost:5001");
});
```

O tipo de cliente gRPC é registrado como transitório com injeção de dependência (DI). Agora, o cliente pode ser injetado e consumido diretamente em tipos criados por DI. ASP.NET Core controladores MVC, hubs de sinalização e serviços gRPCs são locais onde os clientes gRPC podem ser injetados automaticamente:

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a>Configurar o HttpClient

`HttpClientFactory`cria o `HttpClient` usado pelo cliente gRPC. Os `HttpClientFactory` métodos padrão podem ser usados para adicionar middleware de solicitação de saída ou para configurar o `HttpClientHandler` subjacente do `HttpClient`:

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.BaseAddress = new Uri("http://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

Para obter mais informações, consulte [fazer solicitações HTTP usando IHttpClientFactory](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Configurar canal e interceptores

os métodos específicos do gRPC estão disponíveis para:

* Configure um canal subjacente do cliente gRPC.
* Adicione `Interceptor` instâncias que o cliente usará ao fazer chamadas gRPC.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("http://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a>Data limite e propagação de cancelamento

Os clientes gRPC criados pela fábrica em um serviço gRPC podem ser configurados com `EnableCallContextPropagation()` o para propagar automaticamente o token de prazo e cancelamento para chamadas filhas. O `EnableCallContextPropagation()` método de extensão está disponível no pacote NuGet [Grpc. AspNetCore. Server. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) .

A propagação de contexto de chamada funciona lendo o data limite e o token de cancelamento do contexto de solicitação gRPC atual e propagando-os automaticamente para chamadas de saída feitas pelo cliente gRPC. A propagação de contexto de chamada é uma maneira excelente de garantir que cenários gRPC complexos e aninhados sempre propaguem o prazo e o cancelamento.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("http://localhost:5001");
    })
    .EnableCallContextPropagation();
```

Para obter mais informações sobre prazos e cancelamento de RPC, consulte [ciclo de vida RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
