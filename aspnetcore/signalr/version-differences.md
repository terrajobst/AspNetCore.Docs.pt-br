---
title: Diferenças entre Signalr e ASP.NET Core Signalr
author: bradygaster
description: Diferenças entre Signalr e ASP.NET Core Signalr
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/14/2018
uid: signalr/version-differences
ms.openlocfilehash: 70b09493d9b4c96c897465d60e53e93a793c42f9
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746547"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="08e7c-103">Diferenças entre o signalr ASP.NET e o sinalizador de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08e7c-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="08e7c-104">ASP.NET Core Signalr não é compatível com clientes ou servidores para o Signalr ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="08e7c-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="08e7c-105">Este artigo detalha os recursos que foram removidos ou alterados no ASP.NET Core Signalr.</span><span class="sxs-lookup"><span data-stu-id="08e7c-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="08e7c-106">Como identificar a versão do Signalr</span><span class="sxs-lookup"><span data-stu-id="08e7c-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="08e7c-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="08e7c-107">ASP.NET SignalR</span></span> | <span data-ttu-id="08e7c-108">SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08e7c-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="08e7c-109">Pacote NuGet do servidor</span><span class="sxs-lookup"><span data-stu-id="08e7c-109">Server NuGet Package</span></span> | [<span data-ttu-id="08e7c-110">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="08e7c-110">Microsoft.AspNet.SignalR</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | <span data-ttu-id="08e7c-111">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="08e7c-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="08e7c-112">[Microsoft. AspNetCore. signalr](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="08e7c-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span></span> |
| <span data-ttu-id="08e7c-113">Pacotes NuGet do cliente</span><span class="sxs-lookup"><span data-stu-id="08e7c-113">Client NuGet Packages</span></span> | [<span data-ttu-id="08e7c-114">Microsoft.AspNet.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="08e7c-114">Microsoft.AspNet.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[<span data-ttu-id="08e7c-115">Microsoft.AspNet.SignalR.JS</span><span class="sxs-lookup"><span data-stu-id="08e7c-115">Microsoft.AspNet.SignalR.JS</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [<span data-ttu-id="08e7c-116">Microsoft.AspNetCore.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="08e7c-116">Microsoft.AspNetCore.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| <span data-ttu-id="08e7c-117">Pacote de NPM do cliente</span><span class="sxs-lookup"><span data-stu-id="08e7c-117">Client npm Package</span></span> | [<span data-ttu-id="08e7c-118">signalr</span><span class="sxs-lookup"><span data-stu-id="08e7c-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="08e7c-119">Cliente Java</span><span class="sxs-lookup"><span data-stu-id="08e7c-119">Java Client</span></span> | <span data-ttu-id="08e7c-120">[Repositório do GitHub](https://github.com/SignalR/java-client) preterido</span><span class="sxs-lookup"><span data-stu-id="08e7c-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="08e7c-121">Pacote Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="08e7c-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="08e7c-122">Tipo de aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="08e7c-122">Server App Type</span></span> | <span data-ttu-id="08e7c-123">ASP.NET (System. Web) ou OWIN Self-host</span><span class="sxs-lookup"><span data-stu-id="08e7c-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="08e7c-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08e7c-124">ASP.NET Core</span></span> |
| <span data-ttu-id="08e7c-125">Plataformas de servidor com suporte</span><span class="sxs-lookup"><span data-stu-id="08e7c-125">Supported Server Platforms</span></span> | <span data-ttu-id="08e7c-126">.NET Framework 4,5 ou posterior</span><span class="sxs-lookup"><span data-stu-id="08e7c-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="08e7c-127">.NET Framework 4.6.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="08e7c-127">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="08e7c-128">.NET Core 2,1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="08e7c-128">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="08e7c-129">Diferenças de recursos</span><span class="sxs-lookup"><span data-stu-id="08e7c-129">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="08e7c-130">Reconexões automáticas</span><span class="sxs-lookup"><span data-stu-id="08e7c-130">Automatic reconnects</span></span>

<span data-ttu-id="08e7c-131">Não há suporte para reconexões automáticas no Signalr ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="08e7c-131">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="08e7c-132">Se o cliente estiver desconectado, o usuário deverá iniciar explicitamente uma nova conexão se quiser se reconectar.</span><span class="sxs-lookup"><span data-stu-id="08e7c-132">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="08e7c-133">No Signalr ASP.NET, o Signalr tenta se reconectar ao servidor se a conexão for descartada.</span><span class="sxs-lookup"><span data-stu-id="08e7c-133">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

### <a name="protocol-support"></a><span data-ttu-id="08e7c-134">Suporte de protocolo</span><span class="sxs-lookup"><span data-stu-id="08e7c-134">Protocol support</span></span>

<span data-ttu-id="08e7c-135">ASP.NET Core Signalr dá suporte a JSON, bem como um novo protocolo binário baseado em [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="08e7c-135">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="08e7c-136">Além disso, os protocolos personalizados podem ser criados.</span><span class="sxs-lookup"><span data-stu-id="08e7c-136">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="08e7c-137">Transportes</span><span class="sxs-lookup"><span data-stu-id="08e7c-137">Transports</span></span>

<span data-ttu-id="08e7c-138">O transporte de quadro contínuo não tem suporte no Signalr ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="08e7c-138">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="08e7c-139">Diferenças no servidor</span><span class="sxs-lookup"><span data-stu-id="08e7c-139">Differences on the server</span></span>

<span data-ttu-id="08e7c-140">As bibliotecas do lado do servidor do ASP.NET Core Signalr são incluídas no pacote de [metapacote do Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) que faz parte do modelo de **aplicativo Web do ASP.NET Core** para projetos Razor e MVC.</span><span class="sxs-lookup"><span data-stu-id="08e7c-140">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="08e7c-141">ASP.NET Core Signalr é um middleware de ASP.NET Core, portanto, ele deve ser configurado com a chamada de [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) no `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="08e7c-141">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08e7c-142">Para configurar o roteamento, mapeie as rotas para os hubs dentro da chamada do método [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="08e7c-142">To configure routing, map routes to hubs inside the [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) method call in the `Startup.Configure` method.</span></span>


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="08e7c-143">Para configurar o roteamento, mapeie as rotas para os hubs dentro da chamada do método [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="08e7c-143">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="08e7c-144">Sessões adesivas</span><span class="sxs-lookup"><span data-stu-id="08e7c-144">Sticky sessions</span></span>

<span data-ttu-id="08e7c-145">O modelo de dimensionamento para o Signalr ASP.NET permite que os clientes reconectem e enviem mensagens para qualquer servidor no farm.</span><span class="sxs-lookup"><span data-stu-id="08e7c-145">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="08e7c-146">No ASP.NET Core Signalr, o cliente deve interagir com o mesmo servidor durante a conexão.</span><span class="sxs-lookup"><span data-stu-id="08e7c-146">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="08e7c-147">Para expansão usando Redis, isso significa que as sessões adesivas são necessárias.</span><span class="sxs-lookup"><span data-stu-id="08e7c-147">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="08e7c-148">Para o scale out usando o [serviço de signaler do Azure](/azure/azure-signalr/), as sessões adesivas não são necessárias porque o serviço lida com conexões com os clientes.</span><span class="sxs-lookup"><span data-stu-id="08e7c-148">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="08e7c-149">Hub único por conexão</span><span class="sxs-lookup"><span data-stu-id="08e7c-149">Single hub per connection</span></span>

<span data-ttu-id="08e7c-150">No Signalr ASP.NET Core, o modelo de conexão foi simplificado.</span><span class="sxs-lookup"><span data-stu-id="08e7c-150">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="08e7c-151">As conexões são feitas diretamente em um único Hub, em vez de uma única conexão sendo usada para compartilhar o acesso a vários hubs.</span><span class="sxs-lookup"><span data-stu-id="08e7c-151">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="08e7c-152">Streaming</span><span class="sxs-lookup"><span data-stu-id="08e7c-152">Streaming</span></span>

<span data-ttu-id="08e7c-153">ASP.NET Core Signalr agora dá suporte ao [streaming de dados](xref:signalr/streaming) do hub para o cliente.</span><span class="sxs-lookup"><span data-stu-id="08e7c-153">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="08e7c-154">Estado</span><span class="sxs-lookup"><span data-stu-id="08e7c-154">State</span></span>

<span data-ttu-id="08e7c-155">A capacidade de passar um estado arbitrário entre clientes e o Hub (geralmente chamado de HubState) foi removida, bem como suporte para mensagens de progresso.</span><span class="sxs-lookup"><span data-stu-id="08e7c-155">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="08e7c-156">Não há nenhum equivalente de proxies de Hub no momento.</span><span class="sxs-lookup"><span data-stu-id="08e7c-156">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="08e7c-157">Remoção de PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="08e7c-157">PersistentConnection removal</span></span>

<span data-ttu-id="08e7c-158">No Signalr ASP.NET Core, a classe [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) foi removida.</span><span class="sxs-lookup"><span data-stu-id="08e7c-158">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="08e7c-159">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="08e7c-159">GlobalHost</span></span>

<span data-ttu-id="08e7c-160">ASP.NET Core tem injeção de dependência (DI) incorporada à estrutura.</span><span class="sxs-lookup"><span data-stu-id="08e7c-160">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="08e7c-161">Os serviços podem usar DI para acessar o [HubContext](xref:signalr/hubcontext).</span><span class="sxs-lookup"><span data-stu-id="08e7c-161">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="08e7c-162">O `GlobalHost` objeto que é usado no signalr ASP.net para obter um `HubContext` não existe no ASP.NET Core signalr.</span><span class="sxs-lookup"><span data-stu-id="08e7c-162">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="08e7c-163">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="08e7c-163">HubPipeline</span></span>

<span data-ttu-id="08e7c-164">ASP.NET Core sinalizador não tem suporte para `HubPipeline` módulos.</span><span class="sxs-lookup"><span data-stu-id="08e7c-164">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="08e7c-165">Diferenças no cliente</span><span class="sxs-lookup"><span data-stu-id="08e7c-165">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="08e7c-166">TypeScript</span><span class="sxs-lookup"><span data-stu-id="08e7c-166">TypeScript</span></span>

<span data-ttu-id="08e7c-167">O cliente Signalr ASP.NET Core é gravado em [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="08e7c-167">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="08e7c-168">Você pode escrever em JavaScript ou TypeScript ao usar o [cliente JavaScript](xref:signalr/javascript-client).</span><span class="sxs-lookup"><span data-stu-id="08e7c-168">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="08e7c-169">O cliente JavaScript está hospedado em [NPM](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="08e7c-169">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="08e7c-170">Em versões anteriores, o cliente JavaScript foi obtido por meio de um pacote NuGet no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="08e7c-170">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="08e7c-171">Para as versões principais, o [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) pacote NPM contém as bibliotecas JavaScript.</span><span class="sxs-lookup"><span data-stu-id="08e7c-171">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="08e7c-172">Este pacote não está incluído no modelo de **aplicativo Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="08e7c-172">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="08e7c-173">Use o NPM para obter e instalar `@aspnet/signalr` o pacote NPM.</span><span class="sxs-lookup"><span data-stu-id="08e7c-173">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="08e7c-174">jQuery</span><span class="sxs-lookup"><span data-stu-id="08e7c-174">jQuery</span></span>

<span data-ttu-id="08e7c-175">A dependência do jQuery foi removida, no entanto, os projetos ainda podem usar o jQuery.</span><span class="sxs-lookup"><span data-stu-id="08e7c-175">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="08e7c-176">Suporte do Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="08e7c-176">Internet Explorer support</span></span>

<span data-ttu-id="08e7c-177">ASP.NET Core Signalr requer o Microsoft Internet Explorer 11 ou posterior (o Signalr ASP.NET com suporte Microsoft Internet Explorer 8 e posterior).</span><span class="sxs-lookup"><span data-stu-id="08e7c-177">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="08e7c-178">Sintaxe do método de cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="08e7c-178">JavaScript client method syntax</span></span>

<span data-ttu-id="08e7c-179">A sintaxe JavaScript foi alterada da versão anterior do Signalr.</span><span class="sxs-lookup"><span data-stu-id="08e7c-179">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="08e7c-180">Em vez de usar `$connection` o objeto, crie uma conexão usando a API [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="08e7c-180">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="08e7c-181">Use o método [on](/javascript/api/@aspnet/signalr/HubConnection#on) para especificar métodos de cliente que o Hub pode chamar.</span><span class="sxs-lookup"><span data-stu-id="08e7c-181">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="08e7c-182">Depois de criar o método de cliente, inicie a conexão de Hub.</span><span class="sxs-lookup"><span data-stu-id="08e7c-182">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="08e7c-183">Encadeamento um método [Catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) para registrar ou tratar erros.</span><span class="sxs-lookup"><span data-stu-id="08e7c-183">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="08e7c-184">Proxies de Hub</span><span class="sxs-lookup"><span data-stu-id="08e7c-184">Hub proxies</span></span>

<span data-ttu-id="08e7c-185">Os proxies do Hub não são mais gerados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="08e7c-185">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="08e7c-186">Em vez disso, o nome do método é passado para a API [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) como uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="08e7c-186">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="08e7c-187">.NET e outros clientes</span><span class="sxs-lookup"><span data-stu-id="08e7c-187">.NET and other clients</span></span>

<span data-ttu-id="08e7c-188">O `Microsoft.AspNetCore.SignalR.Client` pacote NuGet contém as bibliotecas de cliente .net para ASP.NET Core signalr.</span><span class="sxs-lookup"><span data-stu-id="08e7c-188">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="08e7c-189">Use o [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) para criar e criar uma instância de uma conexão com um Hub.</span><span class="sxs-lookup"><span data-stu-id="08e7c-189">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="08e7c-190">Diferenças de dimensionamento</span><span class="sxs-lookup"><span data-stu-id="08e7c-190">Scaleout differences</span></span>

<span data-ttu-id="08e7c-191">O Signalr ASP.NET dá suporte a SQL Server e Redis.</span><span class="sxs-lookup"><span data-stu-id="08e7c-191">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="08e7c-192">ASP.NET Core Signalr dá suporte ao serviço de Signaler do Azure e Redis.</span><span class="sxs-lookup"><span data-stu-id="08e7c-192">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="08e7c-193">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="08e7c-193">ASP.NET</span></span>

* [<span data-ttu-id="08e7c-194">Expansão do signalr com o barramento de serviço do Azure</span><span class="sxs-lookup"><span data-stu-id="08e7c-194">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="08e7c-195">Expansão do signalr com Redis</span><span class="sxs-lookup"><span data-stu-id="08e7c-195">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="08e7c-196">Expansão do signalr com SQL Server</span><span class="sxs-lookup"><span data-stu-id="08e7c-196">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="08e7c-197">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08e7c-197">ASP.NET Core</span></span>

* [<span data-ttu-id="08e7c-198">Serviço Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="08e7c-198">Azure SignalR Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="08e7c-199">Redis backplane</span><span class="sxs-lookup"><span data-stu-id="08e7c-199">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="08e7c-200">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="08e7c-200">Additional resources</span></span>

* [<span data-ttu-id="08e7c-201">Hubs</span><span class="sxs-lookup"><span data-stu-id="08e7c-201">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="08e7c-202">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="08e7c-202">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="08e7c-203">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="08e7c-203">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="08e7c-204">Plataformas compatíveis</span><span class="sxs-lookup"><span data-stu-id="08e7c-204">Supported platforms</span></span>](xref:signalr/supported-platforms)
