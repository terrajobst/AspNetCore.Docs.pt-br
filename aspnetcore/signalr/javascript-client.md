---
title: ASP.NET Core SignalR JavaScript cliente
author: tdykstra
description: Visão geral do cliente JavaScript de SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 11/14/2018
uid: signalr/javascript-client
ms.openlocfilehash: acdb4d1a59d980010fe89fe381190425cbb12901
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341442"
---
# <a name="aspnet-core-signalr-javascript-client"></a>ASP.NET Core SignalR JavaScript cliente

Por [Rachel Appel](http://twitter.com/rachelappel)

A biblioteca de cliente JavaScript de SignalR do ASP.NET Core permite aos desenvolvedores chamar o código de hub do lado do servidor.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-client-package"></a>Instalar o pacote de cliente do SignalR

A biblioteca de cliente SignalR JavaScript é entregue como um [npm](https://www.npmjs.com/) pacote. Se você estiver usando o Visual Studio, execute `npm install` do **Package Manager Console** enquanto na pasta raiz. Para Visual Studio Code, execute o comando a partir de **Terminal integrado**.

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

NPM instala o conteúdo do pacote na *node_modules\\@aspnet\signalr\dist\browser* pasta. Criar uma nova pasta chamada *signalr* sob o *wwwroot\\lib* pasta. Cópia de *signalr.js* do arquivo para o *wwwroot\lib\signalr* pasta.

## <a name="use-the-signalr-javascript-client"></a>Usar o cliente SignalR JavaScript

Referência de cliente SignalR JavaScript no `<script>` elemento.

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a>Conectar a um hub

O código a seguir cria e inicia uma conexão. Nome do hub é diferencia maiusculas de minúsculas.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

### <a name="cross-origin-connections"></a>Conexões entre origens

Normalmente, os navegadores carga das conexões do mesmo domínio que a página solicitada. No entanto, há ocasiões em que é necessária uma conexão para outro domínio.

Para impedir a leitura de dados confidenciais de outro site, um site mal-intencionado [conexões entre origens](xref:security/cors) estão desabilitados por padrão. Para permitir que uma solicitação entre origens, habilitá-lo no `Startup` classe.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Chamar métodos de hub do cliente

Clientes JavaScript chamem métodos públicos em hubs por meio de [invocar](/javascript/api/%40aspnet/signalr/hubconnection#invoke) método da [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). O `invoke` método aceita dois argumentos:

* O nome do método de hub. No exemplo a seguir, o nome do método no hub é `SendMessage`.
* Quaisquer argumentos definidos no método de hub. No exemplo a seguir, é o nome do argumento `message`. O exemplo de código usa a sintaxe da função de seta tem suporte em versões atuais de todos os principais navegadores, exceto o Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

## <a name="call-client-methods-from-hub"></a>Chamar métodos de cliente do hub

Para receber mensagens do hub, definir um método usando o [na](/javascript/api/%40aspnet/signalr/hubconnection#on) método o `HubConnection`.

* O nome do método de cliente JavaScript. No exemplo a seguir, é o nome do método `ReceiveMessage`.
* O hub passa para o método de argumentos. No exemplo a seguir, o valor do argumento é `message`.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

O código anterior no `connection.on` é executado quando o código do lado do servidor chama-o usando o [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) método.

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

O SignalR determina qual método de cliente para chamar, correspondendo o nome do método e argumentos definidos no `SendAsync` e `connection.on`.

> [!NOTE]
> Como uma prática recomendada, chame o [inicie](/javascript/api/%40aspnet/signalr/hubconnection#start) método na `HubConnection` depois `on`. Isso garante que os manipuladores são registrados antes de todas as mensagens são recebidas.

## <a name="error-handling-and-logging"></a>Registro em log e tratamento de erros

Cadeia de um `catch` método até o final do `start` método para lidar com erros do lado do cliente. Use `console.error` para erros de saída para o console do navegador.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=43-45)]

Configure o rastreamento de log do lado do cliente, passando um agente de log e o tipo de evento para registrar em log quando a conexão é feita. As mensagens são registradas com o nível de log especificado e superior. Níveis de log disponíveis são da seguinte maneira:

* `signalR.LogLevel.Error` &ndash; Mensagens de erro. Logs `Error` somente mensagens.
* `signalR.LogLevel.Warning` &ndash; Mensagens de aviso sobre erros em potencial. Os logs `Warning`, e `Error` mensagens.
* `signalR.LogLevel.Information` &ndash; Mensagens de status sem erros. Os logs `Information`, `Warning`, e `Error` mensagens.
* `signalR.LogLevel.Trace` &ndash; Mensagens de rastreamento. Registra tudo, incluindo dados transportados entre cliente e o hub.

Use o [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) método [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar o nível de log. As mensagens são registradas para o console do navegador.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Os clientes de reconexão

O cliente JavaScript para o SignalR não reconectar-se automaticamente. Você deve escrever código que será reconectada seu cliente manualmente. O código a seguir demonstra uma abordagem típica de reconexão:

1. Uma função (nesse caso, o `start` função) é criado para iniciar a conexão.
1. Chame o `start` função em que a conexão `onclose` manipulador de eventos.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Uma implementação real seria usar uma retirada exponencial ou repetir um número especificado de vezes antes de desistir. 

## <a name="additional-resources"></a>Recursos adicionais

* [Referência de API JavaScript](/javascript/api/?view=signalr-js-latest)
* [Tutorial do JavaScript](xref:tutorials/signalr)
* [Tutorial de WebPack e TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Hubs](xref:signalr/hubs)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
* [Solicitações entre origens (CORS)](xref:security/cors)
