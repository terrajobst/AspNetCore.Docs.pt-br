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
# <a name="differences-between-aspnet-opno-locsignalr-and-aspnet-core-opno-locsignalr"></a>Diferenças entre ASP.NET SignalR e ASP.NET Core SignalR

ASP.NET Core SignalR não é compatível com clientes ou servidores do ASP.NET SignalR. Este artigo detalha os recursos que foram removidos ou alterados em ASP.NET Core SignalR.

## <a name="how-to-identify-the-opno-locsignalr-version"></a>Como identificar a versão de SignalR

|                      | SignalR ASP.NET | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| Pacote NuGet do servidor | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)<br>[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| Pacotes NuGet do cliente | [Microsoft. AspNet.SignalR. Cliente](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet.SignalR. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore.SignalR. Cliente](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| Pacote de NPM do cliente | [signalr](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| Cliente Java | [Repositório do GitHub](https://github.com/SignalR/java-client) (preterido)  | Pacote Maven [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Tipo de aplicativo do servidor | ASP.NET (System. Web) ou OWIN Self-host | ASP.NET Core |
| Plataformas de servidor com suporte | .NET Framework 4,5 ou posterior | .NET Framework 4.6.1 ou posterior<br>.NET Core 2,1 ou posterior |

## <a name="feature-differences"></a>Diferenças de recursos

### <a name="automatic-reconnects"></a>Reconexões automáticas

Não há suporte para reconexões automáticas no ASP.NET Core SignalR. Se o cliente estiver desconectado, o usuário deverá iniciar explicitamente uma nova conexão se quiser se reconectar. No ASP.NET SignalR, SignalR tenta se reconectar ao servidor se a conexão for descartada.

### <a name="protocol-support"></a>Suporte de protocolo

ASP.NET Core SignalR dá suporte a JSON, bem como a um novo protocolo binário com base em [MessagePack](xref:signalr/messagepackhubprotocol). Além disso, os protocolos personalizados podem ser criados.

### <a name="transports"></a>Transportes

O transporte de quadro contínuo não tem suporte no ASP.NET Core SignalR.

## <a name="differences-on-the-server"></a>Diferenças no servidor

O ASP.NET Core SignalR bibliotecas do lado do servidor estão incluídas no pacote de [metapacote do Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) que faz parte do modelo de **aplicativo Web do ASP.NET Core** para projetos Razor e MVC.

ASP.NET Core SignalR é um middleware de ASP.NET Core, portanto, ele deve ser configurado chamando [Addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) no `Startup.ConfigureServices`.

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

Para configurar o roteamento, mapeie as rotas para os hubs dentro da chamada do método [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) no método `Startup.Configure`.


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Para configurar o roteamento, mapeie as rotas para os hubs dentro da chamada do método [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) no método `Startup.Configure`.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>Sessões adesivas

O modelo de dimensionamento para ASP.NET SignalR permite que os clientes se reconectem e enviem mensagens para qualquer servidor no farm. No ASP.NET Core SignalR, o cliente deve interagir com o mesmo servidor durante a conexão. Para expansão usando Redis, isso significa que as sessões adesivas são necessárias. Para o scale out usando o [serviço de SignalR do Azure](/azure/azure-signalr/), as sessões adesivas não são necessárias porque o serviço lida com conexões com clientes.

### <a name="single-hub-per-connection"></a>Hub único por conexão

No ASP.NET Core SignalR, o modelo de conexão foi simplificado. As conexões são feitas diretamente em um único Hub, em vez de uma única conexão sendo usada para compartilhar o acesso a vários hubs.

### <a name="streaming"></a>Streaming

ASP.NET Core SignalR agora dá suporte ao [streaming de dados](xref:signalr/streaming) do hub para o cliente.

### <a name="state"></a>Estado

A capacidade de passar um estado arbitrário entre clientes e o Hub (geralmente chamado de HubState) foi removida, bem como suporte para mensagens de progresso. Não há nenhum equivalente de proxies de Hub no momento.

### <a name="persistentconnection-removal"></a>Remoção de PersistentConnection

No ASP.NET Core SignalR, a classe [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) foi removida.

### <a name="globalhost"></a>GlobalHost

ASP.NET Core tem injeção de dependência (DI) incorporada à estrutura. Os serviços podem usar DI para acessar o [HubContext](xref:signalr/hubcontext). O objeto `GlobalHost` usado no SignalR ASP.NET para obter uma `HubContext` não existe no ASP.NET Core SignalR.

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR não tem suporte para módulos de `HubPipeline`.

## <a name="differences-on-the-client"></a>Diferenças no cliente

### <a name="typescript"></a>TypeScript

O cliente de SignalR ASP.NET Core é gravado em [TypeScript](https://www.typescriptlang.org/). Você pode escrever em JavaScript ou TypeScript ao usar o [cliente JavaScript](xref:signalr/javascript-client).

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a>O cliente JavaScript está hospedado em [NPM](https://www.npmjs.com/)

Em versões anteriores, o cliente JavaScript foi obtido por meio de um pacote NuGet no Visual Studio. Para as versões principais, o pacote [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) NPM contém as bibliotecas JavaScript. Este pacote não está incluído no modelo de **aplicativo Web ASP.NET Core** . Use o NPM para obter e instalar o pacote `@aspnet/signalr` NPM.

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a>jQuery

A dependência do jQuery foi removida, no entanto, os projetos ainda podem usar o jQuery.

### <a name="internet-explorer-support"></a>Suporte do Internet Explorer

ASP.NET Core SignalR requer o Microsoft Internet Explorer 11 ou posterior (ASP.NET SignalR com suporte do Microsoft Internet Explorer 8 e posterior).

### <a name="javascript-client-method-syntax"></a>Sintaxe do método de cliente JavaScript

A sintaxe JavaScript foi alterada da versão anterior do SignalR. Em vez de usar o objeto `$connection`, crie uma conexão usando a API [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) .

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Use o método [on](/javascript/api/@aspnet/signalr/HubConnection#on) para especificar métodos de cliente que o Hub pode chamar.

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

Depois de criar o método de cliente, inicie a conexão de Hub. Encadeamento um método [Catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) para registrar ou tratar erros.

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a>Proxies de Hub

Os proxies do Hub não são mais gerados automaticamente. Em vez disso, o nome do método é passado para a API [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) como uma cadeia de caracteres.

### <a name="net-and-other-clients"></a>.NET e outros clientes

O pacote NuGet `Microsoft.AspNetCore.SignalR.Client` contém as bibliotecas de cliente .NET para ASP.NET Core SignalR.

Use o [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) para criar e criar uma instância de uma conexão com um Hub.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>Diferenças de dimensionamento

ASP.NET SignalR dá suporte a SQL Server e Redis. ASP.NET Core SignalR dá suporte ao serviço de SignalR do Azure e Redis.

### <a name="aspnet"></a>ASP.NET

* [dimensionamento de SignalR com o barramento de serviço do Azure](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [dimensionamento de SignalR com Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [dimensionamento de SignalR com SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Serviço de SignalR do Azure](/azure/azure-signalr/)
* [Redis backplane](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>Recursos adicionais

* [Hubs](xref:signalr/hubs)
* [Cliente JavaScript](xref:signalr/javascript-client)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Plataformas compatíveis](xref:signalr/supported-platforms)
