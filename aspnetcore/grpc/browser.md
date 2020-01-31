---
title: gRPC em aplicativos de navegador
author: jamesnk
description: Saiba como configurar os serviços gRPCs em ASP.NET Core para que possam ser chamados de aplicativos de navegador usando o gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/24/2020
uid: grpc/browser
ms.openlocfilehash: 6359c3b76b3cb1ba2b6d9f9a989f64cbf4c4379d
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76830656"
---
# <a name="grpc-in-browser-apps"></a><span data-ttu-id="4f601-103">gRPC em aplicativos de navegador</span><span class="sxs-lookup"><span data-stu-id="4f601-103">gRPC in browser apps</span></span>

<span data-ttu-id="4f601-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="4f601-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4f601-105">**gRPC-o suporte da Web no .NET é experimental**</span><span class="sxs-lookup"><span data-stu-id="4f601-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="4f601-106">gRPC-Web para .NET é um projeto experimental, não um produto confirmado.</span><span class="sxs-lookup"><span data-stu-id="4f601-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="4f601-107">Queremos:</span><span class="sxs-lookup"><span data-stu-id="4f601-107">We want to:</span></span>
>
> * <span data-ttu-id="4f601-108">Teste a nossa abordagem para implementar o gRPC-Web Works.</span><span class="sxs-lookup"><span data-stu-id="4f601-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="4f601-109">Obtenha comentários sobre se essa abordagem é útil para os desenvolvedores do .NET em comparação com a maneira tradicional de configurar o gRPC-Web por meio de um proxy.</span><span class="sxs-lookup"><span data-stu-id="4f601-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="4f601-110">Deixe comentários em [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) para garantir que criamos algo que os desenvolvedores gostam e que são produtivos com o.</span><span class="sxs-lookup"><span data-stu-id="4f601-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="4f601-111">Não é possível chamar um serviço gRPC HTTP/2 de um aplicativo baseado em navegador.</span><span class="sxs-lookup"><span data-stu-id="4f601-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="4f601-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) é um protocolo que permite que aplicativos JavaScript e de mais alto do navegador chamem serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="4f601-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="4f601-113">Este artigo explica como usar o gRPC-Web no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4f601-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="4f601-114">Configurar gRPC-Web no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4f601-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="4f601-115">os serviços gRPCs hospedados no ASP.NET Core podem ser configurados para dar suporte a gRPC-Web juntamente com HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="4f601-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="4f601-116">gRPC-Web não requer nenhuma alteração nos serviços.</span><span class="sxs-lookup"><span data-stu-id="4f601-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="4f601-117">A única modificação é a configuração de inicialização.</span><span class="sxs-lookup"><span data-stu-id="4f601-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="4f601-118">Para habilitar o gRPC-Web com um serviço de gRPC de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4f601-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="4f601-119">Adicione uma referência ao pacote [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="4f601-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="4f601-120">Configure o aplicativo para usar o gRPC-Web adicionando `AddGrpcWeb` e `UseGrpcWeb` ao *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4f601-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=3,10,14)]

<span data-ttu-id="4f601-121">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="4f601-121">The preceding code:</span></span>

* <span data-ttu-id="4f601-122">Adiciona o gRPC-middleware da Web, `UseGrpcWeb`, após o roteamento e antes dos pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="4f601-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="4f601-123">Especifica o método de `endpoints.MapGrpcService<GreeterService>()` dá suporte a gRPC-Web com `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="4f601-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="4f601-124">Como alternativa, configure todos os serviços para dar suporte a gRPC-Web adicionando `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` a configuraservices.</span><span class="sxs-lookup"><span data-stu-id="4f601-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=5,12,16)]

