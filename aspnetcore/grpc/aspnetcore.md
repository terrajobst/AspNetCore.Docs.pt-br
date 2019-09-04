---
title: Serviços do gRPC com o ASP.NET Core
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços gRPCs com ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 28e6b8589bbe0b6a3723b64736c723c883302571
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238168"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="763d9-103">Serviços do gRPC com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="763d9-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="763d9-104">Este documento mostra como começar a usar os serviços gRPCs usando ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="763d9-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="763d9-105">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="763d9-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="763d9-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="763d9-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="763d9-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="763d9-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="763d9-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="763d9-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="763d9-109">Introdução ao serviço de gRPC no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="763d9-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="763d9-110">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="763d9-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="763d9-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="763d9-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="763d9-112">Consulte Introdução [aos serviços gRPCs](xref:tutorials/grpc/grpc-start) para obter instruções detalhadas sobre como criar um projeto gRPC.</span><span class="sxs-lookup"><span data-stu-id="763d9-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="763d9-113">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="763d9-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="763d9-114">Da linha de comando, execute `dotnet new grpc -o GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="763d9-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="763d9-115">Adicionar serviços gRPCs a um aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="763d9-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="763d9-116">gRPC requer o pacote [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="763d9-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="763d9-117">Configurar o gRPC</span><span class="sxs-lookup"><span data-stu-id="763d9-117">Configure gRPC</span></span>

<span data-ttu-id="763d9-118">Em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="763d9-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="763d9-119">gRPC é habilitado com o `AddGrpc` método.</span><span class="sxs-lookup"><span data-stu-id="763d9-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="763d9-120">Cada serviço gRPC é adicionado ao pipeline de roteamento por meio `MapGrpcService` do método.</span><span class="sxs-lookup"><span data-stu-id="763d9-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]

<span data-ttu-id="763d9-121">ASP.NET Core middleware e recursos compartilham o pipeline de roteamento, portanto um aplicativo pode ser configurado para atender a manipuladores de solicitação adicionais.</span><span class="sxs-lookup"><span data-stu-id="763d9-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="763d9-122">Os manipuladores de solicitação adicionais, como controladores MVC, funcionam em paralelo com os serviços gRPCs configurados.</span><span class="sxs-lookup"><span data-stu-id="763d9-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="763d9-123">Configurar o Kestrel</span><span class="sxs-lookup"><span data-stu-id="763d9-123">Configure Kestrel</span></span>

<span data-ttu-id="763d9-124">Kestrel pontos de extremidade gRPC:</span><span class="sxs-lookup"><span data-stu-id="763d9-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="763d9-125">Exigir HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="763d9-125">Require HTTP/2.</span></span>
* <span data-ttu-id="763d9-126">Deve ser protegido com HTTPS.</span><span class="sxs-lookup"><span data-stu-id="763d9-126">Should be secured with HTTPS.</span></span>

#### <a name="http2"></a><span data-ttu-id="763d9-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="763d9-127">HTTP/2</span></span>

<span data-ttu-id="763d9-128">gRPC requer HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="763d9-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="763d9-129">gRPC para ASP.NET Core valida [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) é `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="763d9-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="763d9-130">O Kestrel [dá suporte a http/2](xref:fundamentals/servers/kestrel#http2-support) na maioria dos sistemas operacionais modernos.</span><span class="sxs-lookup"><span data-stu-id="763d9-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="763d9-131">Os pontos de extremidade Kestrel são configurados para dar suporte a conexões HTTP/1.1 e HTTP/2 por padrão.</span><span class="sxs-lookup"><span data-stu-id="763d9-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="https"></a><span data-ttu-id="763d9-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="763d9-132">HTTPS</span></span>

