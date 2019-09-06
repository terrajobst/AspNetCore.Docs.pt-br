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
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="7e20b-103">integração do gRPC Client Factory no .NET Core</span><span class="sxs-lookup"><span data-stu-id="7e20b-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="7e20b-104">a integração do `HttpClientFactory` gRPC com o oferece uma maneira centralizada de criar clientes do gRPC.</span><span class="sxs-lookup"><span data-stu-id="7e20b-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="7e20b-105">Ele pode ser usado como uma alternativa para [configurar instâncias de cliente gRPC](xref:grpc/client)autônomas.</span><span class="sxs-lookup"><span data-stu-id="7e20b-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="7e20b-106">A integração de fábrica está disponível no pacote NuGet do [.net. ClientFactory do Grpc](https://www.nuget.org/packages/Grpc.Net.ClientFactory) .</span><span class="sxs-lookup"><span data-stu-id="7e20b-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="7e20b-107">A fábrica oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="7e20b-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="7e20b-108">Fornece um local central para configurar instâncias de cliente gRPC lógicas</span><span class="sxs-lookup"><span data-stu-id="7e20b-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="7e20b-109">Gerencia o tempo de vida do subjacente`HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="7e20b-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="7e20b-110">Propagação automática de prazo e cancelamento em um serviço de ASP.NET Core gRPC</span><span class="sxs-lookup"><span data-stu-id="7e20b-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="7e20b-111">Registrar clientes gRPC</span><span class="sxs-lookup"><span data-stu-id="7e20b-111">Register gRPC clients</span></span>

<span data-ttu-id="7e20b-112">Para registrar um cliente gRPC, o método `AddGrpcClient` de extensão genérico pode ser usado `Startup.ConfigureServices`no, especificando a classe de cliente tipada gRPC e o endereço de serviço:</span><span class="sxs-lookup"><span data-stu-id="7e20b-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("http://localhost:5001");
});
```

<span data-ttu-id="7e20b-113">O tipo de cliente gRPC é registrado como transitório com injeção de dependência (DI).</span><span class="sxs-lookup"><span data-stu-id="7e20b-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="7e20b-114">Agora, o cliente pode ser injetado e consumido diretamente em tipos criados por DI.</span><span class="sxs-lookup"><span data-stu-id="7e20b-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="7e20b-115">ASP.NET Core controladores MVC, hubs de sinalização e serviços gRPCs são locais onde os clientes gRPC podem ser injetados automaticamente:</span><span class="sxs-lookup"><span data-stu-id="7e20b-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

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

## <a name="configure-httpclient"></a><span data-ttu-id="7e20b-116">Configurar o HttpClient</span><span class="sxs-lookup"><span data-stu-id="7e20b-116">Configure HttpClient</span></span>

<span data-ttu-id="7e20b-117">`HttpClientFactory`cria o `HttpClient` usado pelo cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="7e20b-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="7e20b-118">Os `HttpClientFactory` métodos padrão podem ser usados para adicionar middleware de solicitação de saída ou para configurar o `HttpClientHandler` subjacente do `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="7e20b-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

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

<span data-ttu-id="7e20b-119">Para obter mais informações, consulte [fazer solicitações HTTP usando IHttpClientFactory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="7e20b-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="7e20b-120">Configurar canal e interceptores</span><span class="sxs-lookup"><span data-stu-id="7e20b-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="7e20b-121">os métodos específicos do gRPC estão disponíveis para:</span><span class="sxs-lookup"><span data-stu-id="7e20b-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="7e20b-122">Configure um canal subjacente do cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="7e20b-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="7e20b-123">Adicione `Interceptor` instâncias que o cliente usará ao fazer chamadas gRPC.</span><span class="sxs-lookup"><span data-stu-id="7e20b-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

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

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="7e20b-124">Data limite e propagação de cancelamento</span><span class="sxs-lookup"><span data-stu-id="7e20b-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="7e20b-125">Os clientes gRPC criados pela fábrica em um serviço gRPC podem ser configurados com `EnableCallContextPropagation()` o para propagar automaticamente o token de prazo e cancelamento para chamadas filhas.</span><span class="sxs-lookup"><span data-stu-id="7e20b-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="7e20b-126">O `EnableCallContextPropagation()` método de extensão está disponível no pacote NuGet [Grpc. AspNetCore. Server. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) .</span><span class="sxs-lookup"><span data-stu-id="7e20b-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="7e20b-127">A propagação de contexto de chamada funciona lendo o data limite e o token de cancelamento do contexto de solicitação gRPC atual e propagando-os automaticamente para chamadas de saída feitas pelo cliente gRPC.</span><span class="sxs-lookup"><span data-stu-id="7e20b-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="7e20b-128">A propagação de contexto de chamada é uma maneira excelente de garantir que cenários gRPC complexos e aninhados sempre propaguem o prazo e o cancelamento.</span><span class="sxs-lookup"><span data-stu-id="7e20b-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("http://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="7e20b-129">Para obter mais informações sobre prazos e cancelamento de RPC, consulte [ciclo de vida RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="7e20b-129">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7e20b-130">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7e20b-130">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