<span data-ttu-id="4f601-125">Algumas configurações adicionais podem ser necessárias para chamar gRPC-Web do navegador, como configurar ASP.NET Core para dar suporte a CORS.</span><span class="sxs-lookup"><span data-stu-id="4f601-125">Some additional configuration may be required to call gRPC-Web from the browser, such as configuring ASP.NET Core to support CORS.</span></span> <span data-ttu-id="4f601-126">Para obter mais informações, consulte [support CORS](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="4f601-126">For more information, see [support CORS](xref:security/cors).</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="4f601-127">Chamar gRPC-Web do navegador</span><span class="sxs-lookup"><span data-stu-id="4f601-127">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="4f601-128">Os aplicativos de navegador podem usar gRPC-Web para chamar serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="4f601-128">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="4f601-129">Há alguns requisitos e limitações ao chamar serviços gRPCs com o gRPC-Web a partir do navegador:</span><span class="sxs-lookup"><span data-stu-id="4f601-129">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="4f601-130">O servidor deve ter sido configurado para dar suporte a gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="4f601-130">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="4f601-131">Não há suporte para streaming de cliente e chamadas de streaming bidirecionais.</span><span class="sxs-lookup"><span data-stu-id="4f601-131">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="4f601-132">Há suporte para o streaming do servidor.</span><span class="sxs-lookup"><span data-stu-id="4f601-132">Server streaming is supported.</span></span>
* <span data-ttu-id="4f601-133">A chamada de serviços gRPCs em um domínio diferente requer que o [CORS](xref:security/cors) seja configurado no servidor.</span><span class="sxs-lookup"><span data-stu-id="4f601-133">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="4f601-134">JavaScript gRPC-cliente Web</span><span class="sxs-lookup"><span data-stu-id="4f601-134">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="4f601-135">Há um cliente JavaScript gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="4f601-135">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="4f601-136">Para obter instruções sobre como usar o gRPC-Web do JavaScript, consulte [gravar código de cliente JavaScript com gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="4f601-136">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="4f601-137">Configurar o gRPC-Web com o cliente .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="4f601-137">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="4f601-138">O cliente .NET gRPC pode ser configurado para fazer chamadas gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="4f601-138">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="4f601-139">Isso é útil para aplicativos [Webassembly](xref:blazor/index#blazor-webassembly) mais úteis, que são hospedados no navegador e têm as mesmas limitações de http do código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4f601-139">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="4f601-140">Chamar gRPC-Web com um cliente .NET é [o mesmo que http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="4f601-140">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="4f601-141">A única modificação é como o canal é criado.</span><span class="sxs-lookup"><span data-stu-id="4f601-141">The only modification is how the channel is created.</span></span>

<span data-ttu-id="4f601-142">Para usar o gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="4f601-142">To use gRPC-Web:</span></span>

* <span data-ttu-id="4f601-143">Adicione uma referência ao pacote [Grpc .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="4f601-143">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="4f601-144">Configure o canal para usar o `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="4f601-144">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="4f601-145">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="4f601-145">The preceding code:</span></span>

* <span data-ttu-id="4f601-146">Configura um canal para usar gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="4f601-146">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="4f601-147">Cria um cliente e faz uma chamada usando o canal.</span><span class="sxs-lookup"><span data-stu-id="4f601-147">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="4f601-148">O `GrpcWebHandler` tem as seguintes opções de configuração quando criadas:</span><span class="sxs-lookup"><span data-stu-id="4f601-148">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="4f601-149">**InnerHandler**: o <xref:System.Net.Http.HttpMessageHandler> subjacente que faz a chamada http, por exemplo, `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="4f601-149">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the HTTP call, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="4f601-150">**Modo**: `GrpcWebMode` enum.</span><span class="sxs-lookup"><span data-stu-id="4f601-150">**Mode**: `GrpcWebMode` enum.</span></span> <span data-ttu-id="4f601-151">`GrpcWebMode.GrpcWebText` configura o conteúdo para ser codificado em base64, o que é necessário para dar suporte a chamadas de streaming de servidor.</span><span class="sxs-lookup"><span data-stu-id="4f601-151">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded, which is required to support server streaming calls.</span></span>
* <span data-ttu-id="4f601-152">**HttpVersion**: `Version`de protocolo http.</span><span class="sxs-lookup"><span data-stu-id="4f601-152">**HttpVersion**: HTTP protocol `Version`.</span></span> <span data-ttu-id="4f601-153">gRPC-Web não requer um protocolo específico e não especifica um ao fazer uma solicitação, a menos que seja configurado.</span><span class="sxs-lookup"><span data-stu-id="4f601-153">gRPC-Web doesn't require a specific protocol and won't specify one when making a request unless configured.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4f601-154">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4f601-154">Additional resources</span></span>

* [<span data-ttu-id="4f601-155">gRPC para o projeto GitHub de clientes Web</span><span class="sxs-lookup"><span data-stu-id="4f601-155">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
