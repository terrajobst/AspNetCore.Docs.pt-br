---
title: Configuração do ASP.NET SignalR Core
author: bradygaster
description: Saiba como configurar aplicativos do SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/06/2018
uid: signalr/configuration
ms.openlocfilehash: 06e86921c65297e93dcd8954ba4983d1577bb615
ms.sourcegitcommit: ca5f03210bedc61c6639a734ae5674bfe095dee8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55073147"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="4eeb5-103">Configuração do ASP.NET SignalR Core</span><span class="sxs-lookup"><span data-stu-id="4eeb5-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="4eeb5-104">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="4eeb5-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="4eeb5-105">SignalR do ASP.NET Core dá suporte a dois protocolos para codificação de mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="4eeb5-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="4eeb5-106">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="4eeb5-107">Serialização JSON pode ser configurada no servidor usando o [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) método de extensão, que pode ser adicionado após [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu `Startup.ConfigureServices` método.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4eeb5-108">O `AddJsonProtocol` leva um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="4eeb5-109">O [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) propriedade desse objeto é um JSON.NET `JsonSerializerSettings` objeto que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="4eeb5-110">Consulte a [documentação do JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="4eeb5-111">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes de "camelCase" padrão, use o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="4eeb5-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver = 
        new DefaultContractResolver();
    });
```

<span data-ttu-id="4eeb5-112">No cliente do .NET, o mesmo `AddJsonProtocol` método de extensão existe no [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="4eeb5-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="4eeb5-113">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="4eeb5-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="4eeb5-114">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="4eeb5-115">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="4eeb5-115">MessagePack serialization options</span></span>

<span data-ttu-id="4eeb5-116">MessagePack serialização pode ser configurada fornecendo um delegado para o [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) chamar.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="4eeb5-117">Ver [MessagePack no SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="4eeb5-118">Não é possível configurar a serialização de MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="4eeb5-119">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="4eeb5-119">Configure server options</span></span>

<span data-ttu-id="4eeb5-120">A tabela a seguir descreve as opções de configuração hubs do SignalR:</span><span class="sxs-lookup"><span data-stu-id="4eeb5-120">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="4eeb5-121">Opção</span><span class="sxs-lookup"><span data-stu-id="4eeb5-121">Option</span></span> | <span data-ttu-id="4eeb5-122">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="4eeb5-122">Default Value</span></span> | <span data-ttu-id="4eeb5-123">Descrição</span><span class="sxs-lookup"><span data-stu-id="4eeb5-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="4eeb5-124">30 segundos</span><span class="sxs-lookup"><span data-stu-id="4eeb5-124">30 seconds</span></span> | <span data-ttu-id="4eeb5-125">O servidor irá considerar o cliente desconectado se ele ainda não recebeu uma mensagem (incluindo keep-alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="4eeb5-126">É possível que ele demorar mais do que esse intervalo de tempo limite para o cliente, na verdade, ser marcado como desconectado devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-126">It is possible for it to take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="4eeb5-127">O valor recomendado é double o `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="4eeb5-128">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4eeb5-128">15 seconds</span></span> | <span data-ttu-id="4eeb5-129">Se o cliente não envia uma mensagem de handshake inicial dentro deste intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="4eeb5-130">Isso é uma configuração avançada que deve ser modificada apenas se os erros de tempo limite de handshake estiverem ocorrendo devido à latência de rede graves.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4eeb5-131">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub do SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4eeb5-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4eeb5-132">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4eeb5-132">15 seconds</span></span> | <span data-ttu-id="4eeb5-133">Se o servidor não enviou uma mensagem dentro deste intervalo, uma mensagem de ping é enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="4eeb5-134">Ao alterar `KeepAliveInterval`, altere o `ServerTimeout` / `serverTimeoutInMilliseconds` configuração no cliente.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="4eeb5-135">Recomendado `ServerTimeout` / `serverTimeoutInMilliseconds` valor é double o `KeepAliveInterval` valor.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="4eeb5-136">Todos os protocolos</span><span class="sxs-lookup"><span data-stu-id="4eeb5-136">All installed protocols</span></span> | <span data-ttu-id="4eeb5-137">Protocolos com suporte por esse hub.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-137">Protocols supported by this hub.</span></span> <span data-ttu-id="4eeb5-138">Por padrão, todos os protocolos registrados no servidor são permitidos, mas protocolos podem ser removidos dessa lista para desabilitar os protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4eeb5-139">Se `true`detalhada mensagens de exceção são retornadas aos clientes quando uma exceção é gerada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="4eeb5-140">O padrão é `false`, conforme essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="4eeb5-141">Opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para o `AddSignalR` chamar em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-141">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="4eeb5-142">Opções para um único hub substituem as opções de globais fornecidas no `AddSignalR` e pode ser configurado usando [AddHubOptions\<T >](/dotnet/api/microsoft.extensions.dependencyinjection.huboptionsdependencyinjectionextensions.addhuboptions):</span><span class="sxs-lookup"><span data-stu-id="4eeb5-142">Options for a single hub override the global options provided in `AddSignalR` and can be configured using [AddHubOptions\<T>](/dotnet/api/microsoft.extensions.dependencyinjection.huboptionsdependencyinjectionextensions.addhuboptions):</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

