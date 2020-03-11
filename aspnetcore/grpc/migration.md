---
title: Migrando serviços gRPCs do C-Core para ASP.NET Core
author: juntaoluo
description: Saiba como mover um aplicativo gRPC baseado em C-Core existente para ser executado no topo da pilha ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 451171a041f7bbb3711babd73d2fa2e245aadd28
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664132"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="439a5-103">Migrando serviços gRPCs do C-Core para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="439a5-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="439a5-104">Por [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="439a5-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="439a5-105">Devido à implementação da pilha subjacente, nem todos os recursos funcionam da mesma maneira entre os aplicativos [gRPC baseados em C-Core](https://grpc.io/blog/grpc-stacks) e os aplicativos baseados em ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="439a5-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="439a5-106">Este documento destaca as principais diferenças de migração entre as duas pilhas.</span><span class="sxs-lookup"><span data-stu-id="439a5-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="439a5-107">tempo de vida de implementação do serviço gRPC</span><span class="sxs-lookup"><span data-stu-id="439a5-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="439a5-108">Na pilha de ASP.NET Core, os serviços gRPCs, por padrão, são criados com um [tempo de vida no escopo](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="439a5-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="439a5-109">Por outro lado, o gRPC C-Core é associado por padrão a um serviço com um [tempo de vida singleton](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="439a5-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="439a5-110">Um tempo de vida no escopo permite que a implementação do serviço resolva outros serviços com tempos de vida de escopo.</span><span class="sxs-lookup"><span data-stu-id="439a5-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="439a5-111">Por exemplo, uma vida útil com escopo também pode resolver `DbContext` do contêiner DI por meio de injeção de construtor.</span><span class="sxs-lookup"><span data-stu-id="439a5-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="439a5-112">Usando tempo de vida no escopo:</span><span class="sxs-lookup"><span data-stu-id="439a5-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="439a5-113">Uma nova instância da implementação do serviço é construída para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="439a5-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="439a5-114">Não é possível compartilhar o estado entre solicitações por meio de membros de instância no tipo de implementação.</span><span class="sxs-lookup"><span data-stu-id="439a5-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="439a5-115">A expectativa é armazenar Estados compartilhados em um serviço singleton no contêiner DI.</span><span class="sxs-lookup"><span data-stu-id="439a5-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="439a5-116">Os Estados compartilhados armazenados são resolvidos no construtor da implementação do serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="439a5-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="439a5-117">Para obter mais informações sobre tempos de vida de serviço, consulte <xref:fundamentals/dependency-injection#service-lifetimes>.</span><span class="sxs-lookup"><span data-stu-id="439a5-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="439a5-118">Adicionar um serviço singleton</span><span class="sxs-lookup"><span data-stu-id="439a5-118">Add a singleton service</span></span>

<span data-ttu-id="439a5-119">Para facilitar a transição de uma implementação gRPC C-Core para ASP.NET Core, é possível alterar o tempo de vida do serviço da implementação do serviço do escopo para singleton.</span><span class="sxs-lookup"><span data-stu-id="439a5-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="439a5-120">Isso envolve a adição de uma instância da implementação do serviço ao contêiner DI:</span><span class="sxs-lookup"><span data-stu-id="439a5-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="439a5-121">No entanto, uma implementação de serviço com um tempo de vida singleton não é mais capaz de resolver serviços com escopo por meio de injeção de construtor.</span><span class="sxs-lookup"><span data-stu-id="439a5-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="439a5-122">Configurar opções de serviços gRPCs</span><span class="sxs-lookup"><span data-stu-id="439a5-122">Configure gRPC services options</span></span>

<span data-ttu-id="439a5-123">Em aplicativos baseados em C-Core, as configurações como `grpc.max_receive_message_length` e `grpc.max_send_message_length` são configuradas com `ChannelOption` ao [construir a instância do servidor](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span><span class="sxs-lookup"><span data-stu-id="439a5-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="439a5-124">No ASP.NET Core, gRPC fornece a configuração por meio do tipo de `GrpcServiceOptions`.</span><span class="sxs-lookup"><span data-stu-id="439a5-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="439a5-125">Por exemplo, um serviço gRPC o tamanho máximo da mensagem de entrada pode ser configurado via `AddGrpc`.</span><span class="sxs-lookup"><span data-stu-id="439a5-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="439a5-126">O exemplo a seguir altera o `MaxReceiveMessageSize` padrão de 4 MB para 16 MB:</span><span class="sxs-lookup"><span data-stu-id="439a5-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="439a5-127">Para obter mais informações sobre a configuração, consulte <xref:grpc/configuration>.</span><span class="sxs-lookup"><span data-stu-id="439a5-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="439a5-128">Registro em log</span><span class="sxs-lookup"><span data-stu-id="439a5-128">Logging</span></span>

<span data-ttu-id="439a5-129">Os aplicativos baseados em núcleo com base no `GrpcEnvironment` para [Configurar o agente](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) para fins de depuração.</span><span class="sxs-lookup"><span data-stu-id="439a5-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="439a5-130">A pilha de ASP.NET Core fornece essa funcionalidade por meio da [API de log](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="439a5-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="439a5-131">Por exemplo, um agente de log pode ser adicionado ao serviço gRPC por meio de injeção de construtor:</span><span class="sxs-lookup"><span data-stu-id="439a5-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="439a5-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="439a5-132">HTTPS</span></span>

<span data-ttu-id="439a5-133">Aplicativos baseados em C-Core configuram HTTPS por meio da [Propriedade Server. Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span><span class="sxs-lookup"><span data-stu-id="439a5-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="439a5-134">Um conceito semelhante é usado para configurar servidores no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="439a5-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="439a5-135">Por exemplo, Kestrel usa a [configuração de ponto de extremidade](xref:fundamentals/servers/kestrel#endpoint-configuration) para essa funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="439a5-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="439a5-136">Interceptores gRPC vs middleware</span><span class="sxs-lookup"><span data-stu-id="439a5-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="439a5-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) oferece funcionalidades semelhantes em comparação com os interceptores em aplicativos gRPC baseados em C-Core.</span><span class="sxs-lookup"><span data-stu-id="439a5-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="439a5-138">ASP.NET Core middleware e interceptores são conceitualmente semelhantes.</span><span class="sxs-lookup"><span data-stu-id="439a5-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="439a5-139">Ambos:</span><span class="sxs-lookup"><span data-stu-id="439a5-139">Both:</span></span>

* <span data-ttu-id="439a5-140">São usados para construir um pipeline que manipula uma solicitação gRPC.</span><span class="sxs-lookup"><span data-stu-id="439a5-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="439a5-141">Permitir que o trabalho seja executado antes ou depois do próximo componente no pipeline.</span><span class="sxs-lookup"><span data-stu-id="439a5-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="439a5-142">Forneça acesso a `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="439a5-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="439a5-143">No middleware, o `HttpContext` é um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="439a5-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="439a5-144">Em interceptores, o `HttpContext` pode ser acessado usando o parâmetro `ServerCallContext` com o método de extensão `ServerCallContext.GetHttpContext`.</span><span class="sxs-lookup"><span data-stu-id="439a5-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="439a5-145">Observe que esse recurso é específico dos interceptores em execução no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="439a5-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="439a5-146">diferenças de interceptador gRPC do middleware ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="439a5-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="439a5-147">Interceptores</span><span class="sxs-lookup"><span data-stu-id="439a5-147">Interceptors:</span></span>
  * <span data-ttu-id="439a5-148">Opere na camada gRPC de abstração usando o [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span><span class="sxs-lookup"><span data-stu-id="439a5-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="439a5-149">Fornecer acesso a:</span><span class="sxs-lookup"><span data-stu-id="439a5-149">Provide access to:</span></span>
    * <span data-ttu-id="439a5-150">A mensagem desserializada enviada a uma chamada.</span><span class="sxs-lookup"><span data-stu-id="439a5-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="439a5-151">A mensagem que está sendo retornada da chamada antes de ser serializada.</span><span class="sxs-lookup"><span data-stu-id="439a5-151">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="439a5-152">Pode detectar e tratar exceções lançadas dos serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="439a5-152">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="439a5-153">Middleware</span><span class="sxs-lookup"><span data-stu-id="439a5-153">Middleware:</span></span>
  * <span data-ttu-id="439a5-154">É executado antes de interceptadores gRPC.</span><span class="sxs-lookup"><span data-stu-id="439a5-154">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="439a5-155">Opera nas mensagens HTTP/2 subjacentes.</span><span class="sxs-lookup"><span data-stu-id="439a5-155">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="439a5-156">Pode acessar somente os bytes dos fluxos de solicitação e resposta.</span><span class="sxs-lookup"><span data-stu-id="439a5-156">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="439a5-157">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="439a5-157">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
