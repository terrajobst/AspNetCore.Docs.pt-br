---
title: Configuração do Signalr ASP.NET Core
author: bradygaster
description: Saiba como configurar ASP.NET Core aplicativos Signalr.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 475d9664c588c06bfcd816959be8a425ee01c023
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68915075"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="19ec5-103">Configuração do Signalr ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="19ec5-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="19ec5-104">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="19ec5-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="19ec5-105">ASP.NET Core Signalr dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="19ec5-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="19ec5-106">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="19ec5-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="19ec5-107">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol), que pode ser adicionado após [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="19ec5-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="19ec5-108">O `AddJsonProtocol` método usa um delegado que recebe um `options` objeto.</span><span class="sxs-lookup"><span data-stu-id="19ec5-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="19ec5-109">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.NET `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="19ec5-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="19ec5-110">Consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="19ec5-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="19ec5-111">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="19ec5-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

<span data-ttu-id="19ec5-112">No cliente .net, o mesmo `AddJsonProtocol` método de extensão existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="19ec5-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="19ec5-113">O `Microsoft.Extensions.DependencyInjection` namespace deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="19ec5-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="19ec5-114">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="19ec5-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="19ec5-115">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="19ec5-115">MessagePack serialization options</span></span>

<span data-ttu-id="19ec5-116">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="19ec5-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="19ec5-117">Consulte [MessagePack no signalr](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="19ec5-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="19ec5-118">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="19ec5-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="19ec5-119">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="19ec5-119">Configure server options</span></span>

