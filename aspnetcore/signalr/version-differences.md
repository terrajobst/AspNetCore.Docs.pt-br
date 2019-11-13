---
title: Diferenças entre SignalR e ASP.NET Core SignalR
author: bradygaster
description: Diferenças entre SignalR e ASP.NET Core SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: 0f644c132b0fcf9a0ecf0ab181791a6477c97f76
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963718"
---
# <a name="differences-between-aspnet-opno-locsignalr-and-aspnet-core-opno-locsignalr"></a><span data-ttu-id="03724-103">Diferenças entre ASP.NET SignalR e ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="03724-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="03724-104">ASP.NET Core SignalR não é compatível com clientes ou servidores do ASP.NET SignalR.</span><span class="sxs-lookup"><span data-stu-id="03724-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="03724-105">Este artigo detalha os recursos que foram removidos ou alterados em ASP.NET Core SignalR.</span><span class="sxs-lookup"><span data-stu-id="03724-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-opno-locsignalr-version"></a><span data-ttu-id="03724-106">Como identificar a versão de SignalR</span><span class="sxs-lookup"><span data-stu-id="03724-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="03724-107">SignalR ASP.NET</span><span class="sxs-lookup"><span data-stu-id="03724-107">ASP.NET SignalR</span></span> | <span data-ttu-id="03724-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="03724-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="03724-109">Pacote NuGet do servidor</span><span class="sxs-lookup"><span data-stu-id="03724-109">Server NuGet Package</span></span> | <span data-ttu-id="03724-110">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="03724-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="03724-111">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="03724-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="03724-112">[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="03724-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="03724-113">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="03724-113">(.NET Framework)</span></span> |
| <span data-ttu-id="03724-114">Pacotes NuGet do cliente</span><span class="sxs-lookup"><span data-stu-id="03724-114">Client NuGet Packages</span></span> | <span data-ttu-id="03724-115">[Microsoft. AspNet.SignalR. Cliente](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="03724-115">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="03724-116">[Microsoft. AspNet.SignalR. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="03724-116">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="03724-117">[Microsoft. AspNetCore.SignalR. Cliente](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="03724-117">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="03724-118">Pacote de NPM do cliente</span><span class="sxs-lookup"><span data-stu-id="03724-118">Client npm Package</span></span> | [<span data-ttu-id="03724-119">signalr</span><span class="sxs-lookup"><span data-stu-id="03724-119">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="03724-120">Cliente Java</span><span class="sxs-lookup"><span data-stu-id="03724-120">Java Client</span></span> | <span data-ttu-id="03724-121">[Repositório do GitHub](https://github.com/SignalR/java-client) (preterido)</span><span class="sxs-lookup"><span data-stu-id="03724-121">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="03724-122">Pacote Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="03724-122">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="03724-123">Tipo de aplicativo do servidor</span><span class="sxs-lookup"><span data-stu-id="03724-123">Server App Type</span></span> | <span data-ttu-id="03724-124">ASP.NET (System. Web) ou OWIN Self-host</span><span class="sxs-lookup"><span data-stu-id="03724-124">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="03724-125">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03724-125">ASP.NET Core</span></span> |
| <span data-ttu-id="03724-126">Plataformas de servidor com suporte</span><span class="sxs-lookup"><span data-stu-id="03724-126">Supported Server Platforms</span></span> | <span data-ttu-id="03724-127">.NET Framework 4,5 ou posterior</span><span class="sxs-lookup"><span data-stu-id="03724-127">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="03724-128">.NET Framework 4.6.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="03724-128">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="03724-129">.NET Core 2,1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="03724-129">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="03724-130">Diferenças de recursos</span><span class="sxs-lookup"><span data-stu-id="03724-130">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="03724-131">Reconexões automáticas</span><span class="sxs-lookup"><span data-stu-id="03724-131">Automatic reconnects</span></span>

<span data-ttu-id="03724-132">Não há suporte para reconexões automáticas no ASP.NET Core SignalR.</span><span class="sxs-lookup"><span data-stu-id="03724-132">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="03724-133">Se o cliente estiver desconectado, o usuário deverá iniciar explicitamente uma nova conexão se quiser se reconectar.</span><span class="sxs-lookup"><span data-stu-id="03724-133">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="03724-134">No ASP.NET SignalR, SignalR tenta se reconectar ao servidor se a conexão for descartada.</span><span class="sxs-lookup"><span data-stu-id="03724-134">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

### <a name="protocol-support"></a><span data-ttu-id="03724-135">Suporte de protocolo</span><span class="sxs-lookup"><span data-stu-id="03724-135">Protocol support</span></span>

<span data-ttu-id="03724-136">ASP.NET Core SignalR dá suporte a JSON, bem como a um novo protocolo binário com base em [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="03724-136">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="03724-137">Além disso, os protocolos personalizados podem ser criados.</span><span class="sxs-lookup"><span data-stu-id="03724-137">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="03724-138">Transportes</span><span class="sxs-lookup"><span data-stu-id="03724-138">Transports</span></span>

<span data-ttu-id="03724-139">O transporte de quadro contínuo não tem suporte no ASP.NET Core SignalR.</span><span class="sxs-lookup"><span data-stu-id="03724-139">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="03724-140">Diferenças no servidor</span><span class="sxs-lookup"><span data-stu-id="03724-140">Differences on the server</span></span>

<span data-ttu-id="03724-141">O ASP.NET Core SignalR bibliotecas do lado do servidor estão incluídas no pacote de [metapacote do Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) que faz parte do modelo de **aplicativo Web do ASP.NET Core** para projetos Razor e MVC.</span><span class="sxs-lookup"><span data-stu-id="03724-141">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="03724-142">ASP.NET Core SignalR é um middleware de ASP.NET Core, portanto, ele deve ser configurado chamando [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) no `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="03724-142">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="03724-143">Para configurar o roteamento, mapeie as rotas para os hubs dentro da chamada do método [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="03724-143">To configure routing, map routes to hubs inside the [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) method call in the `Startup.Configure` method.</span></span>


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="03724-144">Para configurar o roteamento, mapeie as rotas para os hubs dentro da chamada do método [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) no método `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="03724-144">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="03724-145">Sessões adesivas</span><span class="sxs-lookup"><span data-stu-id="03724-145">Sticky sessions</span></span>

<span data-ttu-id="03724-146">O modelo de dimensionamento para ASP.NET SignalR permite que os clientes se reconectem e enviem mensagens para qualquer servidor no farm.</span><span class="sxs-lookup"><span data-stu-id="03724-146">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="03724-147">No ASP.NET Core SignalR, o cliente deve interagir com o mesmo servidor durante a conexão.</span><span class="sxs-lookup"><span data-stu-id="03724-147">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="03724-148">Para expansão usando Redis, isso significa que as sessões adesivas são necessárias.</span><span class="sxs-lookup"><span data-stu-id="03724-148">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="03724-149">Para o scale out usando o [serviço de SignalR do Azure](/azure/azure-signalr/), as sessões adesivas não são necessárias porque o serviço lida com conexões com clientes.</span><span class="sxs-lookup"><span data-stu-id="03724-149">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="03724-150">Hub único por conexão</span><span class="sxs-lookup"><span data-stu-id="03724-150">Single hub per connection</span></span>

<span data-ttu-id="03724-151">No ASP.NET Core SignalR, o modelo de conexão foi simplificado.</span><span class="sxs-lookup"><span data-stu-id="03724-151">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="03724-152">As conexões são feitas diretamente em um único Hub, em vez de uma única conexão sendo usada para compartilhar o acesso a vários hubs.</span><span class="sxs-lookup"><span data-stu-id="03724-152">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="03724-153">Streaming</span><span class="sxs-lookup"><span data-stu-id="03724-153">Streaming</span></span>

<span data-ttu-id="03724-154">ASP.NET Core SignalR agora dá suporte ao [streaming de dados](xref:signalr/streaming) do hub para o cliente.</span><span class="sxs-lookup"><span data-stu-id="03724-154">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="03724-155">Estado</span><span class="sxs-lookup"><span data-stu-id="03724-155">State</span></span>

<span data-ttu-id="03724-156">A capacidade de passar um estado arbitrário entre clientes e o Hub (geralmente chamado de HubState) foi removida, bem como suporte para mensagens de progresso.</span><span class="sxs-lookup"><span data-stu-id="03724-156">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="03724-157">Não há nenhum equivalente de proxies de Hub no momento.</span><span class="sxs-lookup"><span data-stu-id="03724-157">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="03724-158">Remoção de PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="03724-158">PersistentConnection removal</span></span>

<span data-ttu-id="03724-159">No ASP.NET Core SignalR, a classe [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) foi removida.</span><span class="sxs-lookup"><span data-stu-id="03724-159">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="03724-160">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="03724-160">GlobalHost</span></span>

<span data-ttu-id="03724-161">ASP.NET Core tem injeção de dependência (DI) incorporada à estrutura.</span><span class="sxs-lookup"><span data-stu-id="03724-161">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="03724-162">Os serviços podem usar DI para acessar o [HubContext](xref:signalr/hubcontext).</span><span class="sxs-lookup"><span data-stu-id="03724-162">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="03724-163">O objeto `GlobalHost` usado no SignalR ASP.NET para obter uma `HubContext` não existe no ASP.NET Core SignalR.</span><span class="sxs-lookup"><span data-stu-id="03724-163">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="03724-164">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="03724-164">HubPipeline</span></span>

<span data-ttu-id="03724-165">ASP.NET Core SignalR não tem suporte para módulos de `HubPipeline`.</span><span class="sxs-lookup"><span data-stu-id="03724-165">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="03724-166">Diferenças no cliente</span><span class="sxs-lookup"><span data-stu-id="03724-166">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="03724-167">TypeScript</span><span class="sxs-lookup"><span data-stu-id="03724-167">TypeScript</span></span>

<span data-ttu-id="03724-168">O cliente de SignalR ASP.NET Core é gravado em [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="03724-168">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="03724-169">Você pode escrever em JavaScript ou TypeScript ao usar o [cliente JavaScript](xref:signalr/javascript-client).</span><span class="sxs-lookup"><span data-stu-id="03724-169">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="03724-170">O cliente JavaScript está hospedado em [NPM](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="03724-170">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="03724-171">Em versões anteriores, o cliente JavaScript foi obtido por meio de um pacote NuGet no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="03724-171">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="03724-172">Para as versões principais, o pacote [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) NPM contém as bibliotecas JavaScript.</span><span class="sxs-lookup"><span data-stu-id="03724-172">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="03724-173">Este pacote não está incluído no modelo de **aplicativo Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="03724-173">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="03724-174">Use o NPM para obter e instalar o pacote `@aspnet/signalr` NPM.</span><span class="sxs-lookup"><span data-stu-id="03724-174">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="03724-175">jQuery</span><span class="sxs-lookup"><span data-stu-id="03724-175">jQuery</span></span>

<span data-ttu-id="03724-176">A dependência do jQuery foi removida, no entanto, os projetos ainda podem usar o jQuery.</span><span class="sxs-lookup"><span data-stu-id="03724-176">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="03724-177">Suporte do Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="03724-177">Internet Explorer support</span></span>

<span data-ttu-id="03724-178">ASP.NET Core SignalR requer o Microsoft Internet Explorer 11 ou posterior (ASP.NET SignalR com suporte do Microsoft Internet Explorer 8 e posterior).</span><span class="sxs-lookup"><span data-stu-id="03724-178">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="03724-179">Sintaxe do método de cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="03724-179">JavaScript client method syntax</span></span>

<span data-ttu-id="03724-180">A sintaxe JavaScript foi alterada da versão anterior do SignalR.</span><span class="sxs-lookup"><span data-stu-id="03724-180">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="03724-181">Em vez de usar o objeto `$connection`, crie uma conexão usando a API [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="03724-181">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="03724-182">Use o método [on](/javascript/api/@aspnet/signalr/HubConnection#on) para especificar métodos de cliente que o Hub pode chamar.</span><span class="sxs-lookup"><span data-stu-id="03724-182">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="03724-183">Depois de criar o método de cliente, inicie a conexão de Hub.</span><span class="sxs-lookup"><span data-stu-id="03724-183">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="03724-184">Encadeamento um método [Catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) para registrar ou tratar erros.</span><span class="sxs-lookup"><span data-stu-id="03724-184">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="03724-185">Proxies de Hub</span><span class="sxs-lookup"><span data-stu-id="03724-185">Hub proxies</span></span>

<span data-ttu-id="03724-186">Os proxies do Hub não são mais gerados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="03724-186">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="03724-187">Em vez disso, o nome do método é passado para a API [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) como uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="03724-187">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="03724-188">.NET e outros clientes</span><span class="sxs-lookup"><span data-stu-id="03724-188">.NET and other clients</span></span>

<span data-ttu-id="03724-189">O pacote NuGet `Microsoft.AspNetCore.SignalR.Client` contém as bibliotecas de cliente .NET para ASP.NET Core SignalR.</span><span class="sxs-lookup"><span data-stu-id="03724-189">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="03724-190">Use o [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) para criar e criar uma instância de uma conexão com um Hub.</span><span class="sxs-lookup"><span data-stu-id="03724-190">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="03724-191">Diferenças de dimensionamento</span><span class="sxs-lookup"><span data-stu-id="03724-191">Scaleout differences</span></span>

<span data-ttu-id="03724-192">ASP.NET SignalR dá suporte a SQL Server e Redis.</span><span class="sxs-lookup"><span data-stu-id="03724-192">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="03724-193">ASP.NET Core SignalR dá suporte ao serviço de SignalR do Azure e Redis.</span><span class="sxs-lookup"><span data-stu-id="03724-193">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="03724-194">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="03724-194">ASP.NET</span></span>

* <span data-ttu-id="03724-195">[dimensionamento de SignalR com o barramento de serviço do Azure](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span><span class="sxs-lookup"><span data-stu-id="03724-195">[SignalR scaleout with Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span></span>
* <span data-ttu-id="03724-196">[dimensionamento de SignalR com Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span><span class="sxs-lookup"><span data-stu-id="03724-196">[SignalR scaleout with Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span></span>
* <span data-ttu-id="03724-197">[dimensionamento de SignalR com SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span><span class="sxs-lookup"><span data-stu-id="03724-197">[SignalR scaleout with SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span></span>

### <a name="aspnet-core"></a><span data-ttu-id="03724-198">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03724-198">ASP.NET Core</span></span>

* <span data-ttu-id="03724-199">[Serviço de SignalR do Azure](/azure/azure-signalr/)</span><span class="sxs-lookup"><span data-stu-id="03724-199">[Azure SignalR Service](/azure/azure-signalr/)</span></span>
* [<span data-ttu-id="03724-200">Redis backplane</span><span class="sxs-lookup"><span data-stu-id="03724-200">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="03724-201">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="03724-201">Additional resources</span></span>

* [<span data-ttu-id="03724-202">Hubs</span><span class="sxs-lookup"><span data-stu-id="03724-202">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="03724-203">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="03724-203">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="03724-204">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="03724-204">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="03724-205">Plataformas compatíveis</span><span class="sxs-lookup"><span data-stu-id="03724-205">Supported platforms</span></span>](xref:signalr/supported-platforms)