<span data-ttu-id="763d9-133">Os pontos de extremidade Kestrel usados para gRPC devem ser protegidos com HTTPS.</span><span class="sxs-lookup"><span data-stu-id="763d9-133">Kestrel endpoints used for gRPC should be secured with HTTPS.</span></span> <span data-ttu-id="763d9-134">No desenvolvimento, um ponto de extremidade HTTPS é criado `https://localhost:5001` automaticamente quando o certificado de desenvolvimento de ASP.NET Core está presente.</span><span class="sxs-lookup"><span data-stu-id="763d9-134">In development, an HTTPS endpoint is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="763d9-135">Nenhuma configuração é necessária.</span><span class="sxs-lookup"><span data-stu-id="763d9-135">No configuration is required.</span></span>

<span data-ttu-id="763d9-136">Em produção, HTTPS precisa ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="763d9-136">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="763d9-137">No exemplo de *appSettings. JSON* a seguir, um ponto de extremidade http/2 protegido com HTTPS é fornecido:</span><span class="sxs-lookup"><span data-stu-id="763d9-137">In the following *appsettings.json* example, an HTTP/2 endpoint secured with HTTPS is provided:</span></span>

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

<span data-ttu-id="763d9-138">Como alternativa, os pontos de extremidade Kestrel podem ser configurados no *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="763d9-138">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

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

<span data-ttu-id="763d9-139">Quando um ponto de extremidade HTTP/2 é configurado sem HTTPS, o [ListenerOptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols) do ponto de extremidade deve ser `HttpProtocols.Http2`definido como.</span><span class="sxs-lookup"><span data-stu-id="763d9-139">When an HTTP/2 endpoint is configured without HTTPS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="763d9-140">`HttpProtocols.Http1AndHttp2`Não pode ser usado porque HTTPS é necessário para negociar HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="763d9-140">`HttpProtocols.Http1AndHttp2` can't be used because HTTPS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="763d9-141">Sem HTTPS, todas as conexões com o ponto de extremidade padrão para HTTP/1.1 e chamadas gRPC falham.</span><span class="sxs-lookup"><span data-stu-id="763d9-141">Without HTTPS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="763d9-142">Para obter mais informações sobre como habilitar HTTP/2 e HTTPS com Kestrel, consulte [configuração de ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="763d9-142">For more information on enabling HTTP/2 and HTTPS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="763d9-143">o macOS não dá suporte a ASP.NET Core gRPC com [segurança de camada de transporte (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="763d9-143">macOS doesn't support ASP.NET Core gRPC with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="763d9-144">É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS.</span><span class="sxs-lookup"><span data-stu-id="763d9-144">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="763d9-145">Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="763d9-145">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="763d9-146">Integração com APIs de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="763d9-146">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="763d9-147">os serviços gRPCs têm acesso completo aos recursos de ASP.NET Core, como [injeção de dependência](xref:fundamentals/dependency-injection) (di) e [registro em log](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="763d9-147">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="763d9-148">Por exemplo, a implementação do serviço pode resolver um serviço de agente do contêiner DI por meio do Construtor:</span><span class="sxs-lookup"><span data-stu-id="763d9-148">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="763d9-149">Por padrão, a implementação do serviço gRPC pode resolver outros serviços de DI com qualquer tempo de vida (singleton, com escopo ou transitório).</span><span class="sxs-lookup"><span data-stu-id="763d9-149">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="763d9-150">Resolver HttpContext em métodos gRPC</span><span class="sxs-lookup"><span data-stu-id="763d9-150">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="763d9-151">A API gRPC fornece acesso a alguns dados de mensagem HTTP/2, como o método, o host, o cabeçalho e os trailers.</span><span class="sxs-lookup"><span data-stu-id="763d9-151">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="763d9-152">O acesso é por `ServerCallContext` meio do argumento passado para cada método gRPC:</span><span class="sxs-lookup"><span data-stu-id="763d9-152">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="763d9-153">`ServerCallContext`não fornece acesso completo ao `HttpContext` em todas as APIs do ASP.net.</span><span class="sxs-lookup"><span data-stu-id="763d9-153">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="763d9-154">O `GetHttpContext` método de extensão fornece acesso completo `HttpContext` à representação da mensagem http/2 subjacente em APIs ASP.net:</span><span class="sxs-lookup"><span data-stu-id="763d9-154">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="763d9-155">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="763d9-155">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