<span data-ttu-id="19ec5-120">A tabela a seguir descreve as opções para configurar os hubs de sinalização:</span><span class="sxs-lookup"><span data-stu-id="19ec5-120">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="19ec5-121">Opção</span><span class="sxs-lookup"><span data-stu-id="19ec5-121">Option</span></span> | <span data-ttu-id="19ec5-122">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="19ec5-122">Default Value</span></span> | <span data-ttu-id="19ec5-123">Descrição</span><span class="sxs-lookup"><span data-stu-id="19ec5-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="19ec5-124">30 segundos</span><span class="sxs-lookup"><span data-stu-id="19ec5-124">30 seconds</span></span> | <span data-ttu-id="19ec5-125">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="19ec5-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="19ec5-126">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="19ec5-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="19ec5-127">O valor recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="19ec5-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="19ec5-128">15 segundos</span><span class="sxs-lookup"><span data-stu-id="19ec5-128">15 seconds</span></span> | <span data-ttu-id="19ec5-129">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="19ec5-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="19ec5-130">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="19ec5-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="19ec5-131">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)do signalr.</span><span class="sxs-lookup"><span data-stu-id="19ec5-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="19ec5-132">15 segundos</span><span class="sxs-lookup"><span data-stu-id="19ec5-132">15 seconds</span></span> | <span data-ttu-id="19ec5-133">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="19ec5-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="19ec5-134">Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` /configuraçãonocliente `serverTimeoutInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="19ec5-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="19ec5-135">O valor `ServerTimeout` recomendado / `KeepAliveInterval` é odobrodovalor.`serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="19ec5-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="19ec5-136">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="19ec5-136">All installed protocols</span></span> | <span data-ttu-id="19ec5-137">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="19ec5-137">Protocols supported by this hub.</span></span> <span data-ttu-id="19ec5-138">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="19ec5-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="19ec5-139">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="19ec5-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="19ec5-140">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="19ec5-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="19ec5-141">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="19ec5-141">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="19ec5-142">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="19ec5-142">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="19ec5-143">32 KB</span><span class="sxs-lookup"><span data-stu-id="19ec5-143">32 KB</span></span> | <span data-ttu-id="19ec5-144">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="19ec5-144">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="19ec5-145">Opção</span><span class="sxs-lookup"><span data-stu-id="19ec5-145">Option</span></span> | <span data-ttu-id="19ec5-146">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="19ec5-146">Default Value</span></span> | <span data-ttu-id="19ec5-147">Descrição</span><span class="sxs-lookup"><span data-stu-id="19ec5-147">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="19ec5-148">30 segundos</span><span class="sxs-lookup"><span data-stu-id="19ec5-148">30 seconds</span></span> | <span data-ttu-id="19ec5-149">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="19ec5-149">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="19ec5-150">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="19ec5-150">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="19ec5-151">O valor recomendado é o dobro `KeepAliveInterval` do valor.</span><span class="sxs-lookup"><span data-stu-id="19ec5-151">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="19ec5-152">15 segundos</span><span class="sxs-lookup"><span data-stu-id="19ec5-152">15 seconds</span></span> | <span data-ttu-id="19ec5-153">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="19ec5-153">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="19ec5-154">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="19ec5-154">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="19ec5-155">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)do signalr.</span><span class="sxs-lookup"><span data-stu-id="19ec5-155">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="19ec5-156">15 segundos</span><span class="sxs-lookup"><span data-stu-id="19ec5-156">15 seconds</span></span> | <span data-ttu-id="19ec5-157">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="19ec5-157">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="19ec5-158">Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` /configuraçãonocliente `serverTimeoutInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="19ec5-158">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="19ec5-159">O valor `ServerTimeout` recomendado / `KeepAliveInterval` é odobrodovalor.`serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="19ec5-159">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="19ec5-160">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="19ec5-160">All installed protocols</span></span> | <span data-ttu-id="19ec5-161">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="19ec5-161">Protocols supported by this hub.</span></span> <span data-ttu-id="19ec5-162">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="19ec5-162">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="19ec5-163">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="19ec5-163">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="19ec5-164">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="19ec5-164">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="19ec5-165">Opção</span><span class="sxs-lookup"><span data-stu-id="19ec5-165">Option</span></span> | <span data-ttu-id="19ec5-166">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="19ec5-166">Default Value</span></span> | <span data-ttu-id="19ec5-167">Descrição</span><span class="sxs-lookup"><span data-stu-id="19ec5-167">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="19ec5-168">15 segundos</span><span class="sxs-lookup"><span data-stu-id="19ec5-168">15 seconds</span></span> | <span data-ttu-id="19ec5-169">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="19ec5-169">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="19ec5-170">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="19ec5-170">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="19ec5-171">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)do signalr.</span><span class="sxs-lookup"><span data-stu-id="19ec5-171">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="19ec5-172">15 segundos</span><span class="sxs-lookup"><span data-stu-id="19ec5-172">15 seconds</span></span> | <span data-ttu-id="19ec5-173">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="19ec5-173">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="19ec5-174">Ao alterar `KeepAliveInterval`, altere a `ServerTimeout` /configuraçãonocliente `serverTimeoutInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="19ec5-174">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="19ec5-175">O valor `ServerTimeout` recomendado / `KeepAliveInterval` é odobrodovalor.`serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="19ec5-175">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="19ec5-176">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="19ec5-176">All installed protocols</span></span> | <span data-ttu-id="19ec5-177">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="19ec5-177">Protocols supported by this hub.</span></span> <span data-ttu-id="19ec5-178">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="19ec5-178">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="19ec5-179">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="19ec5-179">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="19ec5-180">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="19ec5-180">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="19ec5-181">As opções podem ser configuradas para todos os hubs, fornecendo um `AddSignalR` delegado de `Startup.ConfigureServices`opções para a chamada em.</span><span class="sxs-lookup"><span data-stu-id="19ec5-181">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="19ec5-182">As opções para um único Hub substituem as opções globais `AddSignalR` fornecidas no e podem ser <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>configuradas usando:</span><span class="sxs-lookup"><span data-stu-id="19ec5-182">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="19ec5-183">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="19ec5-183">Advanced HTTP configuration options</span></span>

