---
title: Migrando serviços gRPCs do C-Core para ASP.NET Core
author: juntaoluo
description: Saiba como mover um aplicativo gRPC baseado em C-Core existente para ser executado no topo da pilha ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/migration
ms.openlocfilehash: 39aa711a1a47cf11ec5b08903b4130c7caa1501c
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022298"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="f9178-103">Migrando serviços gRPCs do C-Core para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f9178-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="f9178-104">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="f9178-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="f9178-105">Devido à implementação da pilha subjacente, nem todos os recursos funcionam da mesma maneira entre os aplicativos [gRPC baseados em C-Core](https://grpc.io/blog/grpc-stacks) e os aplicativos baseados em ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9178-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="f9178-106">Este documento destaca as principais diferenças de migração entre as duas pilhas.</span><span class="sxs-lookup"><span data-stu-id="f9178-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="f9178-107">tempo de vida de implementação do serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="f9178-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="f9178-108">Na pilha de ASP.NET Core, os serviços gRPCs, por padrão, são criados com um [tempo de vida no escopo](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="f9178-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="f9178-109">Por outro lado, o gRPC C-Core é associado por padrão a um serviço com um [tempo de vida singleton](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="f9178-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="f9178-110">Um tempo de vida no escopo permite que a implementação do serviço resolva outros serviços com tempos de vida de escopo.</span><span class="sxs-lookup"><span data-stu-id="f9178-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="f9178-111">Por exemplo, um tempo de vida com escopo pode `DbContext` ser resolvido do contêiner di por meio de injeção de construtor.</span><span class="sxs-lookup"><span data-stu-id="f9178-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="f9178-112">Usando tempo de vida no escopo:</span><span class="sxs-lookup"><span data-stu-id="f9178-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="f9178-113">Uma nova instância da implementação do serviço é construída para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="f9178-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="f9178-114">Não é possível compartilhar o estado entre solicitações por meio de membros de instância no tipo de implementação.</span><span class="sxs-lookup"><span data-stu-id="f9178-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="f9178-115">A expectativa é armazenar Estados compartilhados em um serviço singleton no contêiner DI.</span><span class="sxs-lookup"><span data-stu-id="f9178-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="f9178-116">Os Estados compartilhados armazenados são resolvidos no construtor da implementação do serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="f9178-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="f9178-117">Para obter mais informações sobre tempos de vida de <xref:fundamentals/dependency-injection#service-lifetimes>serviço, consulte.</span><span class="sxs-lookup"><span data-stu-id="f9178-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="f9178-118">Adicionar um serviço singleton</span><span class="sxs-lookup"><span data-stu-id="f9178-118">Add a singleton service</span></span>

<span data-ttu-id="f9178-119">Para facilitar a transição de uma implementação gRPC C-Core para ASP.NET Core, é possível alterar o tempo de vida do serviço da implementação do serviço do escopo para singleton.</span><span class="sxs-lookup"><span data-stu-id="f9178-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="f9178-120">Isso envolve a adição de uma instância da implementação do serviço ao contêiner DI:</span><span class="sxs-lookup"><span data-stu-id="f9178-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="f9178-121">No entanto, uma implementação de serviço com um tempo de vida singleton não é mais capaz de resolver serviços com escopo por meio de injeção de construtor.</span><span class="sxs-lookup"><span data-stu-id="f9178-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="f9178-122">Configurar opções de serviços gRPCs</span><span class="sxs-lookup"><span data-stu-id="f9178-122">Configure gRPC services options</span></span>

<span data-ttu-id="f9178-123">Em aplicativos baseados em C-Core, configurações `grpc.max_receive_message_length` como e `grpc.max_send_message_length` são configuradas `ChannelOption` com ao [construir a instância de servidor](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span><span class="sxs-lookup"><span data-stu-id="f9178-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="f9178-124">No ASP.NET Core, gRPC fornece a configuração por `GrpcServiceOptions` meio do tipo.</span><span class="sxs-lookup"><span data-stu-id="f9178-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="f9178-125">Por exemplo, um serviço gRPC o tamanho máximo da mensagem de entrada pode ser configurado `AddGrpc`por meio de:</span><span class="sxs-lookup"><span data-stu-id="f9178-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 16384; // 16 MB
    });
}
```

<span data-ttu-id="f9178-126">Para obter mais informações sobre a configuração <xref:grpc/configuration>, consulte.</span><span class="sxs-lookup"><span data-stu-id="f9178-126">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="f9178-127">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="f9178-127">Logging</span></span>

<span data-ttu-id="f9178-128">Aplicativos baseados em núcleo baseado em C usam o `GrpcEnvironment` para [Configurar o agente](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) para fins de depuração.</span><span class="sxs-lookup"><span data-stu-id="f9178-128">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="f9178-129">A pilha de ASP.NET Core fornece essa funcionalidade por meio da [API de log](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="f9178-129">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="f9178-130">Por exemplo, um agente de log pode ser adicionado ao serviço gRPC por meio de injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="f9178-130">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="f9178-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="f9178-131">HTTPS</span></span>

<span data-ttu-id="f9178-132">Aplicativos baseados em C-Core configuram HTTPS por meio da [Propriedade Server. Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span><span class="sxs-lookup"><span data-stu-id="f9178-132">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="f9178-133">Um conceito semelhante é usado para configurar servidores no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9178-133">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="f9178-134">Por exemplo, Kestrel usa a [configuração de ponto de extremidade](xref:fundamentals/servers/kestrel#endpoint-configuration) para essa funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="f9178-134">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="interceptors-and-middleware"></a><span data-ttu-id="f9178-135">Interceptores e middleware</span><span class="sxs-lookup"><span data-stu-id="f9178-135">Interceptors and Middleware</span></span>

<span data-ttu-id="f9178-136">ASP.NET Core [middleware](xref:fundamentals/middleware/index) oferece funcionalidades semelhantes em comparação com os interceptores em aplicativos gRPC baseados em C-Core.</span><span class="sxs-lookup"><span data-stu-id="f9178-136">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="f9178-137">Middleware e interceptores são conceitualmente os mesmos que ambos são usados para construir um pipeline que manipula uma solicitação gRPC.</span><span class="sxs-lookup"><span data-stu-id="f9178-137">Middleware and interceptors are conceptually the same as both are used to construct a pipeline that handles a gRPC request.</span></span> <span data-ttu-id="f9178-138">Ambos permitem que o trabalho seja executado antes ou depois do próximo componente no pipeline.</span><span class="sxs-lookup"><span data-stu-id="f9178-138">They both allow work to be performed before or after the next component in the pipeline.</span></span> <span data-ttu-id="f9178-139">No entanto, ASP.NET Core middleware opera nas mensagens HTTP/2 subjacentes, enquanto os interceptores operam na camada gRPC de abstração usando o [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span><span class="sxs-lookup"><span data-stu-id="f9178-139">However, ASP.NET Core middleware operates on the underlying HTTP/2 messages, while interceptors operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9178-140">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f9178-140">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
