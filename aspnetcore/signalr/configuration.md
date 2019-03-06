---
title: Configuração do ASP.NET SignalR Core
author: bradygaster
description: Saiba como configurar aplicativos do SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/07/2019
uid: signalr/configuration
ms.openlocfilehash: c5921db895a732c9663c9d962195a2c0635f5aa0
ms.sourcegitcommit: 6ddd8a7675c1c1d997c8ab2d4498538e44954cac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57400652"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="ece6e-103">Configuração do ASP.NET SignalR Core</span><span class="sxs-lookup"><span data-stu-id="ece6e-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="ece6e-104">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="ece6e-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="ece6e-105">SignalR do ASP.NET Core dá suporte a dois protocolos para codificação de mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="ece6e-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="ece6e-106">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="ece6e-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="ece6e-107">Serialização JSON pode ser configurada no servidor usando o [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) método de extensão, que pode ser adicionado após [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="ece6e-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="ece6e-108">O `AddJsonProtocol` leva um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="ece6e-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="ece6e-109">O [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) propriedade desse objeto é um JSON.NET `JsonSerializerSettings` objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="ece6e-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="ece6e-110">Consulte a [documentação do JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="ece6e-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="ece6e-111">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes de "camelCase" padrão, use o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="ece6e-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver = 
        new DefaultContractResolver();
    });
```

<span data-ttu-id="ece6e-112">No cliente do .NET, o mesmo `AddJsonProtocol` método de extensão existe no [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="ece6e-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="ece6e-113">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="ece6e-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="ece6e-114">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="ece6e-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="ece6e-115">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="ece6e-115">MessagePack serialization options</span></span>

<span data-ttu-id="ece6e-116">MessagePack serialização pode ser configurada fornecendo um delegado para o [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) chamar.</span><span class="sxs-lookup"><span data-stu-id="ece6e-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="ece6e-117">Ver [MessagePack no SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="ece6e-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="ece6e-118">Não é possível configurar a serialização de MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="ece6e-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="ece6e-119">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="ece6e-119">Configure server options</span></span>

<span data-ttu-id="ece6e-120">A tabela a seguir descreve as opções de configuração hubs do SignalR:</span><span class="sxs-lookup"><span data-stu-id="ece6e-120">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="ece6e-121">Opção</span><span class="sxs-lookup"><span data-stu-id="ece6e-121">Option</span></span> | <span data-ttu-id="ece6e-122">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="ece6e-122">Default Value</span></span> | <span data-ttu-id="ece6e-123">Descrição</span><span class="sxs-lookup"><span data-stu-id="ece6e-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="ece6e-124">30 segundos</span><span class="sxs-lookup"><span data-stu-id="ece6e-124">30 seconds</span></span> | <span data-ttu-id="ece6e-125">O servidor irá considerar o cliente desconectado se ele ainda não recebeu uma mensagem (incluindo keep-alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="ece6e-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="ece6e-126">Ele pode demorar mais que esse intervalo de tempo limite para o cliente, na verdade, ser marcado como desconectado devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="ece6e-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="ece6e-127">O valor recomendado é double o `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="ece6e-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="ece6e-128">15 segundos</span><span class="sxs-lookup"><span data-stu-id="ece6e-128">15 seconds</span></span> | <span data-ttu-id="ece6e-129">Se o cliente não envia uma mensagem de handshake inicial dentro deste intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="ece6e-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="ece6e-130">Isso é uma configuração avançada que deve ser modificada apenas se os erros de tempo limite de handshake estiverem ocorrendo devido à latência de rede graves.</span><span class="sxs-lookup"><span data-stu-id="ece6e-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ece6e-131">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub do SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ece6e-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ece6e-132">15 segundos</span><span class="sxs-lookup"><span data-stu-id="ece6e-132">15 seconds</span></span> | <span data-ttu-id="ece6e-133">Se o servidor não enviou uma mensagem dentro deste intervalo, uma mensagem de ping é enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="ece6e-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="ece6e-134">Ao alterar `KeepAliveInterval`, altere o `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="ece6e-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="ece6e-135">Recomendado `ServerTimeout` / `serverTimeoutInMilliseconds` valor é double o `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="ece6e-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="ece6e-136">Todos os protocolos</span><span class="sxs-lookup"><span data-stu-id="ece6e-136">All installed protocols</span></span> | <span data-ttu-id="ece6e-137">Protocolos com suporte por esse hub.</span><span class="sxs-lookup"><span data-stu-id="ece6e-137">Protocols supported by this hub.</span></span> <span data-ttu-id="ece6e-138">Por padrão, todos os protocolos registrados no servidor são permitidos, mas protocolos podem ser removidos dessa lista para desabilitar os protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="ece6e-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="ece6e-139">Se `true`detalhada mensagens de exceção são retornadas aos clientes quando uma exceção é gerada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="ece6e-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="ece6e-140">O padrão é `false`, conforme essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="ece6e-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="ece6e-141">Opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para o `AddSignalR` chamar em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ece6e-141">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="ece6e-142">Opções para um único hub substituem as opções de globais fornecidas no `AddSignalR` e pode ser configurado usando [AddHubOptions\<T >](/dotnet/api/microsoft.extensions.dependencyinjection.huboptionsdependencyinjectionextensions.addhuboptions):</span><span class="sxs-lookup"><span data-stu-id="ece6e-142">Options for a single hub override the global options provided in `AddSignalR` and can be configured using [AddHubOptions\<T>](/dotnet/api/microsoft.extensions.dependencyinjection.huboptionsdependencyinjectionextensions.addhuboptions):</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="ece6e-143">Opções avançadas de configuração de HTTP</span><span class="sxs-lookup"><span data-stu-id="ece6e-143">Advanced HTTP configuration options</span></span>

