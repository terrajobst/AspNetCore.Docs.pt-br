---
title: Configuração de SignalR de ASP.NET Core
author: bradygaster
description: Saiba como configurar ASP.NET Core SignalR aplicativos.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 682cc36216a4dc9b38c87b4f67100ab565a70e5c
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963977"
---
# <a name="aspnet-core-opno-locsignalr-configuration"></a><span data-ttu-id="7b3c3-103">Configuração de SignalR de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7b3c3-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="7b3c3-104">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="7b3c3-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="7b3c3-105">ASP.NET Core SignalR dá suporte a dois protocolos para mensagens de codificação: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="7b3c3-106">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-106">Each protocol has serialization configuration options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7b3c3-107">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="7b3c3-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="7b3c3-108">`AddJsonProtocol` pode ser adicionado após [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7b3c3-109">O método `AddJsonProtocol` usa um delegado que recebe um objeto `options`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="7b3c3-110">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um objeto `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="7b3c3-111">Para obter mais informações, consulte a [documentação System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="7b3c3-112">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão de "camelCase", use o seguinte código no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="7b3c3-113">No cliente .NET, o mesmo método de extensão `AddJsonProtocol` existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="7b3c3-114">O namespace de `Microsoft.Extensions.DependencyInjection` deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="7b3c3-115">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="7b3c3-116">Alternar para Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="7b3c3-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="7b3c3-117">Se você precisar de recursos do `Newtonsoft.Json` que não têm suporte no `System.Text.Json`, consulte [mudar para Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="7b3c3-118">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol), que pode ser adicionado após [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-118">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="7b3c3-119">O método `AddJsonProtocol` usa um delegado que recebe um objeto `options`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-119">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="7b3c3-120">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto de `JsonSerializerSettings` de JSON.NET que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-120">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="7b3c3-121">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-121">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="7b3c3-122">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-122">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="7b3c3-123">No cliente .NET, o mesmo método de extensão `AddJsonProtocol` existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-123">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="7b3c3-124">O namespace de `Microsoft.Extensions.DependencyInjection` deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-124">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="7b3c3-125">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-125">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

::: moniker-end

### <a name="messagepack-serialization-options"></a><span data-ttu-id="7b3c3-126">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="7b3c3-126">MessagePack serialization options</span></span>

