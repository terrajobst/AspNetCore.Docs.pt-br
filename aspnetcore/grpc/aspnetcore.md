---
title: Serviços do gRPC com o ASP.NET Core
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços gRPCs com ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/28/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 128f5b36eac9112460c33693db5537134a077476
ms.sourcegitcommit: 23f79bd71d49c4efddb56377c1f553cc993d781b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70130698"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="fb813-103">Serviços do gRPC com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb813-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="fb813-104">Este documento mostra como começar a usar os serviços gRPCs usando ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fb813-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fb813-105">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="fb813-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fb813-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb813-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="fb813-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fb813-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="fb813-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fb813-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="fb813-109">Introdução ao serviço de gRPC no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb813-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="fb813-110">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fb813-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fb813-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb813-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fb813-112">Consulte Introdução [aos serviços gRPCs](xref:tutorials/grpc/grpc-start) para obter instruções detalhadas sobre como criar um projeto gRPC.</span><span class="sxs-lookup"><span data-stu-id="fb813-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="fb813-113">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fb813-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="fb813-114">Da linha de comando, execute `dotnet new grpc -o GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="fb813-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="fb813-115">Adicionar serviços gRPCs a um aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb813-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="fb813-116">gRPC requer o pacote [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="fb813-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="fb813-117">Configurar o gRPC</span><span class="sxs-lookup"><span data-stu-id="fb813-117">Configure gRPC</span></span>

<span data-ttu-id="fb813-118">Em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb813-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="fb813-119">gRPC é habilitado com o `AddGrpc` método.</span><span class="sxs-lookup"><span data-stu-id="fb813-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="fb813-120">Cada serviço gRPC é adicionado ao pipeline de roteamento por meio `MapGrpcService` do método.</span><span class="sxs-lookup"><span data-stu-id="fb813-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]

<span data-ttu-id="fb813-121">ASP.NET Core middleware e recursos compartilham o pipeline de roteamento, portanto um aplicativo pode ser configurado para atender a manipuladores de solicitação adicionais.</span><span class="sxs-lookup"><span data-stu-id="fb813-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="fb813-122">Os manipuladores de solicitação adicionais, como controladores MVC, funcionam em paralelo com os serviços gRPCs configurados.</span><span class="sxs-lookup"><span data-stu-id="fb813-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="fb813-123">Configurar o Kestrel</span><span class="sxs-lookup"><span data-stu-id="fb813-123">Configure Kestrel</span></span>

<span data-ttu-id="fb813-124">Kestrel pontos de extremidade gRPC:</span><span class="sxs-lookup"><span data-stu-id="fb813-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="fb813-125">Exigir HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fb813-125">Require HTTP/2.</span></span>
* <span data-ttu-id="fb813-126">Deve ser protegido com HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fb813-126">Should be secured with HTTPS.</span></span>

#### <a name="http2"></a><span data-ttu-id="fb813-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="fb813-127">HTTP/2</span></span>

<span data-ttu-id="fb813-128">O Kestrel [dá suporte a http/2](xref:fundamentals/servers/kestrel#http2-support) na maioria dos sistemas operacionais modernos.</span><span class="sxs-lookup"><span data-stu-id="fb813-128">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="fb813-129">Os pontos de extremidade Kestrel são configurados para dar suporte a conexões HTTP/1.1 e HTTP/2 por padrão.</span><span class="sxs-lookup"><span data-stu-id="fb813-129">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

> [!NOTE]
> <span data-ttu-id="fb813-130">o macOS não dá suporte a ASP.NET Core gRPC com [segurança de camada de transporte (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="fb813-130">macOS doesn't support ASP.NET Core gRPC with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="fb813-131">É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS.</span><span class="sxs-lookup"><span data-stu-id="fb813-131">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="fb813-132">Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="fb813-132">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

#### <a name="https"></a><span data-ttu-id="fb813-133">HTTPS</span><span class="sxs-lookup"><span data-stu-id="fb813-133">HTTPS</span></span>

<span data-ttu-id="fb813-134">Os pontos de extremidade Kestrel usados para gRPC devem ser protegidos com HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fb813-134">Kestrel endpoints used for gRPC should be secured with HTTPS.</span></span> <span data-ttu-id="fb813-135">No desenvolvimento, um ponto de extremidade HTTPS é criado `https://localhost:5001` automaticamente quando o certificado de desenvolvimento de ASP.NET Core está presente.</span><span class="sxs-lookup"><span data-stu-id="fb813-135">In development, an HTTPS endpoint is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="fb813-136">Nenhuma configuração é necessária.</span><span class="sxs-lookup"><span data-stu-id="fb813-136">No configuration is required.</span></span>

<span data-ttu-id="fb813-137">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="fb813-137">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="fb813-138">No exemplo de *appSettings. JSON* a seguir, um ponto de extremidade http/2 protegido com HTTPS é fornecido:</span><span class="sxs-lookup"><span data-stu-id="fb813-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with HTTPS is provided:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http2"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="fb813-139">Como alternativa, Kestrel endspoints pode ser configurado no *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb813-139">Alternatively, Kestrel endspoints can be configured in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // This endpoint will use HTTP/2 and HTTPS on port 5001.
                options.Listen(IPAddress.Any, 5001, listenOptions =>
                {
                    listenOptions.Protocols = HttpProtocols.Http2;
                    listenOptions.UseHttps("<path to .pfx file>", 
                        "<certificate password>");
                });
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="fb813-140">Para obter mais informações sobre como habilitar HTTP/2 e HTTPS com Kestrel, consulte [configuração de ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="fb813-140">For more information on enabling HTTP/2 and HTTPS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="fb813-141">Integração com APIs de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb813-141">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="fb813-142">os serviços gRPCs têm acesso completo aos recursos de ASP.NET Core, como [injeção de dependência](xref:fundamentals/dependency-injection) (di) e [registro em log](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="fb813-142">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="fb813-143">Por exemplo, a implementação do serviço pode resolver um serviço de agente do contêiner DI por meio do Construtor:</span><span class="sxs-lookup"><span data-stu-id="fb813-143">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="fb813-144">Por padrão, a implementação do serviço gRPC pode resolver outros serviços de DI com qualquer tempo de vida (singleton, com escopo ou transitório).</span><span class="sxs-lookup"><span data-stu-id="fb813-144">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="fb813-145">Resolver HttpContext em métodos gRPC</span><span class="sxs-lookup"><span data-stu-id="fb813-145">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="fb813-146">A API gRPC fornece acesso a alguns dados de mensagem HTTP/2, como o método, o host, o cabeçalho e os trailers.</span><span class="sxs-lookup"><span data-stu-id="fb813-146">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="fb813-147">O acesso é por `ServerCallContext` meio do argumento passado para cada método gRPC:</span><span class="sxs-lookup"><span data-stu-id="fb813-147">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="fb813-148">`ServerCallContext`não fornece acesso completo ao `HttpContext` em todas as APIs do ASP.net.</span><span class="sxs-lookup"><span data-stu-id="fb813-148">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="fb813-149">O `GetHttpContext` método de extensão fornece acesso completo `HttpContext` à representação da mensagem http/2 subjacente em APIs ASP.net:</span><span class="sxs-lookup"><span data-stu-id="fb813-149">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="fb813-150">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="fb813-150">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