<span data-ttu-id="ece6e-144">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="ece6e-144">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="ece6e-145">Essas opções são configuradas passando um delegado para [MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="ece6e-145">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="ece6e-146">A tabela a seguir descreve opções para configurar opções avançadas de HTTP do SignalR do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ece6e-146">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="ece6e-147">Opção</span><span class="sxs-lookup"><span data-stu-id="ece6e-147">Option</span></span> | <span data-ttu-id="ece6e-148">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="ece6e-148">Default Value</span></span> | <span data-ttu-id="ece6e-149">Descrição</span><span class="sxs-lookup"><span data-stu-id="ece6e-149">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="ece6e-150">32 KB</span><span class="sxs-lookup"><span data-stu-id="ece6e-150">32 KB</span></span> | <span data-ttu-id="ece6e-151">O número máximo de bytes recebidos do cliente que os buffers de servidor.</span><span class="sxs-lookup"><span data-stu-id="ece6e-151">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="ece6e-152">Aumentar esse valor permite que o servidor receber mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="ece6e-152">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="ece6e-153">Dados coletados automaticamente a partir de `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="ece6e-153">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="ece6e-154">Uma lista dos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objetos usados para determinar se um cliente está autorizado a conectar-se ao hub.</span><span class="sxs-lookup"><span data-stu-id="ece6e-154">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="ece6e-155">32 KB</span><span class="sxs-lookup"><span data-stu-id="ece6e-155">32 KB</span></span> | <span data-ttu-id="ece6e-156">O número máximo de bytes enviados pelo aplicativo que os buffers de servidor.</span><span class="sxs-lookup"><span data-stu-id="ece6e-156">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="ece6e-157">Aumentar esse valor permite que o servidor enviar mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="ece6e-157">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="ece6e-158">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="ece6e-158">All Transports are enabled.</span></span> | <span data-ttu-id="ece6e-159">Um bitmask de `HttpTransportType` valores que possam restringir os transportes de um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="ece6e-159">A bitmask of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="ece6e-160">Veja a seguir.</span><span class="sxs-lookup"><span data-stu-id="ece6e-160">See below.</span></span> | <span data-ttu-id="ece6e-161">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="ece6e-161">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="ece6e-162">Veja a seguir.</span><span class="sxs-lookup"><span data-stu-id="ece6e-162">See below.</span></span> | <span data-ttu-id="ece6e-163">Opções adicionais específicas para o transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ece6e-163">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="ece6e-164">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando o `LongPolling` propriedade:</span><span class="sxs-lookup"><span data-stu-id="ece6e-164">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="ece6e-165">Opção</span><span class="sxs-lookup"><span data-stu-id="ece6e-165">Option</span></span> | <span data-ttu-id="ece6e-166">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="ece6e-166">Default Value</span></span> | <span data-ttu-id="ece6e-167">Descrição</span><span class="sxs-lookup"><span data-stu-id="ece6e-167">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="ece6e-168">90 segundos</span><span class="sxs-lookup"><span data-stu-id="ece6e-168">90 seconds</span></span> | <span data-ttu-id="ece6e-169">A quantidade máxima de tempo o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma solicitação de pesquisa único.</span><span class="sxs-lookup"><span data-stu-id="ece6e-169">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="ece6e-170">Diminuir esse valor faz com que o cliente emitir novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="ece6e-170">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="ece6e-171">O transporte de WebSocket tem opções adicionais que podem ser configuradas usando o `WebSockets` propriedade:</span><span class="sxs-lookup"><span data-stu-id="ece6e-171">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="ece6e-172">Opção</span><span class="sxs-lookup"><span data-stu-id="ece6e-172">Option</span></span> | <span data-ttu-id="ece6e-173">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="ece6e-173">Default Value</span></span> | <span data-ttu-id="ece6e-174">Descrição</span><span class="sxs-lookup"><span data-stu-id="ece6e-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="ece6e-175">5 segundos</span><span class="sxs-lookup"><span data-stu-id="ece6e-175">5 seconds</span></span> | <span data-ttu-id="ece6e-176">Depois de fecha o servidor, se o cliente não conseguir fechar dentro deste intervalo de tempo, a conexão é encerrada.</span><span class="sxs-lookup"><span data-stu-id="ece6e-176">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="ece6e-177">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="ece6e-177">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="ece6e-178">O delegado recebe os valores solicitados pelo cliente como entrada e deve retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="ece6e-178">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="ece6e-179">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="ece6e-179">Configure client options</span></span>

<span data-ttu-id="ece6e-180">Opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ece6e-180">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="ece6e-181">Ele também está disponível no cliente de Java, mas o `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração construtor, bem como no `HubConnection` em si.</span><span class="sxs-lookup"><span data-stu-id="ece6e-181">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="ece6e-182">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="ece6e-182">Configure logging</span></span>

<span data-ttu-id="ece6e-183">O log está configurado no cliente do .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="ece6e-183">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="ece6e-184">Registro em log provedores e filtros pode ser registrado da mesma forma como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="ece6e-184">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="ece6e-185">Consulte a [entrar no ASP.NET Core](xref:fundamentals/logging/index) documentação para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="ece6e-185">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="ece6e-186">Para registrar os provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="ece6e-186">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="ece6e-187">Consulte a [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) seção do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="ece6e-187">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="ece6e-188">Por exemplo, para habilitar o log de Console, instale o `Microsoft.Extensions.Logging.Console` pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="ece6e-188">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="ece6e-189">Chamar o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="ece6e-189">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="ece6e-190">No cliente JavaScript, um semelhante `configureLogging` existe um método.</span><span class="sxs-lookup"><span data-stu-id="ece6e-190">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="ece6e-191">Forneça um `LogLevel` valor que indica o nível mínimo de mensagens de log para produzir.</span><span class="sxs-lookup"><span data-stu-id="ece6e-191">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="ece6e-192">Os logs são gravados na janela de console do navegador.</span><span class="sxs-lookup"><span data-stu-id="ece6e-192">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="ece6e-193">Para desabilitar o registro em log totalmente, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="ece6e-193">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="ece6e-194">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico do SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="ece6e-194">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="ece6e-195">O cliente SignalR Java usa a [SLF4J](https://www.slf4j.org/) biblioteca para registro em log.</span><span class="sxs-lookup"><span data-stu-id="ece6e-195">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="ece6e-196">É uma API de alto nível de log que permite aos usuários da biblioteca de escolher sua própria implementação de log específico, colocando em uma dependência de log específico.</span><span class="sxs-lookup"><span data-stu-id="ece6e-196">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="ece6e-197">O trecho de código a seguir mostra como usar `java.util.logging` com o cliente de Java do SignalR.</span><span class="sxs-lookup"><span data-stu-id="ece6e-197">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="ece6e-198">Se você não configurar o registro em log em suas dependências, SLF4J carrega um agente de operação não padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="ece6e-198">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="ece6e-199">Isso pode ser ignorado.</span><span class="sxs-lookup"><span data-stu-id="ece6e-199">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="ece6e-200">Configure transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="ece6e-200">Configure allowed transports</span></span>

<span data-ttu-id="ece6e-201">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamar (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ece6e-201">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="ece6e-202">Um bitwise OR dos valores de `HttpTransportType` pode ser usado para restringir o cliente para usar somente os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="ece6e-202">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="ece6e-203">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="ece6e-203">All transports are enabled by default.</span></span>

<span data-ttu-id="ece6e-204">Por exemplo, para desabilitar o transporte de eventos do Server-Sent, mas permita conexões WebSocket e sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="ece6e-204">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="ece6e-205">No cliente JavaScript, transportes são configurados, definindo o `transport` campo no objeto de opções fornecido ao `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="ece6e-205">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="ece6e-206">Nesta versão do Java WebSocket do cliente é o transporte somente disponível.</span><span class="sxs-lookup"><span data-stu-id="ece6e-206">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="ece6e-207">No cliente de Java, o transporte é selecionado com o `withTransport` método no `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ece6e-207">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="ece6e-208">Os padrões de cliente Java para usar o transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ece6e-208">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```
> [!NOTE]
> <span data-ttu-id="ece6e-209">O cliente SignalR Java não dá suporte a fallback de transporte ainda.</span><span class="sxs-lookup"><span data-stu-id="ece6e-209">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="ece6e-210">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="ece6e-210">Configure bearer authentication</span></span>

<span data-ttu-id="ece6e-211">Para fornecer dados de autenticação junto com as solicitações de SignalR, use o `AccessTokenProvider` opção (`accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="ece6e-211">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="ece6e-212">No cliente do .NET, esse token de acesso é passado como um HTTP "Autenticação do portador" token (usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="ece6e-212">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="ece6e-213">No cliente JavaScript, o token de acesso é usado como um token de portador **exceto** em alguns casos em que o navegador APIs restringir a capacidade de aplicar cabeçalhos (especificamente, em solicitações de eventos do Server-sent e WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ece6e-213">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="ece6e-214">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token`.</span><span class="sxs-lookup"><span data-stu-id="ece6e-214">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="ece6e-215">No cliente do .NET, o `AccessTokenProvider` opção pode ser especificada usando o delegado de opções no `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="ece6e-215">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="ece6e-216">No cliente JavaScript, o token de acesso é configurado definindo a `accessTokenFactory` campo no objeto de opções no `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="ece6e-216">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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


<span data-ttu-id="ece6e-217">No cliente de Java do SignalR, você pode configurar um token de portador para usar para autenticação, fornecendo uma fábrica de tokens de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="ece6e-217">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="ece6e-218">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [RxJava](https://github.com/ReactiveX/RxJava) [único<String>](http://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="ece6e-218">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single<String>](http://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="ece6e-219">Com uma chamada para [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever a lógica para gerar tokens de acesso do cliente.</span><span class="sxs-lookup"><span data-stu-id="ece6e-219">With a call to [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="ece6e-220">Configurar tempo limite e opções de keep alive</span><span class="sxs-lookup"><span data-stu-id="ece6e-220">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="ece6e-221">Opções adicionais para configurar o tempo limite e o comportamento de keep alive estão disponíveis no `HubConnection` objeto em si:</span><span class="sxs-lookup"><span data-stu-id="ece6e-221">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="ece6e-222">.NET</span><span class="sxs-lookup"><span data-stu-id="ece6e-222">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ece6e-223">Opção</span><span class="sxs-lookup"><span data-stu-id="ece6e-223">Option</span></span> | <span data-ttu-id="ece6e-224">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="ece6e-224">Default value</span></span> | <span data-ttu-id="ece6e-225">Descrição</span><span class="sxs-lookup"><span data-stu-id="ece6e-225">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="ece6e-226">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="ece6e-226">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ece6e-227">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="ece6e-227">Timeout for server activity.</span></span> <span data-ttu-id="ece6e-228">Se o servidor não enviou uma mensagem nesse intervalo, o cliente considera o servidor desconectado e gatilhos do `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ece6e-228">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ece6e-229">Esse valor deve ser grande o suficiente para uma mensagem de ping a serem enviados do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="ece6e-229">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ece6e-230">O valor recomendado é um número pelo menos duas vezes o servidor `KeepAliveInterval` valor, para dar tempo para pings de chegada.</span><span class="sxs-lookup"><span data-stu-id="ece6e-230">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="ece6e-231">15 segundos</span><span class="sxs-lookup"><span data-stu-id="ece6e-231">15 seconds</span></span> | <span data-ttu-id="ece6e-232">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="ece6e-232">Timeout for initial server handshake.</span></span> <span data-ttu-id="ece6e-233">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancela o handshake e gatilhos do `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ece6e-233">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ece6e-234">Isso é uma configuração avançada que deve ser modificada apenas se os erros de tempo limite de handshake estiverem ocorrendo devido à latência de rede graves.</span><span class="sxs-lookup"><span data-stu-id="ece6e-234">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ece6e-235">Para obter mais detalhes sobre o processo de Handshake, consulte a [especificação de protocolo de Hub do SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ece6e-235">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="ece6e-236">No cliente do .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="ece6e-236">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="ece6e-237">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ece6e-237">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ece6e-238">Opção</span><span class="sxs-lookup"><span data-stu-id="ece6e-238">Option</span></span> | <span data-ttu-id="ece6e-239">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="ece6e-239">Default value</span></span> | <span data-ttu-id="ece6e-240">Descrição</span><span class="sxs-lookup"><span data-stu-id="ece6e-240">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="ece6e-241">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="ece6e-241">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ece6e-242">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="ece6e-242">Timeout for server activity.</span></span> <span data-ttu-id="ece6e-243">Se o servidor não enviou uma mensagem nesse intervalo, o cliente considera as desconectado do servidor e os gatilhos de `onclose` eventos.</span><span class="sxs-lookup"><span data-stu-id="ece6e-243">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="ece6e-244">Esse valor deve ser grande o suficiente para uma mensagem de ping a serem enviados do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="ece6e-244">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ece6e-245">O valor recomendado é um número pelo menos duas vezes o servidor `KeepAliveInterval` valor, para dar tempo para pings de chegada.</span><span class="sxs-lookup"><span data-stu-id="ece6e-245">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="ece6e-246">Java</span><span class="sxs-lookup"><span data-stu-id="ece6e-246">Java</span></span>](#tab/java)

| <span data-ttu-id="ece6e-247">Opção</span><span class="sxs-lookup"><span data-stu-id="ece6e-247">Option</span></span> | <span data-ttu-id="ece6e-248">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="ece6e-248">Default value</span></span> | <span data-ttu-id="ece6e-249">Descrição</span><span class="sxs-lookup"><span data-stu-id="ece6e-249">Description</span></span> |
| ----------- | ------------- | ----------- |
|<span data-ttu-id="ece6e-250">`getServerTimeout` `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="ece6e-250">`getServerTimeout` `setServerTimeout`</span></span> | <span data-ttu-id="ece6e-251">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="ece6e-251">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ece6e-252">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="ece6e-252">Timeout for server activity.</span></span> <span data-ttu-id="ece6e-253">Se o servidor não enviou uma mensagem nesse intervalo, o cliente considera as desconectado do servidor e os gatilhos de `onClose` eventos.</span><span class="sxs-lookup"><span data-stu-id="ece6e-253">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="ece6e-254">Esse valor deve ser grande o suficiente para uma mensagem de ping a serem enviados do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="ece6e-254">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ece6e-255">O valor recomendado é um número pelo menos duas vezes o servidor `KeepAliveInterval` valor, para dar tempo para pings de chegada.</span><span class="sxs-lookup"><span data-stu-id="ece6e-255">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="ece6e-256">15 segundos</span><span class="sxs-lookup"><span data-stu-id="ece6e-256">15 seconds</span></span> | <span data-ttu-id="ece6e-257">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="ece6e-257">Timeout for initial server handshake.</span></span> <span data-ttu-id="ece6e-258">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancela o handshake e gatilhos de `onClose` eventos.</span><span class="sxs-lookup"><span data-stu-id="ece6e-258">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="ece6e-259">Isso é uma configuração avançada que deve ser modificada apenas se os erros de tempo limite de handshake estiverem ocorrendo devido à latência de rede graves.</span><span class="sxs-lookup"><span data-stu-id="ece6e-259">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ece6e-260">Para obter mais detalhes sobre o processo de Handshake, consulte a [especificação de protocolo de Hub do SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ece6e-260">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="ece6e-261">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="ece6e-261">Configure additional options</span></span>

<span data-ttu-id="ece6e-262">Opções adicionais podem ser configuradas na `WithUrl` (`withUrl` em JavaScript) método em `HubConnectionBuilder` ou na configuração de várias APIs no `HttpHubConnectionBuilder` no cliente de Java:</span><span class="sxs-lookup"><span data-stu-id="ece6e-262">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="ece6e-263">.NET</span><span class="sxs-lookup"><span data-stu-id="ece6e-263">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ece6e-264">Opção de .NET</span><span class="sxs-lookup"><span data-stu-id="ece6e-264">.NET Option</span></span> |  <span data-ttu-id="ece6e-265">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="ece6e-265">Default value</span></span> | <span data-ttu-id="ece6e-266">Descrição</span><span class="sxs-lookup"><span data-stu-id="ece6e-266">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="ece6e-267">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="ece6e-267">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="ece6e-268">Defina isso como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="ece6e-268">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ece6e-269">**Só tem suporte quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="ece6e-269">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ece6e-270">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ece6e-270">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="ece6e-271">Vazio</span><span class="sxs-lookup"><span data-stu-id="ece6e-271">Empty</span></span> | <span data-ttu-id="ece6e-272">Uma coleção de certificados TLS para enviar para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="ece6e-272">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="ece6e-273">Vazio</span><span class="sxs-lookup"><span data-stu-id="ece6e-273">Empty</span></span> | <span data-ttu-id="ece6e-274">Uma coleção de cookies HTTP a ser enviado com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="ece6e-274">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="ece6e-275">Vazio</span><span class="sxs-lookup"><span data-stu-id="ece6e-275">Empty</span></span> | <span data-ttu-id="ece6e-276">Credenciais devem ser enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="ece6e-276">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="ece6e-277">5 segundos</span><span class="sxs-lookup"><span data-stu-id="ece6e-277">5 seconds</span></span> | <span data-ttu-id="ece6e-278">WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ece6e-278">WebSockets only.</span></span> <span data-ttu-id="ece6e-279">A quantidade máxima de tempo que o cliente aguardará depois de fechamento para o servidor confirmar a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="ece6e-279">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="ece6e-280">Se o servidor não reconhece o fechamento dentro desse período, o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="ece6e-280">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="ece6e-281">Vazio</span><span class="sxs-lookup"><span data-stu-id="ece6e-281">Empty</span></span> | <span data-ttu-id="ece6e-282">Um mapa de cabeçalhos HTTP adicionais para enviar com todas as solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="ece6e-282">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="ece6e-283">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="ece6e-283">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="ece6e-284">Não é usado para conexões de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ece6e-284">Not used for WebSocket connections.</span></span> <span data-ttu-id="ece6e-285">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="ece6e-285">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="ece6e-286">Modificar as configurações nesse valor padrão e retorná-lo ou retornar um novo `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="ece6e-286">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="ece6e-287">**Ao substituir o manipulador Certifique-se de copiar as configurações que você deseja impedir que o manipulador fornecido, caso contrário, as opções configuradas (como cabeçalhos e Cookies) não se aplicam ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="ece6e-287">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="ece6e-288">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="ece6e-288">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="ece6e-289">Defina esse valor booliano para enviar as credenciais padrão para solicitações HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ece6e-289">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="ece6e-290">Isso permite que o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="ece6e-290">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="ece6e-291">Um delegado que pode ser usado para configurar opções adicionais de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ece6e-291">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="ece6e-292">Recebe uma instância do [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usado para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="ece6e-292">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="ece6e-293">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ece6e-293">JavaScript</span></span>](#tab/javascript)
| <span data-ttu-id="ece6e-294">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="ece6e-294">JavaScript Option</span></span> | <span data-ttu-id="ece6e-295">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="ece6e-295">Default Value</span></span> | <span data-ttu-id="ece6e-296">Descrição</span><span class="sxs-lookup"><span data-stu-id="ece6e-296">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="ece6e-297">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="ece6e-297">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="ece6e-298">Defina isso como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="ece6e-298">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ece6e-299">**Só tem suporte quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="ece6e-299">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ece6e-300">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ece6e-300">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="ece6e-301">Java</span><span class="sxs-lookup"><span data-stu-id="ece6e-301">Java</span></span>](#tab/java)
| <span data-ttu-id="ece6e-302">Opção de Java</span><span class="sxs-lookup"><span data-stu-id="ece6e-302">Java Option</span></span> | <span data-ttu-id="ece6e-303">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="ece6e-303">Default Value</span></span> | <span data-ttu-id="ece6e-304">Descrição</span><span class="sxs-lookup"><span data-stu-id="ece6e-304">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="ece6e-305">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="ece6e-305">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="ece6e-306">Defina isso como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="ece6e-306">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ece6e-307">**Só tem suporte quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="ece6e-307">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ece6e-308">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="ece6e-308">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="ece6e-309">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="ece6e-309">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="ece6e-310">Vazio</span><span class="sxs-lookup"><span data-stu-id="ece6e-310">Empty</span></span> | <span data-ttu-id="ece6e-311">Um mapa de cabeçalhos HTTP adicionais para enviar com todas as solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="ece6e-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="ece6e-312">No cliente do .NET, essas opções podem ser modificadas pelo delegado opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="ece6e-312">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="ece6e-313">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="ece6e-313">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="ece6e-314">No cliente de Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado das `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="ece6e-314">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>


```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="ece6e-315">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ece6e-315">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
