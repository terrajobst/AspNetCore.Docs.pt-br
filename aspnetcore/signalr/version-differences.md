---
title: Diferenças entre o SignalR e SignalR do ASP.NET Core
author: bradygaster
description: Diferenças entre o SignalR e SignalR do ASP.NET Core
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/14/2018
uid: signalr/version-differences
ms.openlocfilehash: d140becaed5b4fc765b7a72571c7f5dce7f4e3d4
ms.sourcegitcommit: 28646e8ca62fb094db1557b5c0c02d5b45531824
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2019
ms.locfileid: "67333453"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="bbe5f-103">Diferenças entre o SignalR do ASP.NET e o SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bbe5f-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="bbe5f-104">SignalR do ASP.NET Core não é compatível com clientes ou servidores para ASP.NET SignalR.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="bbe5f-105">Este artigo fornece detalhes sobre os recursos que foram removidos ou alterados no SignalR do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="bbe5f-106">Como identificar a versão do SignalR</span><span class="sxs-lookup"><span data-stu-id="bbe5f-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="bbe5f-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="bbe5f-107">ASP.NET SignalR</span></span> | <span data-ttu-id="bbe5f-108">SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bbe5f-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="bbe5f-109">Pacote do NuGet Server</span><span class="sxs-lookup"><span data-stu-id="bbe5f-109">Server NuGet Package</span></span> | [<span data-ttu-id="bbe5f-110">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="bbe5f-110">Microsoft.AspNet.SignalR</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | <span data-ttu-id="bbe5f-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="bbe5f-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="bbe5f-112">[Microsoft](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="bbe5f-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span></span> |
| <span data-ttu-id="bbe5f-113">Pacotes NuGet de cliente</span><span class="sxs-lookup"><span data-stu-id="bbe5f-113">Client NuGet Packages</span></span> | [<span data-ttu-id="bbe5f-114">Microsoft.AspNet.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="bbe5f-114">Microsoft.AspNet.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[<span data-ttu-id="bbe5f-115">Microsoft.AspNet.SignalR.JS</span><span class="sxs-lookup"><span data-stu-id="bbe5f-115">Microsoft.AspNet.SignalR.JS</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [<span data-ttu-id="bbe5f-116">Microsoft.AspNetCore.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="bbe5f-116">Microsoft.AspNetCore.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| <span data-ttu-id="bbe5f-117">Pacote npm de cliente</span><span class="sxs-lookup"><span data-stu-id="bbe5f-117">Client npm Package</span></span> | [<span data-ttu-id="bbe5f-118">signalr</span><span class="sxs-lookup"><span data-stu-id="bbe5f-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="bbe5f-119">Cliente de Java</span><span class="sxs-lookup"><span data-stu-id="bbe5f-119">Java Client</span></span> | <span data-ttu-id="bbe5f-120">[Repositório GitHub](https://github.com/SignalR/java-client) (preterido)</span><span class="sxs-lookup"><span data-stu-id="bbe5f-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="bbe5f-121">Pacote do Maven [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="bbe5f-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="bbe5f-122">Tipo de aplicativo de servidor</span><span class="sxs-lookup"><span data-stu-id="bbe5f-122">Server App Type</span></span> | <span data-ttu-id="bbe5f-123">ASP.NET (System. Web) ou a auto-hospedagem de OWIN</span><span class="sxs-lookup"><span data-stu-id="bbe5f-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="bbe5f-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bbe5f-124">ASP.NET Core</span></span> |
| <span data-ttu-id="bbe5f-125">Plataformas de servidor com suporte</span><span class="sxs-lookup"><span data-stu-id="bbe5f-125">Supported Server Platforms</span></span> | <span data-ttu-id="bbe5f-126">.NET framework 4.5 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bbe5f-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="bbe5f-127">.NET Framework 4.6.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bbe5f-127">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="bbe5f-128">.NET core 2.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="bbe5f-128">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="bbe5f-129">Diferenças de recursos</span><span class="sxs-lookup"><span data-stu-id="bbe5f-129">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="bbe5f-130">Reconexão automática</span><span class="sxs-lookup"><span data-stu-id="bbe5f-130">Automatic reconnects</span></span>

<span data-ttu-id="bbe5f-131">Reconexão automática não têm suporte no SignalR do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-131">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="bbe5f-132">Se o cliente for desconectado, o usuário explicitamente deve iniciar uma nova conexão, se eles desejam se reconectar.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-132">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="bbe5f-133">No ASP.NET SignalR, SignalR tentará reconectar-se ao servidor se a conexão for interrompida.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-133">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

### <a name="protocol-support"></a><span data-ttu-id="bbe5f-134">Suporte de protocolo</span><span class="sxs-lookup"><span data-stu-id="bbe5f-134">Protocol support</span></span>

<span data-ttu-id="bbe5f-135">SignalR do ASP.NET Core dá suporte a JSON, bem como um novo protocolo binário, com base em [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="bbe5f-135">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="bbe5f-136">Além disso, os protocolos personalizados podem ser criados.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-136">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="bbe5f-137">Transportes</span><span class="sxs-lookup"><span data-stu-id="bbe5f-137">Transports</span></span>

<span data-ttu-id="bbe5f-138">Não há suporte para o transporte de quadro para sempre SignalR do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-138">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="bbe5f-139">Diferenças no servidor</span><span class="sxs-lookup"><span data-stu-id="bbe5f-139">Differences on the server</span></span>

<span data-ttu-id="bbe5f-140">As bibliotecas do lado do servidor SignalR do ASP.NET Core são incluídas na [metapacote do Microsoft](xref:fundamentals/metapackage-app) que faz parte do pacote a **aplicativo Web ASP.NET Core** modelo Razor e MVC projetos.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-140">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="bbe5f-141">SignalR do ASP.NET Core é um middleware do ASP.NET Core, portanto, ele deve ser configurado por meio da chamada [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-141">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

<span data-ttu-id="bbe5f-142">Para configurar o roteamento, mapear as rotas para os hubs de dentro de [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) chamada de método no `Startup.Configure` método.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-142">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

### <a name="sticky-sessions"></a><span data-ttu-id="bbe5f-143">Sessões temporárias</span><span class="sxs-lookup"><span data-stu-id="bbe5f-143">Sticky sessions</span></span>

<span data-ttu-id="bbe5f-144">O modelo de expansão do SignalR do ASP.NET permite que os clientes para se reconectar e enviar mensagens para qualquer servidor no farm.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-144">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="bbe5f-145">No SignalR do ASP.NET Core, o cliente deve interagir com o mesmo servidor durante a conexão.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-145">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="bbe5f-146">Para escala horizontal usando Redis, isso significa que as sessões temporárias são necessárias.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-146">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="bbe5f-147">Para o uso de expansão [serviço do Azure SignalR](/azure/azure-signalr/), sessões temporárias não são necessárias porque o serviço lida com as conexões aos clientes.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-147">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span> 

### <a name="single-hub-per-connection"></a><span data-ttu-id="bbe5f-148">Hub único por conexão</span><span class="sxs-lookup"><span data-stu-id="bbe5f-148">Single hub per connection</span></span>

<span data-ttu-id="bbe5f-149">O SignalR do ASP.NET Core, o modelo de conexão foi simplificado.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-149">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="bbe5f-150">As conexões são feitas diretamente a um único hub, em vez de uma única conexão está sendo usado para compartilhar o acesso a vários hubs.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-150">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="bbe5f-151">Streaming</span><span class="sxs-lookup"><span data-stu-id="bbe5f-151">Streaming</span></span>

<span data-ttu-id="bbe5f-152">ASP.NET SignalR Core agora dá suporte à [dados de streaming](xref:signalr/streaming) do hub para o cliente.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-152">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="bbe5f-153">Estado</span><span class="sxs-lookup"><span data-stu-id="bbe5f-153">State</span></span>

<span data-ttu-id="bbe5f-154">A capacidade de passar o estado arbitrário entre clientes e o hub (geralmente chamado de HubState) foi removida, bem como suporte para mensagens de progresso.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-154">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="bbe5f-155">Não há nenhum equivalente de proxies de hub no momento.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-155">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="bbe5f-156">Remoção de PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="bbe5f-156">PersistentConnection removal</span></span>

<span data-ttu-id="bbe5f-157">No SignalR do ASP.NET Core, o [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) classe foi removida.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-157">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span> 

### <a name="globalhost"></a><span data-ttu-id="bbe5f-158">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="bbe5f-158">GlobalHost</span></span>

<span data-ttu-id="bbe5f-159">O ASP.NET Core tem dentro da estrutura de injeção de dependência (DI).</span><span class="sxs-lookup"><span data-stu-id="bbe5f-159">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="bbe5f-160">Serviços podem usar a DI para acessar o [HubContext](xref:signalr/hubcontext).</span><span class="sxs-lookup"><span data-stu-id="bbe5f-160">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="bbe5f-161">O `GlobalHost` objeto que é usado no ASP.NET SignalR para obter um `HubContext` não existe no SignalR do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-161">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="bbe5f-162">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="bbe5f-162">HubPipeline</span></span>

<span data-ttu-id="bbe5f-163">SignalR do ASP.NET Core não tem suporte para `HubPipeline` módulos.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-163">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="bbe5f-164">Diferenças no cliente</span><span class="sxs-lookup"><span data-stu-id="bbe5f-164">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="bbe5f-165">TypeScript</span><span class="sxs-lookup"><span data-stu-id="bbe5f-165">TypeScript</span></span>

<span data-ttu-id="bbe5f-166">O cliente SignalR do ASP.NET Core é escrito em [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="bbe5f-166">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="bbe5f-167">Você pode escrever em JavaScript ou TypeScript ao usar o [cliente JavaScript](xref:signalr/javascript-client).</span><span class="sxs-lookup"><span data-stu-id="bbe5f-167">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="bbe5f-168">O cliente JavaScript é hospedado em [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="bbe5f-168">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="bbe5f-169">Nas versões anteriores, o cliente JavaScript foi obtido por meio de um pacote do NuGet no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-169">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="bbe5f-170">Para as versões de núcleo, o [ @aspnet/signalr ](https://www.npmjs.com/package/@aspnet/signalr) pacote npm contém as bibliotecas de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-170">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="bbe5f-171">Este pacote não está incluído na **aplicativo Web ASP.NET Core** modelo.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-171">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="bbe5f-172">Usar npm para obter e instalar o `@aspnet/signalr` pacote npm.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-172">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="bbe5f-173">jQuery</span><span class="sxs-lookup"><span data-stu-id="bbe5f-173">jQuery</span></span>

<span data-ttu-id="bbe5f-174">A dependência no jQuery foi removida, no entanto, projetos ainda podem usar jQuery.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-174">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="bbe5f-175">Suporte do Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="bbe5f-175">Internet Explorer support</span></span>

<span data-ttu-id="bbe5f-176">SignalR do ASP.NET Core requer o Microsoft Internet Explorer 11 ou posterior (o SignalR do ASP.NET com suporte Microsoft Internet Explorer 8 e posterior).</span><span class="sxs-lookup"><span data-stu-id="bbe5f-176">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="bbe5f-177">Sintaxe de método de cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="bbe5f-177">JavaScript client method syntax</span></span>

<span data-ttu-id="bbe5f-178">A sintaxe de JavaScript foi alterado da versão anterior do SignalR.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-178">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="bbe5f-179">Em vez de usar o `$connection` de objeto, criar uma conexão usando o [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-179">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="bbe5f-180">Use o [em](/javascript/api/@aspnet/signalr/HubConnection#on) método para especificar que o hub pode chamar métodos do cliente.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-180">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="bbe5f-181">Depois de criar o método do cliente, inicie a conexão de hub.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-181">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="bbe5f-182">Cadeia de um [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) método para fazer logon ou lidar com erros.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-182">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="bbe5f-183">Proxies de Hub</span><span class="sxs-lookup"><span data-stu-id="bbe5f-183">Hub proxies</span></span>

<span data-ttu-id="bbe5f-184">Os proxies de Hub não automaticamente são gerados.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-184">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="bbe5f-185">Em vez disso, o nome do método é passado para o [invocar](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API como uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-185">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="bbe5f-186">.NET e outros clientes</span><span class="sxs-lookup"><span data-stu-id="bbe5f-186">.NET and other clients</span></span>

<span data-ttu-id="bbe5f-187">O `Microsoft.AspNetCore.SignalR.Client` pacote NuGet contém as bibliotecas de cliente .NET para o SignalR do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-187">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="bbe5f-188">Use o [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) para criar e compilar uma instância de uma conexão a um hub.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-188">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="bbe5f-189">Diferenças de expansão</span><span class="sxs-lookup"><span data-stu-id="bbe5f-189">Scaleout differences</span></span>

<span data-ttu-id="bbe5f-190">SignalR do ASP.NET oferece suporte a SQL Server e o Redis.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-190">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="bbe5f-191">SignalR do ASP.NET Core dá suporte ao serviço do Azure SignalR e Redis.</span><span class="sxs-lookup"><span data-stu-id="bbe5f-191">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="bbe5f-192">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="bbe5f-192">ASP.NET</span></span>

* [<span data-ttu-id="bbe5f-193">Expansão do SignalR com o barramento de serviço do Azure</span><span class="sxs-lookup"><span data-stu-id="bbe5f-193">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="bbe5f-194">Expansão do SignalR com Redis</span><span class="sxs-lookup"><span data-stu-id="bbe5f-194">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="bbe5f-195">Expansão do SignalR com o SQL Server</span><span class="sxs-lookup"><span data-stu-id="bbe5f-195">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="bbe5f-196">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bbe5f-196">ASP.NET Core</span></span>

* [<span data-ttu-id="bbe5f-197">Serviço Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="bbe5f-197">Azure SignalR Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="bbe5f-198">Backplane de redis</span><span class="sxs-lookup"><span data-stu-id="bbe5f-198">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="bbe5f-199">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="bbe5f-199">Additional resources</span></span>

* [<span data-ttu-id="bbe5f-200">Hubs</span><span class="sxs-lookup"><span data-stu-id="bbe5f-200">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="bbe5f-201">Cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="bbe5f-201">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="bbe5f-202">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="bbe5f-202">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="bbe5f-203">Plataformas compatíveis</span><span class="sxs-lookup"><span data-stu-id="bbe5f-203">Supported platforms</span></span>](xref:signalr/supported-platforms)
