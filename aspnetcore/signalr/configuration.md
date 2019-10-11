---
title: Configuração do Signalr ASP.NET Core
author: bradygaster
description: Saiba como configurar ASP.NET Core aplicativos Signalr.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 66f274fcda27392091de6b4be8c7221bc87b7585
ms.sourcegitcommit: c452e6af92e130413106c4863193f377cde4cd9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246488"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="6fc05-103">Configuração do Signalr ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6fc05-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6fc05-104">Opções de serialização JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6fc05-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6fc05-105">ASP.NET Core Signalr dá suporte a dois protocolos para codificar mensagens: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6fc05-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6fc05-106">Cada protocolo tem opções de configuração de serialização.</span><span class="sxs-lookup"><span data-stu-id="6fc05-106">Each protocol has serialization configuration options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6fc05-107">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6fc05-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="6fc05-108">`AddJsonProtocol` pode ser adicionado após [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6fc05-109">O método `AddJsonProtocol` usa um delegado que recebe um objeto `options`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6fc05-110">A propriedade [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) nesse objeto é um objeto `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="6fc05-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6fc05-111">Para obter mais informações, consulte a [documentação System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="6fc05-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="6fc05-112">Por exemplo, para configurar o serializador para não alterar a capitalização de nomes de propriedade, em vez dos nomes padrão "camelCase", use o seguinte código em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6fc05-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="6fc05-113">No cliente .NET, o mesmo método de extensão `AddJsonProtocol` existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6fc05-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6fc05-114">O namespace `Microsoft.Extensions.DependencyInjection` deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="6fc05-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="6fc05-115">Alternar para Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="6fc05-115">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="6fc05-116">Se você precisar de recursos de `Newtonsoft.Json` que não têm suporte no `System.Text.Json`, consulte [mudar para Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="6fc05-116">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="6fc05-117">A serialização JSON pode ser configurada no servidor usando o método de extensão [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol), que pode ser adicionado após [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em seu método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-117">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6fc05-118">O método `AddJsonProtocol` usa um delegado que recebe um objeto `options`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-118">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6fc05-119">A propriedade [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) nesse objeto é um objeto JSON.net `JsonSerializerSettings` que pode ser usado para configurar a serialização de argumentos e valores de retorno.</span><span class="sxs-lookup"><span data-stu-id="6fc05-119">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6fc05-120">Para obter mais informações, consulte a [documentação do JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="6fc05-120">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="6fc05-121">Por exemplo, para configurar o serializador para usar nomes de propriedade "PascalCase", em vez dos nomes padrão "camelCase", use o seguinte código em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6fc05-121">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="6fc05-122">No cliente .NET, o mesmo método de extensão `AddJsonProtocol` existe em [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6fc05-122">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6fc05-123">O namespace `Microsoft.Extensions.DependencyInjection` deve ser importado para resolver o método de extensão:</span><span class="sxs-lookup"><span data-stu-id="6fc05-123">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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

::: moniker-end

> [!NOTE]
> <span data-ttu-id="6fc05-124">Não é possível configurar a serialização JSON no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6fc05-124">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6fc05-125">Opções de serialização MessagePack</span><span class="sxs-lookup"><span data-stu-id="6fc05-125">MessagePack serialization options</span></span>

<span data-ttu-id="6fc05-126">A serialização MessagePack pode ser configurada fornecendo um delegado para a chamada [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6fc05-126">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6fc05-127">Consulte [MessagePack no signalr](xref:signalr/messagepackhubprotocol) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="6fc05-127">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6fc05-128">Não é possível configurar a serialização MessagePack no cliente JavaScript neste momento.</span><span class="sxs-lookup"><span data-stu-id="6fc05-128">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6fc05-129">Configurar opções de servidor</span><span class="sxs-lookup"><span data-stu-id="6fc05-129">Configure server options</span></span>

<span data-ttu-id="6fc05-130">A tabela a seguir descreve as opções para configurar os hubs de sinalização:</span><span class="sxs-lookup"><span data-stu-id="6fc05-130">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="6fc05-131">Opção</span><span class="sxs-lookup"><span data-stu-id="6fc05-131">Option</span></span> | <span data-ttu-id="6fc05-132">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6fc05-132">Default Value</span></span> | <span data-ttu-id="6fc05-133">Descrição</span><span class="sxs-lookup"><span data-stu-id="6fc05-133">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6fc05-134">30 segundos</span><span class="sxs-lookup"><span data-stu-id="6fc05-134">30 seconds</span></span> | <span data-ttu-id="6fc05-135">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="6fc05-135">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6fc05-136">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="6fc05-136">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6fc05-137">O valor recomendado é o dobro do valor `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-137">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6fc05-138">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6fc05-138">15 seconds</span></span> | <span data-ttu-id="6fc05-139">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="6fc05-139">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6fc05-140">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6fc05-140">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6fc05-141">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6fc05-141">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6fc05-142">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6fc05-142">15 seconds</span></span> | <span data-ttu-id="6fc05-143">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="6fc05-143">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6fc05-144">Ao alterar `KeepAliveInterval`, altere a configuração `ServerTimeout` @ no__t-2 @ no__t-3 no cliente.</span><span class="sxs-lookup"><span data-stu-id="6fc05-144">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6fc05-145">O valor recomendado `ServerTimeout` @ no__t-1 @ no__t-2 é o dobro do valor de `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-145">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6fc05-146">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="6fc05-146">All installed protocols</span></span> | <span data-ttu-id="6fc05-147">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="6fc05-147">Protocols supported by this hub.</span></span> <span data-ttu-id="6fc05-148">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="6fc05-148">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6fc05-149">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="6fc05-149">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6fc05-150">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="6fc05-150">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="6fc05-151">O número máximo de itens que podem ser armazenados em buffer para fluxos de carregamento do cliente.</span><span class="sxs-lookup"><span data-stu-id="6fc05-151">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="6fc05-152">Se esse limite for atingido, o processamento de invocações será bloqueado até que o servidor processe itens de fluxo.</span><span class="sxs-lookup"><span data-stu-id="6fc05-152">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="6fc05-153">32 KB</span><span class="sxs-lookup"><span data-stu-id="6fc05-153">32 KB</span></span> | <span data-ttu-id="6fc05-154">Tamanho máximo de uma única mensagem de Hub de entrada.</span><span class="sxs-lookup"><span data-stu-id="6fc05-154">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="6fc05-155">Opção</span><span class="sxs-lookup"><span data-stu-id="6fc05-155">Option</span></span> | <span data-ttu-id="6fc05-156">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6fc05-156">Default Value</span></span> | <span data-ttu-id="6fc05-157">Descrição</span><span class="sxs-lookup"><span data-stu-id="6fc05-157">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6fc05-158">30 segundos</span><span class="sxs-lookup"><span data-stu-id="6fc05-158">30 seconds</span></span> | <span data-ttu-id="6fc05-159">O servidor considerará o cliente desconectado se ele não tiver recebido uma mensagem (incluindo Keep-Alive) nesse intervalo.</span><span class="sxs-lookup"><span data-stu-id="6fc05-159">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6fc05-160">Pode levar mais tempo do que esse intervalo de tempo limite para que o cliente seja realmente marcado como desconectado, devido a como isso é implementado.</span><span class="sxs-lookup"><span data-stu-id="6fc05-160">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6fc05-161">O valor recomendado é o dobro do valor `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-161">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6fc05-162">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6fc05-162">15 seconds</span></span> | <span data-ttu-id="6fc05-163">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="6fc05-163">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6fc05-164">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6fc05-164">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6fc05-165">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6fc05-165">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6fc05-166">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6fc05-166">15 seconds</span></span> | <span data-ttu-id="6fc05-167">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="6fc05-167">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6fc05-168">Ao alterar `KeepAliveInterval`, altere a configuração `ServerTimeout` @ no__t-2 @ no__t-3 no cliente.</span><span class="sxs-lookup"><span data-stu-id="6fc05-168">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6fc05-169">O valor recomendado `ServerTimeout` @ no__t-1 @ no__t-2 é o dobro do valor de `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-169">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6fc05-170">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="6fc05-170">All installed protocols</span></span> | <span data-ttu-id="6fc05-171">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="6fc05-171">Protocols supported by this hub.</span></span> <span data-ttu-id="6fc05-172">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="6fc05-172">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6fc05-173">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="6fc05-173">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6fc05-174">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="6fc05-174">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="6fc05-175">Opção</span><span class="sxs-lookup"><span data-stu-id="6fc05-175">Option</span></span> | <span data-ttu-id="6fc05-176">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6fc05-176">Default Value</span></span> | <span data-ttu-id="6fc05-177">Descrição</span><span class="sxs-lookup"><span data-stu-id="6fc05-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="6fc05-178">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6fc05-178">15 seconds</span></span> | <span data-ttu-id="6fc05-179">Se o cliente não enviar uma mensagem de handshake inicial dentro desse intervalo de tempo, a conexão será fechada.</span><span class="sxs-lookup"><span data-stu-id="6fc05-179">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6fc05-180">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6fc05-180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6fc05-181">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6fc05-181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6fc05-182">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6fc05-182">15 seconds</span></span> | <span data-ttu-id="6fc05-183">Se o servidor não tiver enviado uma mensagem dentro desse intervalo, uma mensagem de ping será enviada automaticamente para manter a conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="6fc05-183">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6fc05-184">Ao alterar `KeepAliveInterval`, altere a configuração `ServerTimeout` @ no__t-2 @ no__t-3 no cliente.</span><span class="sxs-lookup"><span data-stu-id="6fc05-184">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6fc05-185">O valor recomendado `ServerTimeout` @ no__t-1 @ no__t-2 é o dobro do valor de `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-185">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6fc05-186">Todos os protocolos instalados</span><span class="sxs-lookup"><span data-stu-id="6fc05-186">All installed protocols</span></span> | <span data-ttu-id="6fc05-187">Protocolos com suporte neste Hub.</span><span class="sxs-lookup"><span data-stu-id="6fc05-187">Protocols supported by this hub.</span></span> <span data-ttu-id="6fc05-188">Por padrão, todos os protocolos registrados no servidor são permitidos, mas os protocolos podem ser removidos dessa lista para desabilitar protocolos específicos para hubs individuais.</span><span class="sxs-lookup"><span data-stu-id="6fc05-188">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6fc05-189">Se `true`, as mensagens de exceção detalhadas serão retornadas aos clientes quando uma exceção for lançada em um método de Hub.</span><span class="sxs-lookup"><span data-stu-id="6fc05-189">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6fc05-190">O padrão é `false`, pois essas mensagens de exceção podem conter informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="6fc05-190">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="6fc05-191">As opções podem ser configuradas para todos os hubs, fornecendo um delegado de opções para a chamada `AddSignalR` em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-191">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6fc05-192">As opções para um único Hub substituem as opções globais fornecidas em `AddSignalR` e podem ser configuradas usando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="6fc05-192">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6fc05-193">Opções de configuração avançada de HTTP</span><span class="sxs-lookup"><span data-stu-id="6fc05-193">Advanced HTTP configuration options</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6fc05-194">Use `HttpConnectionDispatcherOptions` para definir as configurações avançadas relacionadas aos transportes e ao gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="6fc05-194">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6fc05-195">Essas opções são configuradas passando um delegado para [MapHub @ no__t-1T >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-195">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6fc05-196">Use `HttpConnectionDispatcherOptions` para definir as configurações avançadas relacionadas aos transportes e ao gerenciamento de buffer de memória.</span><span class="sxs-lookup"><span data-stu-id="6fc05-196">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6fc05-197">Essas opções são configuradas passando um delegado para [MapHub @ no__t-1T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-197">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6fc05-198">A tabela a seguir descreve as opções para configurar as opções HTTP avançadas do Signalr ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="6fc05-198">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="6fc05-199">Opção</span><span class="sxs-lookup"><span data-stu-id="6fc05-199">Option</span></span> | <span data-ttu-id="6fc05-200">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6fc05-200">Default Value</span></span> | <span data-ttu-id="6fc05-201">Descrição</span><span class="sxs-lookup"><span data-stu-id="6fc05-201">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6fc05-202">32 KB</span><span class="sxs-lookup"><span data-stu-id="6fc05-202">32 KB</span></span> | <span data-ttu-id="6fc05-203">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer antes de aplicar a pressão de demanda.</span><span class="sxs-lookup"><span data-stu-id="6fc05-203">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="6fc05-204">Aumentar esse valor permite que o servidor receba mensagens maiores mais rapidamente sem aplicar a pressão, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6fc05-204">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6fc05-205">Os dados são automaticamente coletados dos atributos `Authorize` aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="6fc05-205">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6fc05-206">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="6fc05-206">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6fc05-207">32 KB</span><span class="sxs-lookup"><span data-stu-id="6fc05-207">32 KB</span></span> | <span data-ttu-id="6fc05-208">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer antes de observar a pressão de back.</span><span class="sxs-lookup"><span data-stu-id="6fc05-208">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="6fc05-209">Aumentar esse valor permite que o servidor buffere mensagens maiores mais rapidamente sem esperar a pressão de retorno, mas pode aumentar o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6fc05-209">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6fc05-210">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="6fc05-210">All Transports are enabled.</span></span> | <span data-ttu-id="6fc05-211">Uma enumeração de sinalizadores de bits de valores `HttpTransportType` que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="6fc05-211">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6fc05-212">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="6fc05-212">See below.</span></span> | <span data-ttu-id="6fc05-213">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="6fc05-213">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6fc05-214">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="6fc05-214">See below.</span></span> | <span data-ttu-id="6fc05-215">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6fc05-215">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="6fc05-216">Opção</span><span class="sxs-lookup"><span data-stu-id="6fc05-216">Option</span></span> | <span data-ttu-id="6fc05-217">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6fc05-217">Default Value</span></span> | <span data-ttu-id="6fc05-218">Descrição</span><span class="sxs-lookup"><span data-stu-id="6fc05-218">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6fc05-219">32 KB</span><span class="sxs-lookup"><span data-stu-id="6fc05-219">32 KB</span></span> | <span data-ttu-id="6fc05-220">O número máximo de bytes recebidos do cliente que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="6fc05-220">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="6fc05-221">Aumentar esse valor permite que o servidor receba mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6fc05-221">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6fc05-222">Os dados são automaticamente coletados dos atributos `Authorize` aplicados à classe Hub.</span><span class="sxs-lookup"><span data-stu-id="6fc05-222">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6fc05-223">Uma lista de objetos [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usados para determinar se um cliente está autorizado a se conectar ao Hub.</span><span class="sxs-lookup"><span data-stu-id="6fc05-223">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6fc05-224">32 KB</span><span class="sxs-lookup"><span data-stu-id="6fc05-224">32 KB</span></span> | <span data-ttu-id="6fc05-225">O número máximo de bytes enviados pelo aplicativo que o servidor armazena em buffer.</span><span class="sxs-lookup"><span data-stu-id="6fc05-225">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="6fc05-226">Aumentar esse valor permite que o servidor envie mensagens maiores, mas pode afetar negativamente o consumo de memória.</span><span class="sxs-lookup"><span data-stu-id="6fc05-226">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6fc05-227">Todos os transportes estão habilitados.</span><span class="sxs-lookup"><span data-stu-id="6fc05-227">All Transports are enabled.</span></span> | <span data-ttu-id="6fc05-228">Uma enumeração de sinalizadores de bits de valores `HttpTransportType` que pode restringir os transportes que um cliente pode usar para se conectar.</span><span class="sxs-lookup"><span data-stu-id="6fc05-228">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6fc05-229">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="6fc05-229">See below.</span></span> | <span data-ttu-id="6fc05-230">Opções adicionais específicas para o transporte de sondagem longa.</span><span class="sxs-lookup"><span data-stu-id="6fc05-230">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6fc05-231">Veja abaixo.</span><span class="sxs-lookup"><span data-stu-id="6fc05-231">See below.</span></span> | <span data-ttu-id="6fc05-232">Opções adicionais específicas ao transporte do WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6fc05-232">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="6fc05-233">O transporte de sondagem longa tem opções adicionais que podem ser configuradas usando a propriedade `LongPolling`:</span><span class="sxs-lookup"><span data-stu-id="6fc05-233">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6fc05-234">Opção</span><span class="sxs-lookup"><span data-stu-id="6fc05-234">Option</span></span> | <span data-ttu-id="6fc05-235">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6fc05-235">Default Value</span></span> | <span data-ttu-id="6fc05-236">Descrição</span><span class="sxs-lookup"><span data-stu-id="6fc05-236">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6fc05-237">90 segundos</span><span class="sxs-lookup"><span data-stu-id="6fc05-237">90 seconds</span></span> | <span data-ttu-id="6fc05-238">A quantidade máxima de tempo que o servidor aguarda uma mensagem para enviar ao cliente antes de encerrar uma única solicitação de sondagem.</span><span class="sxs-lookup"><span data-stu-id="6fc05-238">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6fc05-239">A redução desse valor faz com que o cliente emita novas solicitações de sondagem com mais frequência.</span><span class="sxs-lookup"><span data-stu-id="6fc05-239">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6fc05-240">O transporte WebSocket tem opções adicionais que podem ser configuradas usando a propriedade `WebSockets`:</span><span class="sxs-lookup"><span data-stu-id="6fc05-240">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6fc05-241">Opção</span><span class="sxs-lookup"><span data-stu-id="6fc05-241">Option</span></span> | <span data-ttu-id="6fc05-242">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6fc05-242">Default Value</span></span> | <span data-ttu-id="6fc05-243">Descrição</span><span class="sxs-lookup"><span data-stu-id="6fc05-243">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6fc05-244">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6fc05-244">5 seconds</span></span> | <span data-ttu-id="6fc05-245">Depois que o servidor for fechado, se o cliente falhar ao fechar dentro desse intervalo de tempo, a conexão será encerrada.</span><span class="sxs-lookup"><span data-stu-id="6fc05-245">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6fc05-246">Um delegado que pode ser usado para definir o cabeçalho `Sec-WebSocket-Protocol` como um valor personalizado.</span><span class="sxs-lookup"><span data-stu-id="6fc05-246">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6fc05-247">O delegado recebe os valores solicitados pelo cliente como entrada e é esperado para retornar o valor desejado.</span><span class="sxs-lookup"><span data-stu-id="6fc05-247">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6fc05-248">Configurar opções do cliente</span><span class="sxs-lookup"><span data-stu-id="6fc05-248">Configure client options</span></span>

<span data-ttu-id="6fc05-249">As opções de cliente podem ser configuradas no tipo `HubConnectionBuilder` (disponível nos clientes .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6fc05-249">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6fc05-250">Ele também está disponível no cliente Java, mas a subclasse `HttpHubConnectionBuilder` é o que contém as opções de configuração do Builder, bem como o `HubConnection` propriamente dito.</span><span class="sxs-lookup"><span data-stu-id="6fc05-250">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6fc05-251">Configurar registro em log</span><span class="sxs-lookup"><span data-stu-id="6fc05-251">Configure logging</span></span>

<span data-ttu-id="6fc05-252">O registro em log é configurado no cliente .NET usando o método `ConfigureLogging`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-252">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6fc05-253">Os provedores e filtros de log podem ser registrados da mesma maneira como estão no servidor.</span><span class="sxs-lookup"><span data-stu-id="6fc05-253">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6fc05-254">Consulte a documentação de [ASP.NET Core de logon](xref:fundamentals/logging/index) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="6fc05-254">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6fc05-255">Para registrar provedores de log, você deve instalar os pacotes necessários.</span><span class="sxs-lookup"><span data-stu-id="6fc05-255">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6fc05-256">Consulte a seção [provedores de log internos](xref:fundamentals/logging/index#built-in-logging-providers) do docs para obter uma lista completa.</span><span class="sxs-lookup"><span data-stu-id="6fc05-256">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6fc05-257">Por exemplo, para habilitar o log do console, instale o pacote NuGet `Microsoft.Extensions.Logging.Console`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-257">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6fc05-258">Chame o método de extensão `AddConsole`:</span><span class="sxs-lookup"><span data-stu-id="6fc05-258">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6fc05-259">No cliente JavaScript, existe um método `configureLogging` semelhante.</span><span class="sxs-lookup"><span data-stu-id="6fc05-259">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6fc05-260">Forneça um valor `LogLevel` indicando o nível mínimo de mensagens de log a serem produzidas.</span><span class="sxs-lookup"><span data-stu-id="6fc05-260">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6fc05-261">Os logs são gravados na janela do console do navegador.</span><span class="sxs-lookup"><span data-stu-id="6fc05-261">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6fc05-262">Em vez de um valor `LogLevel`, você também pode fornecer um valor de `string` representando um nome de nível de log.</span><span class="sxs-lookup"><span data-stu-id="6fc05-262">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="6fc05-263">Isso é útil ao configurar o registro em log do Signalr em ambientes em que você não tem acesso às constantes `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-263">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="6fc05-264">A tabela a seguir lista os níveis de log disponíveis.</span><span class="sxs-lookup"><span data-stu-id="6fc05-264">The following table lists the available log levels.</span></span> <span data-ttu-id="6fc05-265">O valor que você fornece para `configureLogging` define o nível de log **mínimo** que será registrado.</span><span class="sxs-lookup"><span data-stu-id="6fc05-265">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="6fc05-266">As mensagens registradas nesse nível **ou os níveis listados depois dela na tabela**serão registrados.</span><span class="sxs-lookup"><span data-stu-id="6fc05-266">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="6fc05-267">Cadeia de Caracteres</span><span class="sxs-lookup"><span data-stu-id="6fc05-267">String</span></span>                      | <span data-ttu-id="6fc05-268">LogLevel</span><span class="sxs-lookup"><span data-stu-id="6fc05-268">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="6fc05-269">`info` **ou** `information`</span><span class="sxs-lookup"><span data-stu-id="6fc05-269">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="6fc05-270">`warn` **ou** `warning`</span><span class="sxs-lookup"><span data-stu-id="6fc05-270">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="6fc05-271">Para desabilitar totalmente o registro em log, especifique `signalR.LogLevel.None` no método `configureLogging`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-271">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6fc05-272">Para obter mais informações sobre registro em log, consulte a [documentação de diagnóstico do signalr](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6fc05-272">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6fc05-273">O cliente Java Signalr usa a biblioteca [SLF4J](https://www.slf4j.org/) para registro em log.</span><span class="sxs-lookup"><span data-stu-id="6fc05-273">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6fc05-274">É uma API de log de alto nível que permite que os usuários da biblioteca escolham sua própria implementação de log específica, trazendo uma dependência de registro em log específica.</span><span class="sxs-lookup"><span data-stu-id="6fc05-274">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6fc05-275">O trecho de código a seguir mostra como usar `java.util.logging` com o cliente Java Signalr.</span><span class="sxs-lookup"><span data-stu-id="6fc05-275">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6fc05-276">Se você não configurar o registro em log em suas dependências, o SLF4J carregará um agente sem operação padrão com a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="6fc05-276">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6fc05-277">Isso pode ser ignorado com segurança.</span><span class="sxs-lookup"><span data-stu-id="6fc05-277">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6fc05-278">Configurar transportes permitidos</span><span class="sxs-lookup"><span data-stu-id="6fc05-278">Configure allowed transports</span></span>

<span data-ttu-id="6fc05-279">Os transportes usados pelo Signalr podem ser configurados na chamada `WithUrl` (`withUrl` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6fc05-279">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6fc05-280">Um bit-a-ou dos valores de `HttpTransportType` pode ser usado para restringir o cliente a usar apenas os transportes especificados.</span><span class="sxs-lookup"><span data-stu-id="6fc05-280">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6fc05-281">Todos os transportes são habilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="6fc05-281">All transports are enabled by default.</span></span>

<span data-ttu-id="6fc05-282">Por exemplo, para desabilitar o transporte de eventos enviados pelo servidor, mas permitir WebSockets e conexões de sondagem longas:</span><span class="sxs-lookup"><span data-stu-id="6fc05-282">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6fc05-283">No cliente JavaScript, os transportes são configurados definindo o campo `transport` no objeto Options fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6fc05-283">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="6fc05-284">Nesta versão do WebSocket do cliente Java é o único transporte disponível.</span><span class="sxs-lookup"><span data-stu-id="6fc05-284">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="6fc05-285">No cliente Java, o transporte é selecionado com o método `withTransport` no `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-285">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="6fc05-286">O cliente Java usa como padrão o transporte WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6fc05-286">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6fc05-287">O cliente Java do Signalr ainda não dá suporte ao fallback de transporte.</span><span class="sxs-lookup"><span data-stu-id="6fc05-287">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6fc05-288">Configurar a autenticação do portador</span><span class="sxs-lookup"><span data-stu-id="6fc05-288">Configure bearer authentication</span></span>

<span data-ttu-id="6fc05-289">Para fornecer dados de autenticação juntamente com solicitações do Signalr, use a opção `AccessTokenProvider` (`accessTokenFactory` em JavaScript) para especificar uma função que retorna o token de acesso desejado.</span><span class="sxs-lookup"><span data-stu-id="6fc05-289">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6fc05-290">No cliente .NET, esse token de acesso é passado como um token HTTP "autenticação de portador" (usando o cabeçalho `Authorization` com um tipo de `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="6fc05-290">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6fc05-291">No cliente JavaScript, o token de acesso é usado como um token de portador, **exceto** em alguns casos em que as APIs de navegador restringem a capacidade de aplicar cabeçalhos (especificamente, em eventos enviados pelo servidor e solicitações de WebSocket).</span><span class="sxs-lookup"><span data-stu-id="6fc05-291">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6fc05-292">Nesses casos, o token de acesso é fornecido como um valor de cadeia de caracteres de consulta `access_token`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-292">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6fc05-293">No cliente .NET, a opção `AccessTokenProvider` pode ser especificada usando o delegado Options em `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="6fc05-293">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6fc05-294">No cliente JavaScript, o token de acesso é configurado definindo o campo `accessTokenFactory` no objeto Options em `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6fc05-294">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="6fc05-295">No cliente Java do Signalr, você pode configurar um token de portador a ser usado para autenticação, fornecendo um alocador de token de acesso para o [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="6fc05-295">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6fc05-296">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) para fornecer uma [única\<cadeia de caracteres>](https://reactivex.io/documentation/single.html)[de RxJava](https://github.com/ReactiveX/RxJava).</span><span class="sxs-lookup"><span data-stu-id="6fc05-296">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6fc05-297">Com uma chamada para [Single. Defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), você pode escrever lógica para produzir tokens de acesso para seu cliente.</span><span class="sxs-lookup"><span data-stu-id="6fc05-297">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6fc05-298">Configurar opções de tempo limite e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6fc05-298">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6fc05-299">Opções adicionais para configurar o tempo limite e o comportamento Keep-Alive estão disponíveis no próprio objeto `HubConnection`:</span><span class="sxs-lookup"><span data-stu-id="6fc05-299">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="6fc05-300">.NET</span><span class="sxs-lookup"><span data-stu-id="6fc05-300">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6fc05-301">Opção</span><span class="sxs-lookup"><span data-stu-id="6fc05-301">Option</span></span> | <span data-ttu-id="6fc05-302">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6fc05-302">Default value</span></span> | <span data-ttu-id="6fc05-303">Descrição</span><span class="sxs-lookup"><span data-stu-id="6fc05-303">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6fc05-304">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6fc05-304">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6fc05-305">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6fc05-305">Timeout for server activity.</span></span> <span data-ttu-id="6fc05-306">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `Closed` (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6fc05-306">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6fc05-307">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6fc05-307">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6fc05-308">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6fc05-308">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6fc05-309">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6fc05-309">15 seconds</span></span> | <span data-ttu-id="6fc05-310">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6fc05-310">Timeout for initial server handshake.</span></span> <span data-ttu-id="6fc05-311">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento `Closed` (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6fc05-311">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6fc05-312">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6fc05-312">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6fc05-313">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6fc05-313">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6fc05-314">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6fc05-314">15 seconds</span></span> | <span data-ttu-id="6fc05-315">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6fc05-315">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6fc05-316">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6fc05-316">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6fc05-317">Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6fc05-317">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6fc05-318">No cliente .NET, os valores de tempo limite são especificados como valores `TimeSpan`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-318">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="6fc05-319">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6fc05-319">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6fc05-320">Opção</span><span class="sxs-lookup"><span data-stu-id="6fc05-320">Option</span></span> | <span data-ttu-id="6fc05-321">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6fc05-321">Default value</span></span> | <span data-ttu-id="6fc05-322">Descrição</span><span class="sxs-lookup"><span data-stu-id="6fc05-322">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6fc05-323">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6fc05-323">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6fc05-324">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6fc05-324">Timeout for server activity.</span></span> <span data-ttu-id="6fc05-325">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onclose`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-325">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6fc05-326">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6fc05-326">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6fc05-327">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6fc05-327">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6fc05-328">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6fc05-328">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="6fc05-329">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6fc05-329">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6fc05-330">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6fc05-330">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6fc05-331">Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6fc05-331">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="6fc05-332">Java</span><span class="sxs-lookup"><span data-stu-id="6fc05-332">Java</span></span>](#tab/java)

| <span data-ttu-id="6fc05-333">Opção</span><span class="sxs-lookup"><span data-stu-id="6fc05-333">Option</span></span> | <span data-ttu-id="6fc05-334">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6fc05-334">Default value</span></span> | <span data-ttu-id="6fc05-335">Descrição</span><span class="sxs-lookup"><span data-stu-id="6fc05-335">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6fc05-336">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6fc05-336">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6fc05-337">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6fc05-337">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6fc05-338">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6fc05-338">Timeout for server activity.</span></span> <span data-ttu-id="6fc05-339">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onClose`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-339">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6fc05-340">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6fc05-340">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6fc05-341">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6fc05-341">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6fc05-342">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6fc05-342">15 seconds</span></span> | <span data-ttu-id="6fc05-343">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6fc05-343">Timeout for initial server handshake.</span></span> <span data-ttu-id="6fc05-344">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento `onClose`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-344">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6fc05-345">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6fc05-345">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6fc05-346">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6fc05-346">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6fc05-347">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6fc05-347">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6fc05-348">15 segundos (15.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6fc05-348">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6fc05-349">Determina o intervalo no qual o cliente envia mensagens de ping.</span><span class="sxs-lookup"><span data-stu-id="6fc05-349">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6fc05-350">O envio de qualquer mensagem do cliente redefine o temporizador para o início do intervalo.</span><span class="sxs-lookup"><span data-stu-id="6fc05-350">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6fc05-351">Se o cliente não tiver enviado uma mensagem no conjunto de `ClientTimeoutInterval` no servidor, o servidor considerará o cliente desconectado.</span><span class="sxs-lookup"><span data-stu-id="6fc05-351">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="6fc05-352">.NET</span><span class="sxs-lookup"><span data-stu-id="6fc05-352">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6fc05-353">Opção</span><span class="sxs-lookup"><span data-stu-id="6fc05-353">Option</span></span> | <span data-ttu-id="6fc05-354">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6fc05-354">Default value</span></span> | <span data-ttu-id="6fc05-355">Descrição</span><span class="sxs-lookup"><span data-stu-id="6fc05-355">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6fc05-356">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6fc05-356">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6fc05-357">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6fc05-357">Timeout for server activity.</span></span> <span data-ttu-id="6fc05-358">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `Closed` (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6fc05-358">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6fc05-359">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6fc05-359">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6fc05-360">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6fc05-360">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6fc05-361">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6fc05-361">15 seconds</span></span> | <span data-ttu-id="6fc05-362">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6fc05-362">Timeout for initial server handshake.</span></span> <span data-ttu-id="6fc05-363">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento `Closed` (`onclose` em JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6fc05-363">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6fc05-364">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6fc05-364">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6fc05-365">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6fc05-365">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="6fc05-366">No cliente .NET, os valores de tempo limite são especificados como valores `TimeSpan`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-366">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="6fc05-367">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6fc05-367">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6fc05-368">Opção</span><span class="sxs-lookup"><span data-stu-id="6fc05-368">Option</span></span> | <span data-ttu-id="6fc05-369">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6fc05-369">Default value</span></span> | <span data-ttu-id="6fc05-370">Descrição</span><span class="sxs-lookup"><span data-stu-id="6fc05-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6fc05-371">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6fc05-371">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6fc05-372">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6fc05-372">Timeout for server activity.</span></span> <span data-ttu-id="6fc05-373">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onclose`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-373">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6fc05-374">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6fc05-374">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6fc05-375">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6fc05-375">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="6fc05-376">Java</span><span class="sxs-lookup"><span data-stu-id="6fc05-376">Java</span></span>](#tab/java)

| <span data-ttu-id="6fc05-377">Opção</span><span class="sxs-lookup"><span data-stu-id="6fc05-377">Option</span></span> | <span data-ttu-id="6fc05-378">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6fc05-378">Default value</span></span> | <span data-ttu-id="6fc05-379">Descrição</span><span class="sxs-lookup"><span data-stu-id="6fc05-379">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6fc05-380">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6fc05-380">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6fc05-381">30 segundos (30.000 milissegundos)</span><span class="sxs-lookup"><span data-stu-id="6fc05-381">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6fc05-382">Tempo limite para a atividade do servidor.</span><span class="sxs-lookup"><span data-stu-id="6fc05-382">Timeout for server activity.</span></span> <span data-ttu-id="6fc05-383">Se o servidor não tiver enviado uma mensagem nesse intervalo, o cliente considerará o servidor desconectado e disparará o evento `onClose`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-383">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6fc05-384">Esse valor deve ser grande o suficiente para que uma mensagem de ping seja enviada do servidor **e** recebida pelo cliente dentro do intervalo de tempo limite.</span><span class="sxs-lookup"><span data-stu-id="6fc05-384">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6fc05-385">O valor recomendado é um número, pelo menos, o dobro do valor de `KeepAliveInterval` do servidor, para dar tempo para que os pings cheguem.</span><span class="sxs-lookup"><span data-stu-id="6fc05-385">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6fc05-386">15 segundos</span><span class="sxs-lookup"><span data-stu-id="6fc05-386">15 seconds</span></span> | <span data-ttu-id="6fc05-387">Tempo limite para o handshake inicial do servidor.</span><span class="sxs-lookup"><span data-stu-id="6fc05-387">Timeout for initial server handshake.</span></span> <span data-ttu-id="6fc05-388">Se o servidor não enviar uma resposta de handshake nesse intervalo, o cliente cancelará o handshake e disparará o evento `onClose`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-388">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6fc05-389">Essa é uma configuração avançada que só deve ser modificada se os erros de tempo limite de handshake estiverem ocorrendo devido a uma latência de rede grave.</span><span class="sxs-lookup"><span data-stu-id="6fc05-389">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6fc05-390">Para obter mais detalhes sobre o processo de handshake, consulte a [especificação do protocolo de Hub do signalr](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6fc05-390">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="6fc05-391">Configurar opções adicionais</span><span class="sxs-lookup"><span data-stu-id="6fc05-391">Configure additional options</span></span>

<span data-ttu-id="6fc05-392">Opções adicionais podem ser configuradas no método `WithUrl` (`withUrl` no JavaScript) em `HubConnectionBuilder` ou em várias APIs de configuração no `HttpHubConnectionBuilder` no cliente Java:</span><span class="sxs-lookup"><span data-stu-id="6fc05-392">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="6fc05-393">.NET</span><span class="sxs-lookup"><span data-stu-id="6fc05-393">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6fc05-394">Opção .NET</span><span class="sxs-lookup"><span data-stu-id="6fc05-394">.NET Option</span></span> |  <span data-ttu-id="6fc05-395">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6fc05-395">Default value</span></span> | <span data-ttu-id="6fc05-396">Descrição</span><span class="sxs-lookup"><span data-stu-id="6fc05-396">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6fc05-397">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6fc05-397">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6fc05-398">Defina isso para `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6fc05-398">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6fc05-399">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="6fc05-399">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6fc05-400">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="6fc05-400">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6fc05-401">Vazio</span><span class="sxs-lookup"><span data-stu-id="6fc05-401">Empty</span></span> | <span data-ttu-id="6fc05-402">Uma coleção de certificados TLS a serem enviados para autenticar solicitações.</span><span class="sxs-lookup"><span data-stu-id="6fc05-402">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6fc05-403">Vazio</span><span class="sxs-lookup"><span data-stu-id="6fc05-403">Empty</span></span> | <span data-ttu-id="6fc05-404">Uma coleção de cookies HTTP a serem enviados com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6fc05-404">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6fc05-405">Vazio</span><span class="sxs-lookup"><span data-stu-id="6fc05-405">Empty</span></span> | <span data-ttu-id="6fc05-406">Credenciais a serem enviadas com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6fc05-406">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6fc05-407">5 segundos</span><span class="sxs-lookup"><span data-stu-id="6fc05-407">5 seconds</span></span> | <span data-ttu-id="6fc05-408">Somente WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6fc05-408">WebSockets only.</span></span> <span data-ttu-id="6fc05-409">A quantidade máxima de tempo que o cliente aguarda depois de fechar para que o servidor reconheça a solicitação de fechamento.</span><span class="sxs-lookup"><span data-stu-id="6fc05-409">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6fc05-410">Se o servidor não reconhecer o fechamento dentro desse tempo, o cliente se desconectará.</span><span class="sxs-lookup"><span data-stu-id="6fc05-410">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6fc05-411">Vazio</span><span class="sxs-lookup"><span data-stu-id="6fc05-411">Empty</span></span> | <span data-ttu-id="6fc05-412">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6fc05-412">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6fc05-413">Um delegado que pode ser usado para configurar ou substituir o `HttpMessageHandler` usado para enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6fc05-413">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6fc05-414">Não é usado para conexões WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6fc05-414">Not used for WebSocket connections.</span></span> <span data-ttu-id="6fc05-415">Esse delegado deve retornar um valor não nulo e recebe o valor padrão como um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="6fc05-415">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6fc05-416">Modifique as configurações do valor padrão e retorne-o ou retorne uma nova instância `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="6fc05-416">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6fc05-417">**Ao substituir o manipulador, certifique-se de copiar as configurações que deseja manter do manipulador fornecido, caso contrário, as opções configuradas (como cookies e cabeçalhos) não se aplicarão ao novo manipulador.**</span><span class="sxs-lookup"><span data-stu-id="6fc05-417">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6fc05-418">Um proxy HTTP a ser usado ao enviar solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6fc05-418">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6fc05-419">Defina esse booliano para enviar as credenciais padrão para solicitações de HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6fc05-419">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6fc05-420">Isso permite o uso da autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="6fc05-420">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6fc05-421">Um delegado que pode ser usado para configurar opções adicionais do WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6fc05-421">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6fc05-422">Recebe uma instância de [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) que pode ser usada para configurar as opções.</span><span class="sxs-lookup"><span data-stu-id="6fc05-422">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="6fc05-423">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6fc05-423">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6fc05-424">Opção de JavaScript</span><span class="sxs-lookup"><span data-stu-id="6fc05-424">JavaScript Option</span></span> | <span data-ttu-id="6fc05-425">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6fc05-425">Default Value</span></span> | <span data-ttu-id="6fc05-426">Descrição</span><span class="sxs-lookup"><span data-stu-id="6fc05-426">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6fc05-427">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6fc05-427">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6fc05-428">Defina isso para `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6fc05-428">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6fc05-429">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="6fc05-429">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6fc05-430">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="6fc05-430">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="6fc05-431">Java</span><span class="sxs-lookup"><span data-stu-id="6fc05-431">Java</span></span>](#tab/java)

| <span data-ttu-id="6fc05-432">Opção Java</span><span class="sxs-lookup"><span data-stu-id="6fc05-432">Java Option</span></span> | <span data-ttu-id="6fc05-433">Valor padrão</span><span class="sxs-lookup"><span data-stu-id="6fc05-433">Default Value</span></span> | <span data-ttu-id="6fc05-434">Descrição</span><span class="sxs-lookup"><span data-stu-id="6fc05-434">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6fc05-435">Uma função que retorna uma cadeia de caracteres que é fornecida como um token de autenticação de portador em solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="6fc05-435">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6fc05-436">Defina isso para `true` para ignorar a etapa de negociação.</span><span class="sxs-lookup"><span data-stu-id="6fc05-436">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6fc05-437">**Com suporte apenas quando o transporte WebSockets é o único transporte habilitado**.</span><span class="sxs-lookup"><span data-stu-id="6fc05-437">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6fc05-438">Essa configuração não pode ser habilitada ao usar o serviço de Signaler do Azure.</span><span class="sxs-lookup"><span data-stu-id="6fc05-438">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6fc05-439">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6fc05-439">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6fc05-440">Vazio</span><span class="sxs-lookup"><span data-stu-id="6fc05-440">Empty</span></span> | <span data-ttu-id="6fc05-441">Um mapa de cabeçalhos HTTP adicionais para enviar com cada solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="6fc05-441">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6fc05-442">No cliente .NET, essas opções podem ser modificadas pelo delegado de opções fornecido para `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="6fc05-442">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6fc05-443">No cliente JavaScript, essas opções podem ser fornecidas em um objeto JavaScript fornecido para `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6fc05-443">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6fc05-444">No cliente Java, essas opções podem ser configuradas com os métodos no `HttpHubConnectionBuilder` retornado do `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6fc05-444">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6fc05-445">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="6fc05-445">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