<span data-ttu-id="7b3c3-127">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="7b3c3-127">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="7b3c3-128">Consulte [MessagePack em SignalR](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-128">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="7b3c3-129">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-129">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="7b3c3-130">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="7b3c3-130">Configure server options</span></span>

<span data-ttu-id="7b3c3-131">A tabela a seguir descreve as opções para configurar os hubs de SignalR:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-131">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="7b3c3-132">Opção</span><span class="sxs-lookup"><span data-stu-id="7b3c3-132">Option</span></span> | <span data-ttu-id="7b3c3-133">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="7b3c3-133">Default Value</span></span> | <span data-ttu-id="7b3c3-134">Descrição</span><span class="sxs-lookup"><span data-stu-id="7b3c3-134">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="7b3c3-135">30 segundos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-135">30 seconds</span></span> | <span data-ttu-id="7b3c3-136">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-136">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="7b3c3-137">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-137">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="7b3c3-138">O valor recomendado é o dobro do valor de `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-138">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="7b3c3-139">15 segundos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-139">15 seconds</span></span> | <span data-ttu-id="7b3c3-140">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-140">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7b3c3-141">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-141">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7b3c3-142">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-142">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7b3c3-143">15 segundos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-143">15 seconds</span></span> | <span data-ttu-id="7b3c3-144">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-144">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7b3c3-145">Ao alterar `KeepAliveInterval`, altere a configuração `ServerTimeout`/`serverTimeoutInMilliseconds` no cliente.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-145">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7b3c3-146">O valor de `serverTimeoutInMilliseconds` recomendado `ServerTimeout`/é o dobro do valor `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-146">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7b3c3-147">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="7b3c3-147">All installed protocols</span></span> | <span data-ttu-id="7b3c3-148">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-148">Protocols supported by this hub.</span></span> <span data-ttu-id="7b3c3-149">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-149">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7b3c3-150">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-150">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7b3c3-151">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-151">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="7b3c3-152">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-152">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="7b3c3-153">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-153">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="7b3c3-154">32 KB</span><span class="sxs-lookup"><span data-stu-id="7b3c3-154">32 KB</span></span> | <span data-ttu-id="7b3c3-155">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-155">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="7b3c3-156">Opção</span><span class="sxs-lookup"><span data-stu-id="7b3c3-156">Option</span></span> | <span data-ttu-id="7b3c3-157">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="7b3c3-157">Default Value</span></span> | <span data-ttu-id="7b3c3-158">Descrição</span><span class="sxs-lookup"><span data-stu-id="7b3c3-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="7b3c3-159">30 segundos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-159">30 seconds</span></span> | <span data-ttu-id="7b3c3-160">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-160">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="7b3c3-161">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-161">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="7b3c3-162">O valor recomendado é o dobro do valor de `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-162">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="7b3c3-163">15 segundos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-163">15 seconds</span></span> | <span data-ttu-id="7b3c3-164">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-164">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7b3c3-165">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-165">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7b3c3-166">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-166">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7b3c3-167">15 segundos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-167">15 seconds</span></span> | <span data-ttu-id="7b3c3-168">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-168">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7b3c3-169">Ao alterar `KeepAliveInterval`, altere a configuração `ServerTimeout`/`serverTimeoutInMilliseconds` no cliente.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-169">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7b3c3-170">O valor de `serverTimeoutInMilliseconds` recomendado `ServerTimeout`/é o dobro do valor `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-170">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7b3c3-171">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="7b3c3-171">All installed protocols</span></span> | <span data-ttu-id="7b3c3-172">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-172">Protocols supported by this hub.</span></span> <span data-ttu-id="7b3c3-173">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-173">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7b3c3-174">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-174">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7b3c3-175">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-175">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="7b3c3-176">Opção</span><span class="sxs-lookup"><span data-stu-id="7b3c3-176">Option</span></span> | <span data-ttu-id="7b3c3-177">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="7b3c3-177">Default Value</span></span> | <span data-ttu-id="7b3c3-178">Descrição</span><span class="sxs-lookup"><span data-stu-id="7b3c3-178">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="7b3c3-179">15 segundos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-179">15 seconds</span></span> | <span data-ttu-id="7b3c3-180">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-180">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="7b3c3-181">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-181">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7b3c3-182">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-182">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7b3c3-183">15 segundos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-183">15 seconds</span></span> | <span data-ttu-id="7b3c3-184">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-184">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="7b3c3-185">Ao alterar `KeepAliveInterval`, altere a configuração `ServerTimeout`/`serverTimeoutInMilliseconds` no cliente.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-185">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="7b3c3-186">O valor de `serverTimeoutInMilliseconds` recomendado `ServerTimeout`/é o dobro do valor `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-186">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="7b3c3-187">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="7b3c3-187">All installed protocols</span></span> | <span data-ttu-id="7b3c3-188">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-188">Protocols supported by this hub.</span></span> <span data-ttu-id="7b3c3-189">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-189">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="7b3c3-190">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-190">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="7b3c3-191">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-191">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="7b3c3-192">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a chamada `AddSignalR` em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-192">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="7b3c3-193">As opções para um único Hub substituem as opções globais fornecidas em `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-193">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="7b3c3-194">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="7b3c3-194">Advanced HTTP configuration options</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7b3c3-195">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-195">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7b3c3-196">Essas opções são configuradas passando um delegado para [MapHub\<t >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) no `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-196">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="7b3c3-197">Use `HttpConnectionDispatcherOptions` para definir configurações avançadas relacionadas a transportes e gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-197">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="7b3c3-198">Essas opções são configuradas passando um delegado para [MapHub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) no `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-198">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="7b3c3-199">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-199">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="7b3c3-200">Opção</span><span class="sxs-lookup"><span data-stu-id="7b3c3-200">Option</span></span> | <span data-ttu-id="7b3c3-201">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="7b3c3-201">Default Value</span></span> | <span data-ttu-id="7b3c3-202">Descrição</span><span class="sxs-lookup"><span data-stu-id="7b3c3-202">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7b3c3-203">32 KB</span><span class="sxs-lookup"><span data-stu-id="7b3c3-203">32 KB</span></span> | <span data-ttu-id="7b3c3-204">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-204">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="7b3c3-205">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-205">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7b3c3-206">Os dados são automaticamente coletados dos atributos `Authorize` aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-206">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7b3c3-207">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-207">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7b3c3-208">32 KB</span><span class="sxs-lookup"><span data-stu-id="7b3c3-208">32 KB</span></span> | <span data-ttu-id="7b3c3-209">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-209">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="7b3c3-210">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-210">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7b3c3-211">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-211">All Transports are enabled.</span></span> | <span data-ttu-id="7b3c3-212">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que podem restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-212">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7b3c3-213">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-213">See below.</span></span> | <span data-ttu-id="7b3c3-214">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-214">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7b3c3-215">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-215">See below.</span></span> | <span data-ttu-id="7b3c3-216">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-216">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="7b3c3-217">Opção</span><span class="sxs-lookup"><span data-stu-id="7b3c3-217">Option</span></span> | <span data-ttu-id="7b3c3-218">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="7b3c3-218">Default Value</span></span> | <span data-ttu-id="7b3c3-219">Descrição</span><span class="sxs-lookup"><span data-stu-id="7b3c3-219">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="7b3c3-220">32 KB</span><span class="sxs-lookup"><span data-stu-id="7b3c3-220">32 KB</span></span> | <span data-ttu-id="7b3c3-221">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-221">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="7b3c3-222">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-222">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="7b3c3-223">Os dados são automaticamente coletados dos atributos `Authorize` aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-223">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="7b3c3-224">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-224">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="7b3c3-225">32 KB</span><span class="sxs-lookup"><span data-stu-id="7b3c3-225">32 KB</span></span> | <span data-ttu-id="7b3c3-226">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-226">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="7b3c3-227">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-227">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="7b3c3-228">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-228">All Transports are enabled.</span></span> | <span data-ttu-id="7b3c3-229">Uma enumeração de sinalizadores de bits de `HttpTransportType` valores que podem restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-229">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="7b3c3-230">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-230">See below.</span></span> | <span data-ttu-id="7b3c3-231">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-231">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="7b3c3-232">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-232">See below.</span></span> | <span data-ttu-id="7b3c3-233">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-233">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="7b3c3-234">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a propriedade `LongPolling`:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-234">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="7b3c3-235">Opção</span><span class="sxs-lookup"><span data-stu-id="7b3c3-235">Option</span></span> | <span data-ttu-id="7b3c3-236">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="7b3c3-236">Default Value</span></span> | <span data-ttu-id="7b3c3-237">Descrição</span><span class="sxs-lookup"><span data-stu-id="7b3c3-237">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="7b3c3-238">90 segundos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-238">90 seconds</span></span> | <span data-ttu-id="7b3c3-239">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-239">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="7b3c3-240">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-240">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="7b3c3-241">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a propriedade `WebSockets`:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-241">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="7b3c3-242">Opção</span><span class="sxs-lookup"><span data-stu-id="7b3c3-242">Option</span></span> | <span data-ttu-id="7b3c3-243">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="7b3c3-243">Default Value</span></span> | <span data-ttu-id="7b3c3-244">Descrição</span><span class="sxs-lookup"><span data-stu-id="7b3c3-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="7b3c3-245">5 segundos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-245">5 seconds</span></span> | <span data-ttu-id="7b3c3-246">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-246">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="7b3c3-247">Um delegado que pode ser usado para definir o cabeçalho de `Sec-WebSocket-Protocol` como um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-247">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="7b3c3-248">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-248">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="7b3c3-249">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="7b3c3-249">Configure client options</span></span>

<span data-ttu-id="7b3c3-250">As opções de cliente podem ser configuradas no tipo de `HubConnectionBuilder` (disponível nos clientes .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-250">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="7b3c3-251">Ele também está disponível no cliente Java, mas a subclasse `HttpHubConnectionBuilder` é o que contém as opções de configuração do Builder, bem como o `HubConnection` em si.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-251">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="7b3c3-252">Configurar registro em log</span><span class="sxs-lookup"><span data-stu-id="7b3c3-252">Configure logging</span></span>

<span data-ttu-id="7b3c3-253">O registro em log é configurado no cliente .NET usando o método `ConfigureLogging`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-253">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="7b3c3-254">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-254">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="7b3c3-255">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-255">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="7b3c3-256">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-256">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="7b3c3-257">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-257">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="7b3c3-258">Por exemplo, para habilitar o log do console, instale o `Microsoft.Extensions.Logging.Console` pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-258">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="7b3c3-259">Chame o método de extensão `AddConsole`:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-259">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="7b3c3-260">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-260">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="7b3c3-261">Forneça um valor `LogLevel` indicando o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-261">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="7b3c3-262">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-262">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7b3c3-263">Em vez de um valor `LogLevel`, você também pode fornecer um valor de `string` que representa um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-263">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="7b3c3-264">Isso é útil ao configurar SignalR log em ambientes em que você não tem acesso às constantes `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-264">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="7b3c3-265">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-265">The following table lists the available log levels.</span></span> <span data-ttu-id="7b3c3-266">O valor que você fornece para `configureLogging` define o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-266">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="7b3c3-267">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-267">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="7b3c3-268">Cadeia de Caracteres</span><span class="sxs-lookup"><span data-stu-id="7b3c3-268">String</span></span>                      | <span data-ttu-id="7b3c3-269">LogLevel</span><span class="sxs-lookup"><span data-stu-id="7b3c3-269">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="7b3c3-270">`info` **ou** `information`</span><span class="sxs-lookup"><span data-stu-id="7b3c3-270">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="7b3c3-271">`warn` **ou** `warning`</span><span class="sxs-lookup"><span data-stu-id="7b3c3-271">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="7b3c3-272">Para desabilitar totalmente o registro em log, especifique `signalR.LogLevel.None` no método `configureLogging`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-272">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="7b3c3-273">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico deSignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-273">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="7b3c3-274">O cliente Java SignalR usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-274">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7b3c3-275">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-275">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7b3c3-276">O trecho de código a seguir mostra como usar `java.util.logging` com o cliente SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-276">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7b3c3-277">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-277">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7b3c3-278">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-278">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="7b3c3-279">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-279">Configure allowed transports</span></span>

<span data-ttu-id="7b3c3-280">Os transportes usados pelo SignalR podem ser configurados na chamada `WithUrl` (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-280">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="7b3c3-281">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-281">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="7b3c3-282">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-282">All transports are enabled by default.</span></span>

<span data-ttu-id="7b3c3-283">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-283">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="7b3c3-284">No cliente JavaScript, os transportes são configurados definindo o campo `transport` no objeto de opções fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-284">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7b3c3-285">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-285">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="7b3c3-286">No cliente Java, o transporte é selecionado com o método `withTransport` no `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-286">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="7b3c3-287">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-287">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="7b3c3-288">O cliente Java SignalR ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-288">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="7b3c3-289">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="7b3c3-289">Configure bearer authentication</span></span>

<span data-ttu-id="7b3c3-290">Para fornecer dados de autenticação junto com SignalR solicitações, use a opção `AccessTokenProvider` (`accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-290">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="7b3c3-291">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o cabeçalho `Authorization` com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-291">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="7b3c3-292">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-292">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="7b3c3-293">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-293">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="7b3c3-294">No cliente .NET, a opção `AccessTokenProvider` pode ser especificada usando o delegado Options no `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-294">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="7b3c3-295">No cliente JavaScript, o token de acesso é configurado definindo o campo `accessTokenFactory` no objeto Options no `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-295">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="7b3c3-296">No cliente Java SignalR, você pode configurar um token de portador para usar na autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-296">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7b3c3-297">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [única\<cadeia de caracteres>](https://reactivex.io/documentation/single.html)[de RxJava](https://github.com/ReactiveX/RxJava).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-297">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7b3c3-298">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-298">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="7b3c3-299">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="7b3c3-299">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="7b3c3-300">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no próprio objeto `HubConnection`:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-300">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="7b3c3-301">.NET</span><span class="sxs-lookup"><span data-stu-id="7b3c3-301">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7b3c3-302">Opção</span><span class="sxs-lookup"><span data-stu-id="7b3c3-302">Option</span></span> | <span data-ttu-id="7b3c3-303">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="7b3c3-303">Default value</span></span> | <span data-ttu-id="7b3c3-304">Descrição</span><span class="sxs-lookup"><span data-stu-id="7b3c3-304">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7b3c3-305">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="7b3c3-305">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7b3c3-306">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-306">Timeout for server activity.</span></span> <span data-ttu-id="7b3c3-307">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento de `Closed` (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-307">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7b3c3-308">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-308">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7b3c3-309">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-309">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7b3c3-310">15 segundos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-310">15 seconds</span></span> | <span data-ttu-id="7b3c3-311">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-311">Timeout for initial server handshake.</span></span> <span data-ttu-id="7b3c3-312">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento de `Closed` (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-312">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7b3c3-313">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-313">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7b3c3-314">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-314">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="7b3c3-315">15 segundos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-315">15 seconds</span></span> | <span data-ttu-id="7b3c3-316">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-316">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7b3c3-317">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-317">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7b3c3-318">Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-318">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="7b3c3-319">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-319">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="7b3c3-320">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7b3c3-320">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7b3c3-321">Opção</span><span class="sxs-lookup"><span data-stu-id="7b3c3-321">Option</span></span> | <span data-ttu-id="7b3c3-322">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="7b3c3-322">Default value</span></span> | <span data-ttu-id="7b3c3-323">Descrição</span><span class="sxs-lookup"><span data-stu-id="7b3c3-323">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7b3c3-324">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="7b3c3-324">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7b3c3-325">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-325">Timeout for server activity.</span></span> <span data-ttu-id="7b3c3-326">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onclose`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-326">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7b3c3-327">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-327">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7b3c3-328">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-328">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="7b3c3-329">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="7b3c3-329">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="7b3c3-330">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-330">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7b3c3-331">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-331">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7b3c3-332">Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-332">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="7b3c3-333">Java</span><span class="sxs-lookup"><span data-stu-id="7b3c3-333">Java</span></span>](#tab/java)

| <span data-ttu-id="7b3c3-334">Opção</span><span class="sxs-lookup"><span data-stu-id="7b3c3-334">Option</span></span> | <span data-ttu-id="7b3c3-335">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="7b3c3-335">Default value</span></span> | <span data-ttu-id="7b3c3-336">Descrição</span><span class="sxs-lookup"><span data-stu-id="7b3c3-336">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7b3c3-337">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7b3c3-337">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7b3c3-338">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="7b3c3-338">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7b3c3-339">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-339">Timeout for server activity.</span></span> <span data-ttu-id="7b3c3-340">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onClose`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-340">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7b3c3-341">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-341">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7b3c3-342">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-342">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7b3c3-343">15 segundos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-343">15 seconds</span></span> | <span data-ttu-id="7b3c3-344">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-344">Timeout for initial server handshake.</span></span> <span data-ttu-id="7b3c3-345">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento `onClose`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-345">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7b3c3-346">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-346">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7b3c3-347">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-347">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="7b3c3-348">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="7b3c3-348">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="7b3c3-349">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="7b3c3-349">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="7b3c3-350">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-350">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="7b3c3-351">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-351">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="7b3c3-352">Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-352">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="7b3c3-353">.NET</span><span class="sxs-lookup"><span data-stu-id="7b3c3-353">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7b3c3-354">Opção</span><span class="sxs-lookup"><span data-stu-id="7b3c3-354">Option</span></span> | <span data-ttu-id="7b3c3-355">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="7b3c3-355">Default value</span></span> | <span data-ttu-id="7b3c3-356">Descrição</span><span class="sxs-lookup"><span data-stu-id="7b3c3-356">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="7b3c3-357">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="7b3c3-357">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7b3c3-358">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-358">Timeout for server activity.</span></span> <span data-ttu-id="7b3c3-359">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento de `Closed` (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-359">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7b3c3-360">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-360">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7b3c3-361">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-361">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="7b3c3-362">15 segundos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-362">15 seconds</span></span> | <span data-ttu-id="7b3c3-363">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-363">Timeout for initial server handshake.</span></span> <span data-ttu-id="7b3c3-364">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento de `Closed` (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-364">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="7b3c3-365">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-365">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7b3c3-366">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-366">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="7b3c3-367">No cliente .NET, os valores de tempo limite são especificados como `TimeSpan` valores.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-367">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="7b3c3-368">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7b3c3-368">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7b3c3-369">Opção</span><span class="sxs-lookup"><span data-stu-id="7b3c3-369">Option</span></span> | <span data-ttu-id="7b3c3-370">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="7b3c3-370">Default value</span></span> | <span data-ttu-id="7b3c3-371">Descrição</span><span class="sxs-lookup"><span data-stu-id="7b3c3-371">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="7b3c3-372">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="7b3c3-372">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7b3c3-373">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-373">Timeout for server activity.</span></span> <span data-ttu-id="7b3c3-374">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onclose`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-374">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="7b3c3-375">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-375">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7b3c3-376">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-376">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="7b3c3-377">Java</span><span class="sxs-lookup"><span data-stu-id="7b3c3-377">Java</span></span>](#tab/java)

| <span data-ttu-id="7b3c3-378">Opção</span><span class="sxs-lookup"><span data-stu-id="7b3c3-378">Option</span></span> | <span data-ttu-id="7b3c3-379">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="7b3c3-379">Default value</span></span> | <span data-ttu-id="7b3c3-380">Descrição</span><span class="sxs-lookup"><span data-stu-id="7b3c3-380">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="7b3c3-381">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="7b3c3-381">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="7b3c3-382">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="7b3c3-382">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="7b3c3-383">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-383">Timeout for server activity.</span></span> <span data-ttu-id="7b3c3-384">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onClose`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-384">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="7b3c3-385">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-385">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="7b3c3-386">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor, para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-386">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="7b3c3-387">15 segundos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-387">15 seconds</span></span> | <span data-ttu-id="7b3c3-388">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-388">Timeout for initial server handshake.</span></span> <span data-ttu-id="7b3c3-389">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento `onClose`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-389">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="7b3c3-390">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-390">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="7b3c3-391">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação de protocolo de Hub deSignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="7b3c3-391">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="7b3c3-392">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="7b3c3-392">Configure additional options</span></span>

<span data-ttu-id="7b3c3-393">Opções adicionais podem ser configuradas no método `WithUrl` (`withUrl` em JavaScript) no `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` no cliente Java:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-393">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="7b3c3-394">.NET</span><span class="sxs-lookup"><span data-stu-id="7b3c3-394">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="7b3c3-395">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="7b3c3-395">.NET Option</span></span> |  <span data-ttu-id="7b3c3-396">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="7b3c3-396">Default value</span></span> | <span data-ttu-id="7b3c3-397">Descrição</span><span class="sxs-lookup"><span data-stu-id="7b3c3-397">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="7b3c3-398">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-398">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="7b3c3-399">Defina isso como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-399">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7b3c3-400">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-400">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7b3c3-401">Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-401">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="7b3c3-402">Vazio</span><span class="sxs-lookup"><span data-stu-id="7b3c3-402">Empty</span></span> | <span data-ttu-id="7b3c3-403">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-403">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="7b3c3-404">Vazio</span><span class="sxs-lookup"><span data-stu-id="7b3c3-404">Empty</span></span> | <span data-ttu-id="7b3c3-405">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-405">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="7b3c3-406">Vazio</span><span class="sxs-lookup"><span data-stu-id="7b3c3-406">Empty</span></span> | <span data-ttu-id="7b3c3-407">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-407">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="7b3c3-408">5 segundos</span><span class="sxs-lookup"><span data-stu-id="7b3c3-408">5 seconds</span></span> | <span data-ttu-id="7b3c3-409">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-409">WebSockets only.</span></span> <span data-ttu-id="7b3c3-410">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-410">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="7b3c3-411">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-411">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="7b3c3-412">Vazio</span><span class="sxs-lookup"><span data-stu-id="7b3c3-412">Empty</span></span> | <span data-ttu-id="7b3c3-413">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-413">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="7b3c3-414">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-414">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="7b3c3-415">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-415">Not used for WebSocket connections.</span></span> <span data-ttu-id="7b3c3-416">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-416">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="7b3c3-417">Modifique as configurações desse valor padrão e retorne-o ou retorne uma nova instância de `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-417">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="7b3c3-418">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="7b3c3-418">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="7b3c3-419">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-419">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="7b3c3-420">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-420">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="7b3c3-421">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-421">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="7b3c3-422">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-422">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="7b3c3-423">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-423">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="7b3c3-424">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7b3c3-424">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="7b3c3-425">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="7b3c3-425">JavaScript Option</span></span> | <span data-ttu-id="7b3c3-426">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="7b3c3-426">Default Value</span></span> | <span data-ttu-id="7b3c3-427">Descrição</span><span class="sxs-lookup"><span data-stu-id="7b3c3-427">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="7b3c3-428">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-428">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="7b3c3-429">Defina isso como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-429">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7b3c3-430">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-430">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7b3c3-431">Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-431">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="7b3c3-432">Java</span><span class="sxs-lookup"><span data-stu-id="7b3c3-432">Java</span></span>](#tab/java)

| <span data-ttu-id="7b3c3-433">Opção Java</span><span class="sxs-lookup"><span data-stu-id="7b3c3-433">Java Option</span></span> | <span data-ttu-id="7b3c3-434">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="7b3c3-434">Default Value</span></span> | <span data-ttu-id="7b3c3-435">Descrição</span><span class="sxs-lookup"><span data-stu-id="7b3c3-435">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="7b3c3-436">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-436">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="7b3c3-437">Defina isso como `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-437">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="7b3c3-438">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-438">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="7b3c3-439">Essa configuração não pode ser habilitada ao usar o serviço de SignalR do Azure.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-439">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="7b3c3-440">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="7b3c3-440">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="7b3c3-441">Vazio</span><span class="sxs-lookup"><span data-stu-id="7b3c3-441">Empty</span></span> | <span data-ttu-id="7b3c3-442">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="7b3c3-442">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="7b3c3-443">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-443">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="7b3c3-444">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="7b3c3-444">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="7b3c3-445">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="7b3c3-445">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="7b3c3-446">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7b3c3-446">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
