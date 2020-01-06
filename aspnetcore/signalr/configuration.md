---
title: Configuração de SignalR de ASP.NET Core
author: bradygaster
description: Saiba como configurar ASP.NET Core SignalR aplicativos.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/10/2019
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: c225ff88110dc17185a430ac1c422d2433306115
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358106"
---
# <a name="aspnet-core-opno-locsignalr-configuration"></a><span data-ttu-id="6be2f-103">Configuração de SignalR de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6be2f-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6be2f-104">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6be2f-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6be2f-105">ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6be2f-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6be2f-106">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="6be2f-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6be2f-107">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6be2f-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="6be2f-108">`AddJsonProtocol` pode ser adicionado após [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6be2f-109">O método `AddJsonProtocol` usa um delegado que recebe um objeto `options`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6be2f-110">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um objeto `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="6be2f-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6be2f-111">Para obter mais informações, consulte a [documentação System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="6be2f-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="6be2f-112">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="6be2f-113">No cliente .NET, o mesmo método de extensão `AddJsonProtocol` existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6be2f-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6be2f-114">O namespace de `Microsoft.Extensions.DependencyInjection` deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="6be2f-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="6be2f-115">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6be2f-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="6be2f-116">Alternar para Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="6be2f-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="6be2f-117">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json`, consulte [mudar para Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="6be2f-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6be2f-118">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="6be2f-118">MessagePack serialization options</span></span>

<span data-ttu-id="6be2f-119">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6be2f-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6be2f-120">Consulte [MessagePack em SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="6be2f-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6be2f-121">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6be2f-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6be2f-122">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="6be2f-122">Configure server options</span></span>