<span data-ttu-id="19ec5-184">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="19ec5-184">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="19ec5-185">Essas opções são configuradas passando um delegado [para\<MapHub T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="19ec5-185">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="19ec5-186">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do Signalr ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="19ec5-186">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="19ec5-187">Opção</span><span class="sxs-lookup"><span data-stu-id="19ec5-187">Option</span></span> | <span data-ttu-id="19ec5-188">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="19ec5-188">Default Value</span></span> | <span data-ttu-id="19ec5-189">Descrição</span><span class="sxs-lookup"><span data-stu-id="19ec5-189">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="19ec5-190">32 KB</span><span class="sxs-lookup"><span data-stu-id="19ec5-190">32 KB</span></span> | <span data-ttu-id="19ec5-191">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="19ec5-191">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="19ec5-192">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="19ec5-192">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="19ec5-193">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="19ec5-193">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="19ec5-194">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="19ec5-194">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="19ec5-195">32 KB</span><span class="sxs-lookup"><span data-stu-id="19ec5-195">32 KB</span></span> | <span data-ttu-id="19ec5-196">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="19ec5-196">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="19ec5-197">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="19ec5-197">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="19ec5-198">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="19ec5-198">All Transports are enabled.</span></span> | <span data-ttu-id="19ec5-199">Uma enumeração de sinalizadores de `HttpTransportType` bits de valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="19ec5-199">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="19ec5-200">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="19ec5-200">See below.</span></span> | <span data-ttu-id="19ec5-201">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="19ec5-201">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="19ec5-202">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="19ec5-202">See below.</span></span> | <span data-ttu-id="19ec5-203">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="19ec5-203">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="19ec5-204">Opção</span><span class="sxs-lookup"><span data-stu-id="19ec5-204">Option</span></span> | <span data-ttu-id="19ec5-205">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="19ec5-205">Default Value</span></span> | <span data-ttu-id="19ec5-206">Descrição</span><span class="sxs-lookup"><span data-stu-id="19ec5-206">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="19ec5-207">32 KB</span><span class="sxs-lookup"><span data-stu-id="19ec5-207">32 KB</span></span> | <span data-ttu-id="19ec5-208">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="19ec5-208">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="19ec5-209">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="19ec5-209">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="19ec5-210">Dados coletados automaticamente dos `Authorize` atributos aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="19ec5-210">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="19ec5-211">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="19ec5-211">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="19ec5-212">32 KB</span><span class="sxs-lookup"><span data-stu-id="19ec5-212">32 KB</span></span> | <span data-ttu-id="19ec5-213">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="19ec5-213">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="19ec5-214">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="19ec5-214">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="19ec5-215">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="19ec5-215">All Transports are enabled.</span></span> | <span data-ttu-id="19ec5-216">Uma enumeração de sinalizadores de `HttpTransportType` bits de valores que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="19ec5-216">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="19ec5-217">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="19ec5-217">See below.</span></span> | <span data-ttu-id="19ec5-218">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="19ec5-218">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="19ec5-219">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="19ec5-219">See below.</span></span> | <span data-ttu-id="19ec5-220">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="19ec5-220">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="19ec5-221">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a `LongPolling` Propriedade:</span><span class="sxs-lookup"><span data-stu-id="19ec5-221">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="19ec5-222">Opção</span><span class="sxs-lookup"><span data-stu-id="19ec5-222">Option</span></span> | <span data-ttu-id="19ec5-223">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="19ec5-223">Default Value</span></span> | <span data-ttu-id="19ec5-224">Descrição</span><span class="sxs-lookup"><span data-stu-id="19ec5-224">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="19ec5-225">90 segundos</span><span class="sxs-lookup"><span data-stu-id="19ec5-225">90 seconds</span></span> | <span data-ttu-id="19ec5-226">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="19ec5-226">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="19ec5-227">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="19ec5-227">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="19ec5-228">O transporte WebSocket tem opções adicionais que podem ser configuradas `WebSockets` usando a propriedade:</span><span class="sxs-lookup"><span data-stu-id="19ec5-228">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="19ec5-229">Opção</span><span class="sxs-lookup"><span data-stu-id="19ec5-229">Option</span></span> | <span data-ttu-id="19ec5-230">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="19ec5-230">Default Value</span></span> | <span data-ttu-id="19ec5-231">Descrição</span><span class="sxs-lookup"><span data-stu-id="19ec5-231">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="19ec5-232">5 segundos</span><span class="sxs-lookup"><span data-stu-id="19ec5-232">5 seconds</span></span> | <span data-ttu-id="19ec5-233">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="19ec5-233">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="19ec5-234">Um delegado que pode ser usado para definir o `Sec-WebSocket-Protocol` cabeçalho para um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="19ec5-234">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="19ec5-235">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="19ec5-235">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="19ec5-236">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="19ec5-236">Configure client options</span></span>

<span data-ttu-id="19ec5-237">As opções de cliente podem ser configuradas no `HubConnectionBuilder` tipo (disponível nos clientes .net e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="19ec5-237">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="19ec5-238">Ele também está disponível no cliente Java, mas a `HttpHubConnectionBuilder` subclasse é o que contém as opções de configuração do Builder, bem como `HubConnection` por si só.</span><span class="sxs-lookup"><span data-stu-id="19ec5-238">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="19ec5-239">Configurar registro em log</span><span class="sxs-lookup"><span data-stu-id="19ec5-239">Configure logging</span></span>

<span data-ttu-id="19ec5-240">O registro em log é configurado no cliente .NET `ConfigureLogging` usando o método.</span><span class="sxs-lookup"><span data-stu-id="19ec5-240">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="19ec5-241">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="19ec5-241">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="19ec5-242">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="19ec5-242">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="19ec5-243">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="19ec5-243">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="19ec5-244">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="19ec5-244">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="19ec5-245">Por exemplo, para habilitar o log do console, `Microsoft.Extensions.Logging.Console` instale o pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="19ec5-245">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="19ec5-246">Chame o `AddConsole` método de extensão:</span><span class="sxs-lookup"><span data-stu-id="19ec5-246">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="19ec5-247">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="19ec5-247">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="19ec5-248">Forneça um `LogLevel` valor que indique o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="19ec5-248">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="19ec5-249">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="19ec5-249">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="19ec5-250">Em vez de `LogLevel` um valor, você também pode fornecer `string` um valor que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="19ec5-250">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="19ec5-251">Isso é útil ao configurar o `LogLevel` registro em log do signalr em ambientes em que você não tem acesso às constantes.</span><span class="sxs-lookup"><span data-stu-id="19ec5-251">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="19ec5-252">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="19ec5-252">The following table lists the available log levels.</span></span> <span data-ttu-id="19ec5-253">O valor que você fornece `configureLogging` para definir o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="19ec5-253">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="19ec5-254">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="19ec5-254">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="19ec5-255">Cadeia de Caracteres</span><span class="sxs-lookup"><span data-stu-id="19ec5-255">String</span></span>                      | <span data-ttu-id="19ec5-256">LogLevel</span><span class="sxs-lookup"><span data-stu-id="19ec5-256">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="19ec5-257">`info`**ou**`information`</span><span class="sxs-lookup"><span data-stu-id="19ec5-257">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="19ec5-258">`warn`**ou**`warning`</span><span class="sxs-lookup"><span data-stu-id="19ec5-258">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="19ec5-259">Para desabilitar completamente o registro `signalR.LogLevel.None` `configureLogging` em log, especifique no método.</span><span class="sxs-lookup"><span data-stu-id="19ec5-259">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="19ec5-260">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico](xref:signalr/diagnostics)do signalr.</span><span class="sxs-lookup"><span data-stu-id="19ec5-260">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="19ec5-261">O cliente Java Signalr usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="19ec5-261">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="19ec5-262">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="19ec5-262">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="19ec5-263">O trecho de código a seguir mostra como `java.util.logging` usar o com o cliente Java do signalr.</span><span class="sxs-lookup"><span data-stu-id="19ec5-263">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="19ec5-264">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="19ec5-264">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="19ec5-265">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="19ec5-265">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="19ec5-266">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="19ec5-266">Configure allowed transports</span></span>

<span data-ttu-id="19ec5-267">Os transportes usados pelo signalr podem ser configurados na `WithUrl` chamada (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="19ec5-267">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="19ec5-268">Um bit-a-ou dos valores `HttpTransportType` de pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="19ec5-268">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="19ec5-269">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="19ec5-269">All transports are enabled by default.</span></span>

<span data-ttu-id="19ec5-270">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="19ec5-270">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="19ec5-271">No cliente JavaScript, os transportes são configurados definindo o `transport` campo no objeto de opções fornecido para: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="19ec5-271">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="19ec5-272">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="19ec5-272">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="19ec5-273">No cliente Java, o transporte é selecionado com o `withTransport` método `HttpHubConnectionBuilder`no.</span><span class="sxs-lookup"><span data-stu-id="19ec5-273">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="19ec5-274">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="19ec5-274">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="19ec5-275">O cliente Java do Signalr ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="19ec5-275">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="19ec5-276">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="19ec5-276">Configure bearer authentication</span></span>

<span data-ttu-id="19ec5-277">Para fornecer dados de autenticação juntamente com solicitações do signalr, `AccessTokenProvider` use a`accessTokenFactory` opção (em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="19ec5-277">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="19ec5-278">No cliente .net, esse token de acesso é passado como um token http "autenticação de portador" (usando o `Authorization` cabeçalho com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="19ec5-278">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="19ec5-279">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="19ec5-279">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="19ec5-280">Nesses casos, o token de acesso é fornecido como um valor `access_token`de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="19ec5-280">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="19ec5-281">No cliente .net, a `AccessTokenProvider` opção pode ser especificada usando o delegado de opções em: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="19ec5-281">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="19ec5-282">No cliente JavaScript, o token de acesso é configurado definindo o `accessTokenFactory` campo no objeto Options em: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="19ec5-282">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="19ec5-283">No cliente Java do Signalr, você pode configurar um token de portador a ser usado para autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="19ec5-283">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="19ec5-284">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [única\<cadeia de caracteres>](https://reactivex.io/documentation/single.html)[de RxJava](https://github.com/ReactiveX/RxJava).</span><span class="sxs-lookup"><span data-stu-id="19ec5-284">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="19ec5-285">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="19ec5-285">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="19ec5-286">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="19ec5-286">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="19ec5-287">Opções adicionais para configurar o tempo limite e o `HubConnection` comportamento Keep-Alive estão disponíveis no próprio objeto:</span><span class="sxs-lookup"><span data-stu-id="19ec5-287">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="19ec5-288">.NET</span><span class="sxs-lookup"><span data-stu-id="19ec5-288">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="19ec5-289">Opção</span><span class="sxs-lookup"><span data-stu-id="19ec5-289">Option</span></span> | <span data-ttu-id="19ec5-290">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="19ec5-290">Default value</span></span> | <span data-ttu-id="19ec5-291">Descrição</span><span class="sxs-lookup"><span data-stu-id="19ec5-291">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="19ec5-292">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="19ec5-292">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="19ec5-293">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="19ec5-293">Timeout for server activity.</span></span> <span data-ttu-id="19ec5-294">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `Closed` disparará o evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="19ec5-294">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="19ec5-295">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="19ec5-295">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="19ec5-296">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="19ec5-296">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="19ec5-297">15 segundos</span><span class="sxs-lookup"><span data-stu-id="19ec5-297">15 seconds</span></span> | <span data-ttu-id="19ec5-298">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="19ec5-298">Timeout for initial server handshake.</span></span> <span data-ttu-id="19ec5-299">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="19ec5-299">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="19ec5-300">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="19ec5-300">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="19ec5-301">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)do signalr.</span><span class="sxs-lookup"><span data-stu-id="19ec5-301">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="19ec5-302">15 segundos</span><span class="sxs-lookup"><span data-stu-id="19ec5-302">15 seconds</span></span> | <span data-ttu-id="19ec5-303">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="19ec5-303">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="19ec5-304">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="19ec5-304">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="19ec5-305">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="19ec5-305">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="19ec5-306">No cliente .net, os valores de tempo limite são `TimeSpan` especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="19ec5-306">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="19ec5-307">JavaScript</span><span class="sxs-lookup"><span data-stu-id="19ec5-307">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="19ec5-308">Opção</span><span class="sxs-lookup"><span data-stu-id="19ec5-308">Option</span></span> | <span data-ttu-id="19ec5-309">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="19ec5-309">Default value</span></span> | <span data-ttu-id="19ec5-310">Descrição</span><span class="sxs-lookup"><span data-stu-id="19ec5-310">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="19ec5-311">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="19ec5-311">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="19ec5-312">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="19ec5-312">Timeout for server activity.</span></span> <span data-ttu-id="19ec5-313">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onclose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="19ec5-313">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="19ec5-314">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="19ec5-314">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="19ec5-315">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="19ec5-315">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="19ec5-316">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="19ec5-316">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="19ec5-317">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="19ec5-317">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="19ec5-318">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="19ec5-318">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="19ec5-319">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="19ec5-319">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="19ec5-320">Java</span><span class="sxs-lookup"><span data-stu-id="19ec5-320">Java</span></span>](#tab/java)

| <span data-ttu-id="19ec5-321">Opção</span><span class="sxs-lookup"><span data-stu-id="19ec5-321">Option</span></span> | <span data-ttu-id="19ec5-322">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="19ec5-322">Default value</span></span> | <span data-ttu-id="19ec5-323">Descrição</span><span class="sxs-lookup"><span data-stu-id="19ec5-323">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="19ec5-324">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="19ec5-324">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="19ec5-325">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="19ec5-325">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="19ec5-326">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="19ec5-326">Timeout for server activity.</span></span> <span data-ttu-id="19ec5-327">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onClose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="19ec5-327">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="19ec5-328">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="19ec5-328">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="19ec5-329">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="19ec5-329">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="19ec5-330">15 segundos</span><span class="sxs-lookup"><span data-stu-id="19ec5-330">15 seconds</span></span> | <span data-ttu-id="19ec5-331">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="19ec5-331">Timeout for initial server handshake.</span></span> <span data-ttu-id="19ec5-332">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="19ec5-332">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="19ec5-333">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="19ec5-333">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="19ec5-334">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)do signalr.</span><span class="sxs-lookup"><span data-stu-id="19ec5-334">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="19ec5-335">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="19ec5-335">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="19ec5-336">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="19ec5-336">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="19ec5-337">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="19ec5-337">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="19ec5-338">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="19ec5-338">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="19ec5-339">Se o cliente não tiver enviado uma mensagem no `ClientTimeoutInterval` conjunto no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="19ec5-339">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="19ec5-340">.NET</span><span class="sxs-lookup"><span data-stu-id="19ec5-340">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="19ec5-341">Opção</span><span class="sxs-lookup"><span data-stu-id="19ec5-341">Option</span></span> | <span data-ttu-id="19ec5-342">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="19ec5-342">Default value</span></span> | <span data-ttu-id="19ec5-343">Descrição</span><span class="sxs-lookup"><span data-stu-id="19ec5-343">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="19ec5-344">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="19ec5-344">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="19ec5-345">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="19ec5-345">Timeout for server activity.</span></span> <span data-ttu-id="19ec5-346">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `Closed` disparará o evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="19ec5-346">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="19ec5-347">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="19ec5-347">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="19ec5-348">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="19ec5-348">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="19ec5-349">15 segundos</span><span class="sxs-lookup"><span data-stu-id="19ec5-349">15 seconds</span></span> | <span data-ttu-id="19ec5-350">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="19ec5-350">Timeout for initial server handshake.</span></span> <span data-ttu-id="19ec5-351">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `Closed` evento (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="19ec5-351">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="19ec5-352">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="19ec5-352">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="19ec5-353">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)do signalr.</span><span class="sxs-lookup"><span data-stu-id="19ec5-353">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="19ec5-354">No cliente .net, os valores de tempo limite são `TimeSpan` especificados como valores.</span><span class="sxs-lookup"><span data-stu-id="19ec5-354">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="19ec5-355">JavaScript</span><span class="sxs-lookup"><span data-stu-id="19ec5-355">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="19ec5-356">Opção</span><span class="sxs-lookup"><span data-stu-id="19ec5-356">Option</span></span> | <span data-ttu-id="19ec5-357">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="19ec5-357">Default value</span></span> | <span data-ttu-id="19ec5-358">Descrição</span><span class="sxs-lookup"><span data-stu-id="19ec5-358">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="19ec5-359">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="19ec5-359">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="19ec5-360">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="19ec5-360">Timeout for server activity.</span></span> <span data-ttu-id="19ec5-361">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onclose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="19ec5-361">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="19ec5-362">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="19ec5-362">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="19ec5-363">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="19ec5-363">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="19ec5-364">Java</span><span class="sxs-lookup"><span data-stu-id="19ec5-364">Java</span></span>](#tab/java)

| <span data-ttu-id="19ec5-365">Opção</span><span class="sxs-lookup"><span data-stu-id="19ec5-365">Option</span></span> | <span data-ttu-id="19ec5-366">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="19ec5-366">Default value</span></span> | <span data-ttu-id="19ec5-367">Descrição</span><span class="sxs-lookup"><span data-stu-id="19ec5-367">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="19ec5-368">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="19ec5-368">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="19ec5-369">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="19ec5-369">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="19ec5-370">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="19ec5-370">Timeout for server activity.</span></span> <span data-ttu-id="19ec5-371">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e `onClose` disparará o evento.</span><span class="sxs-lookup"><span data-stu-id="19ec5-371">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="19ec5-372">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="19ec5-372">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="19ec5-373">O valor recomendado é um número, pelo menos, o dobro `KeepAliveInterval` do valor do servidor, para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="19ec5-373">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="19ec5-374">15 segundos</span><span class="sxs-lookup"><span data-stu-id="19ec5-374">15 seconds</span></span> | <span data-ttu-id="19ec5-375">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="19ec5-375">Timeout for initial server handshake.</span></span> <span data-ttu-id="19ec5-376">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="19ec5-376">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="19ec5-377">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="19ec5-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="19ec5-378">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)do signalr.</span><span class="sxs-lookup"><span data-stu-id="19ec5-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="19ec5-379">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="19ec5-379">Configure additional options</span></span>

<span data-ttu-id="19ec5-380">Opções adicionais podem ser configuradas `WithUrl` no`withUrl` método (em JavaScript) `HubConnectionBuilder` em `HttpHubConnectionBuilder` ou em várias APIs de configuração no cliente Java:</span><span class="sxs-lookup"><span data-stu-id="19ec5-380">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="19ec5-381">.NET</span><span class="sxs-lookup"><span data-stu-id="19ec5-381">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="19ec5-382">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="19ec5-382">.NET Option</span></span> |  <span data-ttu-id="19ec5-383">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="19ec5-383">Default value</span></span> | <span data-ttu-id="19ec5-384">Descrição</span><span class="sxs-lookup"><span data-stu-id="19ec5-384">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="19ec5-385">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="19ec5-385">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="19ec5-386">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="19ec5-386">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="19ec5-387">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="19ec5-387">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="19ec5-388">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="19ec5-388">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="19ec5-389">Vazio</span><span class="sxs-lookup"><span data-stu-id="19ec5-389">Empty</span></span> | <span data-ttu-id="19ec5-390">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="19ec5-390">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="19ec5-391">Vazio</span><span class="sxs-lookup"><span data-stu-id="19ec5-391">Empty</span></span> | <span data-ttu-id="19ec5-392">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="19ec5-392">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="19ec5-393">Vazio</span><span class="sxs-lookup"><span data-stu-id="19ec5-393">Empty</span></span> | <span data-ttu-id="19ec5-394">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="19ec5-394">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="19ec5-395">5 segundos</span><span class="sxs-lookup"><span data-stu-id="19ec5-395">5 seconds</span></span> | <span data-ttu-id="19ec5-396">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="19ec5-396">WebSockets only.</span></span> <span data-ttu-id="19ec5-397">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="19ec5-397">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="19ec5-398">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="19ec5-398">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="19ec5-399">Vazio</span><span class="sxs-lookup"><span data-stu-id="19ec5-399">Empty</span></span> | <span data-ttu-id="19ec5-400">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="19ec5-400">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="19ec5-401">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="19ec5-401">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="19ec5-402">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="19ec5-402">Not used for WebSocket connections.</span></span> <span data-ttu-id="19ec5-403">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="19ec5-403">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="19ec5-404">Modifique as configurações desse valor padrão e retorne-o ou retorne uma `HttpMessageHandler` nova instância.</span><span class="sxs-lookup"><span data-stu-id="19ec5-404">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="19ec5-405">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="19ec5-405">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="19ec5-406">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="19ec5-406">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="19ec5-407">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="19ec5-407">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="19ec5-408">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="19ec5-408">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="19ec5-409">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="19ec5-409">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="19ec5-410">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="19ec5-410">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="19ec5-411">JavaScript</span><span class="sxs-lookup"><span data-stu-id="19ec5-411">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="19ec5-412">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="19ec5-412">JavaScript Option</span></span> | <span data-ttu-id="19ec5-413">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="19ec5-413">Default Value</span></span> | <span data-ttu-id="19ec5-414">Descrição</span><span class="sxs-lookup"><span data-stu-id="19ec5-414">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="19ec5-415">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="19ec5-415">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="19ec5-416">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="19ec5-416">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="19ec5-417">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="19ec5-417">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="19ec5-418">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="19ec5-418">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="19ec5-419">Java</span><span class="sxs-lookup"><span data-stu-id="19ec5-419">Java</span></span>](#tab/java)

| <span data-ttu-id="19ec5-420">Opção Java</span><span class="sxs-lookup"><span data-stu-id="19ec5-420">Java Option</span></span> | <span data-ttu-id="19ec5-421">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="19ec5-421">Default Value</span></span> | <span data-ttu-id="19ec5-422">Descrição</span><span class="sxs-lookup"><span data-stu-id="19ec5-422">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="19ec5-423">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="19ec5-423">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="19ec5-424">Defina como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="19ec5-424">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="19ec5-425">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="19ec5-425">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="19ec5-426">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="19ec5-426">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="19ec5-427">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="19ec5-427">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="19ec5-428">Vazio</span><span class="sxs-lookup"><span data-stu-id="19ec5-428">Empty</span></span> | <span data-ttu-id="19ec5-429">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="19ec5-429">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="19ec5-430">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="19ec5-430">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="19ec5-431">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="19ec5-431">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="19ec5-432">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="19ec5-432">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="19ec5-433">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="19ec5-433">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
