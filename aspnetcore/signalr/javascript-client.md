---
title: ASP.NET Core SignalR cliente JavaScript
author: bradygaster
description: Visão geral de ASP.NET Core SignalR cliente JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: 3086b4aa532dfe992e19c193ef76f216f7835164
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78657853"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a>ASP.NET Core SignalR cliente JavaScript

Por [Rachel Appel](https://twitter.com/rachelappel)

O ASP.NET Core SignalR biblioteca de cliente JavaScript permite que os desenvolvedores chamem o código de Hub do lado do servidor.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="install-the-opno-locsignalr-client-package"></a>Instalar o pacote do cliente do SignalR

A biblioteca de cliente SignalR JavaScript é entregue como um pacote [NPM](https://www.npmjs.com/) . Se você estiver usando o Visual Studio, execute `npm install` no **console do Gerenciador de pacotes** enquanto estiver na pasta raiz. Para Visual Studio Code, execute o comando do **terminal integrado**.

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

o NPM instala o conteúdo do pacote na pasta *node_modules\\@microsoft\signalr\dist\browser* . Crie uma nova pasta chamada *signalr* na pasta *wwwroot\\lib* . Copie o arquivo *signalr. js* para a pasta *wwwroot\lib\signalr* .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

o NPM instala o conteúdo do pacote na pasta *node_modules\\@aspnet\signalr\dist\browser* . Crie uma nova pasta chamada *signalr* na pasta *wwwroot\\lib* . Copie o arquivo *signalr. js* para a pasta *wwwroot\lib\signalr* .

::: moniker-end

## <a name="use-the-opno-locsignalr-javascript-client"></a>Usar o SignalR cliente JavaScript

Referencie o SignalR cliente JavaScript no elemento `<script>`.

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a>Conectar-se a um hub

O código a seguir cria e inicia uma conexão. O nome do Hub não diferencia maiúsculas de minúsculas.

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a>Conexões entre origens

Normalmente, os navegadores carregam conexões do mesmo domínio que a página solicitada. No entanto, há ocasiões em que uma conexão com outro domínio é necessária.

Para impedir que um site mal-intencionado leia dados confidenciais de outro site, [as conexões entre origens](xref:security/cors) são desabilitadas por padrão. Para permitir uma solicitação entre origens, habilite-a na classe `Startup`.

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Métodos do hub de chamadas do cliente

Os clientes JavaScript chamam métodos públicos em hubs por meio do método [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) de [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection). O método `invoke` aceita dois argumentos:

* O nome do método de Hub. No exemplo a seguir, o nome do método no Hub é `SendMessage`.
* Quaisquer argumentos definidos no método Hub. No exemplo a seguir, o nome do argumento é `message`. O código de exemplo usa a sintaxe de função de seta que tem suporte nas versões atuais de todos os principais navegadores, exceto o Internet Explorer.

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Se você estiver usando o serviço de SignalR do Azure no *modo sem servidor*, não será possível chamar os métodos de Hub de um cliente. Para obter mais informações, consulte a [documentação do serviço deSignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).

O método `invoke` retorna uma [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)de JavaScript. O `Promise` é resolvido com o valor de retorno (se houver) quando o método no servidor retorna. Se o método no servidor gerar um erro, o `Promise` será rejeitado com a mensagem de erro. Use os métodos `then` e `catch` na `Promise` em si para lidar com esses casos (ou `await` sintaxe).

O método `send` retorna um `Promise`JavaScript. O `Promise` é resolvido quando a mensagem é enviada ao servidor. Se houver um erro ao enviar a mensagem, o `Promise` será rejeitado com a mensagem de erro. Use os métodos `then` e `catch` na `Promise` em si para lidar com esses casos (ou `await` sintaxe).

> [!NOTE]
> O uso de `send` não aguarda até que o servidor tenha recebido a mensagem. Consequentemente, não é possível retornar dados ou erros do servidor.

## <a name="call-client-methods-from-hub"></a>Chamar métodos de cliente do Hub

Para receber mensagens do Hub, defina um método usando o método [on](/javascript/api/%40aspnet/signalr/hubconnection#on) do `HubConnection`.

* O nome do método de cliente JavaScript. No exemplo a seguir, o nome do método é `ReceiveMessage`.
* Argumentos que o Hub passa para o método. No exemplo a seguir, o valor do argumento é `message`.

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

O código anterior em `connection.on` é executado quando o código do lado do servidor o chama usando o método [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) .

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR determina qual método de cliente deve ser chamado, correspondendo ao nome do método e aos argumentos definidos em `SendAsync` e `connection.on`.

> [!NOTE]
> Como prática recomendada, chame o método [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) no `HubConnection` depois de `on`. Isso garante que seus manipuladores sejam registrados antes que todas as mensagens sejam recebidas.

## <a name="error-handling-and-logging"></a>Registro em log e tratamento de erros

Encadeamento um método de `catch` ao final do método `start` para manipular erros do lado do cliente. Use `console.error` para gerar erros no console do navegador.

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

Configure o rastreamento de log no lado do cliente passando um agente e um tipo de evento para registrar quando a conexão é feita. As mensagens são registradas com o nível de log especificado e superior. Os níveis de log disponíveis são os seguintes:

* `signalR.LogLevel.Error` &ndash; mensagens de erro. Registra somente `Error` mensagens.
* `signalR.LogLevel.Warning` &ndash; mensagens de aviso sobre possíveis erros. Registra `Warning`e `Error` mensagens.
* `signalR.LogLevel.Information` &ndash; mensagens de status sem erros. Registra `Information`, `Warning`e mensagens de `Error`.
* `signalR.LogLevel.Trace` &ndash; mensagens de rastreamento. Registra tudo, incluindo dados transportados entre o Hub e o cliente.

Use o método [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) no [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar o nível de log. As mensagens são registradas no console do navegador.

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Reconectar clientes

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Reconectar automaticamente

O cliente JavaScript para SignalR pode ser configurado para reconectar-se automaticamente usando o método `withAutomaticReconnect` em [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder). Ele não se reconectará automaticamente por padrão.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Sem parâmetros, `withAutomaticReconnect()` configura o cliente para aguardar 0, 2, 10 e 30 segundos, respectivamente, antes de tentar cada tentativa de reconexão, parando após quatro tentativas com falha.

Antes de iniciar qualquer tentativa de reconexão, o `HubConnection` fará a transição para o estado de `HubConnectionState.Reconnecting` e acionará seus retornos de chamada `onreconnecting` em vez de fazer a transição para o estado de `Disconnected` e disparar seus retornos de chamada de `onclose` como um `HubConnection` sem a reconexão automática configurada. Isso fornece uma oportunidade para avisar os usuários de que a conexão foi perdida e para desabilitar os elementos da interface do usuário.

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Se o cliente se reconectar com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição de volta para o estado de `Connected` e acionará seus retornos de chamada `onreconnected`. Isso fornece uma oportunidade de informar aos usuários que a conexão foi restabelecida.

Como a conexão é totalmente nova no servidor, um novo `connectionId` será fornecido ao retorno de chamada `onreconnected`.

> [!WARNING]
> O parâmetro `connectionId` do retorno de chamada `onreconnected` será indefinido se o `HubConnection` tiver sido configurado para [ignorar a negociação](xref:signalr/configuration#configure-client-options).

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()` não configurará o `HubConnection` para repetir as falhas de início inicial, portanto, as falhas de início precisam ser manipuladas manualmente:

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

Se o cliente não se reconectar com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição para o estado de `Disconnected` e disparará seus retornos de chamada [fechamento](/javascript/api/%40aspnet/signalr/hubconnection#onclose) . Isso fornece uma oportunidade de informar aos usuários que a conexão foi permanentemente perdida e recomenda atualizar a página:

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Para configurar um número personalizado de tentativas de reconexão antes de desconectar ou alterar o tempo de reconexão, `withAutomaticReconnect` aceita uma matriz de números que representa o atraso em milissegundos para aguardar antes de iniciar cada tentativa de reconexão.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

O exemplo anterior configura a `HubConnection` para iniciar a tentativa de reconexão imediatamente após a perda da conexão. Isso também é verdadeiro para a configuração padrão.

Se a primeira tentativa de reconexão falhar, a segunda tentativa de reconexão também será iniciada imediatamente, em vez de esperar 2 segundos, como seria na configuração padrão.

Se a segunda tentativa de reconexão falhar, a terceira tentativa de reconexão será iniciada em 10 segundos, o que é novamente como a configuração padrão.

O comportamento personalizado, em seguida, deriva novamente do comportamento padrão ao parar após a terceira tentativa de reconexão, em vez de tentar mais uma tentativa de reconexão em outros 30 segundos, como seria na configuração padrão.

Se você quiser ter ainda mais controle sobre o tempo e o número de tentativas de reconexão automática, `withAutomaticReconnect` aceitará um objeto que implementa a interface `IRetryPolicy`, que tem um único método chamado `nextRetryDelayInMilliseconds`.

`nextRetryDelayInMilliseconds` usa um único argumento com o tipo `RetryContext`. O `RetryContext` tem três propriedades: `previousRetryCount`, `elapsedMilliseconds` e `retryReason` que são um `number`, um `number` e um `Error` respectivamente. Antes da primeira tentativa de reconexão, `previousRetryCount` e `elapsedMilliseconds` serão zero e o `retryReason` será o erro que causou a perda da conexão. Após cada tentativa de repetição com falha, `previousRetryCount` será incrementada em um, `elapsedMilliseconds` será atualizado para refletir o tempo gasto se reconectando até agora em milissegundos e o `retryReason` será o erro que causou a falha da última tentativa de reconexão.

`nextRetryDelayInMilliseconds` deve retornar um número que representa o número de milissegundos a aguardar antes da próxima tentativa de reconexão ou `null` se a `HubConnection` deve parar de reconectar.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

Como alternativa, você pode escrever um código que reconectará o cliente manualmente, conforme demonstrado na [reconexão manual](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Reconectar manualmente

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Antes de 3,0, o cliente JavaScript para SignalR não se reconecta automaticamente. Você deve escrever um código que reconectará o cliente manualmente.

::: moniker-end

O código a seguir demonstra uma abordagem de reconexão manual típica:

1. Uma função (nesse caso, a função `start`) é criada para iniciar a conexão.
1. Chame a função `start` no manipulador de eventos `onclose` da conexão.

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

Uma implementação do mundo real usaria um retirada exponencial ou tentaria um número especificado de vezes antes de desistir.

## <a name="additional-resources"></a>Recursos adicionais

* [Referência da API de JavaScript](/javascript/api/?view=signalr-js-latest)
* [Tutorial do JavaScript](xref:tutorials/signalr)
* [Tutorial do webpack e TypeScript](xref:tutorials/signalr-typescript-webpack)
* [Hubs](xref:signalr/hubs)
* [Cliente .NET](xref:signalr/dotnet-client)
* [Publicar no Azure](xref:signalr/publish-to-azure-web-app)
* [Solicitações entre origens (CORS)](xref:security/cors)
* [Documentação sem servidor do serviço SignalR do Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)