<span data-ttu-id="6be2f-123">A tabela a seguir descreve as opções para configurar os hubs de SignalR:</span><span class="sxs-lookup"><span data-stu-id="6be2f-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6be2f-124">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-124">Option</span></span> | <span data-ttu-id="6be2f-125">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-125">Default Value</span></span> | <span data-ttu-id="6be2f-126">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6be2f-127">30 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-127">30 seconds</span></span> | <span data-ttu-id="6be2f-128">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="6be2f-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6be2f-129">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6be2f-130">O valor recomendado é o dobro do valor de `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6be2f-131">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-131">15 seconds</span></span> | <span data-ttu-id="6be2f-132">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="6be2f-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6be2f-133">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6be2f-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6be2f-134">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6be2f-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6be2f-135">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-135">15 seconds</span></span> | <span data-ttu-id="6be2f-136">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="6be2f-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6be2f-137">Ao alterar `KeepAliveInterval`, altere a configuração `ServerTimeout`/`serverTimeoutInMilliseconds` no cliente.</span><span class="sxs-lookup"><span data-stu-id="6be2f-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6be2f-138">O valor de `serverTimeoutInMilliseconds` recomendado `ServerTimeout`/é o dobro do valor `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6be2f-139">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="6be2f-139">All installed protocols</span></span> | <span data-ttu-id="6be2f-140">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="6be2f-140">Protocols supported by this hub.</span></span> <span data-ttu-id="6be2f-141">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="6be2f-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6be2f-142">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="6be2f-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6be2f-143">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="6be2f-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="6be2f-144">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="6be2f-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="6be2f-145">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="6be2f-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="6be2f-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="6be2f-146">32 KB</span></span> | <span data-ttu-id="6be2f-147">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="6be2f-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="6be2f-148">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a chamada `AddSignalR` em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="6be2f-149">As opções para um único Hub substituem as opções globais fornecidas em `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="6be2f-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6be2f-150">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="6be2f-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6be2f-151">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="6be2f-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6be2f-152">Essas opções são configuradas passando um delegado para [MapHub\<t >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="6be2f-153">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="6be2f-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6be2f-154">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-154">Option</span></span> | <span data-ttu-id="6be2f-155">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-155">Default Value</span></span> | <span data-ttu-id="6be2f-156">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6be2f-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="6be2f-157">32 KB</span></span> | <span data-ttu-id="6be2f-158">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="6be2f-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="6be2f-159">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6be2f-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6be2f-160">Os dados são automaticamente coletados dos atributos `Authorize` aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="6be2f-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6be2f-161">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="6be2f-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6be2f-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="6be2f-162">32 KB</span></span> | <span data-ttu-id="6be2f-163">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="6be2f-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="6be2f-164">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6be2f-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6be2f-165">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="6be2f-165">All Transports are enabled.</span></span> | <span data-ttu-id="6be2f-166">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que podem restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="6be2f-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6be2f-167">Consulte a seguir.</span><span class="sxs-lookup"><span data-stu-id="6be2f-167">See below.</span></span> | <span data-ttu-id="6be2f-168">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="6be2f-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6be2f-169">Consulte a seguir.</span><span class="sxs-lookup"><span data-stu-id="6be2f-169">See below.</span></span> | <span data-ttu-id="6be2f-170">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6be2f-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="6be2f-171">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a propriedade `LongPolling`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6be2f-172">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-172">Option</span></span> | <span data-ttu-id="6be2f-173">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-173">Default Value</span></span> | <span data-ttu-id="6be2f-174">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6be2f-175">90 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-175">90 seconds</span></span> | <span data-ttu-id="6be2f-176">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="6be2f-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6be2f-177">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="6be2f-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6be2f-178">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a propriedade `WebSockets`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6be2f-179">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-179">Option</span></span> | <span data-ttu-id="6be2f-180">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-180">Default Value</span></span> | <span data-ttu-id="6be2f-181">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6be2f-182">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-182">5 seconds</span></span> | <span data-ttu-id="6be2f-183">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="6be2f-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6be2f-184">Um delegado que pode ser usado para definir o cabeçalho de `Sec-WebSocket-Protocol` como um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6be2f-185">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6be2f-186">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="6be2f-186">Configure client options</span></span>

<span data-ttu-id="6be2f-187">As opções de cliente podem ser configuradas no tipo de `HubConnectionBuilder` (disponível nos clientes .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6be2f-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6be2f-188">Ele também está disponível no cliente Java, mas a subclasse `HttpHubConnectionBuilder` é o que contém as opções de configuração do Builder, bem como o `HubConnection` em si.</span><span class="sxs-lookup"><span data-stu-id="6be2f-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6be2f-189">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="6be2f-189">Configure logging</span></span>

<span data-ttu-id="6be2f-190">O registro em log é configurado no cliente .NET usando o método `ConfigureLogging`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6be2f-191">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6be2f-192">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="6be2f-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6be2f-193">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="6be2f-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6be2f-194">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="6be2f-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6be2f-195">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="6be2f-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6be2f-196">Chame o método de extensão `AddConsole`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6be2f-197">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="6be2f-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6be2f-198">Forneça um valor `LogLevel` indicando o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="6be2f-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6be2f-199">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="6be2f-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="6be2f-200">Em vez de um valor `LogLevel`, você também pode fornecer um valor de `string` que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="6be2f-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="6be2f-201">Isso é útil ao configurar SignalR log em ambientes em que você não tem acesso às constantes `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="6be2f-202">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="6be2f-202">The following table lists the available log levels.</span></span> <span data-ttu-id="6be2f-203">O valor que você fornece para `configureLogging` define o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="6be2f-204">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="6be2f-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="6be2f-205">Cadeia de caracteres</span><span class="sxs-lookup"><span data-stu-id="6be2f-205">String</span></span>                      | <span data-ttu-id="6be2f-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="6be2f-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="6be2f-207">`info` **ou** `information`</span><span class="sxs-lookup"><span data-stu-id="6be2f-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="6be2f-208">`warn` **ou** `warning`</span><span class="sxs-lookup"><span data-stu-id="6be2f-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="6be2f-209">Para desabilitar totalmente o registro em log, especifique `signalR.LogLevel.None` no método `configureLogging`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6be2f-210">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico deSignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6be2f-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6be2f-211">O cliente Java SignalR usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="6be2f-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6be2f-212">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="6be2f-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6be2f-213">O trecho de código a seguir mostra como usar `java.util.logging` com o cliente SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="6be2f-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6be2f-214">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="6be2f-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6be2f-215">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="6be2f-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6be2f-216">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="6be2f-216">Configure allowed transports</span></span>

<span data-ttu-id="6be2f-217">Os transportes usados pelo SignalR podem ser configurados na chamada `WithUrl` (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6be2f-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6be2f-218">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="6be2f-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6be2f-219">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="6be2f-219">All transports are enabled by default.</span></span>

<span data-ttu-id="6be2f-220">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="6be2f-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6be2f-221">No cliente JavaScript, os transportes são configurados definindo o campo `transport` no objeto de opções fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6be2f-222">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="6be2f-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="6be2f-223">No cliente Java, o transporte é selecionado com o método `withTransport` no `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="6be2f-224">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6be2f-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6be2f-225">O cliente Java SignalR ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="6be2f-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6be2f-226">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="6be2f-226">Configure bearer authentication</span></span>

<span data-ttu-id="6be2f-227">Para fornecer dados de autenticação junto com SignalR solicitações, use a opção `AccessTokenProvider` (`accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6be2f-228">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o cabeçalho `Authorization` com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="6be2f-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6be2f-229">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="6be2f-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6be2f-230">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6be2f-231">No cliente .NET, a opção `AccessTokenProvider` pode ser especificada usando o delegado Options no `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6be2f-232">No cliente JavaScript, o token de acesso é configurado definindo o campo `accessTokenFactory` no objeto Options no `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="6be2f-233">No cliente Java SignalR, você pode configurar um token de portador para usar na autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6be2f-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6be2f-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [única\<cadeia de caracteres>](https://reactivex.io/documentation/single.html)[de RxJava](https://github.com/ReactiveX/RxJava).</span><span class="sxs-lookup"><span data-stu-id="6be2f-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6be2f-235">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="6be2f-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6be2f-236">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6be2f-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6be2f-237">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no próprio objeto `HubConnection`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="6be2f-238">.NET</span><span class="sxs-lookup"><span data-stu-id="6be2f-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6be2f-239">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-239">Option</span></span> | <span data-ttu-id="6be2f-240">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-240">Default value</span></span> | <span data-ttu-id="6be2f-241">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6be2f-242">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6be2f-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6be2f-243">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-243">Timeout for server activity.</span></span> <span data-ttu-id="6be2f-244">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento de `Closed` (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6be2f-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6be2f-245">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6be2f-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6be2f-246">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6be2f-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6be2f-247">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-247">15 seconds</span></span> | <span data-ttu-id="6be2f-248">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="6be2f-249">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento de `Closed` (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6be2f-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6be2f-250">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6be2f-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6be2f-251">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6be2f-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6be2f-252">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-252">15 seconds</span></span> | <span data-ttu-id="6be2f-253">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6be2f-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6be2f-254">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6be2f-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6be2f-255">Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6be2f-256">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="6be2f-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="6be2f-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6be2f-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6be2f-258">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-258">Option</span></span> | <span data-ttu-id="6be2f-259">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-259">Default value</span></span> | <span data-ttu-id="6be2f-260">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6be2f-261">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6be2f-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6be2f-262">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-262">Timeout for server activity.</span></span> <span data-ttu-id="6be2f-263">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onclose`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6be2f-264">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6be2f-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6be2f-265">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6be2f-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6be2f-266">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6be2f-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6be2f-267">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6be2f-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6be2f-268">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6be2f-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6be2f-269">Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="6be2f-270">Java</span><span class="sxs-lookup"><span data-stu-id="6be2f-270">Java</span></span>](#tab/java)

| <span data-ttu-id="6be2f-271">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-271">Option</span></span> | <span data-ttu-id="6be2f-272">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-272">Default value</span></span> | <span data-ttu-id="6be2f-273">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6be2f-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6be2f-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6be2f-275">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6be2f-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6be2f-276">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-276">Timeout for server activity.</span></span> <span data-ttu-id="6be2f-277">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onClose`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6be2f-278">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6be2f-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6be2f-279">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6be2f-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6be2f-280">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-280">15 seconds</span></span> | <span data-ttu-id="6be2f-281">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="6be2f-282">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento `onClose`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6be2f-283">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6be2f-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6be2f-284">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6be2f-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6be2f-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6be2f-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6be2f-286">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6be2f-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6be2f-287">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6be2f-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6be2f-288">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6be2f-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6be2f-289">Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6be2f-290">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="6be2f-290">Configure additional options</span></span>

<span data-ttu-id="6be2f-291">Opções adicionais podem ser configuradas no método `WithUrl` (`withUrl` em JavaScript) no `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` no cliente Java:</span><span class="sxs-lookup"><span data-stu-id="6be2f-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="6be2f-292">.NET</span><span class="sxs-lookup"><span data-stu-id="6be2f-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6be2f-293">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="6be2f-293">.NET Option</span></span> |  <span data-ttu-id="6be2f-294">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-294">Default value</span></span> | <span data-ttu-id="6be2f-295">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6be2f-296">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6be2f-297">Defina isso como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6be2f-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6be2f-298">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="6be2f-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6be2f-299">Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure.</span><span class="sxs-lookup"><span data-stu-id="6be2f-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6be2f-300">Vazio</span><span class="sxs-lookup"><span data-stu-id="6be2f-300">Empty</span></span> | <span data-ttu-id="6be2f-301">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="6be2f-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6be2f-302">Vazio</span><span class="sxs-lookup"><span data-stu-id="6be2f-302">Empty</span></span> | <span data-ttu-id="6be2f-303">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6be2f-304">Vazio</span><span class="sxs-lookup"><span data-stu-id="6be2f-304">Empty</span></span> | <span data-ttu-id="6be2f-305">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6be2f-306">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-306">5 seconds</span></span> | <span data-ttu-id="6be2f-307">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6be2f-307">WebSockets only.</span></span> <span data-ttu-id="6be2f-308">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="6be2f-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6be2f-309">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="6be2f-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6be2f-310">Vazio</span><span class="sxs-lookup"><span data-stu-id="6be2f-310">Empty</span></span> | <span data-ttu-id="6be2f-311">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6be2f-312">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6be2f-313">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6be2f-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="6be2f-314">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="6be2f-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6be2f-315">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova instância de `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6be2f-316">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="6be2f-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6be2f-317">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6be2f-318">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6be2f-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6be2f-319">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="6be2f-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6be2f-320">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6be2f-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6be2f-321">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="6be2f-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="6be2f-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6be2f-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6be2f-323">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="6be2f-323">JavaScript Option</span></span> | <span data-ttu-id="6be2f-324">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-324">Default Value</span></span> | <span data-ttu-id="6be2f-325">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6be2f-326">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6be2f-327">Defina isso como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6be2f-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6be2f-328">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="6be2f-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6be2f-329">Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure.</span><span class="sxs-lookup"><span data-stu-id="6be2f-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="6be2f-330">Java</span><span class="sxs-lookup"><span data-stu-id="6be2f-330">Java</span></span>](#tab/java)

| <span data-ttu-id="6be2f-331">Opção Java</span><span class="sxs-lookup"><span data-stu-id="6be2f-331">Java Option</span></span> | <span data-ttu-id="6be2f-332">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-332">Default Value</span></span> | <span data-ttu-id="6be2f-333">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6be2f-334">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6be2f-335">Defina isso como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6be2f-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6be2f-336">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="6be2f-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6be2f-337">Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure.</span><span class="sxs-lookup"><span data-stu-id="6be2f-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6be2f-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6be2f-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6be2f-339">Vazio</span><span class="sxs-lookup"><span data-stu-id="6be2f-339">Empty</span></span> | <span data-ttu-id="6be2f-340">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6be2f-341">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6be2f-342">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6be2f-343">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6be2f-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6be2f-344">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6be2f-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6be2f-345">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6be2f-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6be2f-346">ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6be2f-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6be2f-347">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="6be2f-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6be2f-348">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol), que pode ser adicionado após [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6be2f-349">O método `AddJsonProtocol` usa um delegado que recebe um objeto `options`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6be2f-350">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto de `JsonSerializerSettings` de JSON.NET que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="6be2f-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6be2f-351">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="6be2f-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="6be2f-352">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="6be2f-353">No cliente .NET, o mesmo método de extensão `AddJsonProtocol` existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6be2f-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6be2f-354">O namespace de `Microsoft.Extensions.DependencyInjection` deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="6be2f-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="6be2f-355">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6be2f-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6be2f-356">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="6be2f-356">MessagePack serialization options</span></span>

<span data-ttu-id="6be2f-357">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6be2f-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6be2f-358">Consulte [MessagePack em SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="6be2f-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6be2f-359">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6be2f-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6be2f-360">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="6be2f-360">Configure server options</span></span>

<span data-ttu-id="6be2f-361">A tabela a seguir descreve as opções para configurar os hubs de SignalR:</span><span class="sxs-lookup"><span data-stu-id="6be2f-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6be2f-362">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-362">Option</span></span> | <span data-ttu-id="6be2f-363">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-363">Default Value</span></span> | <span data-ttu-id="6be2f-364">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6be2f-365">30 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-365">30 seconds</span></span> | <span data-ttu-id="6be2f-366">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="6be2f-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6be2f-367">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6be2f-368">O valor recomendado é o dobro do valor de `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6be2f-369">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-369">15 seconds</span></span> | <span data-ttu-id="6be2f-370">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="6be2f-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6be2f-371">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6be2f-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6be2f-372">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6be2f-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6be2f-373">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-373">15 seconds</span></span> | <span data-ttu-id="6be2f-374">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="6be2f-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6be2f-375">Ao alterar `KeepAliveInterval`, altere a configuração `ServerTimeout`/`serverTimeoutInMilliseconds` no cliente.</span><span class="sxs-lookup"><span data-stu-id="6be2f-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6be2f-376">O valor de `serverTimeoutInMilliseconds` recomendado `ServerTimeout`/é o dobro do valor `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6be2f-377">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="6be2f-377">All installed protocols</span></span> | <span data-ttu-id="6be2f-378">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="6be2f-378">Protocols supported by this hub.</span></span> <span data-ttu-id="6be2f-379">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="6be2f-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6be2f-380">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="6be2f-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6be2f-381">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="6be2f-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="6be2f-382">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a chamada `AddSignalR` em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="6be2f-383">As opções para um único Hub substituem as opções globais fornecidas em `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="6be2f-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6be2f-384">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="6be2f-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6be2f-385">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="6be2f-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6be2f-386">Essas opções são configuradas passando um delegado para [MapHub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="6be2f-387">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="6be2f-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6be2f-388">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-388">Option</span></span> | <span data-ttu-id="6be2f-389">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-389">Default Value</span></span> | <span data-ttu-id="6be2f-390">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6be2f-391">32 KB</span><span class="sxs-lookup"><span data-stu-id="6be2f-391">32 KB</span></span> | <span data-ttu-id="6be2f-392">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="6be2f-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="6be2f-393">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6be2f-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6be2f-394">Os dados são automaticamente coletados dos atributos `Authorize` aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="6be2f-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6be2f-395">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="6be2f-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6be2f-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="6be2f-396">32 KB</span></span> | <span data-ttu-id="6be2f-397">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="6be2f-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="6be2f-398">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6be2f-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6be2f-399">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="6be2f-399">All Transports are enabled.</span></span> | <span data-ttu-id="6be2f-400">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que podem restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="6be2f-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6be2f-401">Consulte a seguir.</span><span class="sxs-lookup"><span data-stu-id="6be2f-401">See below.</span></span> | <span data-ttu-id="6be2f-402">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="6be2f-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6be2f-403">Consulte a seguir.</span><span class="sxs-lookup"><span data-stu-id="6be2f-403">See below.</span></span> | <span data-ttu-id="6be2f-404">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6be2f-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="6be2f-405">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a propriedade `LongPolling`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6be2f-406">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-406">Option</span></span> | <span data-ttu-id="6be2f-407">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-407">Default Value</span></span> | <span data-ttu-id="6be2f-408">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6be2f-409">90 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-409">90 seconds</span></span> | <span data-ttu-id="6be2f-410">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="6be2f-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6be2f-411">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="6be2f-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6be2f-412">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a propriedade `WebSockets`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6be2f-413">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-413">Option</span></span> | <span data-ttu-id="6be2f-414">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-414">Default Value</span></span> | <span data-ttu-id="6be2f-415">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6be2f-416">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-416">5 seconds</span></span> | <span data-ttu-id="6be2f-417">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="6be2f-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6be2f-418">Um delegado que pode ser usado para definir o cabeçalho de `Sec-WebSocket-Protocol` como um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6be2f-419">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6be2f-420">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="6be2f-420">Configure client options</span></span>

<span data-ttu-id="6be2f-421">As opções de cliente podem ser configuradas no tipo de `HubConnectionBuilder` (disponível nos clientes .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6be2f-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6be2f-422">Ele também está disponível no cliente Java, mas a subclasse `HttpHubConnectionBuilder` é o que contém as opções de configuração do Builder, bem como o `HubConnection` em si.</span><span class="sxs-lookup"><span data-stu-id="6be2f-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6be2f-423">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="6be2f-423">Configure logging</span></span>

<span data-ttu-id="6be2f-424">O registro em log é configurado no cliente .NET usando o método `ConfigureLogging`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6be2f-425">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6be2f-426">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="6be2f-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6be2f-427">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="6be2f-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6be2f-428">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="6be2f-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6be2f-429">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="6be2f-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6be2f-430">Chame o método de extensão `AddConsole`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6be2f-431">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="6be2f-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6be2f-432">Forneça um valor `LogLevel` indicando o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="6be2f-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6be2f-433">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="6be2f-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6be2f-434">Para desabilitar totalmente o registro em log, especifique `signalR.LogLevel.None` no método `configureLogging`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6be2f-435">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico deSignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6be2f-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6be2f-436">O cliente Java SignalR usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="6be2f-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6be2f-437">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="6be2f-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6be2f-438">O trecho de código a seguir mostra como usar `java.util.logging` com o cliente SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="6be2f-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6be2f-439">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="6be2f-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6be2f-440">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="6be2f-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6be2f-441">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="6be2f-441">Configure allowed transports</span></span>

<span data-ttu-id="6be2f-442">Os transportes usados pelo SignalR podem ser configurados na chamada `WithUrl` (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6be2f-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6be2f-443">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="6be2f-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6be2f-444">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="6be2f-444">All transports are enabled by default.</span></span>

<span data-ttu-id="6be2f-445">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="6be2f-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6be2f-446">No cliente JavaScript, os transportes são configurados definindo o campo `transport` no objeto de opções fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6be2f-447">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="6be2f-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6be2f-448">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="6be2f-448">Configure bearer authentication</span></span>

<span data-ttu-id="6be2f-449">Para fornecer dados de autenticação junto com SignalR solicitações, use a opção `AccessTokenProvider` (`accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6be2f-450">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o cabeçalho `Authorization` com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="6be2f-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6be2f-451">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="6be2f-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6be2f-452">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6be2f-453">No cliente .NET, a opção `AccessTokenProvider` pode ser especificada usando o delegado Options no `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6be2f-454">No cliente JavaScript, o token de acesso é configurado definindo o campo `accessTokenFactory` no objeto Options no `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="6be2f-455">No cliente Java SignalR, você pode configurar um token de portador para usar na autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6be2f-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6be2f-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [única\<cadeia de caracteres>](https://reactivex.io/documentation/single.html)[de RxJava](https://github.com/ReactiveX/RxJava).</span><span class="sxs-lookup"><span data-stu-id="6be2f-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6be2f-457">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="6be2f-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6be2f-458">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6be2f-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6be2f-459">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no próprio objeto `HubConnection`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="6be2f-460">.NET</span><span class="sxs-lookup"><span data-stu-id="6be2f-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6be2f-461">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-461">Option</span></span> | <span data-ttu-id="6be2f-462">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-462">Default value</span></span> | <span data-ttu-id="6be2f-463">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6be2f-464">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6be2f-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6be2f-465">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-465">Timeout for server activity.</span></span> <span data-ttu-id="6be2f-466">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento de `Closed` (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6be2f-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6be2f-467">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6be2f-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6be2f-468">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6be2f-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6be2f-469">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-469">15 seconds</span></span> | <span data-ttu-id="6be2f-470">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="6be2f-471">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento de `Closed` (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6be2f-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6be2f-472">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6be2f-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6be2f-473">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6be2f-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6be2f-474">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-474">15 seconds</span></span> | <span data-ttu-id="6be2f-475">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6be2f-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6be2f-476">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6be2f-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6be2f-477">Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6be2f-478">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="6be2f-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="6be2f-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6be2f-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6be2f-480">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-480">Option</span></span> | <span data-ttu-id="6be2f-481">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-481">Default value</span></span> | <span data-ttu-id="6be2f-482">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6be2f-483">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6be2f-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6be2f-484">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-484">Timeout for server activity.</span></span> <span data-ttu-id="6be2f-485">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onclose`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6be2f-486">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6be2f-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6be2f-487">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6be2f-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6be2f-488">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6be2f-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6be2f-489">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6be2f-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6be2f-490">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6be2f-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6be2f-491">Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="6be2f-492">Java</span><span class="sxs-lookup"><span data-stu-id="6be2f-492">Java</span></span>](#tab/java)

| <span data-ttu-id="6be2f-493">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-493">Option</span></span> | <span data-ttu-id="6be2f-494">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-494">Default value</span></span> | <span data-ttu-id="6be2f-495">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6be2f-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6be2f-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6be2f-497">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6be2f-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6be2f-498">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-498">Timeout for server activity.</span></span> <span data-ttu-id="6be2f-499">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onClose`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6be2f-500">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6be2f-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6be2f-501">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6be2f-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6be2f-502">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-502">15 seconds</span></span> | <span data-ttu-id="6be2f-503">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="6be2f-504">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento `onClose`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6be2f-505">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6be2f-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6be2f-506">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6be2f-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6be2f-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6be2f-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6be2f-508">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6be2f-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6be2f-509">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6be2f-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6be2f-510">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6be2f-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6be2f-511">Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6be2f-512">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="6be2f-512">Configure additional options</span></span>

<span data-ttu-id="6be2f-513">Opções adicionais podem ser configuradas no método `WithUrl` (`withUrl` em JavaScript) no `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` no cliente Java:</span><span class="sxs-lookup"><span data-stu-id="6be2f-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="6be2f-514">.NET</span><span class="sxs-lookup"><span data-stu-id="6be2f-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6be2f-515">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="6be2f-515">.NET Option</span></span> |  <span data-ttu-id="6be2f-516">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-516">Default value</span></span> | <span data-ttu-id="6be2f-517">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6be2f-518">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6be2f-519">Defina isso como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6be2f-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6be2f-520">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="6be2f-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6be2f-521">Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure.</span><span class="sxs-lookup"><span data-stu-id="6be2f-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6be2f-522">Vazio</span><span class="sxs-lookup"><span data-stu-id="6be2f-522">Empty</span></span> | <span data-ttu-id="6be2f-523">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="6be2f-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6be2f-524">Vazio</span><span class="sxs-lookup"><span data-stu-id="6be2f-524">Empty</span></span> | <span data-ttu-id="6be2f-525">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6be2f-526">Vazio</span><span class="sxs-lookup"><span data-stu-id="6be2f-526">Empty</span></span> | <span data-ttu-id="6be2f-527">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6be2f-528">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-528">5 seconds</span></span> | <span data-ttu-id="6be2f-529">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6be2f-529">WebSockets only.</span></span> <span data-ttu-id="6be2f-530">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="6be2f-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6be2f-531">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="6be2f-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6be2f-532">Vazio</span><span class="sxs-lookup"><span data-stu-id="6be2f-532">Empty</span></span> | <span data-ttu-id="6be2f-533">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6be2f-534">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6be2f-535">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6be2f-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="6be2f-536">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="6be2f-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6be2f-537">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova instância de `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6be2f-538">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="6be2f-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6be2f-539">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6be2f-540">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6be2f-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6be2f-541">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="6be2f-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6be2f-542">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6be2f-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6be2f-543">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="6be2f-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="6be2f-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6be2f-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6be2f-545">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="6be2f-545">JavaScript Option</span></span> | <span data-ttu-id="6be2f-546">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-546">Default Value</span></span> | <span data-ttu-id="6be2f-547">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6be2f-548">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6be2f-549">Defina isso como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6be2f-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6be2f-550">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="6be2f-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6be2f-551">Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure.</span><span class="sxs-lookup"><span data-stu-id="6be2f-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="6be2f-552">Java</span><span class="sxs-lookup"><span data-stu-id="6be2f-552">Java</span></span>](#tab/java)

| <span data-ttu-id="6be2f-553">Opção Java</span><span class="sxs-lookup"><span data-stu-id="6be2f-553">Java Option</span></span> | <span data-ttu-id="6be2f-554">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-554">Default Value</span></span> | <span data-ttu-id="6be2f-555">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6be2f-556">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6be2f-557">Defina isso como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6be2f-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6be2f-558">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="6be2f-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6be2f-559">Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure.</span><span class="sxs-lookup"><span data-stu-id="6be2f-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6be2f-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6be2f-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6be2f-561">Vazio</span><span class="sxs-lookup"><span data-stu-id="6be2f-561">Empty</span></span> | <span data-ttu-id="6be2f-562">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6be2f-563">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6be2f-564">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6be2f-565">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6be2f-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6be2f-566">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6be2f-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6be2f-567">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6be2f-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6be2f-568">ASP.NET Core SignalR dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6be2f-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6be2f-569">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="6be2f-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6be2f-570">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol), que pode ser adicionado após [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6be2f-571">O método `AddJsonProtocol` usa um delegado que recebe um objeto `options`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6be2f-572">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto de `JsonSerializerSettings` de JSON.NET que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="6be2f-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6be2f-573">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="6be2f-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="6be2f-574">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="6be2f-575">No cliente .NET, o mesmo método de extensão `AddJsonProtocol` existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6be2f-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6be2f-576">O namespace de `Microsoft.Extensions.DependencyInjection` deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="6be2f-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="6be2f-577">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6be2f-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6be2f-578">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="6be2f-578">MessagePack serialization options</span></span>

<span data-ttu-id="6be2f-579">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6be2f-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6be2f-580">Consulte [MessagePack em SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="6be2f-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6be2f-581">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6be2f-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6be2f-582">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="6be2f-582">Configure server options</span></span>

<span data-ttu-id="6be2f-583">A tabela a seguir descreve as opções para configurar os hubs de SignalR:</span><span class="sxs-lookup"><span data-stu-id="6be2f-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6be2f-584">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-584">Option</span></span> | <span data-ttu-id="6be2f-585">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-585">Default Value</span></span> | <span data-ttu-id="6be2f-586">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="6be2f-587">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-587">15 seconds</span></span> | <span data-ttu-id="6be2f-588">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="6be2f-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6be2f-589">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6be2f-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6be2f-590">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6be2f-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6be2f-591">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-591">15 seconds</span></span> | <span data-ttu-id="6be2f-592">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="6be2f-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6be2f-593">Ao alterar `KeepAliveInterval`, altere a configuração `ServerTimeout`/`serverTimeoutInMilliseconds` no cliente.</span><span class="sxs-lookup"><span data-stu-id="6be2f-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6be2f-594">O valor de `serverTimeoutInMilliseconds` recomendado `ServerTimeout`/é o dobro do valor `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6be2f-595">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="6be2f-595">All installed protocols</span></span> | <span data-ttu-id="6be2f-596">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="6be2f-596">Protocols supported by this hub.</span></span> <span data-ttu-id="6be2f-597">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="6be2f-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6be2f-598">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="6be2f-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6be2f-599">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="6be2f-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="6be2f-600">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a chamada `AddSignalR` em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="6be2f-601">As opções para um único Hub substituem as opções globais fornecidas em `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="6be2f-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6be2f-602">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="6be2f-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6be2f-603">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="6be2f-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6be2f-604">Essas opções são configuradas passando um delegado para [MapHub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="6be2f-605">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="6be2f-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6be2f-606">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-606">Option</span></span> | <span data-ttu-id="6be2f-607">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-607">Default Value</span></span> | <span data-ttu-id="6be2f-608">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6be2f-609">32 KB</span><span class="sxs-lookup"><span data-stu-id="6be2f-609">32 KB</span></span> | <span data-ttu-id="6be2f-610">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="6be2f-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="6be2f-611">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6be2f-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6be2f-612">Os dados são automaticamente coletados dos atributos `Authorize` aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="6be2f-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6be2f-613">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="6be2f-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6be2f-614">32 KB</span><span class="sxs-lookup"><span data-stu-id="6be2f-614">32 KB</span></span> | <span data-ttu-id="6be2f-615">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="6be2f-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="6be2f-616">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6be2f-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6be2f-617">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="6be2f-617">All Transports are enabled.</span></span> | <span data-ttu-id="6be2f-618">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que podem restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="6be2f-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6be2f-619">Consulte a seguir.</span><span class="sxs-lookup"><span data-stu-id="6be2f-619">See below.</span></span> | <span data-ttu-id="6be2f-620">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="6be2f-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6be2f-621">Consulte a seguir.</span><span class="sxs-lookup"><span data-stu-id="6be2f-621">See below.</span></span> | <span data-ttu-id="6be2f-622">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6be2f-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="6be2f-623">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a propriedade `LongPolling`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6be2f-624">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-624">Option</span></span> | <span data-ttu-id="6be2f-625">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-625">Default Value</span></span> | <span data-ttu-id="6be2f-626">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6be2f-627">90 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-627">90 seconds</span></span> | <span data-ttu-id="6be2f-628">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="6be2f-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6be2f-629">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="6be2f-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6be2f-630">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a propriedade `WebSockets`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6be2f-631">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-631">Option</span></span> | <span data-ttu-id="6be2f-632">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-632">Default Value</span></span> | <span data-ttu-id="6be2f-633">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6be2f-634">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-634">5 seconds</span></span> | <span data-ttu-id="6be2f-635">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="6be2f-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6be2f-636">Um delegado que pode ser usado para definir o cabeçalho de `Sec-WebSocket-Protocol` como um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6be2f-637">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6be2f-638">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="6be2f-638">Configure client options</span></span>

<span data-ttu-id="6be2f-639">As opções de cliente podem ser configuradas no tipo de `HubConnectionBuilder` (disponível nos clientes .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6be2f-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6be2f-640">Ele também está disponível no cliente Java, mas a subclasse `HttpHubConnectionBuilder` é o que contém as opções de configuração do Builder, bem como o `HubConnection` em si.</span><span class="sxs-lookup"><span data-stu-id="6be2f-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6be2f-641">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="6be2f-641">Configure logging</span></span>

<span data-ttu-id="6be2f-642">O registro em log é configurado no cliente .NET usando o método `ConfigureLogging`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6be2f-643">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6be2f-644">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="6be2f-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6be2f-645">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="6be2f-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6be2f-646">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="6be2f-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6be2f-647">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="6be2f-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6be2f-648">Chame o método de extensão `AddConsole`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6be2f-649">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="6be2f-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6be2f-650">Forneça um valor `LogLevel` indicando o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="6be2f-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6be2f-651">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="6be2f-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6be2f-652">Para desabilitar totalmente o registro em log, especifique `signalR.LogLevel.None` no método `configureLogging`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6be2f-653">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico deSignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6be2f-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6be2f-654">O cliente Java SignalR usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="6be2f-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6be2f-655">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="6be2f-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6be2f-656">O trecho de código a seguir mostra como usar `java.util.logging` com o cliente SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="6be2f-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6be2f-657">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="6be2f-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6be2f-658">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="6be2f-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6be2f-659">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="6be2f-659">Configure allowed transports</span></span>

<span data-ttu-id="6be2f-660">Os transportes usados pelo SignalR podem ser configurados na chamada `WithUrl` (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6be2f-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6be2f-661">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="6be2f-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6be2f-662">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="6be2f-662">All transports are enabled by default.</span></span>

<span data-ttu-id="6be2f-663">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="6be2f-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6be2f-664">No cliente JavaScript, os transportes são configurados definindo o campo `transport` no objeto de opções fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6be2f-665">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="6be2f-665">Configure bearer authentication</span></span>

<span data-ttu-id="6be2f-666">Para fornecer dados de autenticação junto com SignalR solicitações, use a opção `AccessTokenProvider` (`accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="6be2f-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6be2f-667">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o cabeçalho `Authorization` com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="6be2f-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6be2f-668">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="6be2f-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6be2f-669">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6be2f-670">No cliente .NET, a opção `AccessTokenProvider` pode ser especificada usando o delegado Options no `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6be2f-671">No cliente JavaScript, o token de acesso é configurado definindo o campo `accessTokenFactory` no objeto Options no `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="6be2f-672">No cliente Java SignalR, você pode configurar um token de portador para usar na autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6be2f-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6be2f-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [única\<cadeia de caracteres>](https://reactivex.io/documentation/single.html)[de RxJava](https://github.com/ReactiveX/RxJava).</span><span class="sxs-lookup"><span data-stu-id="6be2f-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6be2f-674">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="6be2f-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6be2f-675">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6be2f-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6be2f-676">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no próprio objeto `HubConnection`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="6be2f-677">.NET</span><span class="sxs-lookup"><span data-stu-id="6be2f-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6be2f-678">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-678">Option</span></span> | <span data-ttu-id="6be2f-679">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-679">Default value</span></span> | <span data-ttu-id="6be2f-680">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6be2f-681">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6be2f-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6be2f-682">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-682">Timeout for server activity.</span></span> <span data-ttu-id="6be2f-683">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento de `Closed` (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6be2f-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6be2f-684">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6be2f-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6be2f-685">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6be2f-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6be2f-686">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-686">15 seconds</span></span> | <span data-ttu-id="6be2f-687">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="6be2f-688">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento de `Closed` (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6be2f-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6be2f-689">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6be2f-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6be2f-690">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6be2f-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="6be2f-691">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="6be2f-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="6be2f-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6be2f-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6be2f-693">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-693">Option</span></span> | <span data-ttu-id="6be2f-694">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-694">Default value</span></span> | <span data-ttu-id="6be2f-695">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6be2f-696">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6be2f-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6be2f-697">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-697">Timeout for server activity.</span></span> <span data-ttu-id="6be2f-698">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onclose`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6be2f-699">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6be2f-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6be2f-700">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6be2f-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="6be2f-701">Java</span><span class="sxs-lookup"><span data-stu-id="6be2f-701">Java</span></span>](#tab/java)

| <span data-ttu-id="6be2f-702">Opção</span><span class="sxs-lookup"><span data-stu-id="6be2f-702">Option</span></span> | <span data-ttu-id="6be2f-703">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-703">Default value</span></span> | <span data-ttu-id="6be2f-704">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6be2f-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6be2f-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6be2f-706">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6be2f-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6be2f-707">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-707">Timeout for server activity.</span></span> <span data-ttu-id="6be2f-708">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onClose`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6be2f-709">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6be2f-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6be2f-710">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor, para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6be2f-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6be2f-711">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-711">15 seconds</span></span> | <span data-ttu-id="6be2f-712">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6be2f-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="6be2f-713">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento `onClose`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6be2f-714">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6be2f-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6be2f-715">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6be2f-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6be2f-716">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="6be2f-716">Configure additional options</span></span>

<span data-ttu-id="6be2f-717">Opções adicionais podem ser configuradas no método `WithUrl` (`withUrl` em JavaScript) no `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` no cliente Java:</span><span class="sxs-lookup"><span data-stu-id="6be2f-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="6be2f-718">.NET</span><span class="sxs-lookup"><span data-stu-id="6be2f-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6be2f-719">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="6be2f-719">.NET Option</span></span> |  <span data-ttu-id="6be2f-720">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-720">Default value</span></span> | <span data-ttu-id="6be2f-721">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6be2f-722">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6be2f-723">Defina isso como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6be2f-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6be2f-724">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="6be2f-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6be2f-725">Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure.</span><span class="sxs-lookup"><span data-stu-id="6be2f-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6be2f-726">Vazio</span><span class="sxs-lookup"><span data-stu-id="6be2f-726">Empty</span></span> | <span data-ttu-id="6be2f-727">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="6be2f-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6be2f-728">Vazio</span><span class="sxs-lookup"><span data-stu-id="6be2f-728">Empty</span></span> | <span data-ttu-id="6be2f-729">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6be2f-730">Vazio</span><span class="sxs-lookup"><span data-stu-id="6be2f-730">Empty</span></span> | <span data-ttu-id="6be2f-731">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6be2f-732">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6be2f-732">5 seconds</span></span> | <span data-ttu-id="6be2f-733">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6be2f-733">WebSockets only.</span></span> <span data-ttu-id="6be2f-734">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="6be2f-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6be2f-735">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="6be2f-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6be2f-736">Vazio</span><span class="sxs-lookup"><span data-stu-id="6be2f-736">Empty</span></span> | <span data-ttu-id="6be2f-737">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6be2f-738">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6be2f-739">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6be2f-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="6be2f-740">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="6be2f-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6be2f-741">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova instância de `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="6be2f-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6be2f-742">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="6be2f-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6be2f-743">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6be2f-744">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6be2f-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6be2f-745">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="6be2f-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6be2f-746">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6be2f-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6be2f-747">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="6be2f-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="6be2f-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6be2f-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6be2f-749">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="6be2f-749">JavaScript Option</span></span> | <span data-ttu-id="6be2f-750">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-750">Default Value</span></span> | <span data-ttu-id="6be2f-751">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6be2f-752">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6be2f-753">Defina isso como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6be2f-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6be2f-754">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="6be2f-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6be2f-755">Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure.</span><span class="sxs-lookup"><span data-stu-id="6be2f-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="6be2f-756">Java</span><span class="sxs-lookup"><span data-stu-id="6be2f-756">Java</span></span>](#tab/java)

| <span data-ttu-id="6be2f-757">Opção Java</span><span class="sxs-lookup"><span data-stu-id="6be2f-757">Java Option</span></span> | <span data-ttu-id="6be2f-758">Valor Padrão</span><span class="sxs-lookup"><span data-stu-id="6be2f-758">Default Value</span></span> | <span data-ttu-id="6be2f-759">Descrição</span><span class="sxs-lookup"><span data-stu-id="6be2f-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6be2f-760">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6be2f-761">Defina isso como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6be2f-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6be2f-762">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="6be2f-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6be2f-763">Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure.</span><span class="sxs-lookup"><span data-stu-id="6be2f-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6be2f-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6be2f-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6be2f-765">Vazio</span><span class="sxs-lookup"><span data-stu-id="6be2f-765">Empty</span></span> | <span data-ttu-id="6be2f-766">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6be2f-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6be2f-767">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6be2f-768">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6be2f-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6be2f-769">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6be2f-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6be2f-770">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6be2f-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end