---
title: OWIN (Open Web Interface para .NET) com o ASP.NET Core
author: ardalis
description: Descubra como o ASP.NET Core dá suporte ao OWIN (Open Web Interface para .NET), que permite que aplicativos Web sejam desacoplados de servidores Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
uid: fundamentals/owin
ms.openlocfilehash: 7edb4db026f1b778d43ac72883690a0b2a18ee31
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814890"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="889c3-103">OWIN (Open Web Interface para .NET) com o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="889c3-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="889c3-104">Por [Steve Smith](https://ardalis.com/) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="889c3-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="889c3-105">O ASP.NET Core dá suporte para OWIN (Open Web Interface para .NET).</span><span class="sxs-lookup"><span data-stu-id="889c3-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="889c3-106">O OWIN permite que os aplicativos Web sejam separados dos servidores Web.</span><span class="sxs-lookup"><span data-stu-id="889c3-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="889c3-107">Ele define uma maneira padrão de usar o middleware em um pipeline para manipular solicitações e respostas associadas.</span><span class="sxs-lookup"><span data-stu-id="889c3-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="889c3-108">O middleware e os aplicativos ASP.NET Core podem interoperar com aplicativos, servidores e middleware baseados no OWIN.</span><span class="sxs-lookup"><span data-stu-id="889c3-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="889c3-109">O OWIN fornece uma camada de desacoplamento que permite duas estruturas com modelos de objeto diferentes para ser usadas juntas.</span><span class="sxs-lookup"><span data-stu-id="889c3-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="889c3-110">O pacote `Microsoft.AspNetCore.Owin` fornece duas implementações de adaptador:</span><span class="sxs-lookup"><span data-stu-id="889c3-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="889c3-111">ASP.NET Core para OWIN</span><span class="sxs-lookup"><span data-stu-id="889c3-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="889c3-112">OWIN para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="889c3-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="889c3-113">Isso permite que o ASP.NET Core seja hospedado em um servidor/host compatível com OWIN ou que outros componentes compatíveis com OWIN sejam executados no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="889c3-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="889c3-114">O uso desses adaptadores implica um custo de desempenho.</span><span class="sxs-lookup"><span data-stu-id="889c3-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="889c3-115">Os aplicativos que usam somente componentes do ASP.NET Core não devem usar o pacote `Microsoft.AspNetCore.Owin` ou os adaptadores.</span><span class="sxs-lookup"><span data-stu-id="889c3-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="889c3-116">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="889c3-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="889c3-117">Executando o middleware do OWIN no pipeline do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="889c3-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="889c3-118">O suporte ao OWIN do ASP.NET Core é implantado como parte do pacote `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="889c3-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="889c3-119">Importe o suporte ao OWIN para seu projeto instalando esse pacote.</span><span class="sxs-lookup"><span data-stu-id="889c3-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="889c3-120">O middleware do OWIN está em conformidade com a [especificação do OWIN](https://owin.org/spec/spec/owin-1.0.0.html), que exige uma interface `Func<IDictionary<string, object>, Task>` e a definição de chaves específicas (como `owin.ResponseBody`).</span><span class="sxs-lookup"><span data-stu-id="889c3-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="889c3-121">O seguinte middleware simples do OWIN exibe “Olá, Mundo”:</span><span class="sxs-lookup"><span data-stu-id="889c3-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="889c3-122">A assinatura de exemplo retorna uma `Task` e aceita uma `IDictionary<string, object>`, conforme solicitado pelo OWIN.</span><span class="sxs-lookup"><span data-stu-id="889c3-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="889c3-123">O código a seguir mostra como adicionar o middleware `OwinHello` (mostrado acima) ao pipeline do ASP.NET Core com o método de extensão `UseOwin`.</span><span class="sxs-lookup"><span data-stu-id="889c3-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="889c3-124">Configure outras ações a serem executadas no pipeline do OWIN.</span><span class="sxs-lookup"><span data-stu-id="889c3-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="889c3-125">Os cabeçalhos de resposta devem ser modificados apenas antes da primeira gravação no fluxo de resposta.</span><span class="sxs-lookup"><span data-stu-id="889c3-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="889c3-126">Não é recomendado fazer várias chamadas a `UseOwin` por motivos de desempenho.</span><span class="sxs-lookup"><span data-stu-id="889c3-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="889c3-127">Os componentes do OWIN funcionarão melhor se forem agrupados.</span><span class="sxs-lookup"><span data-stu-id="889c3-127">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(async (next) =>
    {
        // do something before
        await OwinHello(new OwinEnvironment(HttpContext));
        // do something after
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a><span data-ttu-id="889c3-128">Usando a hospedagem do ASP.NET Core em um servidor baseado no OWIN</span><span class="sxs-lookup"><span data-stu-id="889c3-128">Using ASP.NET Core Hosting on an OWIN-based server</span></span>

<span data-ttu-id="889c3-129">Os servidores baseados no OWIN podem hospedar aplicativos ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="889c3-129">OWIN-based servers can host ASP.NET Core apps.</span></span> <span data-ttu-id="889c3-130">Um desses servidores é o [Nowin](https://github.com/Bobris/Nowin), um servidor Web do OWIN no .NET.</span><span class="sxs-lookup"><span data-stu-id="889c3-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="889c3-131">Na amostra para este artigo, incluí um projeto que referencia o Nowin e usa-o para criar um `IServer` com capacidade de auto-hospedar o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="889c3-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="889c3-132">O `IServer` é uma interface que requer uma propriedade `Features` e um método `Start`.</span><span class="sxs-lookup"><span data-stu-id="889c3-132">`IServer` is an interface that requires a `Features` property and a `Start` method.</span></span>

<span data-ttu-id="889c3-133">`Start` é responsável por configurar e iniciar o servidor, que, nesse caso, é feito por meio de uma série de chamadas à API fluente que definem endereços analisados do IServerAddressesFeature.</span><span class="sxs-lookup"><span data-stu-id="889c3-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="889c3-134">Observe que a configuração fluente da variável `_builder` especifica que as solicitações serão manipuladas pelo `appFunc` definido anteriormente no método.</span><span class="sxs-lookup"><span data-stu-id="889c3-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="889c3-135">Esse `Func` é chamado em cada solicitação para processar as solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="889c3-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="889c3-136">Adicionaremos também uma extensão `IWebHostBuilder` para facilitar a adição e configuração do servidor Nowin.</span><span class="sxs-lookup"><span data-stu-id="889c3-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting.Server;
using Microsoft.Extensions.DependencyInjection;
using Nowin;
using NowinSample;

namespace Microsoft.AspNetCore.Hosting
{
    public static class NowinWebHostBuilderExtensions
    {
        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder)
        {
            return builder.ConfigureServices(services =>
            {
                services.AddSingleton<IServer, NowinServer>();
            });
        }

        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder, Action<ServerBuilder> configure)
        {
            builder.ConfigureServices(services =>
            {
                services.Configure(configure);
            });
            return builder.UseNowin();
        }
    }
}
```

<span data-ttu-id="889c3-137">Com isso em vigor, invoque a extensão no *Program.cs* para executar um aplicativo ASP.NET Core usando este servidor personalizado:</span><span class="sxs-lookup"><span data-stu-id="889c3-137">With this in place, invoke the extension in *Program.cs* to run an ASP.NET Core app using this custom server:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace NowinSample
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseNowin()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}
```

<span data-ttu-id="889c3-138">Saiba mais sobre os [Servidores ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="889c3-138">Learn more about [ASP.NET Core Servers](xref:fundamentals/servers/index).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="889c3-139">Executar o ASP.NET Core em um servidor baseado no OWIN e usar seu suporte do WebSockets</span><span class="sxs-lookup"><span data-stu-id="889c3-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="889c3-140">Outro exemplo de como os recursos dos servidores baseados no OWIN podem ser aproveitados pelo ASP.NET Core é o acesso a recursos como o WebSockets.</span><span class="sxs-lookup"><span data-stu-id="889c3-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="889c3-141">O servidor Web do OWIN no .NET usado no exemplo anterior tem suporte para Soquetes da Web internos, que podem ser aproveitados por um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="889c3-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="889c3-142">O exemplo abaixo mostra um aplicativo Web simples que dá suporte a Soquetes da Web e repete tudo o que foi enviado ao servidor por meio do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="889c3-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

<span data-ttu-id="889c3-143">Essa [amostra](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) é configurada usando o mesmo `NowinServer` que o anterior – a única diferença está em como o aplicativo é configurado em seu método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="889c3-143">This [sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="889c3-144">Um teste usando [um cliente simples do WebSocket](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstra o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="889c3-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Cliente de teste de Soquete da Web](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="889c3-146">Ambiente do OWIN</span><span class="sxs-lookup"><span data-stu-id="889c3-146">OWIN environment</span></span>

<span data-ttu-id="889c3-147">Você pode construir um ambiente do OWIN usando o `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="889c3-147">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="889c3-148">Chaves do OWIN</span><span class="sxs-lookup"><span data-stu-id="889c3-148">OWIN keys</span></span>

<span data-ttu-id="889c3-149">O OWIN depende de um objeto `IDictionary<string,object>` para transmitir informações em uma troca de Solicitação/Resposta HTTP.</span><span class="sxs-lookup"><span data-stu-id="889c3-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="889c3-150">O ASP.NET Core implementa as chaves listadas abaixo.</span><span class="sxs-lookup"><span data-stu-id="889c3-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="889c3-151">Consulte a [especificação primária, extensões](https://owin.org/#spec) e as [Diretrizes de chaves do OWIN e chaves comuns](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="889c3-151">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="889c3-152">Dados de solicitação (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="889c3-152">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="889c3-153">Chave</span><span class="sxs-lookup"><span data-stu-id="889c3-153">Key</span></span>               | <span data-ttu-id="889c3-154">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="889c3-154">Value (type)</span></span> | <span data-ttu-id="889c3-155">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="889c3-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="889c3-156">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="889c3-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="889c3-157">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="889c3-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="889c3-158">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="889c3-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="889c3-159">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="889c3-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="889c3-160">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="889c3-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="889c3-161">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="889c3-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="889c3-162">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="889c3-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="889c3-163">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="889c3-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="889c3-164">Dados de solicitação (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="889c3-164">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="889c3-165">Chave</span><span class="sxs-lookup"><span data-stu-id="889c3-165">Key</span></span>               | <span data-ttu-id="889c3-166">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="889c3-166">Value (type)</span></span> | <span data-ttu-id="889c3-167">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="889c3-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="889c3-168">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="889c3-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="889c3-169">Opcional</span><span class="sxs-lookup"><span data-stu-id="889c3-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="889c3-170">Dados de resposta (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="889c3-170">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="889c3-171">Chave</span><span class="sxs-lookup"><span data-stu-id="889c3-171">Key</span></span>               | <span data-ttu-id="889c3-172">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="889c3-172">Value (type)</span></span> | <span data-ttu-id="889c3-173">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="889c3-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="889c3-174">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="889c3-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="889c3-175">Opcional</span><span class="sxs-lookup"><span data-stu-id="889c3-175">Optional</span></span> |
| <span data-ttu-id="889c3-176">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="889c3-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="889c3-177">Opcional</span><span class="sxs-lookup"><span data-stu-id="889c3-177">Optional</span></span> |
| <span data-ttu-id="889c3-178">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="889c3-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="889c3-179">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="889c3-179">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="889c3-180">Outros dados (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="889c3-180">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="889c3-181">Chave</span><span class="sxs-lookup"><span data-stu-id="889c3-181">Key</span></span>               | <span data-ttu-id="889c3-182">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="889c3-182">Value (type)</span></span> | <span data-ttu-id="889c3-183">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="889c3-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="889c3-184">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="889c3-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="889c3-185">owin.Version</span><span class="sxs-lookup"><span data-stu-id="889c3-185">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="889c3-186">Chaves comuns</span><span class="sxs-lookup"><span data-stu-id="889c3-186">Common keys</span></span>

| <span data-ttu-id="889c3-187">Chave</span><span class="sxs-lookup"><span data-stu-id="889c3-187">Key</span></span>               | <span data-ttu-id="889c3-188">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="889c3-188">Value (type)</span></span> | <span data-ttu-id="889c3-189">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="889c3-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="889c3-190">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="889c3-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="889c3-191">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="889c3-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="889c3-192">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="889c3-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="889c3-193">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="889c3-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="889c3-194">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="889c3-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="889c3-195">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="889c3-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="889c3-196">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="889c3-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="889c3-197">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="889c3-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="889c3-198">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="889c3-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="889c3-199">Chave</span><span class="sxs-lookup"><span data-stu-id="889c3-199">Key</span></span>               | <span data-ttu-id="889c3-200">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="889c3-200">Value (type)</span></span> | <span data-ttu-id="889c3-201">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="889c3-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="889c3-202">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="889c3-202">sendfile.SendAsync</span></span> | <span data-ttu-id="889c3-203">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="889c3-203">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="889c3-204">Por solicitação</span><span class="sxs-lookup"><span data-stu-id="889c3-204">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="889c3-205">Opaque v0.3.0</span><span class="sxs-lookup"><span data-stu-id="889c3-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="889c3-206">Chave</span><span class="sxs-lookup"><span data-stu-id="889c3-206">Key</span></span>               | <span data-ttu-id="889c3-207">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="889c3-207">Value (type)</span></span> | <span data-ttu-id="889c3-208">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="889c3-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="889c3-209">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="889c3-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="889c3-210">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="889c3-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="889c3-211">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="889c3-211">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="889c3-212">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="889c3-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="889c3-213">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="889c3-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="889c3-214">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="889c3-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="889c3-215">Chave</span><span class="sxs-lookup"><span data-stu-id="889c3-215">Key</span></span>               | <span data-ttu-id="889c3-216">Valor (tipo)</span><span class="sxs-lookup"><span data-stu-id="889c3-216">Value (type)</span></span> | <span data-ttu-id="889c3-217">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="889c3-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="889c3-218">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="889c3-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="889c3-219">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="889c3-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="889c3-220">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="889c3-220">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="889c3-221">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="889c3-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="889c3-222">Sem especificação</span><span class="sxs-lookup"><span data-stu-id="889c3-222">Non-spec</span></span> |
| <span data-ttu-id="889c3-223">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="889c3-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="889c3-224">Consulte a etapa 5.5 [RFC6455 Seção 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2)</span><span class="sxs-lookup"><span data-stu-id="889c3-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="889c3-225">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="889c3-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="889c3-226">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="889c3-226">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="889c3-227">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="889c3-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="889c3-228">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="889c3-228">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="889c3-229">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="889c3-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="889c3-230">Consulte [Assinatura do delegado](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="889c3-230">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="889c3-231">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="889c3-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="889c3-232">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="889c3-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="889c3-233">Opcional</span><span class="sxs-lookup"><span data-stu-id="889c3-233">Optional</span></span> |
| <span data-ttu-id="889c3-234">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="889c3-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="889c3-235">Opcional</span><span class="sxs-lookup"><span data-stu-id="889c3-235">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="889c3-236">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="889c3-236">Additional resources</span></span>

* [<span data-ttu-id="889c3-237">Middleware</span><span class="sxs-lookup"><span data-stu-id="889c3-237">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="889c3-238">Servidores</span><span class="sxs-lookup"><span data-stu-id="889c3-238">Servers</span></span>](xref:fundamentals/servers/index)
