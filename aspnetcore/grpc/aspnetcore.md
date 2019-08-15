---
title: Serviços do gRPC com o ASP.NET Core
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços gRPCs com ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/07/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 38111c152c581c50767f9cd4e5fa257bd3fd804e
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022309"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="47241-103">Serviços do gRPC com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47241-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="47241-104">Este documento mostra como começar a usar os serviços gRPCs usando ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="47241-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="47241-105">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="47241-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="47241-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47241-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="47241-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="47241-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="47241-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="47241-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="47241-109">Introdução ao serviço de gRPC no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47241-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="47241-110">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="47241-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="47241-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47241-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="47241-112">Consulte Introdução [aos serviços gRPCs](xref:tutorials/grpc/grpc-start) para obter instruções detalhadas sobre como criar um projeto gRPC.</span><span class="sxs-lookup"><span data-stu-id="47241-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="47241-113">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="47241-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="47241-114">Da linha de comando, execute `dotnet new grpc -o GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="47241-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="47241-115">Adicionar serviços gRPCs a um aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47241-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="47241-116">gRPC requer o pacote [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="47241-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="47241-117">Configurar o gRPC</span><span class="sxs-lookup"><span data-stu-id="47241-117">Configure gRPC</span></span>

<span data-ttu-id="47241-118">gRPC é habilitado com o `AddGrpc` método:</span><span class="sxs-lookup"><span data-stu-id="47241-118">gRPC is enabled with the `AddGrpc` method:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

<span data-ttu-id="47241-119">Cada serviço gRPC é adicionado ao pipeline de roteamento por meio `MapGrpcService` do método:</span><span class="sxs-lookup"><span data-stu-id="47241-119">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

<span data-ttu-id="47241-120">ASP.NET Core middleware e recursos compartilham o pipeline de roteamento, portanto um aplicativo pode ser configurado para atender a manipuladores de solicitação adicionais.</span><span class="sxs-lookup"><span data-stu-id="47241-120">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="47241-121">Os manipuladores de solicitação adicionais, como controladores MVC, funcionam em paralelo com os serviços gRPCs configurados.</span><span class="sxs-lookup"><span data-stu-id="47241-121">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="47241-122">Integração com APIs de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47241-122">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="47241-123">os serviços gRPCs têm acesso completo aos recursos de ASP.NET Core, como [injeção de dependência](xref:fundamentals/dependency-injection) (di) e [registro em log](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="47241-123">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="47241-124">Por exemplo, a implementação do serviço pode resolver um serviço de agente do contêiner DI por meio do Construtor:</span><span class="sxs-lookup"><span data-stu-id="47241-124">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="47241-125">Por padrão, a implementação do serviço gRPC pode resolver outros serviços de DI com qualquer tempo de vida (singleton, com escopo ou transitório).</span><span class="sxs-lookup"><span data-stu-id="47241-125">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="47241-126">Resolver HttpContext em métodos gRPC</span><span class="sxs-lookup"><span data-stu-id="47241-126">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="47241-127">A API gRPC fornece acesso a alguns dados de mensagem HTTP/2, como o método, o host, o cabeçalho e os trailers.</span><span class="sxs-lookup"><span data-stu-id="47241-127">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="47241-128">O acesso é por `ServerCallContext` meio do argumento passado para cada método gRPC:</span><span class="sxs-lookup"><span data-stu-id="47241-128">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="47241-129">`ServerCallContext`não fornece acesso completo ao `HttpContext` em todas as APIs do ASP.net.</span><span class="sxs-lookup"><span data-stu-id="47241-129">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="47241-130">O `GetHttpContext` método de extensão fornece acesso completo `HttpContext` à representação da mensagem http/2 subjacente em APIs ASP.net:</span><span class="sxs-lookup"><span data-stu-id="47241-130">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="47241-131">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="47241-131">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
