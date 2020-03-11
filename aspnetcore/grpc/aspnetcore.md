---
title: Serviços do gRPC com o ASP.NET Core
author: juntaoluo
description: Aprenda os conceitos básicos ao escrever serviços gRPCs com ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 6107704a4b4d9c629a7abe907efd5b1932019130
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667625"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="e94dc-103">Serviços do gRPC com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e94dc-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="e94dc-104">Este documento mostra como começar a usar os serviços gRPCs usando ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e94dc-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e94dc-105">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="e94dc-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e94dc-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e94dc-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e94dc-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e94dc-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e94dc-108">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e94dc-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="e94dc-109">Introdução ao serviço de gRPC no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e94dc-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="e94dc-110">[Exibir ou baixar um código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="e94dc-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e94dc-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e94dc-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e94dc-112">Consulte Introdução [aos serviços gRPCs](xref:tutorials/grpc/grpc-start) para obter instruções detalhadas sobre como criar um projeto gRPC.</span><span class="sxs-lookup"><span data-stu-id="e94dc-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="e94dc-113">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="e94dc-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e94dc-114">Da linha de comando, execute `dotnet new grpc -o GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="e94dc-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="e94dc-115">Adicionar serviços gRPCs a um aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e94dc-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="e94dc-116">gRPC requer o pacote [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="e94dc-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="e94dc-117">Configurar o gRPC</span><span class="sxs-lookup"><span data-stu-id="e94dc-117">Configure gRPC</span></span>

<span data-ttu-id="e94dc-118">Em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="e94dc-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="e94dc-119">gRPC é habilitado com o método `AddGrpc`.</span><span class="sxs-lookup"><span data-stu-id="e94dc-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="e94dc-120">Cada serviço gRPC é adicionado ao pipeline de roteamento por meio do método `MapGrpcService`.</span><span class="sxs-lookup"><span data-stu-id="e94dc-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="e94dc-121">ASP.NET Core middleware e recursos compartilham o pipeline de roteamento, portanto um aplicativo pode ser configurado para atender a manipuladores de solicitação adicionais.</span><span class="sxs-lookup"><span data-stu-id="e94dc-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="e94dc-122">Os manipuladores de solicitação adicionais, como controladores MVC, funcionam em paralelo com os serviços gRPCs configurados.</span><span class="sxs-lookup"><span data-stu-id="e94dc-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="e94dc-123">Configurar o Kestrel</span><span class="sxs-lookup"><span data-stu-id="e94dc-123">Configure Kestrel</span></span>

<span data-ttu-id="e94dc-124">Kestrel pontos de extremidade gRPC:</span><span class="sxs-lookup"><span data-stu-id="e94dc-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="e94dc-125">Exigir HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e94dc-125">Require HTTP/2.</span></span>
* <span data-ttu-id="e94dc-126">Deve ser protegido com [TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="e94dc-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="e94dc-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="e94dc-127">HTTP/2</span></span>

<span data-ttu-id="e94dc-128">gRPC requer HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e94dc-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="e94dc-129">gRPC para ASP.NET Core valida [HttpRequest. o protocolo](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) é `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="e94dc-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="e94dc-130">O Kestrel [dá suporte a http/2](xref:fundamentals/servers/kestrel#http2-support) na maioria dos sistemas operacionais modernos.</span><span class="sxs-lookup"><span data-stu-id="e94dc-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="e94dc-131">Os pontos de extremidade Kestrel são configurados para dar suporte a conexões HTTP/1.1 e HTTP/2 por padrão.</span><span class="sxs-lookup"><span data-stu-id="e94dc-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="e94dc-132">TLS</span><span class="sxs-lookup"><span data-stu-id="e94dc-132">TLS</span></span>

<span data-ttu-id="e94dc-133">Os pontos de extremidade Kestrel usados para gRPC devem ser protegidos com TLS.</span><span class="sxs-lookup"><span data-stu-id="e94dc-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="e94dc-134">No desenvolvimento, um ponto de extremidade protegido com TLS é criado automaticamente em `https://localhost:5001` quando o certificado de desenvolvimento de ASP.NET Core está presente.</span><span class="sxs-lookup"><span data-stu-id="e94dc-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="e94dc-135">Nenhuma configuração é necessária.</span><span class="sxs-lookup"><span data-stu-id="e94dc-135">No configuration is required.</span></span> <span data-ttu-id="e94dc-136">Um prefixo de `https` verifica se o ponto de extremidade Kestrel está usando TLS.</span><span class="sxs-lookup"><span data-stu-id="e94dc-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="e94dc-137">Em produção, o TLS deve ser configurado explicitamente.</span><span class="sxs-lookup"><span data-stu-id="e94dc-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="e94dc-138">No exemplo de *appSettings. JSON* a seguir, um ponto de extremidade http/2 protegido com TLS é fornecido:</span><span class="sxs-lookup"><span data-stu-id="e94dc-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="e94dc-139">Como alternativa, os pontos de extremidade Kestrel podem ser configurados no *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e94dc-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="e94dc-140">Negociação de protocolo</span><span class="sxs-lookup"><span data-stu-id="e94dc-140">Protocol negotiation</span></span>

<span data-ttu-id="e94dc-141">O TLS é usado para mais do que proteger a comunicação.</span><span class="sxs-lookup"><span data-stu-id="e94dc-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="e94dc-142">O handshake [ALPN (negociação do protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3) TLS é usado para negociar o protocolo de conexão entre o cliente e o servidor quando um ponto de extremidade dá suporte a vários protocolos.</span><span class="sxs-lookup"><span data-stu-id="e94dc-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="e94dc-143">Essa negociação determina se a conexão usa HTTP/1.1 ou HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="e94dc-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="e94dc-144">Se um ponto de extremidade HTTP/2 estiver configurado sem TLS, o [ListenerOptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols) do ponto de extremidade deverá ser definido como `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="e94dc-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="e94dc-145">Um ponto de extremidade com vários protocolos (por exemplo, `HttpProtocols.Http1AndHttp2`) não pode ser usado sem TLS porque não há negociação.</span><span class="sxs-lookup"><span data-stu-id="e94dc-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there is no negotiation.</span></span> <span data-ttu-id="e94dc-146">Todas as conexões com o ponto de extremidade não seguro padrão para HTTP/1.1 e chamadas gRPC falham.</span><span class="sxs-lookup"><span data-stu-id="e94dc-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="e94dc-147">Para obter mais informações sobre como habilitar HTTP/2 e TLS com Kestrel, consulte [configuração de ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="e94dc-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="e94dc-148">O macOS não é compatível com gRPC do ASP.NET Core com TLS.</span><span class="sxs-lookup"><span data-stu-id="e94dc-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="e94dc-149">É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS.</span><span class="sxs-lookup"><span data-stu-id="e94dc-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="e94dc-150">Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="e94dc-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="e94dc-151">Integração com APIs de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e94dc-151">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="e94dc-152">os serviços gRPCs têm acesso completo aos recursos de ASP.NET Core, como [injeção de dependência](xref:fundamentals/dependency-injection) (di) e [registro em log](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="e94dc-152">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="e94dc-153">Por exemplo, a implementação do serviço pode resolver um serviço de agente do contêiner DI por meio do Construtor:</span><span class="sxs-lookup"><span data-stu-id="e94dc-153">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="e94dc-154">Por padrão, a implementação do serviço gRPC pode resolver outros serviços de DI com qualquer tempo de vida (singleton, com escopo ou transitório).</span><span class="sxs-lookup"><span data-stu-id="e94dc-154">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="e94dc-155">Resolver HttpContext em métodos gRPC</span><span class="sxs-lookup"><span data-stu-id="e94dc-155">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="e94dc-156">A API gRPC fornece acesso a alguns dados de mensagem HTTP/2, como o método, o host, o cabeçalho e os trailers.</span><span class="sxs-lookup"><span data-stu-id="e94dc-156">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="e94dc-157">O acesso é por meio do argumento `ServerCallContext` passado para cada método gRPC:</span><span class="sxs-lookup"><span data-stu-id="e94dc-157">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="e94dc-158">`ServerCallContext` não fornece acesso completo a `HttpContext` em todas as APIs ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e94dc-158">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="e94dc-159">O método de extensão `GetHttpContext` fornece acesso completo ao `HttpContext` que representa a mensagem HTTP/2 subjacente em APIs ASP.NET:</span><span class="sxs-lookup"><span data-stu-id="e94dc-159">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="e94dc-160">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e94dc-160">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