<span data-ttu-id="4eeb5-143">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-143">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="4eeb5-144">Essas opções são configuradas passando um delegado para [MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub).</span><span class="sxs-lookup"><span data-stu-id="4eeb5-144">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub).</span></span>

| <span data-ttu-id="4eeb5-145">Opção</span><span class="sxs-lookup"><span data-stu-id="4eeb5-145">Option</span></span> | <span data-ttu-id="4eeb5-146">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="4eeb5-146">Default Value</span></span> | <span data-ttu-id="4eeb5-147">Descrição</span><span class="sxs-lookup"><span data-stu-id="4eeb5-147">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="4eeb5-148">32 KB</span><span class="sxs-lookup"><span data-stu-id="4eeb5-148">32 KB</span></span> | <span data-ttu-id="4eeb5-149">O número máximo de bytes recebidos do cliente que os buffers de servidor.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-149">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="4eeb5-150">Aumentar esse valor permite que o servidor receber mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-150">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="4eeb5-151">Dados coletados automaticamente a partir de `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-151">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="4eeb5-152">Uma lista dos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objetos usados para determinar se um cliente está autorizado a conectar-se ao hub.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-152">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="4eeb5-153">32 KB</span><span class="sxs-lookup"><span data-stu-id="4eeb5-153">32 KB</span></span> | <span data-ttu-id="4eeb5-154">O número máximo de bytes enviados pelo aplicativo que os buffers de servidor.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-154">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="4eeb5-155">Aumentar esse valor permite que o servidor enviar mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-155">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="4eeb5-156">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-156">All Transports are enabled.</span></span> | <span data-ttu-id="4eeb5-157">Um bitmask de `HttpTransportType` valores que possam restringir os transportes de um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-157">A bitmask of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="4eeb5-158">Veja a seguir.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-158">See below.</span></span> | <span data-ttu-id="4eeb5-159">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-159">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="4eeb5-160">Veja a seguir.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-160">See below.</span></span> | <span data-ttu-id="4eeb5-161">Opções adicionais específicas para o transporte de WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-161">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="4eeb5-162">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando o `LongPolling` propriedade:</span><span class="sxs-lookup"><span data-stu-id="4eeb5-162">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="4eeb5-163">Opção</span><span class="sxs-lookup"><span data-stu-id="4eeb5-163">Option</span></span> | <span data-ttu-id="4eeb5-164">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="4eeb5-164">Default Value</span></span> | <span data-ttu-id="4eeb5-165">Descrição</span><span class="sxs-lookup"><span data-stu-id="4eeb5-165">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="4eeb5-166">90 segundos</span><span class="sxs-lookup"><span data-stu-id="4eeb5-166">90 seconds</span></span> | <span data-ttu-id="4eeb5-167">A quantidade máxima de tempo o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma solicitação de pesquisa único.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-167">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="4eeb5-168">Diminuir esse valor faz com que o cliente emitir novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-168">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="4eeb5-169">O transporte de WebSocket tem opções adicionais que podem ser configuradas usando o `WebSockets` propriedade:</span><span class="sxs-lookup"><span data-stu-id="4eeb5-169">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="4eeb5-170">Opção</span><span class="sxs-lookup"><span data-stu-id="4eeb5-170">Option</span></span> | <span data-ttu-id="4eeb5-171">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="4eeb5-171">Default Value</span></span> | <span data-ttu-id="4eeb5-172">Descrição</span><span class="sxs-lookup"><span data-stu-id="4eeb5-172">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="4eeb5-173">5 segundos</span><span class="sxs-lookup"><span data-stu-id="4eeb5-173">5 seconds</span></span> | <span data-ttu-id="4eeb5-174">Depois de fecha o servidor, se o cliente não conseguir fechar dentro deste intervalo de tempo, a conexão é encerrada.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-174">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="4eeb5-175">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-175">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="4eeb5-176">O delegado recebe os valores solicitados pelo cliente como entrada e deve retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-176">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="4eeb5-177">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="4eeb5-177">Configure client options</span></span>

<span data-ttu-id="4eeb5-178">Opções de cliente podem ser configuradas na `HubConnectionBuilder` (disponível em clientes .NET e JavaScript), do tipo, bem como no `HubConnection` em si.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-178">Client options can be configured on the `HubConnectionBuilder` type (available in both .NET and JavaScript clients), as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="4eeb5-179">Configurar o registro em log</span><span class="sxs-lookup"><span data-stu-id="4eeb5-179">Configure logging</span></span>

<span data-ttu-id="4eeb5-180">O log está configurado no cliente do .NET usando o `ConfigureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-180">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="4eeb5-181">Registro em log provedores e filtros pode ser registrado da mesma forma como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-181">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="4eeb5-182">Consulte a [entrar no ASP.NET Core](xref:fundamentals/logging/index) documentação para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-182">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="4eeb5-183">Para registrar os provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-183">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="4eeb5-184">Consulte a [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) seção do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-184">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="4eeb5-185">Por exemplo, para habilitar o log de Console, instale o `Microsoft.Extensions.Logging.Console` pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-185">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="4eeb5-186">Chamar o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="4eeb5-186">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="4eeb5-187">No cliente JavaScript, um semelhante `configureLogging` existe um método.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-187">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="4eeb5-188">Forneça um `LogLevel` valor que indica o nível mínimo de mensagens de log para produzir.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-188">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="4eeb5-189">Os logs são gravados na janela de console do navegador.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-189">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="4eeb5-190">Para desabilitar o registro em log totalmente, especifique `signalR.LogLevel.None` no `configureLogging` método.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-190">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="4eeb5-191">Níveis de log disponíveis para o cliente JavaScript estão listados abaixo.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-191">Log levels available to the JavaScript client are listed below.</span></span> <span data-ttu-id="4eeb5-192">Definindo o nível de log para um desses valores habilita o log de mensagens no **ou superior** desse nível.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-192">Setting the log level to one of these values enables logging of messages at **or above** that level.</span></span>

