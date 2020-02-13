---
title: Usar o gRPC em aplicativos de navegador
author: jamesnk
description: Saiba como configurar os serviços gRPCs em ASP.NET Core para que possam ser chamados de aplicativos de navegador usando o gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/10/2020
uid: grpc/browser
ms.openlocfilehash: 333fc8c4277bbac47042d4904c276e963186914a
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172272"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="29b12-103">Usar o gRPC em aplicativos de navegador</span><span class="sxs-lookup"><span data-stu-id="29b12-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="29b12-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="29b12-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="29b12-105">**gRPC-o suporte da Web no .NET é experimental**</span><span class="sxs-lookup"><span data-stu-id="29b12-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="29b12-106">gRPC-Web para .NET é um projeto experimental, não um produto confirmado.</span><span class="sxs-lookup"><span data-stu-id="29b12-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="29b12-107">Queremos:</span><span class="sxs-lookup"><span data-stu-id="29b12-107">We want to:</span></span>
>
> * <span data-ttu-id="29b12-108">Teste a nossa abordagem para implementar o gRPC-Web Works.</span><span class="sxs-lookup"><span data-stu-id="29b12-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="29b12-109">Obtenha comentários sobre se essa abordagem é útil para os desenvolvedores do .NET em comparação com a maneira tradicional de configurar o gRPC-Web por meio de um proxy.</span><span class="sxs-lookup"><span data-stu-id="29b12-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="29b12-110">Deixe comentários em [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) para garantir que criamos algo que os desenvolvedores gostam e que são produtivos com o.</span><span class="sxs-lookup"><span data-stu-id="29b12-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="29b12-111">Não é possível chamar um serviço gRPC HTTP/2 de um aplicativo baseado em navegador.</span><span class="sxs-lookup"><span data-stu-id="29b12-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="29b12-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) é um protocolo que permite que aplicativos JavaScript e de mais alto do navegador chamem serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="29b12-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="29b12-113">Este artigo explica como usar o gRPC-Web no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="29b12-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="29b12-114">Configurar gRPC-Web no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29b12-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="29b12-115">os serviços gRPCs hospedados no ASP.NET Core podem ser configurados para dar suporte a gRPC-Web juntamente com HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="29b12-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="29b12-116">gRPC-Web não requer nenhuma alteração nos serviços.</span><span class="sxs-lookup"><span data-stu-id="29b12-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="29b12-117">A única modificação é a configuração de inicialização.</span><span class="sxs-lookup"><span data-stu-id="29b12-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="29b12-118">Para habilitar o gRPC-Web com um serviço de gRPC de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="29b12-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="29b12-119">Adicione uma referência ao pacote [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="29b12-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="29b12-120">Configure o aplicativo para usar o gRPC-Web adicionando `AddGrpcWeb` e `UseGrpcWeb` ao *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="29b12-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="29b12-121">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="29b12-121">The preceding code:</span></span>

* <span data-ttu-id="29b12-122">Adiciona o gRPC-middleware da Web, `UseGrpcWeb`, após o roteamento e antes dos pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="29b12-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="29b12-123">Especifica o método de `endpoints.MapGrpcService<GreeterService>()` dá suporte a gRPC-Web com `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="29b12-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="29b12-124">Como alternativa, configure todos os serviços para dar suporte a gRPC-Web adicionando `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` a configuraservices.</span><span class="sxs-lookup"><span data-stu-id="29b12-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

