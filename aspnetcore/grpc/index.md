---
title: Introdução ao gRPC no .NET Core
author: juntaoluo
description: Saiba mais sobre os serviços de gRPC com a pilha do ASP.NET Core e o servidor Kestrel.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
uid: grpc/index
ms.openlocfilehash: d97eea1da28424680a3cfa38102637b1e20ff661
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77446951"
---
# <a name="introduction-to-grpc-on-net-core"></a><span data-ttu-id="92cfb-103">Introdução ao gRPC no .NET Core</span><span class="sxs-lookup"><span data-stu-id="92cfb-103">Introduction to gRPC on .NET Core</span></span>

<span data-ttu-id="92cfb-104">Por [John Luo](https://github.com/juntaoluo) e [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="92cfb-104">By [John Luo](https://github.com/juntaoluo) and [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="92cfb-105">[gRPC](https://grpc.io/docs/guides/) é uma estrutura de RPC (Chamada de Procedimento Remoto) de linguagem independente de alto desempenho.</span><span class="sxs-lookup"><span data-stu-id="92cfb-105">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span>

<span data-ttu-id="92cfb-106">Os principais benefícios de gRPC são:</span><span class="sxs-lookup"><span data-stu-id="92cfb-106">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="92cfb-107">Estrutura RPC leve, de alto desempenho e moderna.</span><span class="sxs-lookup"><span data-stu-id="92cfb-107">Modern, high-performance, lightweight RPC framework.</span></span>
* <span data-ttu-id="92cfb-108">Desenvolvimento da API de primeiro contrato, usando buffers de protocolo, por padrão, permitindo implementações independente de linguagem.</span><span class="sxs-lookup"><span data-stu-id="92cfb-108">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="92cfb-109">As ferramentas disponíveis para várias linguagens gerarem clientes e servidores fortemente tipados.</span><span class="sxs-lookup"><span data-stu-id="92cfb-109">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="92cfb-110">Dá suporte ao cliente, servidor e chamadas bi-direcionais de streaming.</span><span class="sxs-lookup"><span data-stu-id="92cfb-110">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="92cfb-111">Uso de rede reduzida com a serialização binária Protobuf.</span><span class="sxs-lookup"><span data-stu-id="92cfb-111">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="92cfb-112">Esses benefícios tornam o gRPC ideal para:</span><span class="sxs-lookup"><span data-stu-id="92cfb-112">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="92cfb-113">Microsserviços leves em que a eficiência é crítica.</span><span class="sxs-lookup"><span data-stu-id="92cfb-113">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="92cfb-114">Sistemas poliglotas nos quais múltiplas linguagens são necessárias para o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="92cfb-114">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="92cfb-115">Serviços ponto a ponto em tempo real que precisam lidar com solicitações ou respostas de streaming.</span><span class="sxs-lookup"><span data-stu-id="92cfb-115">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="92cfb-116">C#Suporte de ferramentas para arquivos. proto</span><span class="sxs-lookup"><span data-stu-id="92cfb-116">C# Tooling support for .proto files</span></span>

<span data-ttu-id="92cfb-117">o gRPC usa uma abordagem de primeiro contrato para o desenvolvimento de API.</span><span class="sxs-lookup"><span data-stu-id="92cfb-117">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="92cfb-118">Os serviços e as mensagens são definidos nos arquivos *\*. proto* :</span><span class="sxs-lookup"><span data-stu-id="92cfb-118">Services and messages are defined in *\*.proto* files:</span></span>

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="92cfb-119">Os tipos .NET para serviços, clientes e mensagens são gerados automaticamente incluindo *\*arquivos. proto* em um projeto:</span><span class="sxs-lookup"><span data-stu-id="92cfb-119">.NET types for services, clients and messages are automatically generated by including *\*.proto* files in a project:</span></span>

* <span data-ttu-id="92cfb-120">Adicione uma referência de pacote ao pacote [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="92cfb-120">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
* <span data-ttu-id="92cfb-121">Adicione arquivos *\*. proto* ao grupo de itens de `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="92cfb-121">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

<span data-ttu-id="92cfb-122">Para obter mais informações sobre o suporte de ferramentas do gRPC, consulte <xref:grpc/basics>.</span><span class="sxs-lookup"><span data-stu-id="92cfb-122">For more information on gRPC tooling support, see <xref:grpc/basics>.</span></span>

## <a name="grpc-services-on-aspnet-core"></a><span data-ttu-id="92cfb-123">serviços gRPCs no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="92cfb-123">gRPC services on ASP.NET Core</span></span>

<span data-ttu-id="92cfb-124">os serviços gRPCs podem ser hospedados em ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92cfb-124">gRPC services can be hosted on ASP.NET Core.</span></span> <span data-ttu-id="92cfb-125">Os serviços têm integração total com recursos populares de ASP.NET Core, como registro em log, injeção de dependência (DI), autenticação e autorização.</span><span class="sxs-lookup"><span data-stu-id="92cfb-125">Services have full integration with popular ASP.NET Core features such as logging, dependency injection (DI), authentication and authorization.</span></span>

<span data-ttu-id="92cfb-126">O modelo de projeto de serviço gRPC fornece um serviço inicial:</span><span class="sxs-lookup"><span data-stu-id="92cfb-126">The gRPC service project template provides a starter service:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

<span data-ttu-id="92cfb-127">`GreeterService` herda do tipo `GreeterBase`, que é gerado a partir do serviço de `Greeter` no arquivo *\*. proto* .</span><span class="sxs-lookup"><span data-stu-id="92cfb-127">`GreeterService` inherits from the `GreeterBase` type, which is generated from the `Greeter` service in the *\*.proto* file.</span></span> <span data-ttu-id="92cfb-128">O serviço torna-se acessível para clientes no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="92cfb-128">The service is made accessible to clients in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="92cfb-129">Para saber mais sobre os serviços gRPCs no ASP.NET Core, consulte <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="92cfb-129">To learn more about gRPC services on ASP.NET Core, see <xref:grpc/aspnetcore>.</span></span>

## <a name="call-grpc-services-with-a-net-client"></a><span data-ttu-id="92cfb-130">Chamar serviços gRPCs com um cliente .NET</span><span class="sxs-lookup"><span data-stu-id="92cfb-130">Call gRPC services with a .NET client</span></span>

<span data-ttu-id="92cfb-131">Os clientes gRPC são tipos de cliente concretos que são [gerados a partir de arquivos *\*. proto* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="92cfb-131">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="92cfb-132">O cliente gRPC concreto tem métodos que se convertem para o serviço gRPC no arquivo *\*. proto* .</span><span class="sxs-lookup"><span data-stu-id="92cfb-132">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

<span data-ttu-id="92cfb-133">Um cliente gRPC é criado usando um canal, que representa uma conexão de vida longa para um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="92cfb-133">A gRPC client is created using a channel, which represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="92cfb-134">Um canal pode ser criado usando `GrpcChannel.ForAddress`.</span><span class="sxs-lookup"><span data-stu-id="92cfb-134">A channel can be created using `GrpcChannel.ForAddress`.</span></span>

<span data-ttu-id="92cfb-135">Para obter mais informações sobre como criar clientes e chamar métodos de serviço diferentes, consulte <xref:grpc/client>.</span><span class="sxs-lookup"><span data-stu-id="92cfb-135">For more information on creating clients, and calling different service methods, see <xref:grpc/client>.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="92cfb-136">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="92cfb-136">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
