---
title: Serviços do gRPC com o ASP.NET Core
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços de gRPC com o ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/aspnetcore
ms.openlocfilehash: ca06478e6168c59d9abf43d99213fa8a7091e178
ms.sourcegitcommit: 756114cab5e24e99ec23de62b0c1c16b15197ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67169530"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="9ad89-103">Serviços do gRPC com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ad89-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="9ad89-104">Este documento mostra como começar com os serviços de gRPC usando o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9ad89-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="9ad89-105">Introdução ao serviço de gRPC no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ad89-105">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="9ad89-106">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9ad89-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9ad89-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9ad89-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9ad89-108">Ver [Introdução aos serviços de gRPC](xref:tutorials/grpc/grpc-start) para obter instruções detalhadas sobre como criar um projeto gRPC.</span><span class="sxs-lookup"><span data-stu-id="9ad89-108">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="9ad89-109">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="9ad89-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9ad89-110">Da linha de comando, execute `dotnet new grpc -o GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="9ad89-110">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="9ad89-111">Adicionar serviços de gRPC para um aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ad89-111">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="9ad89-112">gRPC requer os seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="9ad89-112">gRPC requires the following packages:</span></span>

* [<span data-ttu-id="9ad89-113">Grpc.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="9ad89-113">Grpc.AspNetCore.Server</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server)
* <span data-ttu-id="9ad89-114">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) para protobuf APIs da mensagem.</span><span class="sxs-lookup"><span data-stu-id="9ad89-114">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) for protobuf message APIs.</span></span>
* [<span data-ttu-id="9ad89-115">Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="9ad89-115">Grpc.Tools</span></span>](https://www.nuget.org/packages/Grpc.Tools/)

### <a name="configure-grpc"></a><span data-ttu-id="9ad89-116">Configurar gRPC</span><span class="sxs-lookup"><span data-stu-id="9ad89-116">Configure gRPC</span></span>

<span data-ttu-id="9ad89-117">gRPC é habilitada com o `AddGrpc` método:</span><span class="sxs-lookup"><span data-stu-id="9ad89-117">gRPC is enabled with the `AddGrpc` method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

<span data-ttu-id="9ad89-118">Cada serviço gRPC é adicionado ao pipeline de roteamento por meio de `MapGrpcService` método:</span><span class="sxs-lookup"><span data-stu-id="9ad89-118">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

<span data-ttu-id="9ad89-119">O pipeline de roteamento de compartilhamento de recursos e o middleware do ASP.NET Core, portanto, um aplicativo pode ser configurado para servir os manipuladores de solicitação adicionais.</span><span class="sxs-lookup"><span data-stu-id="9ad89-119">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="9ad89-120">Os manipuladores de solicitação adicionais, como controladores MVC, trabalhassem em paralelo com os serviços configurados gRPC.</span><span class="sxs-lookup"><span data-stu-id="9ad89-120">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="9ad89-121">Integração com as APIs do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ad89-121">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="9ad89-122">gRPC serviços têm acesso completo aos recursos do ASP.NET Core, como [injeção de dependência](xref:fundamentals/dependency-injection) (DI) e [log](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="9ad89-122">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="9ad89-123">Por exemplo, a implementação do serviço pode resolver um serviço de agente de log do contêiner de DI por meio do construtor:</span><span class="sxs-lookup"><span data-stu-id="9ad89-123">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="9ad89-124">Por padrão, a implementação do serviço gRPC pode resolver outros serviços de DI com qualquer tempo de vida (Singleton, escopo ou transitório).</span><span class="sxs-lookup"><span data-stu-id="9ad89-124">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="9ad89-125">Resolver HttpContext em gRPC métodos</span><span class="sxs-lookup"><span data-stu-id="9ad89-125">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="9ad89-126">A API gRPC fornece acesso a alguns dados de mensagem HTTP/2, como o método, host, cabeçalho e trailers.</span><span class="sxs-lookup"><span data-stu-id="9ad89-126">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="9ad89-127">O acesso é por meio de `ServerCallContext` argumento passado para cada método gRPC:</span><span class="sxs-lookup"><span data-stu-id="9ad89-127">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="9ad89-128">`ServerCallContext` não fornece acesso completo ao `HttpContext` em todas as APIs do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="9ad89-128">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="9ad89-129">O `GetHttpContext` método de extensão fornece acesso completo para o `HttpContext` que representa a mensagem HTTP/2 subjacente nas APIs do ASP.NET:</span><span class="sxs-lookup"><span data-stu-id="9ad89-129">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="9ad89-130">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9ad89-130">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
