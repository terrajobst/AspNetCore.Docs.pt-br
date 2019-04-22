---
title: ASP.NET Core SignalR JavaScript cliente
author: bradygaster
description: Visão geral do cliente JavaScript de SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/javascript-client
ms.openlocfilehash: f1f072e63928502fa1bad62e808ff035e57f2fd3
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59983007"
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

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Conexões entre origens

Normalmente, os navegadores carga das conexões do mesmo domínio que a página solicitada. No entanto, há ocasiões em que é necessária uma conexão para outro domínio.

Para impedir a leitura de dados confidenciais de outro site, um site mal-intencionado [conexões entre origens](xref:security/cors) estão desabilitados por padrão. Para permitir que uma solicitação entre origens, habilitá-lo no `Startup` classe.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Chamar métodos de hub do cliente

Clientes JavaScript chamem métodos públicos em hubs por meio de [invocar](/javascript/api/%40aspnet/signalr/hubconnection#invoke) método da [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). O `invoke` método aceita dois argumentos:

* O nome do método de hub. No exemplo a seguir, o nome do método no hub é `SendMessage`.
* Quaisquer argumentos definidos no método de hub. No exemplo a seguir, é o nome do argumento `message`. O exemplo de código usa a sintaxe da função de seta tem suporte em versões atuais de todos os principais navegadores, exceto o Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Se você estiver usando o serviço do Azure SignalR no *modo sem servidor*, você não pode chamar métodos de hub de um cliente. Para obter mais informações, consulte o [documentação do SignalR Service](/azure/azure-signalr/signalr-concept-serverless-development-config).

O `invoke` método retorna um JavaScript [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise). O `Promise` é resolvido com o valor de retorno (se houver) quando o método no servidor de retornar. Se o método no servidor gerará um erro, o `Promise` será rejeitada com a mensagem de erro. Use o `then` e `catch` métodos na `Promise` em si para lidar com esses casos (ou `await` sintaxe).

O `send` método retorna um JavaScript `Promise`. O `Promise` é resolvido quando a mensagem foi enviada ao servidor. Se não houver um erro ao enviar a mensagem, o `Promise` será rejeitada com a mensagem de erro. Use o `then` e `catch` métodos na `Promise` em si para lidar com esses casos (ou `await` sintaxe).

> [!NOTE]
> Usando `send` não Aguarde até que o servidor recebeu a mensagem. Consequentemente, não é possível retornar dados ou erros do servidor.

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

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

Configure o rastreamento de log do lado do cliente, passando um agente de log e o tipo de evento para registrar em log quando a conexão é feita. As mensagens são registradas com o nível de log especificado e superior. Níveis de log disponíveis são da seguinte maneira:

* `signalR.LogLevel.Error` &ndash; Mensagens de erro. Logs `Error` somente mensagens.
* `signalR.LogLevel.Warning` &ndash; Mensagens de aviso sobre erros em potencial. Os logs `Warning`, e `Error` mensagens.
* `signalR.LogLevel.Information` &ndash; Mensagens de status sem erros. Os logs `Information`, `Warning`, e `Error` mensagens.
* `signalR.LogLevel.Trace` &ndash; Mensagens de rastreamento. Registra tudo, incluindo dados transportados entre cliente e o hub.

Use o [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) método [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar o nível de log. As mensagens são registradas para o console do navegador.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Os clientes de reconexão

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Reconectar-se automaticamente

O cliente JavaScript para o SignalR pode ser configurado para reconectar-se automaticamente usando o `withAutomaticReconnect` método no [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). Ele não se reconectar automaticamente por padrão.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Sem parâmetros, `withAutomaticReconnect()` configura o cliente para aguardar 0, 2, 10 e 30 segundos respectivamente antes de tentar a cada tentativa de reconexão, parando depois de quatro tentativas com falha.

Antes de iniciar qualquer tentativa de reconexão, o `HubConnection` será a transição para o `HubConnectionState.Reconnecting` de estado e acionar seus `onreconnecting` retornos de chamada em vez de fazer a transição para o `Disconnected` estado e disparar seu `onclose` retornos de chamada, como um `HubConnection`sem reconectar automático configurado. Isso fornece uma oportunidade para avisar os usuários que a conexão foi perdida e para desativar elementos da interface do usuário.

```javascript
connection.onreconnecting((error) => {
  console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

  document.getElementById("messageInput").disabled = true;

  const li = document.createElement("li");
  li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
  document.getElementById("messagesList").appendChild(li);
});
```

Se o cliente se reconecta com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição para o `Connected` de estado e acionar seu `onreconnected` retornos de chamada. Isso fornece uma oportunidade para informar os usuários que a conexão ser restabelecida.

Como a conexão parece inteiramente nova para o servidor, um novo `connectionId` será fornecido para o `onreconnected` retorno de chamada.

> [!WARNING]
> O `onreconnected` do retorno de chamada `connectionId` parâmetro será indefinido se o `HubConnection` foi configurado para [ignorar negociação](xref:signalr/configuration#configure-client-options).

```javascript
connection.onreconnected((connectionId) => {
  console.assert(connection.state === signalR.HubConnectionState.Connected);

  document.getElementById("messageInput").disabled = false;

  const li = document.createElement("li");
  li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
  document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()` não configure o `HubConnection` para repetir as falhas de inicialização inicial, portanto, falhas de inicialização precisam ser feito manualmente:

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

Se o cliente não reconectar-se com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` será a transição para o `Disconnected` de estado e acionar seus [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) retornos de chamada. Isso fornece uma oportunidade para informar os usuários a conexão foi perdido permanentemente e recomendável atualizar a página:

```javascript
connection.onclose((error) => {
  console.assert(connection.state === signalR.HubConnectionState.Disconnected);

  document.getElementById("messageInput").disabled = true;

  const li = document.createElement("li");
  li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
  document.getElementById("messagesList").appendChild(li);
})
```

Para configurar um número personalizado de tentativas de reconexão antes de desconectar ou alterar o tempo de reconexão, `withAutomaticReconnect` aceita uma matriz de números que representa o atraso em milissegundos para esperar antes de começar a cada tentativa de reconexão.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

O exemplo anterior configura o `HubConnection` para iniciar a tentativa de reconexão imediatamente depois que a conexão for perdida. Isso também é verdadeiro para a configuração padrão.

Se a primeira tentativa de reconexão falhar, a segunda tentativa de reconexão também será iniciado imediatamente em vez de aguardar de 2 segundos como apareceria na configuração padrão.

Se a segunda tentativa de reconexão falhar, a terceira tentativa de reconexão será iniciado em 10 segundos, que é novamente como a configuração padrão.

O comportamento personalizado, em seguida, diverge novamente do comportamento padrão, interrompendo depois reconectar a terceira tentativa falha em vez de tentar uma reconexão mais tentativa em outro 30 segundos como apareceria na configuração padrão.

Se você quiser ainda mais controle sobre o tempo e o número de automático se reconectar, as tentativas `withAutomaticReconnect` aceita um objeto que implementa o `IReconnectPolicy` interface, que tem um único método chamado `nextRetryDelayInMilliseconds`.

`nextRetryDelayInMilliseconds` leva dois argumentos: `previousRetryCount` e `elapsedMilliseconds`, que são os dois números. Antes da primeira tentativa de reconexão, ambos `previousRetryCount` e `elapsedMilliseconds` será zero. Após cada tentativa de repetição com falha, `previousRetryCount` será incrementado por um e `elapsedMilliseconds` será atualizada para refletir a quantidade de tempo gasto se reconectar até o momento em milissegundos.

`nextRetryDelayInMilliseconds` deve retornar um número que representa o número de milissegundos a aguardar antes da próxima tentativa de reconexão ou `null` se o `HubConnection` deve parar de se reconectar.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: (previousRetryCount, elapsedMilliseconds) => {
          if (elapsedMilliseconds < 60000) {
            // If we've been reconnecting for less than 60 seconds so far,
            // wait between 0 and 10 seconds before the next reconnect attempt.
            return Math.random() * 10000;
          } else {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
          }
        })
    .build();
```

Como alternativa, você pode escrever código que será reconectada seu cliente manualmente, conforme demonstrado [reconectar manualmente](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Reconectar manualmente

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Antes do 3.0, o cliente JavaScript para o SignalR não reconectados automaticamente. Você deve escrever código que será reconectada seu cliente manualmente.

::: moniker-end

O código a seguir demonstra uma abordagem típica de reconexão manual:

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
* [Documentação de sem servidor SignalR Service do Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)
