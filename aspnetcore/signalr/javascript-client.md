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
ms.openlocfilehash: eaf737642cdbd7ab2b1b5c16538b47a70cddd332
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354693"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="37f24-103">ASP.NET Core SignalR cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f24-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="37f24-104">Por [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="37f24-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="37f24-105">O ASP.NET Core SignalR biblioteca de cliente JavaScript permite que os desenvolvedores chamem o código de Hub do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="37f24-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="37f24-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="37f24-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="37f24-107">Instalar o pacote do cliente do SignalR</span><span class="sxs-lookup"><span data-stu-id="37f24-107">Install the SignalR client package</span></span>

<span data-ttu-id="37f24-108">A biblioteca de cliente SignalR JavaScript é entregue como um pacote [NPM](https://www.npmjs.com/) .</span><span class="sxs-lookup"><span data-stu-id="37f24-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="37f24-109">Se você estiver usando o Visual Studio, execute `npm install` no **console do Gerenciador de pacotes** enquanto estiver na pasta raiz.</span><span class="sxs-lookup"><span data-stu-id="37f24-109">If you're using Visual Studio, run `npm install` from the **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="37f24-110">Para Visual Studio Code, execute o comando do **terminal integrado**.</span><span class="sxs-lookup"><span data-stu-id="37f24-110">For Visual Studio Code, run the command from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

  ```console
  npm init -y
  npm install @microsoft/signalr
  ```

<span data-ttu-id="37f24-111">NPM instala o conteúdo do pacote na *node_modules\\@microsoft\signalr\dist\browser* pasta.</span><span class="sxs-lookup"><span data-stu-id="37f24-111">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="37f24-112">Crie uma nova pasta chamada *signalr* na pasta *wwwroot\\lib* .</span><span class="sxs-lookup"><span data-stu-id="37f24-112">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="37f24-113">Copie o arquivo *signalr. js* para a pasta *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="37f24-113">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

<span data-ttu-id="37f24-114">NPM instala o conteúdo do pacote na *node_modules\\@aspnet\signalr\dist\browser* pasta.</span><span class="sxs-lookup"><span data-stu-id="37f24-114">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="37f24-115">Crie uma nova pasta chamada *signalr* na pasta *wwwroot\\lib* .</span><span class="sxs-lookup"><span data-stu-id="37f24-115">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="37f24-116">Copie o arquivo *signalr. js* para a pasta *wwwroot\lib\signalr* .</span><span class="sxs-lookup"><span data-stu-id="37f24-116">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

## <a name="use-the-opno-locsignalr-javascript-client"></a><span data-ttu-id="37f24-117">Usar o SignalR cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f24-117">Use the SignalR JavaScript client</span></span>

<span data-ttu-id="37f24-118">Referencie o SignalR cliente JavaScript no elemento `<script>`.</span><span class="sxs-lookup"><span data-stu-id="37f24-118">Reference the SignalR JavaScript client in the `<script>` element.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="37f24-119">Conectar-se a um hub</span><span class="sxs-lookup"><span data-stu-id="37f24-119">Connect to a hub</span></span>

<span data-ttu-id="37f24-120">O código a seguir cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="37f24-120">The following code creates and starts a connection.</span></span> <span data-ttu-id="37f24-121">O nome do Hub não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="37f24-121">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="37f24-122">Conexões entre origens</span><span class="sxs-lookup"><span data-stu-id="37f24-122">Cross-origin connections</span></span>

<span data-ttu-id="37f24-123">Normalmente, os navegadores carregam conexões do mesmo domínio que a página solicitada.</span><span class="sxs-lookup"><span data-stu-id="37f24-123">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="37f24-124">No entanto, há ocasiões em que uma conexão com outro domínio é necessária.</span><span class="sxs-lookup"><span data-stu-id="37f24-124">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="37f24-125">Para impedir que um site mal-intencionado leia dados confidenciais de outro site, [as conexões entre origens](xref:security/cors) são desabilitadas por padrão.</span><span class="sxs-lookup"><span data-stu-id="37f24-125">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="37f24-126">Para permitir uma solicitação entre origens, habilite-a na classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="37f24-126">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="37f24-127">Métodos do hub de chamadas do cliente</span><span class="sxs-lookup"><span data-stu-id="37f24-127">Call hub methods from client</span></span>

<span data-ttu-id="37f24-128">Os clientes JavaScript chamam métodos públicos em hubs por meio do método [Invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) de [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="37f24-128">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="37f24-129">O método `invoke` aceita dois argumentos:</span><span class="sxs-lookup"><span data-stu-id="37f24-129">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="37f24-130">O nome do método de Hub.</span><span class="sxs-lookup"><span data-stu-id="37f24-130">The name of the hub method.</span></span> <span data-ttu-id="37f24-131">No exemplo a seguir, o nome do método no Hub é `SendMessage`.</span><span class="sxs-lookup"><span data-stu-id="37f24-131">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="37f24-132">Quaisquer argumentos definidos no método Hub.</span><span class="sxs-lookup"><span data-stu-id="37f24-132">Any arguments defined in the hub method.</span></span> <span data-ttu-id="37f24-133">No exemplo a seguir, o nome do argumento é `message`.</span><span class="sxs-lookup"><span data-stu-id="37f24-133">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="37f24-134">O código de exemplo usa a sintaxe de função de seta que tem suporte nas versões atuais de todos os principais navegadores, exceto o Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="37f24-134">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="37f24-135">Se você estiver usando o serviço de SignalR do Azure no *modo sem servidor*, não será possível chamar os métodos de Hub de um cliente.</span><span class="sxs-lookup"><span data-stu-id="37f24-135">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="37f24-136">Para obter mais informações, consulte a [documentação do serviço deSignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="37f24-136">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="37f24-137">O método `invoke` retorna uma [promessa](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37f24-137">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="37f24-138">O `Promise` é resolvido com o valor de retorno (se houver) quando o método no servidor retorna.</span><span class="sxs-lookup"><span data-stu-id="37f24-138">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="37f24-139">Se o método no servidor gerar um erro, o `Promise` será rejeitado com a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="37f24-139">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="37f24-140">Use os métodos `then` e `catch` na `Promise` em si para lidar com esses casos (ou `await` sintaxe).</span><span class="sxs-lookup"><span data-stu-id="37f24-140">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="37f24-141">O método `send` retorna um `Promise`JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37f24-141">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="37f24-142">O `Promise` é resolvido quando a mensagem é enviada ao servidor.</span><span class="sxs-lookup"><span data-stu-id="37f24-142">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="37f24-143">Se houver um erro ao enviar a mensagem, o `Promise` será rejeitado com a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="37f24-143">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="37f24-144">Use os métodos `then` e `catch` na `Promise` em si para lidar com esses casos (ou `await` sintaxe).</span><span class="sxs-lookup"><span data-stu-id="37f24-144">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="37f24-145">O uso de `send` não aguarda até que o servidor tenha recebido a mensagem.</span><span class="sxs-lookup"><span data-stu-id="37f24-145">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="37f24-146">Consequentemente, não é possível retornar dados ou erros do servidor.</span><span class="sxs-lookup"><span data-stu-id="37f24-146">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="37f24-147">Chamar métodos de cliente do Hub</span><span class="sxs-lookup"><span data-stu-id="37f24-147">Call client methods from hub</span></span>

<span data-ttu-id="37f24-148">Para receber mensagens do Hub, defina um método usando o método [on](/javascript/api/%40aspnet/signalr/hubconnection#on) do `HubConnection`.</span><span class="sxs-lookup"><span data-stu-id="37f24-148">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="37f24-149">O nome do método de cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37f24-149">The name of the JavaScript client method.</span></span> <span data-ttu-id="37f24-150">No exemplo a seguir, o nome do método é `ReceiveMessage`.</span><span class="sxs-lookup"><span data-stu-id="37f24-150">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="37f24-151">Argumentos que o Hub passa para o método.</span><span class="sxs-lookup"><span data-stu-id="37f24-151">Arguments the hub passes to the method.</span></span> <span data-ttu-id="37f24-152">No exemplo a seguir, o valor do argumento é `message`.</span><span class="sxs-lookup"><span data-stu-id="37f24-152">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="37f24-153">O código anterior em `connection.on` é executado quando o código do lado do servidor o chama usando o método [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) .</span><span class="sxs-lookup"><span data-stu-id="37f24-153">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="37f24-154"> determina qual método de cliente deve ser chamado, correspondendo ao nome do método e aos argumentos definidos em `SendAsync` e `connection.on`.</span><span class="sxs-lookup"><span data-stu-id="37f24-154"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="37f24-155">Como prática recomendada, chame o método [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) no `HubConnection` depois de `on`.</span><span class="sxs-lookup"><span data-stu-id="37f24-155">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="37f24-156">Isso garante que seus manipuladores sejam registrados antes que todas as mensagens sejam recebidas.</span><span class="sxs-lookup"><span data-stu-id="37f24-156">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="37f24-157">Registro em log e tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="37f24-157">Error handling and logging</span></span>

<span data-ttu-id="37f24-158">Encadeamento um método de `catch` ao final do método `start` para manipular erros do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="37f24-158">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="37f24-159">Use `console.error` para gerar erros no console do navegador.</span><span class="sxs-lookup"><span data-stu-id="37f24-159">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="37f24-160">Configure o rastreamento de log no lado do cliente passando um agente e um tipo de evento para registrar quando a conexão é feita.</span><span class="sxs-lookup"><span data-stu-id="37f24-160">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="37f24-161">As mensagens são registradas com o nível de log especificado e superior.</span><span class="sxs-lookup"><span data-stu-id="37f24-161">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="37f24-162">Os níveis de log disponíveis são os seguintes:</span><span class="sxs-lookup"><span data-stu-id="37f24-162">Available log levels are as follows:</span></span>

* <span data-ttu-id="37f24-163">`signalR.LogLevel.Error` &ndash; mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="37f24-163">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="37f24-164">Registra somente `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="37f24-164">Logs `Error` messages only.</span></span>
* <span data-ttu-id="37f24-165">`signalR.LogLevel.Warning` &ndash; mensagens de aviso sobre possíveis erros.</span><span class="sxs-lookup"><span data-stu-id="37f24-165">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="37f24-166">Registra `Warning`e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="37f24-166">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="37f24-167">`signalR.LogLevel.Information` &ndash; mensagens de status sem erros.</span><span class="sxs-lookup"><span data-stu-id="37f24-167">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="37f24-168">Registra `Information`, `Warning`e mensagens de `Error`.</span><span class="sxs-lookup"><span data-stu-id="37f24-168">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="37f24-169">`signalR.LogLevel.Trace` &ndash; mensagens de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="37f24-169">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="37f24-170">Registra tudo, incluindo dados transportados entre o Hub e o cliente.</span><span class="sxs-lookup"><span data-stu-id="37f24-170">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="37f24-171">Use o método [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) no [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar o nível de log.</span><span class="sxs-lookup"><span data-stu-id="37f24-171">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="37f24-172">As mensagens são registradas no console do navegador.</span><span class="sxs-lookup"><span data-stu-id="37f24-172">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="37f24-173">Reconectar clientes</span><span class="sxs-lookup"><span data-stu-id="37f24-173">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="37f24-174">Reconectar automaticamente</span><span class="sxs-lookup"><span data-stu-id="37f24-174">Automatically reconnect</span></span>

<span data-ttu-id="37f24-175">O cliente JavaScript para SignalR pode ser configurado para reconectar-se automaticamente usando o método `withAutomaticReconnect` em [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="37f24-175">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="37f24-176">Ele não se reconectará automaticamente por padrão.</span><span class="sxs-lookup"><span data-stu-id="37f24-176">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="37f24-177">Sem parâmetros, `withAutomaticReconnect()` configura o cliente para aguardar 0, 2, 10 e 30 segundos, respectivamente, antes de tentar cada tentativa de reconexão, parando após quatro tentativas com falha.</span><span class="sxs-lookup"><span data-stu-id="37f24-177">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="37f24-178">Antes de iniciar qualquer tentativa de reconexão, o `HubConnection` fará a transição para o estado de `HubConnectionState.Reconnecting` e acionará seus retornos de chamada `onreconnecting` em vez de fazer a transição para o estado de `Disconnected` e disparar seus retornos de chamada de `onclose` como um `HubConnection` sem a reconexão automática configurada.</span><span class="sxs-lookup"><span data-stu-id="37f24-178">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="37f24-179">Isso fornece uma oportunidade para avisar os usuários de que a conexão foi perdida e para desabilitar os elementos da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="37f24-179">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="37f24-180">Se o cliente se reconectar com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição de volta para o estado de `Connected` e acionará seus retornos de chamada `onreconnected`.</span><span class="sxs-lookup"><span data-stu-id="37f24-180">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="37f24-181">Isso fornece uma oportunidade de informar aos usuários que a conexão foi restabelecida.</span><span class="sxs-lookup"><span data-stu-id="37f24-181">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="37f24-182">Como a conexão é totalmente nova no servidor, um novo `connectionId` será fornecido ao retorno de chamada `onreconnected`.</span><span class="sxs-lookup"><span data-stu-id="37f24-182">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="37f24-183">O parâmetro `connectionId` do retorno de chamada `onreconnected` será indefinido se o `HubConnection` tiver sido configurado para [ignorar a negociação](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="37f24-183">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="37f24-184">`withAutomaticReconnect()` não configurará o `HubConnection` para repetir as falhas de início inicial, portanto, as falhas de início precisam ser manipuladas manualmente:</span><span class="sxs-lookup"><span data-stu-id="37f24-184">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="37f24-185">Se o cliente não se reconectar com êxito dentro de suas primeiras quatro tentativas, o `HubConnection` fará a transição para o estado de `Disconnected` e disparará seus retornos de chamada [fechamento](/javascript/api/%40aspnet/signalr/hubconnection#onclose) .</span><span class="sxs-lookup"><span data-stu-id="37f24-185">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="37f24-186">Isso fornece uma oportunidade de informar aos usuários que a conexão foi permanentemente perdida e recomenda atualizar a página:</span><span class="sxs-lookup"><span data-stu-id="37f24-186">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="37f24-187">Para configurar um número personalizado de tentativas de reconexão antes de desconectar ou alterar o tempo de reconexão, `withAutomaticReconnect` aceita uma matriz de números que representa o atraso em milissegundos para aguardar antes de iniciar cada tentativa de reconexão.</span><span class="sxs-lookup"><span data-stu-id="37f24-187">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="37f24-188">O exemplo anterior configura a `HubConnection` para iniciar a tentativa de reconexão imediatamente após a perda da conexão.</span><span class="sxs-lookup"><span data-stu-id="37f24-188">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="37f24-189">Isso também é verdadeiro para a configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="37f24-189">This is also true for the default configuration.</span></span>

<span data-ttu-id="37f24-190">Se a primeira tentativa de reconexão falhar, a segunda tentativa de reconexão também será iniciada imediatamente, em vez de esperar 2 segundos, como seria na configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="37f24-190">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="37f24-191">Se a segunda tentativa de reconexão falhar, a terceira tentativa de reconexão será iniciada em 10 segundos, o que é novamente como a configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="37f24-191">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="37f24-192">O comportamento personalizado, em seguida, deriva novamente do comportamento padrão ao parar após a terceira tentativa de reconexão, em vez de tentar mais uma tentativa de reconexão em outros 30 segundos, como seria na configuração padrão.</span><span class="sxs-lookup"><span data-stu-id="37f24-192">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="37f24-193">Se você quiser ter ainda mais controle sobre o tempo e o número de tentativas de reconexão automática, `withAutomaticReconnect` aceitará um objeto que implementa a interface `IRetryPolicy`, que tem um único método chamado `nextRetryDelayInMilliseconds`.</span><span class="sxs-lookup"><span data-stu-id="37f24-193">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="37f24-194">`nextRetryDelayInMilliseconds` usa um único argumento com o tipo `RetryContext`.</span><span class="sxs-lookup"><span data-stu-id="37f24-194">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="37f24-195">O `RetryContext` tem três propriedades: `previousRetryCount`, `elapsedMilliseconds` e `retryReason` que são um `number`, um `number` e um `Error` respectivamente.</span><span class="sxs-lookup"><span data-stu-id="37f24-195">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="37f24-196">Antes da primeira tentativa de reconexão, `previousRetryCount` e `elapsedMilliseconds` serão zero e o `retryReason` será o erro que causou a perda da conexão.</span><span class="sxs-lookup"><span data-stu-id="37f24-196">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="37f24-197">Após cada tentativa de repetição com falha, `previousRetryCount` será incrementada em um, `elapsedMilliseconds` será atualizado para refletir o tempo gasto se reconectando até agora em milissegundos e o `retryReason` será o erro que causou a falha da última tentativa de reconexão.</span><span class="sxs-lookup"><span data-stu-id="37f24-197">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="37f24-198">`nextRetryDelayInMilliseconds` deve retornar um número que representa o número de milissegundos a aguardar antes da próxima tentativa de reconexão ou `null` se a `HubConnection` deve parar de reconectar.</span><span class="sxs-lookup"><span data-stu-id="37f24-198">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="37f24-199">Como alternativa, você pode escrever um código que reconectará o cliente manualmente, conforme demonstrado na [reconexão manual](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="37f24-199">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="37f24-200">Reconectar manualmente</span><span class="sxs-lookup"><span data-stu-id="37f24-200">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="37f24-201">Antes de 3,0, o cliente JavaScript para SignalR não se reconecta automaticamente.</span><span class="sxs-lookup"><span data-stu-id="37f24-201">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="37f24-202">Você deve escrever um código que reconectará o cliente manualmente.</span><span class="sxs-lookup"><span data-stu-id="37f24-202">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="37f24-203">O código a seguir demonstra uma abordagem de reconexão manual típica:</span><span class="sxs-lookup"><span data-stu-id="37f24-203">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="37f24-204">Uma função (nesse caso, a função `start`) é criada para iniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="37f24-204">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="37f24-205">Chame a função `start` no manipulador de eventos `onclose` da conexão.</span><span class="sxs-lookup"><span data-stu-id="37f24-205">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="37f24-206">Uma implementação do mundo real usaria um retirada exponencial ou tentaria um número especificado de vezes antes de desistir.</span><span class="sxs-lookup"><span data-stu-id="37f24-206">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37f24-207">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="37f24-207">Additional resources</span></span>

* [<span data-ttu-id="37f24-208">Referência de API JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f24-208">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="37f24-209">Tutorial do JavaScript</span><span class="sxs-lookup"><span data-stu-id="37f24-209">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="37f24-210">Tutorial do webpack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="37f24-210">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="37f24-211">Hubs</span><span class="sxs-lookup"><span data-stu-id="37f24-211">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="37f24-212">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="37f24-212">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="37f24-213">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="37f24-213">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="37f24-214">Solicitações entre origens (CORS)</span><span class="sxs-lookup"><span data-stu-id="37f24-214">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="37f24-215">[Documentação sem servidor do serviço SignalR do Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="37f24-215">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
