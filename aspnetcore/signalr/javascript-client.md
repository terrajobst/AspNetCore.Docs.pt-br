---
title: ASP.NET Core SignalR JavaScript cliente
author: bradygaster
description: Visão geral do cliente JavaScript de SignalR do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/javascript-client
ms.openlocfilehash: 1565aa38a69113781d7c272a1710298cccc1f045
ms.sourcegitcommit: 3eedd6180fbbdcb81a8e1ebdbeb035bf4f2feb92
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67284513"
---
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="47625-103">ASP.NET Core SignalR JavaScript cliente</span><span class="sxs-lookup"><span data-stu-id="47625-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="47625-104">Por [Rachel Appel](http://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="47625-104">By [Rachel Appel](http://twitter.com/rachelappel)</span></span>

<span data-ttu-id="47625-105">A biblioteca de cliente JavaScript de SignalR do ASP.NET Core permite aos desenvolvedores chamar o código de hub do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="47625-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="47625-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="47625-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="47625-107">Instalar o pacote de cliente do SignalR</span><span class="sxs-lookup"><span data-stu-id="47625-107">Install the SignalR client package</span></span>

<span data-ttu-id="47625-108">A biblioteca de cliente SignalR JavaScript é entregue como um [npm](https://www.npmjs.com/) pacote.</span><span class="sxs-lookup"><span data-stu-id="47625-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="47625-109">Se você estiver usando o Visual Studio, execute `npm install` do **Package Manager Console** enquanto na pasta raiz.</span><span class="sxs-lookup"><span data-stu-id="47625-109">If you're using Visual Studio, run `npm install` from the **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="47625-110">Para Visual Studio Code, execute o comando a partir de **Terminal integrado**.</span><span class="sxs-lookup"><span data-stu-id="47625-110">For Visual Studio Code, run the command from the **Integrated Terminal**.</span></span>

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

<span data-ttu-id="47625-111">NPM instala o conteúdo do pacote na *node_modules\\@aspnet\signalr\dist\browser* pasta.</span><span class="sxs-lookup"><span data-stu-id="47625-111">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="47625-112">Criar uma nova pasta chamada *signalr* sob o *wwwroot\\lib* pasta.</span><span class="sxs-lookup"><span data-stu-id="47625-112">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="47625-113">Cópia de *signalr.js* do arquivo para o *wwwroot\lib\signalr* pasta.</span><span class="sxs-lookup"><span data-stu-id="47625-113">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

## <a name="use-the-signalr-javascript-client"></a><span data-ttu-id="47625-114">Usar o cliente SignalR JavaScript</span><span class="sxs-lookup"><span data-stu-id="47625-114">Use the SignalR JavaScript client</span></span>

<span data-ttu-id="47625-115">Referência de cliente SignalR JavaScript no `<script>` elemento.</span><span class="sxs-lookup"><span data-stu-id="47625-115">Reference the SignalR JavaScript client in the `<script>` element.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="47625-116">Conectar a um hub</span><span class="sxs-lookup"><span data-stu-id="47625-116">Connect to a hub</span></span>

<span data-ttu-id="47625-117">O código a seguir cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="47625-117">The following code creates and starts a connection.</span></span> <span data-ttu-id="47625-118">Nome do hub é diferencia maiusculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="47625-118">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="47625-119">Conexões entre origens</span><span class="sxs-lookup"><span data-stu-id="47625-119">Cross-origin connections</span></span>

<span data-ttu-id="47625-120">Normalmente, os navegadores carga das conexões do mesmo domínio que a página solicitada.</span><span class="sxs-lookup"><span data-stu-id="47625-120">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="47625-121">No entanto, há ocasiões em que é necessária uma conexão para outro domínio.</span><span class="sxs-lookup"><span data-stu-id="47625-121">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="47625-122">Para impedir a leitura de dados confidenciais de outro site, um site mal-intencionado [conexões entre origens](xref:security/cors) estão desabilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="47625-122">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="47625-123">Para permitir que uma solicitação entre origens, habilitá-lo no `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="47625-123">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="47625-124">Chamar métodos de hub do cliente</span><span class="sxs-lookup"><span data-stu-id="47625-124">Call hub methods from client</span></span>

<span data-ttu-id="47625-125">Clientes JavaScript chamem métodos públicos em hubs por meio de [invocar](/javascript/api/%40aspnet/signalr/hubconnection#invoke) método da [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="47625-125">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="47625-126">O `invoke` método aceita dois argumentos:</span><span class="sxs-lookup"><span data-stu-id="47625-126">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="47625-127">O nome do método de hub.</span><span class="sxs-lookup"><span data-stu-id="47625-127">The name of the hub method.</span></span> <span data-ttu-id="47625-128">No exemplo a seguir, o nome do método no hub é `SendMessage`.</span><span class="sxs-lookup"><span data-stu-id="47625-128">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="47625-129">Quaisquer argumentos definidos no método de hub.</span><span class="sxs-lookup"><span data-stu-id="47625-129">Any arguments defined in the hub method.</span></span> <span data-ttu-id="47625-130">No exemplo a seguir, é o nome do argumento `message`.</span><span class="sxs-lookup"><span data-stu-id="47625-130">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="47625-131">O exemplo de código usa a sintaxe da função de seta tem suporte em versões atuais de todos os principais navegadores, exceto o Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="47625-131">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="47625-132">Se você estiver usando o serviço do Azure SignalR no *modo sem servidor*, você não pode chamar métodos de hub de um cliente.</span><span class="sxs-lookup"><span data-stu-id="47625-132">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="47625-133">Para obter mais informações, consulte o [documentação do SignalR Service](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="47625-133">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="47625-134">O `invoke` método retorna um JavaScript [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span><span class="sxs-lookup"><span data-stu-id="47625-134">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="47625-135">O `Promise` é resolvido com o valor de retorno (se houver) quando o método no servidor de retornar.</span><span class="sxs-lookup"><span data-stu-id="47625-135">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="47625-136">Se o método no servidor gerará um erro, o `Promise` será rejeitada com a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="47625-136">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="47625-137">Use o `then` e `catch` métodos na `Promise` em si para lidar com esses casos (ou `await` sintaxe).</span><span class="sxs-lookup"><span data-stu-id="47625-137">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="47625-138">O `send` método retorna um JavaScript `Promise`.</span><span class="sxs-lookup"><span data-stu-id="47625-138">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="47625-139">O `Promise` é resolvido quando a mensagem foi enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="47625-139">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="47625-140">Se não houver um erro ao enviar a mensagem, o `Promise` será rejeitada com a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="47625-140">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="47625-141">Use o `then` e `catch` métodos na `Promise` em si para lidar com esses casos (ou `await` sintaxe).</span><span class="sxs-lookup"><span data-stu-id="47625-141">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="47625-142">Usando `send` não Aguarde até que o servidor recebeu a mensagem.</span><span class="sxs-lookup"><span data-stu-id="47625-142">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="47625-143">Consequentemente, não é possível retornar dados ou erros do servidor.</span><span class="sxs-lookup"><span data-stu-id="47625-143">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="47625-144">Chamar métodos de cliente do hub</span><span class="sxs-lookup"><span data-stu-id="47625-144">Call client methods from hub</span></span>

<span data-ttu-id="47625-145">Para receber mensagens do hub, definir um método usando o [na](/javascript/api/%40aspnet/signalr/hubconnection#on) método o `HubConnection`.</span><span class="sxs-lookup"><span data-stu-id="47625-145">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="47625-146">O nome do método de cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="47625-146">The name of the JavaScript client method.</span></span> <span data-ttu-id="47625-147">No exemplo a seguir, é o nome do método `ReceiveMessage`.</span><span class="sxs-lookup"><span data-stu-id="47625-147">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="47625-148">O hub passa para o método de argumentos.</span><span class="sxs-lookup"><span data-stu-id="47625-148">Arguments the hub passes to the method.</span></span> <span data-ttu-id="47625-149">No exemplo a seguir, o valor do argumento é `message`.</span><span class="sxs-lookup"><span data-stu-id="47625-149">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="47625-150">O código anterior no `connection.on` é executado quando o código do lado do servidor chama-o usando o [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) método.</span><span class="sxs-lookup"><span data-stu-id="47625-150">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="47625-151">O SignalR determina qual método de cliente para chamar, correspondendo o nome do método e argumentos definidos no `SendAsync` e `connection.on`.</span><span class="sxs-lookup"><span data-stu-id="47625-151">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="47625-152">Como uma prática recomendada, chame o [inicie](/javascript/api/%40aspnet/signalr/hubconnection#start) método na `HubConnection` depois `on`.</span><span class="sxs-lookup"><span data-stu-id="47625-152">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="47625-153">Isso garante que os manipuladores são registrados antes de todas as mensagens são recebidas.</span><span class="sxs-lookup"><span data-stu-id="47625-153">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="47625-154">Registro em log e tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="47625-154">Error handling and logging</span></span>

<span data-ttu-id="47625-155">Cadeia de um `catch` método até o final do `start` método para lidar com erros do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="47625-155">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="47625-156">Use `console.error` para erros de saída para o console do navegador.</span><span class="sxs-lookup"><span data-stu-id="47625-156">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="47625-157">Configure o rastreamento de log do lado do cliente, passando um agente de log e o tipo de evento para registrar em log quando a conexão é feita.</span><span class="sxs-lookup"><span data-stu-id="47625-157">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="47625-158">As mensagens são registradas com o nível de log especificado e superior.</span><span class="sxs-lookup"><span data-stu-id="47625-158">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="47625-159">Níveis de log disponíveis são da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="47625-159">Available log levels are as follows:</span></span>

* <span data-ttu-id="47625-160">`signalR.LogLevel.Error` &ndash; Mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="47625-160">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="47625-161">Logs `Error` somente mensagens.</span><span class="sxs-lookup"><span data-stu-id="47625-161">Logs `Error` messages only.</span></span>
* <span data-ttu-id="47625-162">`signalR.LogLevel.Warning` &ndash; Mensagens de aviso sobre erros em potencial.</span><span class="sxs-lookup"><span data-stu-id="47625-162">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="47625-163">Os logs `Warning`, e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="47625-163">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="47625-164">`signalR.LogLevel.Information` &ndash; Mensagens de status sem erros.</span><span class="sxs-lookup"><span data-stu-id="47625-164">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="47625-165">Os logs `Information`, `Warning`, e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="47625-165">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="47625-166">`signalR.LogLevel.Trace` &ndash; Mensagens de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="47625-166">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="47625-167">Registra tudo, incluindo dados transportados entre cliente e o hub.</span><span class="sxs-lookup"><span data-stu-id="47625-167">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="47625-168">Use o [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) método [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar o nível de log.</span><span class="sxs-lookup"><span data-stu-id="47625-168">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="47625-169">As mensagens são registradas para o console do navegador.</span><span class="sxs-lookup"><span data-stu-id="47625-169">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="47625-170">Os clientes de reconexão</span><span class="sxs-lookup"><span data-stu-id="47625-170">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="47625-171">Reconectar-se automaticamente</span><span class="sxs-lookup"><span data-stu-id="47625-171">Automatically reconnect</span></span>

<span data-ttu-id="47625-172">O cliente JavaScript para o SignalR pode ser configurado para reconectar-se automaticamente usando o `withAutomaticReconnect` método no [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="47625-172">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="47625-173">Ele não se reconectar automaticamente por padrão.</span><span class="sxs-lookup"><span data-stu-id="47625-173">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="47625-174">Sem parâmetros, `withAutomaticReconnect()` configura o cliente para aguardar 0, 2, 10 e 30 segundos respectivamente antes de tentar a cada tentativa de reconexão, parando depois de quatro tentativas com falha.</span><span class="sxs-lookup"><span data-stu-id="47625-174">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="47625-175">Antes de iniciar qualquer tentativa de reconexão, o `HubConnection` será a transição para o `HubConnectionState.Reconnecting` de estado e acionar seus `onreconnecting` retornos de chamada em vez de fazer a transição para o `Disconnected` estado e disparar seu `onclose` retornos de chamada, como um `HubConnection`sem reconectar automático configurado.</span><span class="sxs-lookup"><span data-stu-id="47625-175">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="47625-176">Isso fornece uma oportunidade para avisar os usuários que a conexão foi perdida e para desativar elementos da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="47625-176">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="47625-177">Se o cliente se reconecta com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição para o `Connected` de estado e acionar seu `onreconnected` retornos de chamada.</span><span class="sxs-lookup"><span data-stu-id="47625-177">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="47625-178">Isso fornece uma oportunidade para informar os usuários que a conexão ser restabelecida.</span><span class="sxs-lookup"><span data-stu-id="47625-178">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="47625-179">Como a conexão parece inteiramente nova para o servidor, um novo `connectionId` será fornecido para o `onreconnected` retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="47625-179">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="47625-180">O `onreconnected` do retorno de chamada `connectionId` parâmetro será indefinido se o `HubConnection` foi configurado para [ignorar negociação](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="47625-180">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="47625-181">`withAutomaticReconnect()` não configure o `HubConnection` para repetir as falhas de inicialização inicial, portanto, falhas de inicialização precisam ser feito manualmente:</span><span class="sxs-lookup"><span data-stu-id="47625-181">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="47625-182">Se o cliente não reconectar-se com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` será a transição para o `Disconnected` de estado e acionar seus [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) retornos de chamada.</span><span class="sxs-lookup"><span data-stu-id="47625-182">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="47625-183">Isso fornece uma oportunidade para informar os usuários a conexão foi perdido permanentemente e recomendável atualizar a página:</span><span class="sxs-lookup"><span data-stu-id="47625-183">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="47625-184">Para configurar um número personalizado de tentativas de reconexão antes de desconectar ou alterar o tempo de reconexão, `withAutomaticReconnect` aceita uma matriz de números que representa o atraso em milissegundos para esperar antes de começar a cada tentativa de reconexão.</span><span class="sxs-lookup"><span data-stu-id="47625-184">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="47625-185">O exemplo anterior configura o `HubConnection` para iniciar a tentativa de reconexão imediatamente depois que a conexão for perdida.</span><span class="sxs-lookup"><span data-stu-id="47625-185">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="47625-186">Isso também é verdadeiro para a configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="47625-186">This is also true for the default configuration.</span></span>

<span data-ttu-id="47625-187">Se a primeira tentativa de reconexão falhar, a segunda tentativa de reconexão também será iniciado imediatamente em vez de aguardar de 2 segundos como apareceria na configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="47625-187">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="47625-188">Se a segunda tentativa de reconexão falhar, a terceira tentativa de reconexão será iniciado em 10 segundos, que é novamente como a configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="47625-188">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="47625-189">O comportamento personalizado, em seguida, diverge novamente do comportamento padrão, interrompendo depois reconectar a terceira tentativa falha em vez de tentar uma reconexão mais tentativa em outro 30 segundos como apareceria na configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="47625-189">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="47625-190">Se você quiser ainda mais controle sobre o tempo e o número de automático se reconectar, as tentativas `withAutomaticReconnect` aceita um objeto que implementa o `IReconnectPolicy` interface, que tem um único método chamado `nextRetryDelayInMilliseconds`.</span><span class="sxs-lookup"><span data-stu-id="47625-190">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IReconnectPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="47625-191">`nextRetryDelayInMilliseconds` leva dois argumentos: `previousRetryCount` e `elapsedMilliseconds`, que são os dois números.</span><span class="sxs-lookup"><span data-stu-id="47625-191">`nextRetryDelayInMilliseconds` takes two arguments, `previousRetryCount` and `elapsedMilliseconds`, which are both numbers.</span></span> <span data-ttu-id="47625-192">Antes da primeira tentativa de reconexão, ambos `previousRetryCount` e `elapsedMilliseconds` será zero.</span><span class="sxs-lookup"><span data-stu-id="47625-192">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero.</span></span> <span data-ttu-id="47625-193">Após cada tentativa de repetição com falha, `previousRetryCount` será incrementado por um e `elapsedMilliseconds` será atualizada para refletir a quantidade de tempo gasto se reconectar até o momento em milissegundos.</span><span class="sxs-lookup"><span data-stu-id="47625-193">After each failed retry attempt, `previousRetryCount` will be incremented by one and `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds.</span></span>

<span data-ttu-id="47625-194">`nextRetryDelayInMilliseconds` deve retornar um número que representa o número de milissegundos a aguardar antes da próxima tentativa de reconexão ou `null` se o `HubConnection` deve parar de se reconectar.</span><span class="sxs-lookup"><span data-stu-id="47625-194">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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
        }
    })
    .build();
```

<span data-ttu-id="47625-195">Como alternativa, você pode escrever código que será reconectada seu cliente manualmente, conforme demonstrado [reconectar manualmente](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="47625-195">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="47625-196">Reconectar manualmente</span><span class="sxs-lookup"><span data-stu-id="47625-196">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="47625-197">Antes do 3.0, o cliente JavaScript para o SignalR não reconectados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="47625-197">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="47625-198">Você deve escrever código que será reconectada seu cliente manualmente.</span><span class="sxs-lookup"><span data-stu-id="47625-198">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="47625-199">O código a seguir demonstra uma abordagem típica de reconexão manual:</span><span class="sxs-lookup"><span data-stu-id="47625-199">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="47625-200">Uma função (nesse caso, o `start` função) é criado para iniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="47625-200">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="47625-201">Chame o `start` função em que a conexão `onclose` manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="47625-201">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="47625-202">Uma implementação real seria usar uma retirada exponencial ou repetir um número especificado de vezes antes de desistir.</span><span class="sxs-lookup"><span data-stu-id="47625-202">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="47625-203">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="47625-203">Additional resources</span></span>

* [<span data-ttu-id="47625-204">Referência de API JavaScript</span><span class="sxs-lookup"><span data-stu-id="47625-204">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="47625-205">Tutorial do JavaScript</span><span class="sxs-lookup"><span data-stu-id="47625-205">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="47625-206">Tutorial de WebPack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="47625-206">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="47625-207">Hubs</span><span class="sxs-lookup"><span data-stu-id="47625-207">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="47625-208">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="47625-208">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="47625-209">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="47625-209">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="47625-210">Solicitações entre origens (CORS)</span><span class="sxs-lookup"><span data-stu-id="47625-210">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="47625-211">Documentação de sem servidor SignalR Service do Azure</span><span class="sxs-lookup"><span data-stu-id="47625-211">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