| <span data-ttu-id="4eeb5-193">Nível</span><span class="sxs-lookup"><span data-stu-id="4eeb5-193">Level</span></span> | <span data-ttu-id="4eeb5-194">Descrição</span><span class="sxs-lookup"><span data-stu-id="4eeb5-194">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="4eeb5-195">Nenhuma mensagem será registrada.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-195">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="4eeb5-196">Mensagens que indicam uma falha em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-196">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="4eeb5-197">Mensagens que indicam uma falha na operação atual.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-197">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="4eeb5-198">Mensagens que indicam um problema de não-fatais.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-198">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="4eeb5-199">Mensagens informativas.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-199">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="4eeb5-200">Mensagens de diagnóstico útil para depuração.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-200">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="4eeb5-201">Mensagens de diagnóstico muito detalhadas projetadas para diagnosticar problemas específicos.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-201">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

### <a name="configure-allowed-transports"></a><span data-ttu-id="4eeb5-202">Configure transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="4eeb5-202">Configure allowed transports</span></span>

<span data-ttu-id="4eeb5-203">Os transportes usados pelo SignalR podem ser configurados na `WithUrl` chamar (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4eeb5-203">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="4eeb5-204">Um bitwise OR dos valores de `HttpTransportType` pode ser usado para restringir o cliente para usar somente os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-204">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="4eeb5-205">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-205">All transports are enabled by default.</span></span>

<span data-ttu-id="4eeb5-206">Por exemplo, para desabilitar o transporte de eventos do Server-Sent, mas permita conexões WebSocket e sondagem longa:</span><span class="sxs-lookup"><span data-stu-id="4eeb5-206">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="4eeb5-207">No cliente JavaScript, transportes são configurados, definindo o `transport` campo no objeto de opções fornecido ao `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="4eeb5-207">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="4eeb5-208">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="4eeb5-208">Configure bearer authentication</span></span>

<span data-ttu-id="4eeb5-209">Para fornecer dados de autenticação junto com as solicitações de SignalR, use o `AccessTokenProvider` opção (`accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-209">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="4eeb5-210">No cliente do .NET, esse token de acesso é passado como um HTTP "Autenticação do portador" token (usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="4eeb5-210">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="4eeb5-211">No cliente JavaScript, o token de acesso é usado como um token de portador **exceto** em alguns casos em que o navegador APIs restringir a capacidade de aplicar cabeçalhos (especificamente, em solicitações de eventos do Server-sent e WebSockets).</span><span class="sxs-lookup"><span data-stu-id="4eeb5-211">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="4eeb5-212">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token`.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-212">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="4eeb5-213">No cliente do .NET, o `AccessTokenProvider` opção pode ser especificada usando o delegado de opções no `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="4eeb5-213">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="4eeb5-214">No cliente JavaScript, o token de acesso é configurado definindo a `accessTokenFactory` campo no objeto de opções no `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="4eeb5-214">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="4eeb5-215">Configurar tempo limite e opções de keep alive</span><span class="sxs-lookup"><span data-stu-id="4eeb5-215">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="4eeb5-216">Opções adicionais para configurar o tempo limite e o comportamento de keep alive estão disponíveis no `HubConnection` objeto em si:</span><span class="sxs-lookup"><span data-stu-id="4eeb5-216">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

| <span data-ttu-id="4eeb5-217">Opção de .NET</span><span class="sxs-lookup"><span data-stu-id="4eeb5-217">.NET Option</span></span> | <span data-ttu-id="4eeb5-218">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="4eeb5-218">JavaScript Option</span></span> | <span data-ttu-id="4eeb5-219">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="4eeb5-219">Default Value</span></span> | <span data-ttu-id="4eeb5-220">Descrição</span><span class="sxs-lookup"><span data-stu-id="4eeb5-220">Description</span></span> |
| ----------- | ----------------- | ------------- | ----------- |
| `ServerTimeout` | `serverTimeoutInMilliseconds` | <span data-ttu-id="4eeb5-221">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="4eeb5-221">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4eeb5-222">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-222">Timeout for server activity.</span></span> <span data-ttu-id="4eeb5-223">Se o servidor não enviou uma mensagem nesse intervalo, o cliente considera o servidor desconectado e gatilhos do `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4eeb5-223">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4eeb5-224">Esse valor deve ser grande o suficiente para uma mensagem de ping a serem enviados do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-224">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4eeb5-225">O valor recomendado é um número pelo menos duas vezes o servidor `KeepAliveInterval` valor, para dar tempo para pings de chegada.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-225">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="4eeb5-226">Não é configurável</span><span class="sxs-lookup"><span data-stu-id="4eeb5-226">Not configurable</span></span> | <span data-ttu-id="4eeb5-227">15 segundos</span><span class="sxs-lookup"><span data-stu-id="4eeb5-227">15 seconds</span></span> | <span data-ttu-id="4eeb5-228">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-228">Timeout for initial server handshake.</span></span> <span data-ttu-id="4eeb5-229">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancela o handshake e gatilhos do `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4eeb5-229">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4eeb5-230">Isso é uma configuração avançada que deve ser modificada apenas se os erros de tempo limite de handshake estiverem ocorrendo devido à latência de rede graves.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-230">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4eeb5-231">Para obter mais detalhes sobre o processo de Handshake, consulte a [especificação de protocolo de Hub do SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4eeb5-231">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="4eeb5-232">No cliente do .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-232">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span> <span data-ttu-id="4eeb5-233">No cliente JavaScript, os valores de tempo limite são especificados como um número que indica a duração em milissegundos.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-233">In the JavaScript client, timeout values are specified as a number indicating the duration in milliseconds.</span></span>

### <a name="configure-additional-options"></a><span data-ttu-id="4eeb5-234">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="4eeb5-234">Configure additional options</span></span>

<span data-ttu-id="4eeb5-235">Opções adicionais podem ser configuradas na `WithUrl` (`withUrl` em JavaScript) método em `HubConnectionBuilder`:</span><span class="sxs-lookup"><span data-stu-id="4eeb5-235">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder`:</span></span>

| <span data-ttu-id="4eeb5-236">Opção de .NET</span><span class="sxs-lookup"><span data-stu-id="4eeb5-236">.NET Option</span></span> | <span data-ttu-id="4eeb5-237">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="4eeb5-237">JavaScript Option</span></span> | <span data-ttu-id="4eeb5-238">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="4eeb5-238">Default Value</span></span> | <span data-ttu-id="4eeb5-239">Descrição</span><span class="sxs-lookup"><span data-stu-id="4eeb5-239">Description</span></span> |
| ----------- | ----------------- | ------------- | ----------- |
| `AccessTokenProvider` | `accessTokenFactory` | `null` | <span data-ttu-id="4eeb5-240">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-240">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `skipNegotiation` | `false` | <span data-ttu-id="4eeb5-241">Defina isso como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-241">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4eeb5-242">**Só tem suporte quando o transporte de WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-242">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4eeb5-243">Essa configuração não pode ser habilitada ao usar o serviço do Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-243">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="4eeb5-244">Não é configurável \*</span><span class="sxs-lookup"><span data-stu-id="4eeb5-244">Not configurable \*</span></span> | <span data-ttu-id="4eeb5-245">Vazio</span><span class="sxs-lookup"><span data-stu-id="4eeb5-245">Empty</span></span> | <span data-ttu-id="4eeb5-246">Uma coleção de certificados TLS para enviar para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-246">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="4eeb5-247">Não é configurável \*</span><span class="sxs-lookup"><span data-stu-id="4eeb5-247">Not configurable \*</span></span> | <span data-ttu-id="4eeb5-248">Vazio</span><span class="sxs-lookup"><span data-stu-id="4eeb5-248">Empty</span></span> | <span data-ttu-id="4eeb5-249">Uma coleção de cookies HTTP a ser enviado com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-249">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="4eeb5-250">Não é configurável \*</span><span class="sxs-lookup"><span data-stu-id="4eeb5-250">Not configurable \*</span></span> | <span data-ttu-id="4eeb5-251">Vazio</span><span class="sxs-lookup"><span data-stu-id="4eeb5-251">Empty</span></span> | <span data-ttu-id="4eeb5-252">Credenciais devem ser enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-252">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="4eeb5-253">Não é configurável \*</span><span class="sxs-lookup"><span data-stu-id="4eeb5-253">Not configurable \*</span></span> | <span data-ttu-id="4eeb5-254">5 segundos</span><span class="sxs-lookup"><span data-stu-id="4eeb5-254">5 seconds</span></span> | <span data-ttu-id="4eeb5-255">WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-255">WebSockets only.</span></span> <span data-ttu-id="4eeb5-256">A quantidade máxima de tempo que o cliente aguardará depois de fechamento para o servidor confirmar a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-256">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="4eeb5-257">Se o servidor não reconhece o fechamento dentro desse período, o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-257">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="4eeb5-258">Não é configurável \*</span><span class="sxs-lookup"><span data-stu-id="4eeb5-258">Not configurable \*</span></span> | <span data-ttu-id="4eeb5-259">Vazio</span><span class="sxs-lookup"><span data-stu-id="4eeb5-259">Empty</span></span> | <span data-ttu-id="4eeb5-260">Um dicionário de cabeçalhos HTTP adicionais para enviar com todas as solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-260">A dictionary of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | <span data-ttu-id="4eeb5-261">Não é configurável \*</span><span class="sxs-lookup"><span data-stu-id="4eeb5-261">Not configurable \*</span></span> | `null` | <span data-ttu-id="4eeb5-262">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-262">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="4eeb5-263">Não é usado para conexões de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-263">Not used for WebSocket connections.</span></span> <span data-ttu-id="4eeb5-264">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-264">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="4eeb5-265">Modificar as configurações nesse valor padrão e retorná-lo ou retornar um novo `HttpMessageHandler` instância.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-265">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="4eeb5-266">**Ao substituir o manipulador Certifique-se de copiar as configurações que você deseja impedir que o manipulador fornecido, caso contrário, as opções configuradas (como cabeçalhos e Cookies) não se aplicam ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="4eeb5-266">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | <span data-ttu-id="4eeb5-267">Não é configurável \*</span><span class="sxs-lookup"><span data-stu-id="4eeb5-267">Not configurable \*</span></span> | `null` | <span data-ttu-id="4eeb5-268">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-268">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | <span data-ttu-id="4eeb5-269">Não é configurável \*</span><span class="sxs-lookup"><span data-stu-id="4eeb5-269">Not configurable \*</span></span> | `false` | <span data-ttu-id="4eeb5-270">Defina esse valor booliano para enviar as credenciais padrão para solicitações HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-270">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="4eeb5-271">Isso permite que o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-271">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | <span data-ttu-id="4eeb5-272">Não é configurável \*</span><span class="sxs-lookup"><span data-stu-id="4eeb5-272">Not configurable \*</span></span> | `null` | <span data-ttu-id="4eeb5-273">Um delegado que pode ser usado para configurar opções adicionais de WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-273">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="4eeb5-274">Recebe uma instância do [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usado para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-274">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

<span data-ttu-id="4eeb5-275">Opções marcadas com um asterisco (\*) não são configuráveis no cliente JavaScript, devido a limitações no navegador APIs.</span><span class="sxs-lookup"><span data-stu-id="4eeb5-275">Options marked with an asterisk (\*) aren't configurable in the JavaScript client, due to limitations in browser APIs.</span></span>

<span data-ttu-id="4eeb5-276">No cliente do .NET, essas opções podem ser modificadas pelo delegado opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="4eeb5-276">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="4eeb5-277">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="4eeb5-277">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

## <a name="additional-resources"></a><span data-ttu-id="4eeb5-278">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4eeb5-278">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
