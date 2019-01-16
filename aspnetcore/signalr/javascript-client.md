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
# <a name="aspnet-core-signalr-javascript-client"></a><span data-ttu-id="cdaa3-103">ASP.NET Core SignalR JavaScript cliente</span><span class="sxs-lookup"><span data-stu-id="cdaa3-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="cdaa3-104">Por [Rachel Appel](http://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="cdaa3-104">By [Rachel Appel](http://twitter.com/rachelappel)</span></span>

<span data-ttu-id="cdaa3-105">A biblioteca de cliente JavaScript de SignalR do ASP.NET Core permite aos desenvolvedores chamar o código de hub do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="cdaa3-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cdaa3-106">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-client-package"></a><span data-ttu-id="cdaa3-107">Instalar o pacote de cliente do SignalR</span><span class="sxs-lookup"><span data-stu-id="cdaa3-107">Install the SignalR client package</span></span>

<span data-ttu-id="cdaa3-108">A biblioteca de cliente SignalR JavaScript é entregue como um [npm](https://www.npmjs.com/) pacote.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="cdaa3-109">Se você estiver usando o Visual Studio, execute `npm install` do **Package Manager Console** enquanto na pasta raiz.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-109">If you're using Visual Studio, run `npm install` from the **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="cdaa3-110">Para Visual Studio Code, execute o comando a partir de **Terminal integrado**.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-110">For Visual Studio Code, run the command from the **Integrated Terminal**.</span></span>

  ```console
  npm init -y
  npm install @aspnet/signalr
  ```

<span data-ttu-id="cdaa3-111">NPM instala o conteúdo do pacote na *node_modules\\@aspnet\signalr\dist\browser* pasta.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-111">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="cdaa3-112">Criar uma nova pasta chamada *signalr* sob o *wwwroot\\lib* pasta.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-112">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="cdaa3-113">Cópia de *signalr.js* do arquivo para o *wwwroot\lib\signalr* pasta.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-113">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

## <a name="use-the-signalr-javascript-client"></a><span data-ttu-id="cdaa3-114">Usar o cliente SignalR JavaScript</span><span class="sxs-lookup"><span data-stu-id="cdaa3-114">Use the SignalR JavaScript client</span></span>

<span data-ttu-id="cdaa3-115">Referência de cliente SignalR JavaScript no `<script>` elemento.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-115">Reference the SignalR JavaScript client in the `<script>` element.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="cdaa3-116">Conectar a um hub</span><span class="sxs-lookup"><span data-stu-id="cdaa3-116">Connect to a hub</span></span>

<span data-ttu-id="cdaa3-117">O código a seguir cria e inicia uma conexão.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-117">The following code creates and starts a connection.</span></span> <span data-ttu-id="cdaa3-118">Nome do hub é diferencia maiusculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-118">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

### <a name="cross-origin-connections"></a><span data-ttu-id="cdaa3-119">Conexões entre origens</span><span class="sxs-lookup"><span data-stu-id="cdaa3-119">Cross-origin connections</span></span>

<span data-ttu-id="cdaa3-120">Normalmente, os navegadores carga das conexões do mesmo domínio que a página solicitada.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-120">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="cdaa3-121">No entanto, há ocasiões em que é necessária uma conexão para outro domínio.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-121">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="cdaa3-122">Para impedir a leitura de dados confidenciais de outro site, um site mal-intencionado [conexões entre origens](xref:security/cors) estão desabilitados por padrão.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-122">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="cdaa3-123">Para permitir que uma solicitação entre origens, habilitá-lo no `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-123">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="cdaa3-124">Chamar métodos de hub do cliente</span><span class="sxs-lookup"><span data-stu-id="cdaa3-124">Call hub methods from client</span></span>

<span data-ttu-id="cdaa3-125">Clientes JavaScript chamem métodos públicos em hubs por meio de [invocar](/javascript/api/%40aspnet/signalr/hubconnection#invoke) método da [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span><span class="sxs-lookup"><span data-stu-id="cdaa3-125">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="cdaa3-126">O `invoke` método aceita dois argumentos:</span><span class="sxs-lookup"><span data-stu-id="cdaa3-126">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="cdaa3-127">O nome do método de hub.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-127">The name of the hub method.</span></span> <span data-ttu-id="cdaa3-128">No exemplo a seguir, o nome do método no hub é `SendMessage`.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-128">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="cdaa3-129">Quaisquer argumentos definidos no método de hub.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-129">Any arguments defined in the hub method.</span></span> <span data-ttu-id="cdaa3-130">No exemplo a seguir, é o nome do argumento `message`.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-130">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="cdaa3-131">O exemplo de código usa a sintaxe da função de seta tem suporte em versões atuais de todos os principais navegadores, exceto o Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-131">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="cdaa3-132">Chamar métodos de cliente do hub</span><span class="sxs-lookup"><span data-stu-id="cdaa3-132">Call client methods from hub</span></span>

<span data-ttu-id="cdaa3-133">Para receber mensagens do hub, definir um método usando o [na](/javascript/api/%40aspnet/signalr/hubconnection#on) método o `HubConnection`.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-133">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="cdaa3-134">O nome do método de cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-134">The name of the JavaScript client method.</span></span> <span data-ttu-id="cdaa3-135">No exemplo a seguir, é o nome do método `ReceiveMessage`.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-135">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="cdaa3-136">O hub passa para o método de argumentos.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-136">Arguments the hub passes to the method.</span></span> <span data-ttu-id="cdaa3-137">No exemplo a seguir, o valor do argumento é `message`.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-137">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="cdaa3-138">O código anterior no `connection.on` é executado quando o código do lado do servidor chama-o usando o [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) método.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-138">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="cdaa3-139">O SignalR determina qual método de cliente para chamar, correspondendo o nome do método e argumentos definidos no `SendAsync` e `connection.on`.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-139">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="cdaa3-140">Como uma prática recomendada, chame o [inicie](/javascript/api/%40aspnet/signalr/hubconnection#start) método na `HubConnection` depois `on`.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-140">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="cdaa3-141">Isso garante que os manipuladores são registrados antes de todas as mensagens são recebidas.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-141">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="cdaa3-142">Registro em log e tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="cdaa3-142">Error handling and logging</span></span>

<span data-ttu-id="cdaa3-143">Cadeia de um `catch` método até o final do `start` método para lidar com erros do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-143">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="cdaa3-144">Use `console.error` para erros de saída para o console do navegador.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-144">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=43-45)]

<span data-ttu-id="cdaa3-145">Configure o rastreamento de log do lado do cliente, passando um agente de log e o tipo de evento para registrar em log quando a conexão é feita.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-145">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="cdaa3-146">As mensagens são registradas com o nível de log especificado e superior.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-146">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="cdaa3-147">Níveis de log disponíveis são da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="cdaa3-147">Available log levels are as follows:</span></span>

* <span data-ttu-id="cdaa3-148">`signalR.LogLevel.Error` &ndash; Mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-148">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="cdaa3-149">Logs `Error` somente mensagens.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-149">Logs `Error` messages only.</span></span>
* <span data-ttu-id="cdaa3-150">`signalR.LogLevel.Warning` &ndash; Mensagens de aviso sobre erros em potencial.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-150">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="cdaa3-151">Os logs `Warning`, e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-151">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="cdaa3-152">`signalR.LogLevel.Information` &ndash; Mensagens de status sem erros.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-152">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="cdaa3-153">Os logs `Information`, `Warning`, e `Error` mensagens.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-153">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="cdaa3-154">`signalR.LogLevel.Trace` &ndash; Mensagens de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-154">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="cdaa3-155">Registra tudo, incluindo dados transportados entre cliente e o hub.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-155">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="cdaa3-156">Use o [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) método [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) para configurar o nível de log.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-156">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="cdaa3-157">As mensagens são registradas para o console do navegador.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-157">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="cdaa3-158">Os clientes de reconexão</span><span class="sxs-lookup"><span data-stu-id="cdaa3-158">Reconnect clients</span></span>

<span data-ttu-id="cdaa3-159">O cliente JavaScript para o SignalR não reconectar-se automaticamente.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-159">The JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="cdaa3-160">Você deve escrever código que será reconectada seu cliente manualmente.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-160">You must write code that will reconnect your client manually.</span></span> <span data-ttu-id="cdaa3-161">O código a seguir demonstra uma abordagem típica de reconexão:</span><span class="sxs-lookup"><span data-stu-id="cdaa3-161">The following code demonstrates a typical reconnection approach:</span></span>

1. <span data-ttu-id="cdaa3-162">Uma função (nesse caso, o `start` função) é criado para iniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-162">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="cdaa3-163">Chame o `start` função em que a conexão `onclose` manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-163">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="cdaa3-164">Uma implementação real seria usar uma retirada exponencial ou repetir um número especificado de vezes antes de desistir.</span><span class="sxs-lookup"><span data-stu-id="cdaa3-164">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="cdaa3-165">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cdaa3-165">Additional resources</span></span>

* [<span data-ttu-id="cdaa3-166">Referência de API JavaScript</span><span class="sxs-lookup"><span data-stu-id="cdaa3-166">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="cdaa3-167">Tutorial do JavaScript</span><span class="sxs-lookup"><span data-stu-id="cdaa3-167">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="cdaa3-168">Tutorial de WebPack e TypeScript</span><span class="sxs-lookup"><span data-stu-id="cdaa3-168">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="cdaa3-169">Hubs</span><span class="sxs-lookup"><span data-stu-id="cdaa3-169">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="cdaa3-170">Cliente .NET</span><span class="sxs-lookup"><span data-stu-id="cdaa3-170">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="cdaa3-171">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="cdaa3-171">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="cdaa3-172">Solicitações entre origens (CORS)</span><span class="sxs-lookup"><span data-stu-id="cdaa3-172">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
