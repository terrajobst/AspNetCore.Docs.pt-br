---
title: Registro em log e diagnóstico no gRPC no .NET
author: jamesnk
description: Saiba como coletar diagnósticos do seu aplicativo gRPC no .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: ce6ad96d9e26c9cd3844093536745f8f9bea4a76
ms.sourcegitcommit: 0365af91518004c4a44a30dc3a8ac324558a399b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204341"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="f65c2-103">Registro em log e diagnóstico no gRPC no .NET</span><span class="sxs-lookup"><span data-stu-id="f65c2-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="f65c2-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="f65c2-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="f65c2-105">Este artigo fornece orientação para a coleta de diagnósticos do seu aplicativo gRPC para ajudar a solucionar problemas.</span><span class="sxs-lookup"><span data-stu-id="f65c2-105">This article provides guidance for gathering diagnostics from your gRPC app to help troubleshoot issues.</span></span>

## <a name="grpc-services-logging"></a><span data-ttu-id="f65c2-106">log de serviços gRPCs</span><span class="sxs-lookup"><span data-stu-id="f65c2-106">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="f65c2-107">Os logs do lado do servidor podem conter informações confidenciais do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f65c2-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="f65c2-108">**Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.</span><span class="sxs-lookup"><span data-stu-id="f65c2-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="f65c2-109">Como os serviços gRPCs são hospedados em ASP.NET Core, ele usa o sistema de registro em log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f65c2-109">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="f65c2-110">Na configuração padrão, o gRPC registra muito pouca informação, mas isso pode ser configurado.</span><span class="sxs-lookup"><span data-stu-id="f65c2-110">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="f65c2-111">Consulte a documentação em [log de ASP.NET Core](xref:fundamentals/logging/index#configuration) para obter detalhes sobre como configurar o log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f65c2-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="f65c2-112">gRPC adiciona logs na `Grpc` categoria.</span><span class="sxs-lookup"><span data-stu-id="f65c2-112">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="f65c2-113">Para habilitar logs detalhados do gRPC, configure `Grpc` os prefixos `Debug` para o nível em seu arquivo *appSettings. JSON* adicionando os seguintes itens à `LogLevel` subseção em `Logging`:</span><span class="sxs-lookup"><span data-stu-id="f65c2-113">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7)]

<span data-ttu-id="f65c2-114">Você também pode configurar isso em *Startup.cs* com `ConfigureLogging`:</span><span class="sxs-lookup"><span data-stu-id="f65c2-114">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5)]

<span data-ttu-id="f65c2-115">Se você não estiver usando a configuração baseada em JSON, defina o seguinte valor de configuração em seu sistema de configuração:</span><span class="sxs-lookup"><span data-stu-id="f65c2-115">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="f65c2-116">Verifique a documentação do seu sistema de configuração para determinar como especificar valores de configuração aninhados.</span><span class="sxs-lookup"><span data-stu-id="f65c2-116">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="f65c2-117">Por exemplo, ao usar variáveis de ambiente, `_` dois caracteres são usados em vez `:` do (por exemplo `Logging__LogLevel__Grpc`,).</span><span class="sxs-lookup"><span data-stu-id="f65c2-117">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="f65c2-118">É recomendável usar `Debug` o nível ao coletar diagnósticos mais detalhados para seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f65c2-118">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="f65c2-119">O `Trace` nível produz diagnósticos de nível muito baixo e raramente é necessário para diagnosticar problemas em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f65c2-119">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

### <a name="sample-logging-output"></a><span data-ttu-id="f65c2-120">Exemplo de saída de registro em log</span><span class="sxs-lookup"><span data-stu-id="f65c2-120">Sample logging output</span></span>

<span data-ttu-id="f65c2-121">Aqui está um exemplo de saída do console no `Debug` nível de um serviço gRPC:</span><span class="sxs-lookup"><span data-stu-id="f65c2-121">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

## <a name="access-server-side-logs"></a><span data-ttu-id="f65c2-122">Acessar logs do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="f65c2-122">Access server-side logs</span></span>

<span data-ttu-id="f65c2-123">A maneira como você acessa os logs do lado do servidor depende do ambiente no qual você está executando o.</span><span class="sxs-lookup"><span data-stu-id="f65c2-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app"></a><span data-ttu-id="f65c2-124">Como um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="f65c2-124">As a console app</span></span>

<span data-ttu-id="f65c2-125">Se você estiver executando o em um aplicativo de console, o [agente de log do console](xref:fundamentals/logging/index#console-provider) deverá ser habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="f65c2-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="f65c2-126">os logs do gRPC serão exibidos no console do.</span><span class="sxs-lookup"><span data-stu-id="f65c2-126">gRPC logs will appear in the console.</span></span>

### <a name="other-environments"></a><span data-ttu-id="f65c2-127">Outros ambientes</span><span class="sxs-lookup"><span data-stu-id="f65c2-127">Other environments</span></span>

<span data-ttu-id="f65c2-128">Se o aplicativo for implantado em outro ambiente (por exemplo, Docker, kubernetes ou serviço do Windows) <xref:fundamentals/logging/index> , consulte para obter mais informações sobre como configurar provedores de log adequados para o ambiente.</span><span class="sxs-lookup"><span data-stu-id="f65c2-128">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="grpc-client-logging"></a><span data-ttu-id="f65c2-129">log de cliente do gRPC</span><span class="sxs-lookup"><span data-stu-id="f65c2-129">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="f65c2-130">Os logs do lado do cliente podem conter informações confidenciais do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f65c2-130">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="f65c2-131">**Nunca** poste logs brutos de aplicativos de produção em fóruns públicos, como o github.</span><span class="sxs-lookup"><span data-stu-id="f65c2-131">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="f65c2-132">Para obter logs do cliente .net, você pode definir a `GrpcChannelOptions.LoggerFactory` Propriedade quando o canal do cliente é criado.</span><span class="sxs-lookup"><span data-stu-id="f65c2-132">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="f65c2-133">Se você estiver chamando um serviço gRPC de um aplicativo ASP.NET Core, a fábrica do agente poderá ser resolvida da injeção de dependência (DI):</span><span class="sxs-lookup"><span data-stu-id="f65c2-133">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="f65c2-134">Uma maneira alternativa de habilitar o log do cliente é usar a [fábrica do cliente do gRPC](xref:grpc/clientfactory) para criar o cliente.</span><span class="sxs-lookup"><span data-stu-id="f65c2-134">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="f65c2-135">Um cliente gRPC registrado com a fábrica do cliente e resolvido a partir de DI usará automaticamente o log configurado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f65c2-135">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="f65c2-136">Se seu aplicativo não estiver usando di, você poderá criar uma `ILoggerFactory` nova instância com [LoggerFactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span><span class="sxs-lookup"><span data-stu-id="f65c2-136">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="f65c2-137">Para acessar esse método, adicione o pacote [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) ao seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f65c2-137">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/net-client-loggerfactory-create.cs?highlight=1,8)]

### <a name="sample-logging-output"></a><span data-ttu-id="f65c2-138">Exemplo de saída de registro em log</span><span class="sxs-lookup"><span data-stu-id="f65c2-138">Sample logging output</span></span>

<span data-ttu-id="f65c2-139">Aqui está um exemplo de saída do console no `Debug` nível de um cliente gRPC:</span><span class="sxs-lookup"><span data-stu-id="f65c2-139">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

```
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="additional-resources"></a><span data-ttu-id="f65c2-140">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f65c2-140">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