<span data-ttu-id="29b12-125">Algumas configurações adicionais podem ser necessárias para chamar gRPC-Web do navegador, como configurar ASP.NET Core para dar suporte a CORS.</span><span class="sxs-lookup"><span data-stu-id="29b12-125">Some additional configuration may be required to call gRPC-Web from the browser, such as configuring ASP.NET Core to support CORS.</span></span> <span data-ttu-id="29b12-126">Para obter mais informações, consulte [support CORS](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="29b12-126">For more information, see [support CORS](xref:security/cors).</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="29b12-127">Chamar gRPC-Web do navegador</span><span class="sxs-lookup"><span data-stu-id="29b12-127">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="29b12-128">Os aplicativos de navegador podem usar gRPC-Web para chamar serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="29b12-128">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="29b12-129">Há alguns requisitos e limitações ao chamar serviços gRPCs com o gRPC-Web a partir do navegador:</span><span class="sxs-lookup"><span data-stu-id="29b12-129">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="29b12-130">O servidor deve ter sido configurado para dar suporte a gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="29b12-130">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="29b12-131">Não há suporte para streaming de cliente e chamadas de streaming bidirecionais.</span><span class="sxs-lookup"><span data-stu-id="29b12-131">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="29b12-132">Há suporte para o streaming do servidor.</span><span class="sxs-lookup"><span data-stu-id="29b12-132">Server streaming is supported.</span></span>
* <span data-ttu-id="29b12-133">A chamada de serviços gRPCs em um domínio diferente requer que o [CORS](xref:security/cors) seja configurado no servidor.</span><span class="sxs-lookup"><span data-stu-id="29b12-133">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="29b12-134">JavaScript gRPC-cliente Web</span><span class="sxs-lookup"><span data-stu-id="29b12-134">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="29b12-135">Há um cliente JavaScript gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="29b12-135">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="29b12-136">Para obter instruções sobre como usar o gRPC-Web do JavaScript, consulte [gravar código de cliente JavaScript com gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="29b12-136">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="29b12-137">Configurar o gRPC-Web com o cliente .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="29b12-137">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="29b12-138">O cliente .NET gRPC pode ser configurado para fazer chamadas gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="29b12-138">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="29b12-139">Isso é útil para aplicativos [Webassembly](xref:blazor/index#blazor-webassembly) mais úteis, que são hospedados no navegador e têm as mesmas limitações de http do código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="29b12-139">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="29b12-140">Chamar gRPC-Web com um cliente .NET é [o mesmo que http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="29b12-140">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="29b12-141">A única modificação é como o canal é criado.</span><span class="sxs-lookup"><span data-stu-id="29b12-141">The only modification is how the channel is created.</span></span>

<span data-ttu-id="29b12-142">Para usar o gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="29b12-142">To use gRPC-Web:</span></span>

* <span data-ttu-id="29b12-143">Adicione uma referência ao pacote [Grpc .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="29b12-143">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="29b12-144">Verifique se a referência ao pacote [Grpc .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) é 2.27.0 ou superior.</span><span class="sxs-lookup"><span data-stu-id="29b12-144">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="29b12-145">Configure o canal para usar o `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="29b12-145">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="29b12-146">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="29b12-146">The preceding code:</span></span>

* <span data-ttu-id="29b12-147">Configura um canal para usar gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="29b12-147">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="29b12-148">Cria um cliente e faz uma chamada usando o canal.</span><span class="sxs-lookup"><span data-stu-id="29b12-148">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="29b12-149">O `GrpcWebHandler` tem as seguintes opções de configuração quando criadas:</span><span class="sxs-lookup"><span data-stu-id="29b12-149">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="29b12-150">**InnerHandler**: a <xref:System.Net.Http.HttpMessageHandler> subjacente que faz a solicitação HTTP gRPC, por exemplo, `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="29b12-150">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="29b12-151">**Modo**: um tipo de enumeração que especifica se a `Content-Type` solicitação de solicitação HTTP gRPC é `application/grpc-web` ou `application/grpc-web-text`.</span><span class="sxs-lookup"><span data-stu-id="29b12-151">**Mode**: An enumeration type that specifies whether the gRPC HTTP request request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="29b12-152">`GrpcWebMode.GrpcWeb` configura o conteúdo a ser enviado sem codificação.</span><span class="sxs-lookup"><span data-stu-id="29b12-152">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="29b12-153">Valor padrão.</span><span class="sxs-lookup"><span data-stu-id="29b12-153">Default value.</span></span>
    * <span data-ttu-id="29b12-154">`GrpcWebMode.GrpcWebText` configura o conteúdo para ser codificado em base64.</span><span class="sxs-lookup"><span data-stu-id="29b12-154">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="29b12-155">Necessário para chamadas de streaming de servidor em navegadores.</span><span class="sxs-lookup"><span data-stu-id="29b12-155">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="29b12-156">**HttpVersion**: o protocolo http `Version` usado para definir [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) na solicitação HTTP gRPC subjacente.</span><span class="sxs-lookup"><span data-stu-id="29b12-156">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="29b12-157">gRPC-Web não requer uma versão específica e não substitui o padrão, a menos que especificado.</span><span class="sxs-lookup"><span data-stu-id="29b12-157">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="29b12-158">Clientes gRPC gerados têm métodos Sync e Async para chamar métodos unários.</span><span class="sxs-lookup"><span data-stu-id="29b12-158">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="29b12-159">Por exemplo, `SayHello` é sincronização e `SayHelloAsync` é Async.</span><span class="sxs-lookup"><span data-stu-id="29b12-159">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="29b12-160">Chamar um método de sincronização em um aplicativo Webassembly mais eficiente fará com que o aplicativo fique sem resposta.</span><span class="sxs-lookup"><span data-stu-id="29b12-160">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="29b12-161">Os métodos assíncronos sempre devem ser usados no Webassembly mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="29b12-161">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="29b12-162">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="29b12-162">Additional resources</span></span>

* [<span data-ttu-id="29b12-163">gRPC para o projeto GitHub de clientes Web</span><span class="sxs-lookup"><span data-stu-id="29b12-163">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
