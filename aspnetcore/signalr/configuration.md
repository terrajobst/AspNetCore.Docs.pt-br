---
title: Configuração do Signalr ASP.NET Core
author: bradygaster
description: Saiba como configurar ASP.NET Core aplicativos Signalr.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 156ffac83fbdf61fd88ad8acc307c2c701c46bca
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773924"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="b75f8-103">Configuração do Signalr ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b75f8-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b75f8-104">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b75f8-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b75f8-105">ASP.NET Core Signalr dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b75f8-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b75f8-106">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="b75f8-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b75f8-107">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol), que pode ser adicionado após [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b75f8-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b75f8-108">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="b75f8-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b75f8-109">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="b75f8-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b75f8-110">Consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="b75f8-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="b75f8-111">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="b75f8-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

<span data-ttu-id="b75f8-112">No cliente .net, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b75f8-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b75f8-113">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="b75f8-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="b75f8-114">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="b75f8-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b75f8-115">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="b75f8-115">MessagePack serialization options</span></span>

<span data-ttu-id="b75f8-116">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b75f8-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b75f8-117">Consulte [MessagePack no signalr](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="b75f8-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b75f8-118">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="b75f8-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b75f8-119">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="b75f8-119">Configure server options</span></span>

<span data-ttu-id="b75f8-120">A tabela a seguir descreve as opções para configurar os hubs de sinalização:</span><span class="sxs-lookup"><span data-stu-id="b75f8-120">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="b75f8-121">Opção</span><span class="sxs-lookup"><span data-stu-id="b75f8-121">Option</span></span> | <span data-ttu-id="b75f8-122">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b75f8-122">Default Value</span></span> | <span data-ttu-id="b75f8-123">Descrição</span><span class="sxs-lookup"><span data-stu-id="b75f8-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b75f8-124">30 segundos</span><span class="sxs-lookup"><span data-stu-id="b75f8-124">30 seconds</span></span> | <span data-ttu-id="b75f8-125">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="b75f8-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b75f8-126">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="b75f8-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b75f8-127">O valor recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="b75f8-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b75f8-128">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b75f8-128">15 seconds</span></span> | <span data-ttu-id="b75f8-129">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="b75f8-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b75f8-130">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="b75f8-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b75f8-131">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b75f8-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b75f8-132">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b75f8-132">15 seconds</span></span> | <span data-ttu-id="b75f8-133">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="b75f8-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b75f8-134">Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` /configuraçãonocliente `serverTimeoutInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="b75f8-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b75f8-135">O valor `ServerTimeout` recomendado / `KeepAliveInterval` é odobrodovalor.`serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="b75f8-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b75f8-136">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="b75f8-136">All installed protocols</span></span> | <span data-ttu-id="b75f8-137">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="b75f8-137">Protocols supported by this hub.</span></span> <span data-ttu-id="b75f8-138">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="b75f8-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b75f8-139">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="b75f8-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b75f8-140">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="b75f8-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="b75f8-141">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="b75f8-141">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="b75f8-142">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="b75f8-142">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="b75f8-143">32 KB</span><span class="sxs-lookup"><span data-stu-id="b75f8-143">32 KB</span></span> | <span data-ttu-id="b75f8-144">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="b75f8-144">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="b75f8-145">Opção</span><span class="sxs-lookup"><span data-stu-id="b75f8-145">Option</span></span> | <span data-ttu-id="b75f8-146">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b75f8-146">Default Value</span></span> | <span data-ttu-id="b75f8-147">Descrição</span><span class="sxs-lookup"><span data-stu-id="b75f8-147">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b75f8-148">30 segundos</span><span class="sxs-lookup"><span data-stu-id="b75f8-148">30 seconds</span></span> | <span data-ttu-id="b75f8-149">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="b75f8-149">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b75f8-150">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="b75f8-150">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b75f8-151">O valor recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="b75f8-151">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b75f8-152">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b75f8-152">15 seconds</span></span> | <span data-ttu-id="b75f8-153">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="b75f8-153">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b75f8-154">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="b75f8-154">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b75f8-155">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b75f8-155">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b75f8-156">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b75f8-156">15 seconds</span></span> | <span data-ttu-id="b75f8-157">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="b75f8-157">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b75f8-158">Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` /configuraçãonocliente `serverTimeoutInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="b75f8-158">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b75f8-159">O valor `ServerTimeout` recomendado / `KeepAliveInterval` é odobrodovalor.`serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="b75f8-159">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b75f8-160">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="b75f8-160">All installed protocols</span></span> | <span data-ttu-id="b75f8-161">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="b75f8-161">Protocols supported by this hub.</span></span> <span data-ttu-id="b75f8-162">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="b75f8-162">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b75f8-163">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="b75f8-163">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b75f8-164">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="b75f8-164">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="b75f8-165">Opção</span><span class="sxs-lookup"><span data-stu-id="b75f8-165">Option</span></span> | <span data-ttu-id="b75f8-166">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b75f8-166">Default Value</span></span> | <span data-ttu-id="b75f8-167">Descrição</span><span class="sxs-lookup"><span data-stu-id="b75f8-167">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="b75f8-168">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b75f8-168">15 seconds</span></span> | <span data-ttu-id="b75f8-169">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="b75f8-169">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b75f8-170">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="b75f8-170">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b75f8-171">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b75f8-171">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b75f8-172">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b75f8-172">15 seconds</span></span> | <span data-ttu-id="b75f8-173">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="b75f8-173">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b75f8-174">Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` /configuraçãonocliente `serverTimeoutInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="b75f8-174">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b75f8-175">O valor `ServerTimeout` recomendado / `KeepAliveInterval` é odobrodovalor.`serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="b75f8-175">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b75f8-176">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="b75f8-176">All installed protocols</span></span> | <span data-ttu-id="b75f8-177">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="b75f8-177">Protocols supported by this hub.</span></span> <span data-ttu-id="b75f8-178">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="b75f8-178">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b75f8-179">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="b75f8-179">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b75f8-180">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="b75f8-180">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="b75f8-181">As opções podem ser configuradas para todos os hubs, fornecendo um `AddSignalR` delegado de `Startup.ConfigureServices`opções para a chamada em.</span><span class="sxs-lookup"><span data-stu-id="b75f8-181">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="b75f8-182">As opções para um único Hub substituem as opções globais `AddSignalR` fornecidas no e podem ser <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configuradas usando:</span><span class="sxs-lookup"><span data-stu-id="b75f8-182">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b75f8-183">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="b75f8-183">Advanced HTTP configuration options</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b75f8-184">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="b75f8-184">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b75f8-185">Essas opções são configuradas passando um delegado [para\<MapHub T >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="b75f8-185">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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
::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="b75f8-186">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="b75f8-186">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b75f8-187">Essas opções são configuradas passando um delegado [para\<MapHub T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="b75f8-187">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

::: moniker-end

<span data-ttu-id="b75f8-188">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do Signalr ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b75f8-188">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="b75f8-189">Opção</span><span class="sxs-lookup"><span data-stu-id="b75f8-189">Option</span></span> | <span data-ttu-id="b75f8-190">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b75f8-190">Default Value</span></span> | <span data-ttu-id="b75f8-191">Descrição</span><span class="sxs-lookup"><span data-stu-id="b75f8-191">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b75f8-192">32 KB</span><span class="sxs-lookup"><span data-stu-id="b75f8-192">32 KB</span></span> | <span data-ttu-id="b75f8-193">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="b75f8-193">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="b75f8-194">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="b75f8-194">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b75f8-195">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="b75f8-195">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b75f8-196">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="b75f8-196">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b75f8-197">32 KB</span><span class="sxs-lookup"><span data-stu-id="b75f8-197">32 KB</span></span> | <span data-ttu-id="b75f8-198">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="b75f8-198">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="b75f8-199">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="b75f8-199">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b75f8-200">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="b75f8-200">All Transports are enabled.</span></span> | <span data-ttu-id="b75f8-201">Uma enumeração de sinalizadores de `HttpTransportType` bits de valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="b75f8-201">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b75f8-202">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="b75f8-202">See below.</span></span> | <span data-ttu-id="b75f8-203">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="b75f8-203">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b75f8-204">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="b75f8-204">See below.</span></span> | <span data-ttu-id="b75f8-205">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b75f8-205">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="b75f8-206">Opção</span><span class="sxs-lookup"><span data-stu-id="b75f8-206">Option</span></span> | <span data-ttu-id="b75f8-207">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b75f8-207">Default Value</span></span> | <span data-ttu-id="b75f8-208">Descrição</span><span class="sxs-lookup"><span data-stu-id="b75f8-208">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b75f8-209">32 KB</span><span class="sxs-lookup"><span data-stu-id="b75f8-209">32 KB</span></span> | <span data-ttu-id="b75f8-210">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="b75f8-210">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="b75f8-211">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="b75f8-211">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b75f8-212">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="b75f8-212">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b75f8-213">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="b75f8-213">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b75f8-214">32 KB</span><span class="sxs-lookup"><span data-stu-id="b75f8-214">32 KB</span></span> | <span data-ttu-id="b75f8-215">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="b75f8-215">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="b75f8-216">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="b75f8-216">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b75f8-217">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="b75f8-217">All Transports are enabled.</span></span> | <span data-ttu-id="b75f8-218">Uma enumeração de sinalizadores de `HttpTransportType` bits de valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="b75f8-218">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b75f8-219">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="b75f8-219">See below.</span></span> | <span data-ttu-id="b75f8-220">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="b75f8-220">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b75f8-221">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="b75f8-221">See below.</span></span> | <span data-ttu-id="b75f8-222">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b75f8-222">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="b75f8-223">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="b75f8-223">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b75f8-224">Opção</span><span class="sxs-lookup"><span data-stu-id="b75f8-224">Option</span></span> | <span data-ttu-id="b75f8-225">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b75f8-225">Default Value</span></span> | <span data-ttu-id="b75f8-226">Descrição</span><span class="sxs-lookup"><span data-stu-id="b75f8-226">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b75f8-227">90 segundos</span><span class="sxs-lookup"><span data-stu-id="b75f8-227">90 seconds</span></span> | <span data-ttu-id="b75f8-228">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="b75f8-228">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b75f8-229">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="b75f8-229">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b75f8-230">O transporte WebSocket tem opções adicionais que podem ser configuradas `WebSockets` usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="b75f8-230">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b75f8-231">Opção</span><span class="sxs-lookup"><span data-stu-id="b75f8-231">Option</span></span> | <span data-ttu-id="b75f8-232">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b75f8-232">Default Value</span></span> | <span data-ttu-id="b75f8-233">Descrição</span><span class="sxs-lookup"><span data-stu-id="b75f8-233">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b75f8-234">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b75f8-234">5 seconds</span></span> | <span data-ttu-id="b75f8-235">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="b75f8-235">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b75f8-236">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="b75f8-236">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b75f8-237">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="b75f8-237">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b75f8-238">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="b75f8-238">Configure client options</span></span>

<span data-ttu-id="b75f8-239">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b75f8-239">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b75f8-240">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como `HubConnection` por si só.</span><span class="sxs-lookup"><span data-stu-id="b75f8-240">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b75f8-241">Configurar registro em log</span><span class="sxs-lookup"><span data-stu-id="b75f8-241">Configure logging</span></span>

<span data-ttu-id="b75f8-242">O registro em log é configurado no cliente .NET `ConfigureLogging` usando o método.</span><span class="sxs-lookup"><span data-stu-id="b75f8-242">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b75f8-243">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="b75f8-243">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b75f8-244">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="b75f8-244">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b75f8-245">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="b75f8-245">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b75f8-246">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="b75f8-246">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b75f8-247">Por exemplo, para habilitar o log do console, `Microsoft.Extensions.Logging.Console` instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="b75f8-247">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b75f8-248">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="b75f8-248">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b75f8-249">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="b75f8-249">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b75f8-250">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="b75f8-250">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b75f8-251">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="b75f8-251">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b75f8-252">Em vez de `LogLevel` um valor, você também pode fornecer `string` um valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="b75f8-252">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="b75f8-253">Isso é útil ao configurar o `LogLevel` registro em log do signalr em ambientes em que você não tem acesso às constantes.</span><span class="sxs-lookup"><span data-stu-id="b75f8-253">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="b75f8-254">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="b75f8-254">The following table lists the available log levels.</span></span> <span data-ttu-id="b75f8-255">O valor que você fornece `configureLogging` para definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="b75f8-255">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="b75f8-256">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="b75f8-256">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="b75f8-257">Cadeia de Caracteres</span><span class="sxs-lookup"><span data-stu-id="b75f8-257">String</span></span>                      | <span data-ttu-id="b75f8-258">LogLevel</span><span class="sxs-lookup"><span data-stu-id="b75f8-258">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="b75f8-259">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="b75f8-259">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="b75f8-260">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="b75f8-260">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="b75f8-261">Para desabilitar completamente o registro `signalR.LogLevel.None` `configureLogging` em log, especifique no método.</span><span class="sxs-lookup"><span data-stu-id="b75f8-261">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b75f8-262">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico do signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b75f8-262">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b75f8-263">O cliente Java Signalr usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="b75f8-263">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b75f8-264">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="b75f8-264">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b75f8-265">O trecho de código a seguir mostra como `java.util.logging` usar o com o cliente Java do signalr.</span><span class="sxs-lookup"><span data-stu-id="b75f8-265">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b75f8-266">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="b75f8-266">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b75f8-267">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="b75f8-267">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b75f8-268">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="b75f8-268">Configure allowed transports</span></span>

<span data-ttu-id="b75f8-269">Os transportes usados pelo signalr podem ser configurados na `WithUrl` chamada (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b75f8-269">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b75f8-270">Um bit-a-ou dos valores `HttpTransportType` de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="b75f8-270">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b75f8-271">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="b75f8-271">All transports are enabled by default.</span></span>

<span data-ttu-id="b75f8-272">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="b75f8-272">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b75f8-273">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="b75f8-273">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b75f8-274">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="b75f8-274">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="b75f8-275">No cliente Java, o transporte é selecionado com o `withTransport` método `HttpHubConnectionBuilder`no.</span><span class="sxs-lookup"><span data-stu-id="b75f8-275">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="b75f8-276">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b75f8-276">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b75f8-277">O cliente Java do Signalr ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="b75f8-277">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b75f8-278">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="b75f8-278">Configure bearer authentication</span></span>

<span data-ttu-id="b75f8-279">Para fornecer dados de autenticação juntamente com solicitações do signalr, `AccessTokenProvider` use a`accessTokenFactory` opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="b75f8-279">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b75f8-280">No cliente .net, esse token de acesso é passado como um token http "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="b75f8-280">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b75f8-281">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="b75f8-281">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b75f8-282">Nesses casos, o token de acesso é fornecido como um valor `access_token`de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="b75f8-282">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b75f8-283">No cliente .net, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="b75f8-283">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b75f8-284">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="b75f8-284">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="b75f8-285">No cliente Java do Signalr, você pode configurar um token de portador a ser usado para autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b75f8-285">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b75f8-286">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [única\<cadeia de caracteres>](https://reactivex.io/documentation/single.html)[de RxJava](https://github.com/ReactiveX/RxJava).</span><span class="sxs-lookup"><span data-stu-id="b75f8-286">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b75f8-287">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="b75f8-287">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b75f8-288">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="b75f8-288">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b75f8-289">Opções adicionais para configurar o tempo limite e o `HubConnection` comportamento Keep-Alive estão disponíveis no próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="b75f8-289">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="b75f8-290">.NET</span><span class="sxs-lookup"><span data-stu-id="b75f8-290">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b75f8-291">Opção</span><span class="sxs-lookup"><span data-stu-id="b75f8-291">Option</span></span> | <span data-ttu-id="b75f8-292">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b75f8-292">Default value</span></span> | <span data-ttu-id="b75f8-293">Descrição</span><span class="sxs-lookup"><span data-stu-id="b75f8-293">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b75f8-294">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b75f8-294">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b75f8-295">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b75f8-295">Timeout for server activity.</span></span> <span data-ttu-id="b75f8-296">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `Closed` disparará o evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b75f8-296">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b75f8-297">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="b75f8-297">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b75f8-298">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="b75f8-298">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b75f8-299">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b75f8-299">15 seconds</span></span> | <span data-ttu-id="b75f8-300">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="b75f8-300">Timeout for initial server handshake.</span></span> <span data-ttu-id="b75f8-301">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b75f8-301">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b75f8-302">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="b75f8-302">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b75f8-303">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b75f8-303">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b75f8-304">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b75f8-304">15 seconds</span></span> | <span data-ttu-id="b75f8-305">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="b75f8-305">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b75f8-306">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="b75f8-306">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b75f8-307">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="b75f8-307">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="b75f8-308">No cliente .net, os valores de tempo limite são `TimeSpan` especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="b75f8-308">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="b75f8-309">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b75f8-309">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b75f8-310">Opção</span><span class="sxs-lookup"><span data-stu-id="b75f8-310">Option</span></span> | <span data-ttu-id="b75f8-311">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b75f8-311">Default value</span></span> | <span data-ttu-id="b75f8-312">Descrição</span><span class="sxs-lookup"><span data-stu-id="b75f8-312">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b75f8-313">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b75f8-313">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b75f8-314">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b75f8-314">Timeout for server activity.</span></span> <span data-ttu-id="b75f8-315">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onclose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="b75f8-315">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b75f8-316">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="b75f8-316">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b75f8-317">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="b75f8-317">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="b75f8-318">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b75f8-318">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="b75f8-319">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="b75f8-319">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b75f8-320">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="b75f8-320">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b75f8-321">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="b75f8-321">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="b75f8-322">Java</span><span class="sxs-lookup"><span data-stu-id="b75f8-322">Java</span></span>](#tab/java)

| <span data-ttu-id="b75f8-323">Opção</span><span class="sxs-lookup"><span data-stu-id="b75f8-323">Option</span></span> | <span data-ttu-id="b75f8-324">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b75f8-324">Default value</span></span> | <span data-ttu-id="b75f8-325">Descrição</span><span class="sxs-lookup"><span data-stu-id="b75f8-325">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b75f8-326">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b75f8-326">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b75f8-327">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b75f8-327">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b75f8-328">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b75f8-328">Timeout for server activity.</span></span> <span data-ttu-id="b75f8-329">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onClose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="b75f8-329">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b75f8-330">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="b75f8-330">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b75f8-331">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="b75f8-331">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b75f8-332">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b75f8-332">15 seconds</span></span> | <span data-ttu-id="b75f8-333">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="b75f8-333">Timeout for initial server handshake.</span></span> <span data-ttu-id="b75f8-334">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b75f8-334">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b75f8-335">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="b75f8-335">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b75f8-336">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b75f8-336">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="b75f8-337">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="b75f8-337">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="b75f8-338">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b75f8-338">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b75f8-339">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="b75f8-339">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b75f8-340">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="b75f8-340">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b75f8-341">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="b75f8-341">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="b75f8-342">.NET</span><span class="sxs-lookup"><span data-stu-id="b75f8-342">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b75f8-343">Opção</span><span class="sxs-lookup"><span data-stu-id="b75f8-343">Option</span></span> | <span data-ttu-id="b75f8-344">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b75f8-344">Default value</span></span> | <span data-ttu-id="b75f8-345">Descrição</span><span class="sxs-lookup"><span data-stu-id="b75f8-345">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b75f8-346">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b75f8-346">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b75f8-347">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b75f8-347">Timeout for server activity.</span></span> <span data-ttu-id="b75f8-348">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `Closed` disparará o evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b75f8-348">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b75f8-349">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="b75f8-349">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b75f8-350">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="b75f8-350">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b75f8-351">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b75f8-351">15 seconds</span></span> | <span data-ttu-id="b75f8-352">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="b75f8-352">Timeout for initial server handshake.</span></span> <span data-ttu-id="b75f8-353">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b75f8-353">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b75f8-354">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="b75f8-354">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b75f8-355">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b75f8-355">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="b75f8-356">No cliente .net, os valores de tempo limite são `TimeSpan` especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="b75f8-356">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="b75f8-357">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b75f8-357">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b75f8-358">Opção</span><span class="sxs-lookup"><span data-stu-id="b75f8-358">Option</span></span> | <span data-ttu-id="b75f8-359">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b75f8-359">Default value</span></span> | <span data-ttu-id="b75f8-360">Descrição</span><span class="sxs-lookup"><span data-stu-id="b75f8-360">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b75f8-361">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b75f8-361">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b75f8-362">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b75f8-362">Timeout for server activity.</span></span> <span data-ttu-id="b75f8-363">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onclose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="b75f8-363">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b75f8-364">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="b75f8-364">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b75f8-365">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="b75f8-365">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="b75f8-366">Java</span><span class="sxs-lookup"><span data-stu-id="b75f8-366">Java</span></span>](#tab/java)

| <span data-ttu-id="b75f8-367">Opção</span><span class="sxs-lookup"><span data-stu-id="b75f8-367">Option</span></span> | <span data-ttu-id="b75f8-368">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b75f8-368">Default value</span></span> | <span data-ttu-id="b75f8-369">Descrição</span><span class="sxs-lookup"><span data-stu-id="b75f8-369">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b75f8-370">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b75f8-370">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b75f8-371">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="b75f8-371">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b75f8-372">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="b75f8-372">Timeout for server activity.</span></span> <span data-ttu-id="b75f8-373">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onClose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="b75f8-373">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b75f8-374">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="b75f8-374">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b75f8-375">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor, para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="b75f8-375">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b75f8-376">15 segundos</span><span class="sxs-lookup"><span data-stu-id="b75f8-376">15 seconds</span></span> | <span data-ttu-id="b75f8-377">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="b75f8-377">Timeout for initial server handshake.</span></span> <span data-ttu-id="b75f8-378">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b75f8-378">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b75f8-379">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="b75f8-379">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b75f8-380">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b75f8-380">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="b75f8-381">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="b75f8-381">Configure additional options</span></span>

<span data-ttu-id="b75f8-382">Opções adicionais podem ser configuradas `WithUrl` no`withUrl` método (em JavaScript) `HubConnectionBuilder` em `HttpHubConnectionBuilder` ou em várias APIs de configuração no cliente Java:</span><span class="sxs-lookup"><span data-stu-id="b75f8-382">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="b75f8-383">.NET</span><span class="sxs-lookup"><span data-stu-id="b75f8-383">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b75f8-384">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="b75f8-384">.NET Option</span></span> |  <span data-ttu-id="b75f8-385">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b75f8-385">Default value</span></span> | <span data-ttu-id="b75f8-386">Descrição</span><span class="sxs-lookup"><span data-stu-id="b75f8-386">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b75f8-387">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b75f8-387">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b75f8-388">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="b75f8-388">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b75f8-389">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b75f8-389">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b75f8-390">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="b75f8-390">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b75f8-391">Vazio</span><span class="sxs-lookup"><span data-stu-id="b75f8-391">Empty</span></span> | <span data-ttu-id="b75f8-392">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="b75f8-392">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b75f8-393">Vazio</span><span class="sxs-lookup"><span data-stu-id="b75f8-393">Empty</span></span> | <span data-ttu-id="b75f8-394">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b75f8-394">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b75f8-395">Vazio</span><span class="sxs-lookup"><span data-stu-id="b75f8-395">Empty</span></span> | <span data-ttu-id="b75f8-396">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b75f8-396">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b75f8-397">5 segundos</span><span class="sxs-lookup"><span data-stu-id="b75f8-397">5 seconds</span></span> | <span data-ttu-id="b75f8-398">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b75f8-398">WebSockets only.</span></span> <span data-ttu-id="b75f8-399">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="b75f8-399">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b75f8-400">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="b75f8-400">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b75f8-401">Vazio</span><span class="sxs-lookup"><span data-stu-id="b75f8-401">Empty</span></span> | <span data-ttu-id="b75f8-402">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b75f8-402">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b75f8-403">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b75f8-403">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b75f8-404">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b75f8-404">Not used for WebSocket connections.</span></span> <span data-ttu-id="b75f8-405">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="b75f8-405">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b75f8-406">Modifique as configurações desse valor padrão e retorne-o ou retorne uma `HttpMessageHandler` nova instância.</span><span class="sxs-lookup"><span data-stu-id="b75f8-406">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b75f8-407">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="b75f8-407">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b75f8-408">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b75f8-408">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b75f8-409">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b75f8-409">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b75f8-410">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="b75f8-410">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b75f8-411">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b75f8-411">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b75f8-412">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="b75f8-412">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="b75f8-413">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b75f8-413">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b75f8-414">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="b75f8-414">JavaScript Option</span></span> | <span data-ttu-id="b75f8-415">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b75f8-415">Default Value</span></span> | <span data-ttu-id="b75f8-416">Descrição</span><span class="sxs-lookup"><span data-stu-id="b75f8-416">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b75f8-417">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b75f8-417">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b75f8-418">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="b75f8-418">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b75f8-419">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b75f8-419">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b75f8-420">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="b75f8-420">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="b75f8-421">Java</span><span class="sxs-lookup"><span data-stu-id="b75f8-421">Java</span></span>](#tab/java)

| <span data-ttu-id="b75f8-422">Opção Java</span><span class="sxs-lookup"><span data-stu-id="b75f8-422">Java Option</span></span> | <span data-ttu-id="b75f8-423">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="b75f8-423">Default Value</span></span> | <span data-ttu-id="b75f8-424">Descrição</span><span class="sxs-lookup"><span data-stu-id="b75f8-424">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b75f8-425">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="b75f8-425">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b75f8-426">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="b75f8-426">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b75f8-427">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="b75f8-427">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b75f8-428">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="b75f8-428">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b75f8-429">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b75f8-429">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b75f8-430">Vazio</span><span class="sxs-lookup"><span data-stu-id="b75f8-430">Empty</span></span> | <span data-ttu-id="b75f8-431">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="b75f8-431">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b75f8-432">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="b75f8-432">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b75f8-433">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="b75f8-433">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b75f8-434">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b75f8-434">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b75f8-435">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b75f8-435">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
