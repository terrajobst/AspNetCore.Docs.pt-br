---
title: Usar o gRPC em aplicativos de navegador
author: jamesnk
description: Saiba como configurar os serviços gRPCs em ASP.NET Core para que possam ser chamados de aplicativos de navegador usando o gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/16/2020
uid: grpc/browser
ms.openlocfilehash: 3beeffc26ffd3c2dc85bfc22a46d97d5fd78d3d0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664195"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="5e09e-103">Usar o gRPC em aplicativos de navegador</span><span class="sxs-lookup"><span data-stu-id="5e09e-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="5e09e-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5e09e-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5e09e-105">**gRPC-o suporte da Web no .NET é experimental**</span><span class="sxs-lookup"><span data-stu-id="5e09e-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="5e09e-106">gRPC-Web para .NET é um projeto experimental, não um produto confirmado.</span><span class="sxs-lookup"><span data-stu-id="5e09e-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="5e09e-107">Queremos:</span><span class="sxs-lookup"><span data-stu-id="5e09e-107">We want to:</span></span>
>
> * <span data-ttu-id="5e09e-108">Teste a nossa abordagem para implementar o gRPC-Web Works.</span><span class="sxs-lookup"><span data-stu-id="5e09e-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="5e09e-109">Obtenha comentários sobre se essa abordagem é útil para os desenvolvedores do .NET em comparação com a maneira tradicional de configurar o gRPC-Web por meio de um proxy.</span><span class="sxs-lookup"><span data-stu-id="5e09e-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="5e09e-110">Deixe comentários em [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) para garantir que criamos algo que os desenvolvedores gostam e que são produtivos com o.</span><span class="sxs-lookup"><span data-stu-id="5e09e-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="5e09e-111">Não é possível chamar um serviço gRPC HTTP/2 de um aplicativo baseado em navegador.</span><span class="sxs-lookup"><span data-stu-id="5e09e-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="5e09e-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) é um protocolo que permite que aplicativos JavaScript e de mais alto do navegador chamem serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="5e09e-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="5e09e-113">Este artigo explica como usar o gRPC-Web no .NET Core.</span><span class="sxs-lookup"><span data-stu-id="5e09e-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="5e09e-114">Configurar gRPC-Web no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5e09e-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="5e09e-115">os serviços gRPCs hospedados no ASP.NET Core podem ser configurados para dar suporte a gRPC-Web juntamente com HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="5e09e-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="5e09e-116">gRPC-Web não requer nenhuma alteração nos serviços.</span><span class="sxs-lookup"><span data-stu-id="5e09e-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="5e09e-117">A única modificação é a configuração de inicialização.</span><span class="sxs-lookup"><span data-stu-id="5e09e-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="5e09e-118">Para habilitar o gRPC-Web com um serviço de gRPC de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5e09e-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="5e09e-119">Adicione uma referência ao pacote [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="5e09e-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="5e09e-120">Configure o aplicativo para usar o gRPC-Web adicionando `AddGrpcWeb` e `UseGrpcWeb` ao *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="5e09e-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="5e09e-121">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="5e09e-121">The preceding code:</span></span>

* <span data-ttu-id="5e09e-122">Adiciona o gRPC-middleware da Web, `UseGrpcWeb`, após o roteamento e antes dos pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="5e09e-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="5e09e-123">Especifica o método de `endpoints.MapGrpcService<GreeterService>()` dá suporte a gRPC-Web com `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="5e09e-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="5e09e-124">Como alternativa, configure todos os serviços para dar suporte a gRPC-Web adicionando `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` a configuraservices.</span><span class="sxs-lookup"><span data-stu-id="5e09e-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

### <a name="grpc-web-and-cors"></a><span data-ttu-id="5e09e-125">gRPC-Web e CORS</span><span class="sxs-lookup"><span data-stu-id="5e09e-125">gRPC-Web and CORS</span></span>

<span data-ttu-id="5e09e-126">A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que servia a página da Web.</span><span class="sxs-lookup"><span data-stu-id="5e09e-126">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="5e09e-127">Essa restrição se aplica a fazer chamadas gRPC com aplicativos de navegador.</span><span class="sxs-lookup"><span data-stu-id="5e09e-127">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="5e09e-128">Por exemplo, um aplicativo de navegador servido por `https://www.contoso.com` é impedido de chamar gRPC-Web Services hospedados no `https://services.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="5e09e-128">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="5e09e-129">O CORS (compartilhamento de recursos entre origens) pode ser usado para relaxar essa restrição.</span><span class="sxs-lookup"><span data-stu-id="5e09e-129">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="5e09e-130">Para permitir que o aplicativo de navegador faça chamadas gRPC-Web entre origens, configure o [CORS no ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="5e09e-130">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="5e09e-131">Use o suporte interno a CORS e exponha cabeçalhos específicos do gRPC com <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="5e09e-131">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="5e09e-132">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="5e09e-132">The preceding code:</span></span>

* <span data-ttu-id="5e09e-133">Chama `AddCors` para adicionar serviços CORS e configura uma política CORS que expõe cabeçalhos específicos do gRPC.</span><span class="sxs-lookup"><span data-stu-id="5e09e-133">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="5e09e-134">Chama `UseCors` para adicionar o middleware CORS após o roteamento e antes dos pontos de extremidade.</span><span class="sxs-lookup"><span data-stu-id="5e09e-134">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="5e09e-135">Especifica o método de `endpoints.MapGrpcService<GreeterService>()` dá suporte a CORS com `RequiresCors`.</span><span class="sxs-lookup"><span data-stu-id="5e09e-135">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="5e09e-136">Chamar gRPC-Web do navegador</span><span class="sxs-lookup"><span data-stu-id="5e09e-136">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="5e09e-137">Os aplicativos de navegador podem usar gRPC-Web para chamar serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="5e09e-137">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="5e09e-138">Há alguns requisitos e limitações ao chamar serviços gRPCs com o gRPC-Web a partir do navegador:</span><span class="sxs-lookup"><span data-stu-id="5e09e-138">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="5e09e-139">O servidor deve ter sido configurado para dar suporte a gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="5e09e-139">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="5e09e-140">Não há suporte para streaming de cliente e chamadas de streaming bidirecionais.</span><span class="sxs-lookup"><span data-stu-id="5e09e-140">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="5e09e-141">Há suporte para o streaming do servidor.</span><span class="sxs-lookup"><span data-stu-id="5e09e-141">Server streaming is supported.</span></span>
* <span data-ttu-id="5e09e-142">A chamada de serviços gRPCs em um domínio diferente requer que o [CORS](xref:security/cors) seja configurado no servidor.</span><span class="sxs-lookup"><span data-stu-id="5e09e-142">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="5e09e-143">JavaScript gRPC-cliente Web</span><span class="sxs-lookup"><span data-stu-id="5e09e-143">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="5e09e-144">Há um cliente JavaScript gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="5e09e-144">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="5e09e-145">Para obter instruções sobre como usar o gRPC-Web do JavaScript, consulte [gravar código de cliente JavaScript com gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="5e09e-145">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="5e09e-146">Configurar o gRPC-Web com o cliente .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="5e09e-146">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="5e09e-147">O cliente .NET gRPC pode ser configurado para fazer chamadas gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="5e09e-147">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="5e09e-148">Isso é útil para aplicativos [Webassembly](xref:blazor/index#blazor-webassembly) mais úteis, que são hospedados no navegador e têm as mesmas limitações de http do código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5e09e-148">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="5e09e-149">Chamar gRPC-Web com um cliente .NET é [o mesmo que http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="5e09e-149">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="5e09e-150">A única modificação é como o canal é criado.</span><span class="sxs-lookup"><span data-stu-id="5e09e-150">The only modification is how the channel is created.</span></span>

<span data-ttu-id="5e09e-151">Para usar o gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="5e09e-151">To use gRPC-Web:</span></span>

* <span data-ttu-id="5e09e-152">Adicione uma referência ao pacote [Grpc .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="5e09e-152">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="5e09e-153">Verifique se a referência ao pacote [Grpc .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) é 2.27.0 ou superior.</span><span class="sxs-lookup"><span data-stu-id="5e09e-153">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="5e09e-154">Configure o canal para usar o `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="5e09e-154">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="5e09e-155">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="5e09e-155">The preceding code:</span></span>

* <span data-ttu-id="5e09e-156">Configura um canal para usar gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="5e09e-156">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="5e09e-157">Cria um cliente e faz uma chamada usando o canal.</span><span class="sxs-lookup"><span data-stu-id="5e09e-157">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="5e09e-158">O `GrpcWebHandler` tem as seguintes opções de configuração quando criadas:</span><span class="sxs-lookup"><span data-stu-id="5e09e-158">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="5e09e-159">**InnerHandler**: a <xref:System.Net.Http.HttpMessageHandler> subjacente que faz a solicitação HTTP gRPC, por exemplo, `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="5e09e-159">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="5e09e-160">**Modo**: um tipo de enumeração que especifica se a `Content-Type` solicitação de solicitação HTTP gRPC é `application/grpc-web` ou `application/grpc-web-text`.</span><span class="sxs-lookup"><span data-stu-id="5e09e-160">**Mode**: An enumeration type that specifies whether the gRPC HTTP request request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="5e09e-161">`GrpcWebMode.GrpcWeb` configura o conteúdo a ser enviado sem codificação.</span><span class="sxs-lookup"><span data-stu-id="5e09e-161">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="5e09e-162">Valor padrão.</span><span class="sxs-lookup"><span data-stu-id="5e09e-162">Default value.</span></span>
    * <span data-ttu-id="5e09e-163">`GrpcWebMode.GrpcWebText` configura o conteúdo para ser codificado em base64.</span><span class="sxs-lookup"><span data-stu-id="5e09e-163">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="5e09e-164">Necessário para chamadas de streaming de servidor em navegadores.</span><span class="sxs-lookup"><span data-stu-id="5e09e-164">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="5e09e-165">**HttpVersion**: o protocolo http `Version` usado para definir [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) na solicitação HTTP gRPC subjacente.</span><span class="sxs-lookup"><span data-stu-id="5e09e-165">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="5e09e-166">gRPC-Web não requer uma versão específica e não substitui o padrão, a menos que especificado.</span><span class="sxs-lookup"><span data-stu-id="5e09e-166">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5e09e-167">Clientes gRPC gerados têm métodos Sync e Async para chamar métodos unários.</span><span class="sxs-lookup"><span data-stu-id="5e09e-167">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="5e09e-168">Por exemplo, `SayHello` é sincronização e `SayHelloAsync` é Async.</span><span class="sxs-lookup"><span data-stu-id="5e09e-168">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="5e09e-169">Chamar um método de sincronização em um aplicativo Webassembly mais eficiente fará com que o aplicativo fique sem resposta.</span><span class="sxs-lookup"><span data-stu-id="5e09e-169">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="5e09e-170">Os métodos assíncronos sempre devem ser usados no Webassembly mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="5e09e-170">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5e09e-171">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="5e09e-171">Additional resources</span></span>

* [<span data-ttu-id="5e09e-172">gRPC para o projeto GitHub de clientes Web</span><span class="sxs-lookup"><span data-stu-id="5e09e-172">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
