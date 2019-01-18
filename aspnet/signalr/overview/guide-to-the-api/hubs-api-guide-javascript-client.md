---
uid: signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client
title: ASP.NET SignalR guia da API Hubs – cliente JavaScript | Microsoft Docs
author: pfletcher
description: Este documento fornece uma introdução ao uso da API de Hubs de SignalR versão 2 nos clientes JavaScript, como navegadores e applicat da Windows Store (WinJS)...
ms.author: riande
ms.date: 01/15/2019
ms.assetid: a9fd4dc0-1b96-4443-82ca-932a5b4a8ea4
msc.legacyurl: /signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client
msc.type: authoredcontent
ms.openlocfilehash: 12d675b6a2f2f6acdd8c3a5d0d27b5ad2fb1efc4
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396305"
---
<a name="aspnet-signalr-hubs-api-guide---javascript-client"></a><span data-ttu-id="0d186-103">ASP.NET SignalR guia da API Hubs – cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="0d186-103">ASP.NET SignalR Hubs API Guide - JavaScript Client</span></span>
====================

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="0d186-104">Este documento fornece uma introdução ao uso da API de Hubs de SignalR versão 2 nos clientes JavaScript, como navegadores e aplicativos da Windows Store (WinJS).</span><span class="sxs-lookup"><span data-stu-id="0d186-104">This document provides an introduction to using the Hubs API for SignalR version 2 in JavaScript clients, such as browsers and Windows Store (WinJS) applications.</span></span>
>
> <span data-ttu-id="0d186-105">A API de Hubs de SignalR permite que você faça chamadas de procedimento remoto (RPCs) de um servidor para clientes conectados e de clientes para o servidor.</span><span class="sxs-lookup"><span data-stu-id="0d186-105">The SignalR Hubs API enables you to make remote procedure calls (RPCs) from a server to connected clients and from clients to the server.</span></span> <span data-ttu-id="0d186-106">No código do servidor, você define métodos que podem ser chamados por clientes e você chamar métodos que são executados no cliente.</span><span class="sxs-lookup"><span data-stu-id="0d186-106">In server code, you define methods that can be called by clients, and you call methods that run on the client.</span></span> <span data-ttu-id="0d186-107">No código do cliente, você define métodos que podem ser chamados a partir do servidor e você chamar métodos que são executados no servidor.</span><span class="sxs-lookup"><span data-stu-id="0d186-107">In client code, you define methods that can be called from the server, and you call methods that run on the server.</span></span> <span data-ttu-id="0d186-108">O SignalR é responsável por todos os detalhes de cliente-servidor para você.</span><span class="sxs-lookup"><span data-stu-id="0d186-108">SignalR takes care of all of the client-to-server plumbing for you.</span></span>
>
> <span data-ttu-id="0d186-109">O SignalR também oferece uma API de nível inferior chamada conexões persistentes.</span><span class="sxs-lookup"><span data-stu-id="0d186-109">SignalR also offers a lower-level API called Persistent Connections.</span></span> <span data-ttu-id="0d186-110">Para obter uma introdução ao SignalR, Hubs e conexões persistentes, consulte [Introdução ao SignalR](../getting-started/introduction-to-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="0d186-110">For an introduction to SignalR, Hubs, and Persistent Connections, see [Introduction to SignalR](../getting-started/introduction-to-signalr.md).</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="0d186-111">Versões de software usadas neste tópico</span><span class="sxs-lookup"><span data-stu-id="0d186-111">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="0d186-112">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="0d186-112">Visual Studio 2017</span></span>](https://visualstudio.microsoft.com/downloads/)
> - <span data-ttu-id="0d186-113">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="0d186-113">.NET 4.5</span></span>
> - <span data-ttu-id="0d186-114">Versão 2 do SignalR</span><span class="sxs-lookup"><span data-stu-id="0d186-114">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="0d186-115">Versões anteriores deste tópico</span><span class="sxs-lookup"><span data-stu-id="0d186-115">Previous versions of this topic</span></span>
>
> <span data-ttu-id="0d186-116">Para obter informações sobre versões anteriores do SignalR, consulte [versões mais antigas do SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="0d186-116">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="0d186-117">Perguntas e comentários</span><span class="sxs-lookup"><span data-stu-id="0d186-117">Questions and comments</span></span>
>
> <span data-ttu-id="0d186-118">Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="0d186-118">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="0d186-119">Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="0d186-119">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>

## <a name="overview"></a><span data-ttu-id="0d186-120">Visão geral</span><span class="sxs-lookup"><span data-stu-id="0d186-120">Overview</span></span>

<span data-ttu-id="0d186-121">Este documento contém as seguintes seções:</span><span class="sxs-lookup"><span data-stu-id="0d186-121">This document contains the following sections:</span></span>

- [<span data-ttu-id="0d186-122">O proxy gerado e o que ele faz para você</span><span class="sxs-lookup"><span data-stu-id="0d186-122">The generated proxy and what it does for you</span></span>](#genproxy)

    - [<span data-ttu-id="0d186-123">Quando usar o proxy gerado</span><span class="sxs-lookup"><span data-stu-id="0d186-123">When to use the generated proxy</span></span>](#cantusegenproxy)
- [<span data-ttu-id="0d186-124">Configuração do cliente</span><span class="sxs-lookup"><span data-stu-id="0d186-124">Client Setup</span></span>](#clientsetup)

    - [<span data-ttu-id="0d186-125">Como referenciar o proxy gerado dinamicamente</span><span class="sxs-lookup"><span data-stu-id="0d186-125">How to reference the dynamically generated proxy</span></span>](#dynamicproxy)
    - [<span data-ttu-id="0d186-126">Como criar um arquivo físico para o SignalR gerado proxy</span><span class="sxs-lookup"><span data-stu-id="0d186-126">How to create a physical file for the SignalR generated proxy</span></span>](#manualproxy)
- [<span data-ttu-id="0d186-127">Como estabelecer uma conexão</span><span class="sxs-lookup"><span data-stu-id="0d186-127">How to establish a connection</span></span>](#establishconnection)

    - [<span data-ttu-id="0d186-128">$. connection.hub é o mesmo objeto que $.hubConnection() cria</span><span class="sxs-lookup"><span data-stu-id="0d186-128">$.connection.hub is the same object that $.hubConnection() creates</span></span>](#connequivalence)
    - [<span data-ttu-id="0d186-129">Execução assíncrona do método start</span><span class="sxs-lookup"><span data-stu-id="0d186-129">Asynchronous execution of the start method</span></span>](#asyncstart)
- [<span data-ttu-id="0d186-130">Como estabelecer uma conexão entre domínios</span><span class="sxs-lookup"><span data-stu-id="0d186-130">How to establish a cross-domain connection</span></span>](#crossdomain)
- [<span data-ttu-id="0d186-131">Como configurar a conexão</span><span class="sxs-lookup"><span data-stu-id="0d186-131">How to configure the connection</span></span>](#configureconnection)

    - [<span data-ttu-id="0d186-132">Como especificar parâmetros de cadeia de caracteres de consulta</span><span class="sxs-lookup"><span data-stu-id="0d186-132">How to specify query string parameters</span></span>](#querystring)
    - [<span data-ttu-id="0d186-133">Como especificar o método de transporte</span><span class="sxs-lookup"><span data-stu-id="0d186-133">How to specify the transport method</span></span>](#transport)
- [<span data-ttu-id="0d186-134">Como obter um proxy para uma classe de Hub</span><span class="sxs-lookup"><span data-stu-id="0d186-134">How to get a proxy for a Hub class</span></span>](#getproxy)
- [<span data-ttu-id="0d186-135">Como definir métodos no cliente que o servidor pode chamar</span><span class="sxs-lookup"><span data-stu-id="0d186-135">How to define methods on the client that the server can call</span></span>](#callclient)
- [<span data-ttu-id="0d186-136">Como chamar métodos de servidor do cliente</span><span class="sxs-lookup"><span data-stu-id="0d186-136">How to call server methods from the client</span></span>](#callserver)
- [<span data-ttu-id="0d186-137">Como manipular eventos de tempo de vida da conexão</span><span class="sxs-lookup"><span data-stu-id="0d186-137">How to handle connection lifetime events</span></span>](#connectionlifetime)
- [<span data-ttu-id="0d186-138">Como tratar erros</span><span class="sxs-lookup"><span data-stu-id="0d186-138">How to handle errors</span></span>](#handleerrors)
- [<span data-ttu-id="0d186-139">Como habilitar o registro em log do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="0d186-139">How to enable client-side logging</span></span>](#logging)

<span data-ttu-id="0d186-140">Para obter documentação sobre como programar o servidor ou clientes .NET, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="0d186-140">For documentation on how to program the server or .NET clients, see the following resources:</span></span>

- [<span data-ttu-id="0d186-141">Guia de API de Hubs de SignalR – servidor</span><span class="sxs-lookup"><span data-stu-id="0d186-141">SignalR Hubs API Guide - Server</span></span>](hubs-api-guide-server.md)
- [<span data-ttu-id="0d186-142">O SignalR guia da API Hubs – cliente .NET</span><span class="sxs-lookup"><span data-stu-id="0d186-142">SignalR Hubs API Guide - .NET Client</span></span>](hubs-api-guide-net-client.md)

<span data-ttu-id="0d186-143">O componente do servidor SignalR 2 só está disponível no .NET 4.5 (que não haja um cliente .NET para 2 de SignalR no .NET 4.0).</span><span class="sxs-lookup"><span data-stu-id="0d186-143">The SignalR 2 server component is only available on .NET 4.5 (though there is a .NET client for SignalR 2 on .NET 4.0).</span></span>

<a id="genproxy"></a>

## <a name="the-generated-proxy-and-what-it-does-for-you"></a><span data-ttu-id="0d186-144">O proxy gerado e o que ele faz para você</span><span class="sxs-lookup"><span data-stu-id="0d186-144">The generated proxy and what it does for you</span></span>

<span data-ttu-id="0d186-145">Você pode programar um cliente JavaScript para se comunicar com um serviço SignalR com ou sem um proxy que o SignalR gera para você.</span><span class="sxs-lookup"><span data-stu-id="0d186-145">You can program a JavaScript client to communicate with a SignalR service with or without a proxy that SignalR generates for you.</span></span> <span data-ttu-id="0d186-146">O que o proxy faz para você é simplificar a sintaxe do código que você usa para se conectar, os métodos de gravação que o servidor chama, e chama métodos no servidor.</span><span class="sxs-lookup"><span data-stu-id="0d186-146">What the proxy does for you is simplify the syntax of the code you use to connect, write methods that the server calls, and call methods on the server.</span></span>

<span data-ttu-id="0d186-147">Quando você escreve o código para chamar métodos de servidor, o proxy gerado permite que você use sintaxe que se parece como se você estava em execução em uma função local: você pode escrever `serverMethod(arg1, arg2)` em vez de `invoke('serverMethod', arg1, arg2)`.</span><span class="sxs-lookup"><span data-stu-id="0d186-147">When you write code to call server methods, the generated proxy enables you to use syntax that looks as though you were executing a local function: you can write `serverMethod(arg1, arg2)` instead of `invoke('serverMethod', arg1, arg2)`.</span></span> <span data-ttu-id="0d186-148">A sintaxe do proxy gerado também permite que um erro do lado do cliente inteligível e imediato se você digitar incorretamente um nome de método do servidor.</span><span class="sxs-lookup"><span data-stu-id="0d186-148">The generated proxy syntax also enables an immediate and intelligible client-side error if you mistype a server method name.</span></span> <span data-ttu-id="0d186-149">E se você criar manualmente o arquivo que define os proxies, você também pode obter suporte ao IntelliSense para escrever código que chama os métodos de servidor.</span><span class="sxs-lookup"><span data-stu-id="0d186-149">And if you manually create the file that defines the proxies, you can also get IntelliSense support for writing code that calls server methods.</span></span>

<span data-ttu-id="0d186-150">Por exemplo, suponha que você tem a seguinte classe Hub no servidor:</span><span class="sxs-lookup"><span data-stu-id="0d186-150">For example, suppose you have the following Hub class on the server:</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample1.cs?highlight=1,3,5)]

<span data-ttu-id="0d186-151">Os exemplos de código a seguir mostram o que é de código do JavaScript semelhante para invocar o `NewContosoChatMessage` método no servidor e receber invocações da `addContosoChatMessageToPage` método do servidor.</span><span class="sxs-lookup"><span data-stu-id="0d186-151">The following code examples show what JavaScript code looks like for invoking the `NewContosoChatMessage` method on the server and receiving invocations of the `addContosoChatMessageToPage` method from the server.</span></span>

<span data-ttu-id="0d186-152">**Com o proxy gerado**</span><span class="sxs-lookup"><span data-stu-id="0d186-152">**With the generated proxy**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample2.js?highlight=1-2,8)]

<span data-ttu-id="0d186-153">**Sem o proxy gerado**</span><span class="sxs-lookup"><span data-stu-id="0d186-153">**Without the generated proxy**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample3.js?highlight=2-3,9)]

<a id="cantusegenproxy"></a>

### <a name="when-to-use-the-generated-proxy"></a><span data-ttu-id="0d186-154">Quando usar o proxy gerado</span><span class="sxs-lookup"><span data-stu-id="0d186-154">When to use the generated proxy</span></span>

<span data-ttu-id="0d186-155">Se você quiser registrar vários manipuladores de eventos para um método de cliente que chama o servidor, você não pode usar o proxy gerado.</span><span class="sxs-lookup"><span data-stu-id="0d186-155">If you want to register multiple event handlers for a client method that the server calls, you can't use the generated proxy.</span></span> <span data-ttu-id="0d186-156">Caso contrário, você pode optar por usar o proxy gerado ou não com base em sua preferência de codificação.</span><span class="sxs-lookup"><span data-stu-id="0d186-156">Otherwise, you can choose to use the generated proxy or not based on your coding preference.</span></span> <span data-ttu-id="0d186-157">Se você optar por não usá-lo, você não precisa fazer referência à URL "/ hubs do signalr" em um `script` elemento no código do cliente.</span><span class="sxs-lookup"><span data-stu-id="0d186-157">If you choose not to use it, you don't have to reference the "signalr/hubs" URL in a `script` element in your client code.</span></span>

<a id="clientsetup"></a>

## <a name="client-setup"></a><span data-ttu-id="0d186-158">Configuração do cliente</span><span class="sxs-lookup"><span data-stu-id="0d186-158">Client setup</span></span>

<span data-ttu-id="0d186-159">Referências a jQuery e o arquivo de JavaScript do SignalR core exige que um cliente JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0d186-159">A JavaScript client requires references to jQuery and the SignalR core JavaScript file.</span></span> <span data-ttu-id="0d186-160">A versão do jQuery deve ser 1.6.4 ou versões posteriores principais, como 1.7.2, 1.8.2 ou 1.9.1.</span><span class="sxs-lookup"><span data-stu-id="0d186-160">The jQuery version must be 1.6.4 or major later versions, such as 1.7.2, 1.8.2, or 1.9.1.</span></span> <span data-ttu-id="0d186-161">Se você decidir usar o proxy gerado, você também precisa de uma referência para o proxy SignalR gerado arquivo JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0d186-161">If you decide to use the generated proxy, you also need a reference to the SignalR generated proxy JavaScript file.</span></span> <span data-ttu-id="0d186-162">O exemplo a seguir mostra as referências de aparência em uma página HTML que usa o proxy gerado.</span><span class="sxs-lookup"><span data-stu-id="0d186-162">The following example shows what the references might look like in an HTML page that uses the generated proxy.</span></span>

[!code-html[Main](hubs-api-guide-javascript-client/samples/sample4.html)]

<span data-ttu-id="0d186-163">Essas referências devem ser incluídas nesta ordem: jQuery, SignalR core depois disso e SignalR proxies sobrenome.</span><span class="sxs-lookup"><span data-stu-id="0d186-163">These references must be included in this order: jQuery first, SignalR core after that, and SignalR proxies last.</span></span>

<a id="dynamicproxy"></a>

### <a name="how-to-reference-the-dynamically-generated-proxy"></a><span data-ttu-id="0d186-164">Como referenciar o proxy gerado dinamicamente</span><span class="sxs-lookup"><span data-stu-id="0d186-164">How to reference the dynamically generated proxy</span></span>

<span data-ttu-id="0d186-165">No exemplo anterior, a referência para o proxy SignalR gerado é código JavaScript gerado dinamicamente, não para um arquivo físico.</span><span class="sxs-lookup"><span data-stu-id="0d186-165">In the preceding example, the reference to the SignalR generated proxy is to dynamically generated JavaScript code, not to a physical file.</span></span> <span data-ttu-id="0d186-166">O SignalR cria o código JavaScript para o proxy em tempo real e atende a cliente em resposta à URL "hubs de signalr /".</span><span class="sxs-lookup"><span data-stu-id="0d186-166">SignalR creates the JavaScript code for the proxy on the fly and serves it to the client in response to the "/signalr/hubs" URL.</span></span> <span data-ttu-id="0d186-167">Se você especificou uma URL base diferente para conexões do SignalR no servidor em seu `MapSignalR` método, a URL do arquivo proxy gerado dinamicamente é sua URL personalizada com "/ hubs" acrescentado a ele.</span><span class="sxs-lookup"><span data-stu-id="0d186-167">If you specified a different base URL for SignalR connections on the server in your `MapSignalR` method, the URL for the dynamically generated proxy file is your custom URL with "/hubs" appended to it.</span></span>

> [!NOTE]
> <span data-ttu-id="0d186-168">Para clientes de JavaScript do Windows 8 (Windows Store), use o arquivo físico do proxy em vez daquele gerado dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="0d186-168">For Windows 8 (Windows Store) JavaScript clients, use the physical proxy file instead of the dynamically generated one.</span></span> <span data-ttu-id="0d186-169">Para obter mais informações, consulte [como criar um arquivo físico para o SignalR gerado proxy](#manualproxy) mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="0d186-169">For more information, see [How to create a physical file for the SignalR generated proxy](#manualproxy) later in this topic.</span></span>


<span data-ttu-id="0d186-170">Em um ASP.NET MVC 4 ou 5 exibição do Razor, use o til para referir-se a raiz do aplicativo na sua referência de arquivo de proxy:</span><span class="sxs-lookup"><span data-stu-id="0d186-170">In an ASP.NET MVC 4 or 5 Razor view, use the tilde to refer to the application root in your proxy file reference:</span></span>

[!code-html[Main](hubs-api-guide-javascript-client/samples/sample5.html)]

<span data-ttu-id="0d186-171">Para obter mais informações sobre como usar o SignalR no MVC 5, consulte [Introdução ao SignalR e ao MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).</span><span class="sxs-lookup"><span data-stu-id="0d186-171">For more information about using SignalR in MVC 5, see [Getting Started with SignalR and MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).</span></span>

<span data-ttu-id="0d186-172">Em um modo de exibição do Razor do ASP.NET MVC 3, use `Url.Content` para sua referência de arquivo de proxy:</span><span class="sxs-lookup"><span data-stu-id="0d186-172">In an ASP.NET MVC 3 Razor view, use `Url.Content` for your proxy file reference:</span></span>

[!code-cshtml[Main](hubs-api-guide-javascript-client/samples/sample6.cshtml)]

<span data-ttu-id="0d186-173">Em um aplicativo Web Forms do ASP.NET, use `ResolveClientUrl` para os proxies de referência do arquivo ou registrá-lo por meio do ScriptManager usando um caminho relativo do aplicativo raiz (começando com um til):</span><span class="sxs-lookup"><span data-stu-id="0d186-173">In an ASP.NET Web Forms application, use `ResolveClientUrl` for your proxies file reference or register it via the ScriptManager using an app root relative path (starting with a tilde):</span></span>

[!code-aspx[Main](hubs-api-guide-javascript-client/samples/sample7.aspx)]

<span data-ttu-id="0d186-174">Como regra geral, use o mesmo método para especificar a URL "hubs de signalr /" que você usa para arquivos CSS ou JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0d186-174">As a general rule, use the same method for specifying the "/signalr/hubs" URL that you use for CSS or JavaScript files.</span></span> <span data-ttu-id="0d186-175">Se você especificar uma URL sem usar um til, em alguns cenários de seu aplicativo funcionará corretamente quando você testar no Visual Studio usando o IIS Express, mas falhará com um erro 404 quando você implanta para o IIS completo.</span><span class="sxs-lookup"><span data-stu-id="0d186-175">If you specify a URL without using a tilde, in some scenarios your application will work correctly when you test in Visual Studio using IIS Express but will fail with a 404 error when you deploy to full IIS.</span></span> <span data-ttu-id="0d186-176">Para obter mais informações, consulte **Resolvendo referências aos recursos no nível de raiz** na [servidores Web no Visual Studio para projetos Web ASP.NET](https://msdn.microsoft.com/library/58wxa9w5.aspx) no site do MSDN.</span><span class="sxs-lookup"><span data-stu-id="0d186-176">For more information, see **Resolving References to Root-Level Resources** in [Web Servers in Visual Studio for ASP.NET Web Projects](https://msdn.microsoft.com/library/58wxa9w5.aspx) on the MSDN site.</span></span>

<span data-ttu-id="0d186-177">Quando você executa um projeto web no Visual Studio 2017 no modo de depuração e se você usar o Internet Explorer como navegador, você pode ver o arquivo de proxy na **Gerenciador de soluções** sob **Scripts**.</span><span class="sxs-lookup"><span data-stu-id="0d186-177">When you run a web project in Visual Studio 2017 in debug mode, and if you use Internet Explorer as your browser, you can see the proxy file in **Solution Explorer** under **Scripts**.</span></span>

<span data-ttu-id="0d186-178">Para ver o conteúdo do arquivo, clique duas vezes em **hubs**.</span><span class="sxs-lookup"><span data-stu-id="0d186-178">To see the contents of the file, double-click **hubs**.</span></span> <span data-ttu-id="0d186-179">Se você não estiver usando o Visual Studio 2012 ou 2013 e o Internet Explorer, ou se você não estiver no modo de depuração, você também pode obter o conteúdo do arquivo, navegando até a URL "hubs de signalR /".</span><span class="sxs-lookup"><span data-stu-id="0d186-179">If you are not using Visual Studio 2012 or 2013 and Internet Explorer, or if you are not in debug mode, you can also get the contents of the file by browsing to the "/signalR/hubs" URL.</span></span> <span data-ttu-id="0d186-180">Por exemplo, se seu site estiver em execução `http://localhost:56699`, vá para `http://localhost:56699/SignalR/hubs` no seu navegador.</span><span class="sxs-lookup"><span data-stu-id="0d186-180">For example, if your site is running at `http://localhost:56699`, go to `http://localhost:56699/SignalR/hubs` in your browser.</span></span>

<a id="manualproxy"></a>

### <a name="how-to-create-a-physical-file-for-the-signalr-generated-proxy"></a><span data-ttu-id="0d186-181">Como criar um arquivo físico para o SignalR gerado proxy</span><span class="sxs-lookup"><span data-stu-id="0d186-181">How to create a physical file for the SignalR generated proxy</span></span>

<span data-ttu-id="0d186-182">Como alternativa para o proxy gerado dinamicamente, você pode criar um arquivo físico que tem o código de proxy e fazer referência a esse arquivo.</span><span class="sxs-lookup"><span data-stu-id="0d186-182">As an alternative to the dynamically generated proxy, you can create a physical file that has the proxy code and reference that file.</span></span> <span data-ttu-id="0d186-183">Você talvez queira fazer isso para um controle de cache ou no comportamento de agrupamento, ou para obter o IntelliSense quando você está codificando as chamadas para métodos de servidor.</span><span class="sxs-lookup"><span data-stu-id="0d186-183">You might want to do that for control over caching or bundling behavior, or to get IntelliSense when you are coding calls to server methods.</span></span>

<span data-ttu-id="0d186-184">Para criar um arquivo de proxy, execute as seguintes etapas:</span><span class="sxs-lookup"><span data-stu-id="0d186-184">To create a proxy file, perform the following steps:</span></span>

1. <span data-ttu-id="0d186-185">Instalar o [Microsoft.AspNet.SignalR.Utils](https://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="0d186-185">Install the [Microsoft.AspNet.SignalR.Utils](https://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) NuGet package.</span></span>
2. <span data-ttu-id="0d186-186">Abra um prompt de comando e navegue até a *ferramentas* pasta que contém o arquivo SignalR.exe.</span><span class="sxs-lookup"><span data-stu-id="0d186-186">Open a command prompt and browse to the *tools* folder that contains the SignalR.exe file.</span></span> <span data-ttu-id="0d186-187">A pasta de ferramentas está no seguinte local:</span><span class="sxs-lookup"><span data-stu-id="0d186-187">The tools folder is at the following location:</span></span>

    `[your solution folder]\packages\Microsoft.AspNet.SignalR.Utils.2.1.0\tools`
3. <span data-ttu-id="0d186-188">Insira o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="0d186-188">Enter the following command:</span></span>

    `signalr ghp /path:[path to the .dll that contains your Hub class]`

    <span data-ttu-id="0d186-189">O caminho para seu *. dll* normalmente é o *bin* na sua pasta de projeto.</span><span class="sxs-lookup"><span data-stu-id="0d186-189">The path to your *.dll* is typically the *bin* folder in your project folder.</span></span>

    <span data-ttu-id="0d186-190">Este comando cria um arquivo chamado *Server. js* na mesma pasta que *signalr.exe*.</span><span class="sxs-lookup"><span data-stu-id="0d186-190">This command creates a file named *server.js* in the same folder as *signalr.exe*.</span></span>
4. <span data-ttu-id="0d186-191">Colocar o *Server. js* de arquivo em uma pasta apropriada em seu projeto, renomeá-lo conforme apropriado para seu aplicativo e adicionar uma referência a ele no lugar a referência "/ hubs do signalr".</span><span class="sxs-lookup"><span data-stu-id="0d186-191">Put the *server.js* file in an appropriate folder in your project, rename it as appropriate for your application, and add a reference to it in place of the "signalr/hubs" reference.</span></span>

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a><span data-ttu-id="0d186-192">Como estabelecer uma conexão</span><span class="sxs-lookup"><span data-stu-id="0d186-192">How to establish a connection</span></span>

<span data-ttu-id="0d186-193">Antes de estabelecer uma conexão, você precisa criar um objeto de conexão, criar um proxy e registrar manipuladores de eventos para os métodos que podem ser chamados a partir do servidor.</span><span class="sxs-lookup"><span data-stu-id="0d186-193">Before you can establish a connection, you have to create a connection object, create a proxy, and register event handlers for methods that can be called from the server.</span></span> <span data-ttu-id="0d186-194">Ao configurar os proxy e manipuladores de eventos, estabelece a conexão chamando o `start` método.</span><span class="sxs-lookup"><span data-stu-id="0d186-194">When the proxy and event handlers are set up, establish the connection by calling the `start` method.</span></span>

<span data-ttu-id="0d186-195">Se você estiver usando o proxy gerado, você não precisa criar o objeto de conexão em seu próprio código, porque o código proxy gerado faz isso para você.</span><span class="sxs-lookup"><span data-stu-id="0d186-195">If you are using the generated proxy, you don't have to create the connection object in your own code because the generated proxy code does it for you.</span></span>

<a id="nogenconnection"></a>

<span data-ttu-id="0d186-196">**Estabelecer uma conexão (com o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-196">**Establish a connection (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample8.js?highlight=5)]

<span data-ttu-id="0d186-197">**Estabelecer uma conexão (sem o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-197">**Establish a connection (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample9.js?highlight=1,6)]

<span data-ttu-id="0d186-198">O código de exemplo usa o padrão "/ signalr" URL para se conectar ao seu serviço SignalR.</span><span class="sxs-lookup"><span data-stu-id="0d186-198">The sample code uses the default "/signalr" URL to connect to your SignalR service.</span></span> <span data-ttu-id="0d186-199">Para obter informações sobre como especificar uma URL de base diferente, consulte [o guia da API de Hubs de SignalR do ASP.NET - Server - URL /signalr](hubs-api-guide-server.md#signalrurl).</span><span class="sxs-lookup"><span data-stu-id="0d186-199">For information about how to specify a different base URL, see [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](hubs-api-guide-server.md#signalrurl).</span></span>

<span data-ttu-id="0d186-200">Por padrão, o local de hub é o servidor atual; Se você estiver se conectando a um servidor diferente, especifique a URL antes de chamar o `start` método, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="0d186-200">By default, the hub location is the current server; if you are connecting to a different server, specify the URL before calling the `start` method, as shown in the following example:</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample10.js)]

> [!NOTE]
> <span data-ttu-id="0d186-201">Normalmente você registrar manipuladores de eventos antes de chamar o `start` método para estabelecer a conexão.</span><span class="sxs-lookup"><span data-stu-id="0d186-201">Normally you register event handlers before calling the `start` method to establish the connection.</span></span> <span data-ttu-id="0d186-202">Se você deseja registrar alguns manipuladores de eventos depois de estabelecer a conexão, você pode fazer isso, mas você deve registrar pelo menos um dos seus os manipuladores de eventos antes de chamar o `start` método.</span><span class="sxs-lookup"><span data-stu-id="0d186-202">If you want to register some event handlers after establishing the connection, you can do that, but you must register at least one of your event handler(s) before calling the `start` method.</span></span> <span data-ttu-id="0d186-203">Um motivo para isso é que pode haver vários Hubs em um aplicativo, mas você não iria querer disparar o `OnConnected` evento em cada Hub se você apenas for ser usado para um deles.</span><span class="sxs-lookup"><span data-stu-id="0d186-203">One reason for this is that there can be many Hubs in an application, but you wouldn't want to trigger the `OnConnected` event on every Hub if you are only going to use to one of them.</span></span> <span data-ttu-id="0d186-204">Quando a conexão é estabelecida, a presença de um método de cliente no proxy de um Hub é o que informa ao SignalR para disparar o `OnConnected` eventos.</span><span class="sxs-lookup"><span data-stu-id="0d186-204">When the connection is established, the presence of a client method on a Hub's proxy is what tells SignalR to trigger the `OnConnected` event.</span></span> <span data-ttu-id="0d186-205">Se você não registrar quaisquer manipuladores de evento antes de chamar o `start` método, você poderá invocar métodos em Hub, mas o Hub `OnConnected` método não será chamado e nenhum método de cliente será invocado do servidor.</span><span class="sxs-lookup"><span data-stu-id="0d186-205">If you don't register any event handlers before calling the `start` method, you will be able to invoke methods on the Hub, but the Hub's `OnConnected` method won't be called and no client methods will be invoked from the server.</span></span>


<a id="connequivalence"></a>

### <a name="connectionhub-is-the-same-object-that-hubconnection-creates"></a><span data-ttu-id="0d186-206">$. connection.hub é o mesmo objeto que $.hubConnection() cria</span><span class="sxs-lookup"><span data-stu-id="0d186-206">$.connection.hub is the same object that $.hubConnection() creates</span></span>

<span data-ttu-id="0d186-207">Como você pode ver nos exemplos, quando você usa o proxy gerado, `$.connection.hub` refere-se ao objeto de conexão.</span><span class="sxs-lookup"><span data-stu-id="0d186-207">As you can see from the examples, when you use the generated proxy, `$.connection.hub` refers to the connection object.</span></span> <span data-ttu-id="0d186-208">Isso é o mesmo objeto que você obtém chamando `$.hubConnection()` quando você não estiver usando o proxy gerado.</span><span class="sxs-lookup"><span data-stu-id="0d186-208">This is the same object that you get by calling `$.hubConnection()` when you aren't using the generated proxy.</span></span> <span data-ttu-id="0d186-209">O código de proxy gerado cria a conexão para você, executando a instrução a seguir:</span><span class="sxs-lookup"><span data-stu-id="0d186-209">The generated proxy code creates the connection for you by executing the following statement:</span></span>

![Criando uma conexão no arquivo proxy gerado](hubs-api-guide-javascript-client/_static/image3.png)

<span data-ttu-id="0d186-211">Quando você estiver usando o proxy gerado, você pode fazer qualquer coisa com `$.connection.hub` que você pode fazer com um objeto de conexão quando você não estiver usando o proxy gerado.</span><span class="sxs-lookup"><span data-stu-id="0d186-211">When you're using the generated proxy, you can do anything with `$.connection.hub` that you can do with a connection object when you're not using the generated proxy.</span></span>

<a id="asyncstart"></a>

### <a name="asynchronous-execution-of-the-start-method"></a><span data-ttu-id="0d186-212">Execução assíncrona do método start</span><span class="sxs-lookup"><span data-stu-id="0d186-212">Asynchronous execution of the start method</span></span>

<span data-ttu-id="0d186-213">O `start` método executa de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="0d186-213">The `start` method executes asynchronously.</span></span> <span data-ttu-id="0d186-214">Ele retorna um [objeto do jQuery adiado](http://api.jquery.com/category/deferred-object/), que significa que você pode adicionar funções de retorno de chamada chamando métodos, como `pipe`, `done`, e `fail`.</span><span class="sxs-lookup"><span data-stu-id="0d186-214">It returns a [jQuery Deferred object](http://api.jquery.com/category/deferred-object/), which means that you can add callback functions by calling methods such as `pipe`, `done`, and `fail`.</span></span> <span data-ttu-id="0d186-215">Se você tiver o código que você deseja executar depois que a conexão for estabelecida, como uma chamada para um método de servidor, coloque esse código em uma função de retorno de chamada, ou chamá-lo de uma função de retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="0d186-215">If you have code that you want to execute after the connection is established, such as a call to a server method, put that code in a callback function or call it from a callback function.</span></span> <span data-ttu-id="0d186-216">O `.done` método de retorno de chamada é executado depois que a conexão foi estabelecida e, depois de qualquer código que você tem em seu `OnConnected` conclui a execução de método do manipulador de eventos no servidor.</span><span class="sxs-lookup"><span data-stu-id="0d186-216">The `.done` callback method is executed after the connection has been established, and after any code that you have in your `OnConnected` event handler method on the server finishes executing.</span></span>

<span data-ttu-id="0d186-217">Se você colocar a instrução "Agora conectado" do exemplo anterior, como a próxima linha de código após o `start` chamada de método (não em um `.done` retorno de chamada), o `console.log` linha será executada antes que a conexão seja estabelecida, conforme mostrado no exemplo a seguir exemplo:</span><span class="sxs-lookup"><span data-stu-id="0d186-217">If you put the "Now connected" statement from the preceding example as the next line of code after the `start` method call (not in a `.done` callback), the `console.log` line will execute before the connection is established, as shown in the following example:</span></span>

![Maneira errada para escrever código que é executado após o estabelecimento de conexão](hubs-api-guide-javascript-client/_static/image5.png)

<a id="crossdomain"></a>

## <a name="how-to-establish-a-cross-domain-connection"></a><span data-ttu-id="0d186-219">Como estabelecer uma conexão entre domínios</span><span class="sxs-lookup"><span data-stu-id="0d186-219">How to establish a cross-domain connection</span></span>

<span data-ttu-id="0d186-220">Normalmente se o navegador carrega uma página a partir `http://contoso.com`, a conexão do SignalR está no mesmo domínio, no `http://contoso.com/signalr`.</span><span class="sxs-lookup"><span data-stu-id="0d186-220">Typically if the browser loads a page from `http://contoso.com`, the SignalR connection is in the same domain, at `http://contoso.com/signalr`.</span></span> <span data-ttu-id="0d186-221">Se a página a partir `http://contoso.com` faz uma conexão para `http://fabrikam.com/signalr`, que é uma conexão entre domínios.</span><span class="sxs-lookup"><span data-stu-id="0d186-221">If the page from `http://contoso.com` makes a connection to `http://fabrikam.com/signalr`, that is a cross-domain connection.</span></span> <span data-ttu-id="0d186-222">Por motivos de segurança, conexões entre domínios estão desabilitadas por padrão.</span><span class="sxs-lookup"><span data-stu-id="0d186-222">For security reasons, cross-domain connections are disabled by default.</span></span>

<span data-ttu-id="0d186-223">No SignalR 1.x, solicitações de domínio cruzado foram controlado por um sinalizador EnableCrossDomain único.</span><span class="sxs-lookup"><span data-stu-id="0d186-223">In SignalR 1.x, cross domain requests were controlled by a single EnableCrossDomain flag.</span></span> <span data-ttu-id="0d186-224">Esse sinalizador controlado solicitações JSONP e CORS.</span><span class="sxs-lookup"><span data-stu-id="0d186-224">This flag controlled both JSONP and CORS requests.</span></span> <span data-ttu-id="0d186-225">Para obter maior flexibilidade, suporte a todos os CORS foi removido do componente de servidor do SignalR (clientes JavaScript ainda usarem CORS normalmente se ele for detectado que o navegador dá suporte a ele), e o middleware OWIN nova foi disponibilizado dar suporte a esses cenários.</span><span class="sxs-lookup"><span data-stu-id="0d186-225">For greater flexibility, all CORS support has been removed from the server component of SignalR (JavaScript clients still use CORS normally if it is detected that the browser supports it), and new OWIN middleware has been made available to support these scenarios.</span></span>

<span data-ttu-id="0d186-226">Se JSONP é necessário no cliente (para dar suporte a solicitações de domínio cruzado em navegadores mais antigos), ele precisará ser habilitada explicitamente definindo `EnableJSONP` sobre o `HubConfiguration` do objeto para `true`, conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="0d186-226">If JSONP is required on the client (to support cross-domain requests in older browsers), it will need to be enabled explicitly by setting `EnableJSONP` on the `HubConfiguration` object to `true`, as shown below.</span></span> <span data-ttu-id="0d186-227">JSONP está desabilitado por padrão, pois é menos segura do que o CORS.</span><span class="sxs-lookup"><span data-stu-id="0d186-227">JSONP is disabled by default, as it is less secure than CORS.</span></span>

<span data-ttu-id="0d186-228">**Adicionando owin ao projeto:** Para instalar essa biblioteca, execute o seguinte comando no Console do Gerenciador de pacotes:</span><span class="sxs-lookup"><span data-stu-id="0d186-228">**Adding Microsoft.Owin.Cors to your project:** To install this library, run the following command in the Package Manager Console:</span></span>

`Install-Package Microsoft.Owin.Cors`

<span data-ttu-id="0d186-229">Esse comando adicionará o 2.1.0 versão do pacote ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="0d186-229">This command will add the 2.1.0 version of the package to your project.</span></span>

### <a name="calling-usecors"></a><span data-ttu-id="0d186-230">Chamar UseCors</span><span class="sxs-lookup"><span data-stu-id="0d186-230">Calling UseCors</span></span>

 <span data-ttu-id="0d186-231">O trecho de código a seguir demonstra como implementar conexões entre domínios no SignalR 2.</span><span class="sxs-lookup"><span data-stu-id="0d186-231">The following code snippet demonstrates how to implement cross-domain connections in SignalR 2.</span></span>

<span data-ttu-id="0d186-232">**Implementando solicitações entre domínios no SignalR 2**</span><span class="sxs-lookup"><span data-stu-id="0d186-232">**Implementing cross-domain requests in SignalR 2**</span></span>

<span data-ttu-id="0d186-233">O código a seguir demonstra como habilitar o CORS ou JSONP em um projeto SignalR 2.</span><span class="sxs-lookup"><span data-stu-id="0d186-233">The following code demonstrates how to enable CORS or JSONP in a SignalR 2 project.</span></span> <span data-ttu-id="0d186-234">Este exemplo de código usa `Map` e `RunSignalR` em vez de `MapSignalR`, de modo que o middleware do CORS é executado somente para as solicitações de SignalR que exigem suporte CORS (em vez de para todo o tráfego no caminho especificado no `MapSignalR`.) Mapa também pode ser usado para qualquer outro middleware que precisa ser executada para um prefixo de URL específico, em vez de todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0d186-234">This code sample uses `Map` and `RunSignalR` instead of `MapSignalR`, so that the CORS middleware runs only for the SignalR requests that require CORS support (rather than for all traffic at the path specified in `MapSignalR`.) Map can also be used for any other middleware that needs to run for a specific URL prefix, rather than for the entire application.</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample11.cs)]

> [!NOTE]
>
> - <span data-ttu-id="0d186-235">Não defina `jQuery.support.cors` como true no seu código.</span><span class="sxs-lookup"><span data-stu-id="0d186-235">Don't set `jQuery.support.cors` to true in your code.</span></span>
>
>     ![Não defina jQuery.support.cors como true](hubs-api-guide-javascript-client/_static/image7.png)
>
>     <span data-ttu-id="0d186-237">O SignalR lida com o uso de CORS.</span><span class="sxs-lookup"><span data-stu-id="0d186-237">SignalR handles the use of CORS.</span></span> <span data-ttu-id="0d186-238">Definindo `jQuery.support.cors` para true desabilita JSONP porque ele faz com que o SignalR assumir o navegador dá suporte a CORS.</span><span class="sxs-lookup"><span data-stu-id="0d186-238">Setting `jQuery.support.cors` to true disables JSONP because it causes SignalR to assume the browser supports CORS.</span></span>
> - <span data-ttu-id="0d186-239">Quando você estiver se conectando a uma URL de localhost, o Internet Explorer 10 não considerá-la uma conexão entre domínios, para que o aplicativo funcione localmente com o IE 10, mesmo se você não tiver habilitado conexões entre domínios no servidor.</span><span class="sxs-lookup"><span data-stu-id="0d186-239">When you're connecting to a localhost URL, Internet Explorer 10 won't consider it a cross-domain connection, so the application will work locally with IE 10 even if you haven't enabled cross-domain connections on the server.</span></span>
> - <span data-ttu-id="0d186-240">Para obter informações sobre como usar conexões entre domínios com o Internet Explorer 9, consulte [esse thread de StackOverflow](http://stackoverflow.com/questions/13573397/siganlr-ie9-cross-domain-request-dont-work).</span><span class="sxs-lookup"><span data-stu-id="0d186-240">For information about using cross-domain connections with Internet Explorer 9, see [this StackOverflow thread](http://stackoverflow.com/questions/13573397/siganlr-ie9-cross-domain-request-dont-work).</span></span>
> - <span data-ttu-id="0d186-241">Para obter informações sobre como usar conexões entre domínios com o Chrome, consulte [esse thread de StackOverflow](http://stackoverflow.com/questions/15467373/signalr-1-0-1-cross-domain-request-cors-with-chrome).</span><span class="sxs-lookup"><span data-stu-id="0d186-241">For information about using cross-domain connections with Chrome, see [this StackOverflow thread](http://stackoverflow.com/questions/15467373/signalr-1-0-1-cross-domain-request-cors-with-chrome).</span></span>
> - <span data-ttu-id="0d186-242">O código de exemplo usa o padrão "/ signalr" URL para se conectar ao seu serviço SignalR.</span><span class="sxs-lookup"><span data-stu-id="0d186-242">The sample code uses the default "/signalr" URL to connect to your SignalR service.</span></span> <span data-ttu-id="0d186-243">Para obter informações sobre como especificar uma URL de base diferente, consulte [o guia da API de Hubs de SignalR do ASP.NET - Server - URL /signalr](hubs-api-guide-server.md#signalrurl).</span><span class="sxs-lookup"><span data-stu-id="0d186-243">For information about how to specify a different base URL, see [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](hubs-api-guide-server.md#signalrurl).</span></span>


<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a><span data-ttu-id="0d186-244">Como configurar a conexão</span><span class="sxs-lookup"><span data-stu-id="0d186-244">How to configure the connection</span></span>

<span data-ttu-id="0d186-245">Antes de estabelecer uma conexão, você pode especificar parâmetros de cadeia de caracteres de consulta ou especificar o método de transporte.</span><span class="sxs-lookup"><span data-stu-id="0d186-245">Before you establish a connection, you can specify query string parameters or specify the transport method.</span></span>

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a><span data-ttu-id="0d186-246">Como especificar parâmetros de cadeia de caracteres de consulta</span><span class="sxs-lookup"><span data-stu-id="0d186-246">How to specify query string parameters</span></span>

<span data-ttu-id="0d186-247">Se você quiser enviar dados para o servidor quando o cliente se conecta, você pode adicionar parâmetros de cadeia de caracteres de consulta para o objeto de conexão.</span><span class="sxs-lookup"><span data-stu-id="0d186-247">If you want to send data to the server when the client connects, you can add query string parameters to the connection object.</span></span> <span data-ttu-id="0d186-248">Os exemplos a seguir mostram como definir um parâmetro de cadeia de caracteres de consulta no código do cliente.</span><span class="sxs-lookup"><span data-stu-id="0d186-248">The following examples show how to set a query string parameter in client code.</span></span>

<span data-ttu-id="0d186-249">**Defina um valor de cadeia de caracteres de consulta antes de chamar o método start (com o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-249">**Set a query string value before calling the start method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample12.js?highlight=1)]

<span data-ttu-id="0d186-250">**Definir um valor de cadeia de caracteres de consulta antes de chamar o método start (sem o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-250">**Set a query string value before calling the start method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample13.js?highlight=2)]

<span data-ttu-id="0d186-251">O exemplo a seguir mostra como ler um parâmetro de cadeia de caracteres de consulta no código do servidor.</span><span class="sxs-lookup"><span data-stu-id="0d186-251">The following example shows how to read a query string parameter in server code.</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample14.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a><span data-ttu-id="0d186-252">Como especificar o método de transporte</span><span class="sxs-lookup"><span data-stu-id="0d186-252">How to specify the transport method</span></span>

<span data-ttu-id="0d186-253">Como parte do processo de conexão, um cliente SignalR normalmente negocia com o servidor para determinar o melhor transporte com suporte pelo cliente e servidor.</span><span class="sxs-lookup"><span data-stu-id="0d186-253">As part of the process of connecting, a SignalR client normally negotiates with the server to determine the best transport that is supported by both server and client.</span></span> <span data-ttu-id="0d186-254">Se você já souber qual transporte você deseja usar, você pode ignorar esse processo de negociação, especificando o método de transporte ao chamar o `start` método.</span><span class="sxs-lookup"><span data-stu-id="0d186-254">If you already know which transport you want to use, you can bypass this negotiation process by specifying the transport method when you call the `start` method.</span></span>

<span data-ttu-id="0d186-255">**Código do cliente que especifica o método de transporte (com o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-255">**Client code that specifies the transport method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample15.js?highlight=1)]

<span data-ttu-id="0d186-256">**Código do cliente que especifica o método de transporte (sem o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-256">**Client code that specifies the transport method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample16.js?highlight=2)]

<span data-ttu-id="0d186-257">Como alternativa, você pode especificar vários métodos de transporte na ordem em que você deseja que o SignalR para testá-los:</span><span class="sxs-lookup"><span data-stu-id="0d186-257">As an alternative, you can specify multiple transport methods in the order in which you want SignalR to try them:</span></span>

<span data-ttu-id="0d186-258">**Código do cliente que especifica um esquema de fallback de transporte personalizado (com o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-258">**Client code that specifies a custom transport fallback scheme (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample17.js?highlight=1)]

<span data-ttu-id="0d186-259">**Código do cliente que especifica um esquema de fallback de transporte personalizado (sem o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-259">**Client code that specifies a custom transport fallback scheme (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample18.js?highlight=2)]

<span data-ttu-id="0d186-260">Você pode usar os seguintes valores para especificar o método de transporte:</span><span class="sxs-lookup"><span data-stu-id="0d186-260">You can use the following values for specifying the transport method:</span></span>

- <span data-ttu-id="0d186-261">"webSockets"</span><span class="sxs-lookup"><span data-stu-id="0d186-261">"webSockets"</span></span>
- <span data-ttu-id="0d186-262">"foreverFrame"</span><span class="sxs-lookup"><span data-stu-id="0d186-262">"foreverFrame"</span></span>
- <span data-ttu-id="0d186-263">"serverSentEvents"</span><span class="sxs-lookup"><span data-stu-id="0d186-263">"serverSentEvents"</span></span>
- <span data-ttu-id="0d186-264">"longPolling"</span><span class="sxs-lookup"><span data-stu-id="0d186-264">"longPolling"</span></span>

<span data-ttu-id="0d186-265">Os exemplos a seguir mostram como descobrir qual método de transporte está sendo usado por uma conexão.</span><span class="sxs-lookup"><span data-stu-id="0d186-265">The following examples show how to find out which transport method is being used by a connection.</span></span>

<span data-ttu-id="0d186-266">**Código do cliente que exibe o método de transporte usado por uma conexão (com o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-266">**Client code that displays the transport method used by a connection (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample19.js?highlight=2)]

<span data-ttu-id="0d186-267">**Código do cliente que exibe o método de transporte usado por uma conexão (sem o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-267">**Client code that displays the transport method used by a connection (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample20.js?highlight=3)]

<span data-ttu-id="0d186-268">Para obter informações sobre como verificar o método de transporte no código do servidor, consulte [o guia da API de Hubs de SignalR do ASP.NET - Server - como obter informações sobre o cliente da propriedade de contexto](hubs-api-guide-server.md#contextproperty).</span><span class="sxs-lookup"><span data-stu-id="0d186-268">For information about how to check the transport method in server code, see [ASP.NET SignalR Hubs API Guide - Server - How to get information about the client from the Context property](hubs-api-guide-server.md#contextproperty).</span></span> <span data-ttu-id="0d186-269">Para obter mais informações sobre os transportes e fallbacks, consulte [Introdução ao SignalR - transportes e Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span><span class="sxs-lookup"><span data-stu-id="0d186-269">For more information about transports and fallbacks, see [Introduction to SignalR - Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span></span>

<a id="getproxy"></a>

## <a name="how-to-get-a-proxy-for-a-hub-class"></a><span data-ttu-id="0d186-270">Como obter um proxy para uma classe de Hub</span><span class="sxs-lookup"><span data-stu-id="0d186-270">How to get a proxy for a Hub class</span></span>

<span data-ttu-id="0d186-271">Cada objeto de conexão que você crie encapsula informações sobre uma conexão a um serviço SignalR que contém uma ou mais classes de Hub.</span><span class="sxs-lookup"><span data-stu-id="0d186-271">Each connection object that you create encapsulates information about a connection to a SignalR service that contains one or more Hub classes.</span></span> <span data-ttu-id="0d186-272">Para se comunicar com uma classe Hub, você usa um objeto de proxy que você cria por conta própria (se você não estiver usando o proxy gerado) ou que é gerado para você.</span><span class="sxs-lookup"><span data-stu-id="0d186-272">To communicate with a Hub class, you use a proxy object which you create yourself (if you're not using the generated proxy) or which is generated for you.</span></span>

<span data-ttu-id="0d186-273">No cliente, o nome do proxy é uma versão em camel case do nome da classe Hub.</span><span class="sxs-lookup"><span data-stu-id="0d186-273">On the client the proxy name is a camel-cased version of the Hub class name.</span></span> <span data-ttu-id="0d186-274">O SignalR faz automaticamente essa alteração para que o código JavaScript pode estar em conformidade com as convenções de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0d186-274">SignalR automatically makes this change so that JavaScript code can conform to JavaScript conventions.</span></span>

<span data-ttu-id="0d186-275">**Classe de Hub no servidor**</span><span class="sxs-lookup"><span data-stu-id="0d186-275">**Hub class on server**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample21.cs?highlight=1)]

<span data-ttu-id="0d186-276">**Obter uma referência para o proxy de cliente gerado para o Hub**</span><span class="sxs-lookup"><span data-stu-id="0d186-276">**Get a reference to the generated client proxy for the Hub**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample22.js?highlight=1)]

<span data-ttu-id="0d186-277">**Criar proxy de cliente para a classe Hub (sem proxy gerada)**</span><span class="sxs-lookup"><span data-stu-id="0d186-277">**Create client proxy for the Hub class (without generated proxy)**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample23.cs?highlight=1)]

<span data-ttu-id="0d186-278">Se você decorar a classe Hub com um `HubName` de atributo, use o nome exato sem alterar o caso.</span><span class="sxs-lookup"><span data-stu-id="0d186-278">If you decorate your Hub class with a `HubName` attribute, use the exact name without changing case.</span></span>

<span data-ttu-id="0d186-279">**Classe de Hub no servidor com o atributo HubName**</span><span class="sxs-lookup"><span data-stu-id="0d186-279">**Hub class on server with HubName attribute**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample24.cs?highlight=1)]

<span data-ttu-id="0d186-280">**Obter uma referência para o proxy de cliente gerado para o Hub**</span><span class="sxs-lookup"><span data-stu-id="0d186-280">**Get a reference to the generated client proxy for the Hub**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample25.js?highlight=1)]

<span data-ttu-id="0d186-281">**Criar proxy de cliente para a classe Hub (sem proxy gerada)**</span><span class="sxs-lookup"><span data-stu-id="0d186-281">**Create client proxy for the Hub class (without generated proxy)**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample26.cs?highlight=1)]

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a><span data-ttu-id="0d186-282">Como definir métodos no cliente que o servidor pode chamar</span><span class="sxs-lookup"><span data-stu-id="0d186-282">How to define methods on the client that the server can call</span></span>

<span data-ttu-id="0d186-283">Para definir um método que o servidor pode chamar de um Hub, adicione um manipulador de eventos para o proxy do Hub usando o `client` propriedade do proxy gerado ou chamada de `on` método se você não estiver usando o proxy gerado.</span><span class="sxs-lookup"><span data-stu-id="0d186-283">To define a method that the server can call from a Hub, add an event handler to the Hub proxy by using the `client` property of the generated proxy, or call the `on` method if you aren't using the generated proxy.</span></span> <span data-ttu-id="0d186-284">Os parâmetros podem ser objetos complexos.</span><span class="sxs-lookup"><span data-stu-id="0d186-284">The parameters can be complex objects.</span></span>

<span data-ttu-id="0d186-285">Adicionar o manipulador de eventos antes de chamar o `start` método para estabelecer a conexão.</span><span class="sxs-lookup"><span data-stu-id="0d186-285">Add the event handler before you call the `start` method to establish the connection.</span></span> <span data-ttu-id="0d186-286">(Se você quiser adicionar manipuladores de eventos após a chamada a `start` método, consulte a Observação [como estabelecer uma conexão](#establishconnection) anteriormente neste documento e usar a sintaxe mostrada para definir um método sem usar o proxy gerado.)</span><span class="sxs-lookup"><span data-stu-id="0d186-286">(If you want to add event handlers after calling the `start` method, see the note in [How to establish a connection](#establishconnection) earlier in this document, and use the syntax shown for defining a method without using the generated proxy.)</span></span>

<span data-ttu-id="0d186-287">Correspondência de nomes do método diferencia maiusculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="0d186-287">Method name matching is case-insensitive.</span></span> <span data-ttu-id="0d186-288">Por exemplo, `Clients.All.addContosoChatMessageToPage` no servidor serão executadas `AddContosoChatMessageToPage`, `addContosoChatMessageToPage`, ou `addcontosochatmessagetopage` no cliente.</span><span class="sxs-lookup"><span data-stu-id="0d186-288">For example, `Clients.All.addContosoChatMessageToPage` on the server will execute `AddContosoChatMessageToPage`, `addContosoChatMessageToPage`, or `addcontosochatmessagetopage` on the client.</span></span>

<span data-ttu-id="0d186-289">**Definir o método no cliente (com o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-289">**Define method on client (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample27.js?highlight=2)]

<span data-ttu-id="0d186-290">**Maneira alternativa para definir o método no cliente (com o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-290">**Alternate way to define method on client (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample28.js?highlight=1-2)]

<span data-ttu-id="0d186-291">**Definir o método no cliente (sem o proxy gerado, ou ao adicionar depois de chamar o método start)**</span><span class="sxs-lookup"><span data-stu-id="0d186-291">**Define method on client (without the generated proxy, or when adding after calling the start method)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample29.js?highlight=3)]

<span data-ttu-id="0d186-292">**Código de servidor que chama o método de cliente**</span><span class="sxs-lookup"><span data-stu-id="0d186-292">**Server code that calls the client method**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample30.cs?highlight=5)]

<span data-ttu-id="0d186-293">Os exemplos a seguir incluem um objeto complexo como um parâmetro de método.</span><span class="sxs-lookup"><span data-stu-id="0d186-293">The following examples include a complex object as a method parameter.</span></span>

<span data-ttu-id="0d186-294">**Definir o método no cliente que usa um objeto complexo (com o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-294">**Define method on client that takes a complex object (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample31.js?highlight=2-3)]

<span data-ttu-id="0d186-295">**Definir o método no cliente que usa um objeto complexo (sem o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-295">**Define method on client that takes a complex object (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample32.js?highlight=3-4)]

<span data-ttu-id="0d186-296">**Código de servidor que define o objeto complexo**</span><span class="sxs-lookup"><span data-stu-id="0d186-296">**Server code that defines the complex object**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample33.cs?highlight=1)]

<span data-ttu-id="0d186-297">**Código de servidor que chama o método de cliente usando um objeto complexo**</span><span class="sxs-lookup"><span data-stu-id="0d186-297">**Server code that calls the client method using a complex object**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample34.cs?highlight=3)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a><span data-ttu-id="0d186-298">Como chamar métodos de servidor do cliente</span><span class="sxs-lookup"><span data-stu-id="0d186-298">How to call server methods from the client</span></span>

<span data-ttu-id="0d186-299">Para chamar um método de servidor do cliente, use o `server` propriedade do proxy gerado ou o `invoke` método no proxy de Hub, se você não estiver usando o proxy gerado.</span><span class="sxs-lookup"><span data-stu-id="0d186-299">To call a server method from the client, use the `server` property of the generated proxy or the `invoke` method on the Hub proxy if you aren't using the generated proxy.</span></span> <span data-ttu-id="0d186-300">O valor de retorno ou os parâmetros podem ser objetos complexos.</span><span class="sxs-lookup"><span data-stu-id="0d186-300">The return value or parameters can be complex objects.</span></span>

<span data-ttu-id="0d186-301">Passe em uma versão de maiusculas e minúsculas do nome do método no Hub.</span><span class="sxs-lookup"><span data-stu-id="0d186-301">Pass in a camel-case version of the method name on the Hub.</span></span> <span data-ttu-id="0d186-302">O SignalR faz automaticamente essa alteração para que o código JavaScript pode estar em conformidade com as convenções de JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0d186-302">SignalR automatically makes this change so that JavaScript code can conform to JavaScript conventions.</span></span>

<span data-ttu-id="0d186-303">Os exemplos a seguir mostram como chamar um método de servidor que não tem um valor de retorno e como chamar um método de servidor que tem um valor de retorno.</span><span class="sxs-lookup"><span data-stu-id="0d186-303">The following examples show how to call a server method that doesn't have a return value and how to call a server method that does have a return value.</span></span>

<span data-ttu-id="0d186-304">**Método de servidor sem o atributo HubMethodName**</span><span class="sxs-lookup"><span data-stu-id="0d186-304">**Server method with no HubMethodName attribute**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample35.cs?highlight=3)]

<span data-ttu-id="0d186-305">**Código de servidor que define o objeto complexo passado em um parâmetro**</span><span class="sxs-lookup"><span data-stu-id="0d186-305">**Server code that defines the complex object passed in a parameter**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample36.cs)]

<span data-ttu-id="0d186-306">**Código do cliente que invoca o método de servidor (com o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-306">**Client code that invokes the server method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample37.js?highlight=1)]

<span data-ttu-id="0d186-307">**Código do cliente que invoca o método de servidor (sem o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-307">**Client code that invokes the server method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample38.js?highlight=1)]

<span data-ttu-id="0d186-308">Se você decorado o método de Hub com um `HubMethodName` de atributo, use esse nome sem alterar o caso.</span><span class="sxs-lookup"><span data-stu-id="0d186-308">If you decorated the Hub method with a `HubMethodName` attribute, use that name without changing case.</span></span>

<span data-ttu-id="0d186-309">**Método de servidor** com um atributo HubMethodName</span><span class="sxs-lookup"><span data-stu-id="0d186-309">**Server method** with a HubMethodName attribute</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample39.cs?highlight=3)]

<span data-ttu-id="0d186-310">**Código do cliente que invoca o método de servidor (com o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-310">**Client code that invokes the server method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample40.js?highlight=1)]

<span data-ttu-id="0d186-311">**Código do cliente que invoca o método de servidor (sem o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-311">**Client code that invokes the server method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample41.js?highlight=1)]

<span data-ttu-id="0d186-312">Os exemplos anteriores mostram como chamar um método de servidor que não tem nenhum valor de retorno.</span><span class="sxs-lookup"><span data-stu-id="0d186-312">The preceding examples show how to call a server method that has no return value.</span></span> <span data-ttu-id="0d186-313">Os exemplos a seguir mostram como chamar um método de servidor que tem um valor de retorno.</span><span class="sxs-lookup"><span data-stu-id="0d186-313">The following examples show how to call a server method that has a return value.</span></span>

<span data-ttu-id="0d186-314">**Código de servidor para um método que tem um valor de retorno**</span><span class="sxs-lookup"><span data-stu-id="0d186-314">**Server code for a method that has a return value**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample42.cs?highlight=3)]

<span data-ttu-id="0d186-315">**A classe de estoque para o** retornar valor</span><span class="sxs-lookup"><span data-stu-id="0d186-315">**The Stock class used for the** return value</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample43.cs?highlight=1)]

<span data-ttu-id="0d186-316">**Código do cliente que invoca o método de servidor (com o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-316">**Client code that invokes the server method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample44.js?highlight=2,4-5)]

<span data-ttu-id="0d186-317">**Código do cliente que invoca o método de servidor (sem o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-317">**Client code that invokes the server method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample45.js?highlight=2,4-5)]

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a><span data-ttu-id="0d186-318">Como manipular eventos de tempo de vida da conexão</span><span class="sxs-lookup"><span data-stu-id="0d186-318">How to handle connection lifetime events</span></span>

<span data-ttu-id="0d186-319">SignalR fornece eventos de tempo de vida que você pode manipular de conexão a seguir:</span><span class="sxs-lookup"><span data-stu-id="0d186-319">SignalR provides the following connection lifetime events that you can handle:</span></span>

- <span data-ttu-id="0d186-320">`starting`: Gerado antes de todos os dados são enviados pela conexão.</span><span class="sxs-lookup"><span data-stu-id="0d186-320">`starting`: Raised before any data is sent over the connection.</span></span>
- <span data-ttu-id="0d186-321">`received`: Gerado quando nenhum dado for recebido na conexão.</span><span class="sxs-lookup"><span data-stu-id="0d186-321">`received`: Raised when any data is received on the connection.</span></span> <span data-ttu-id="0d186-322">Fornece os dados recebidos.</span><span class="sxs-lookup"><span data-stu-id="0d186-322">Provides the received data.</span></span>
- <span data-ttu-id="0d186-323">`connectionSlow`: Gerado quando o cliente detecta uma conexão lenta ou remoção com frequência.</span><span class="sxs-lookup"><span data-stu-id="0d186-323">`connectionSlow`: Raised when the client detects a slow or frequently dropping connection.</span></span>
- <span data-ttu-id="0d186-324">`reconnecting`: Gerado quando o transporte subjacente inicia a reconexão.</span><span class="sxs-lookup"><span data-stu-id="0d186-324">`reconnecting`: Raised when the underlying transport begins reconnecting.</span></span>
- <span data-ttu-id="0d186-325">`reconnected`: Gerado quando o transporte subjacente foi reconectada.</span><span class="sxs-lookup"><span data-stu-id="0d186-325">`reconnected`: Raised when the underlying transport has reconnected.</span></span>
- <span data-ttu-id="0d186-326">`stateChanged`: Gerado quando o estado de conexão é alterado.</span><span class="sxs-lookup"><span data-stu-id="0d186-326">`stateChanged`: Raised when the connection state changes.</span></span> <span data-ttu-id="0d186-327">Fornece o estado antigo e o novo estado (conectando, conectado, reconectando ou Disconnected).</span><span class="sxs-lookup"><span data-stu-id="0d186-327">Provides the old state and the new state (Connecting, Connected, Reconnecting, or Disconnected).</span></span>
- <span data-ttu-id="0d186-328">`disconnected`: Gerado quando a conexão foi desconectada.</span><span class="sxs-lookup"><span data-stu-id="0d186-328">`disconnected`: Raised when the connection has disconnected.</span></span>

<span data-ttu-id="0d186-329">Por exemplo, se você quiser exibir mensagens de aviso quando há problemas de conexão que podem causar atrasos notáveis, lidar com o `connectionSlow` eventos.</span><span class="sxs-lookup"><span data-stu-id="0d186-329">For example, if you want to display warning messages when there are connection problems that might cause noticeable delays, handle the `connectionSlow` event.</span></span>

<span data-ttu-id="0d186-330">**Manipular o evento de connectionSlow (com o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-330">**Handle the connectionSlow event (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample46.js?highlight=1)]

<span data-ttu-id="0d186-331">**Manipular o evento connectionSlow (sem o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-331">**Handle the connectionSlow event (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample47.js?highlight=2)]

<span data-ttu-id="0d186-332">Para obter mais informações, consulte [Noções básicas e tratamento de eventos de tempo de vida de Conexão no SignalR](handling-connection-lifetime-events.md).</span><span class="sxs-lookup"><span data-stu-id="0d186-332">For more information, see [Understanding and Handling Connection Lifetime Events in SignalR](handling-connection-lifetime-events.md).</span></span>

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a><span data-ttu-id="0d186-333">Como tratar erros</span><span class="sxs-lookup"><span data-stu-id="0d186-333">How to handle errors</span></span>

<span data-ttu-id="0d186-334">O cliente SignalR JavaScript fornece um `error` que você pode adicionar um manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="0d186-334">The SignalR JavaScript client provides an `error` event that you can add a handler for.</span></span> <span data-ttu-id="0d186-335">Você também pode usar o método fail para adicionar um manipulador de erros que resultarem de uma invocação de método do servidor.</span><span class="sxs-lookup"><span data-stu-id="0d186-335">You can also use the fail method to add a handler for errors that result from a server method invocation.</span></span>

<span data-ttu-id="0d186-336">Se você não explicitamente habilitar mensagens de erro detalhadas no servidor, o objeto de exceção SignalR retorna após um erro contém algumas informações sobre o erro.</span><span class="sxs-lookup"><span data-stu-id="0d186-336">If you don't explicitly enable detailed error messages on the server, the exception object that SignalR returns after an error contains minimal information about the error.</span></span> <span data-ttu-id="0d186-337">Por exemplo, se uma chamada para `newContosoChatMessage` falhar, a mensagem de erro no objeto de erro contém "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" envio de mensagens de erro detalhadas para clientes em produção não é recomendado por motivos de segurança, mas se você quiser habilitar mensagens de erro detalhadas para solução de problemas, use o seguinte código no servidor.</span><span class="sxs-lookup"><span data-stu-id="0d186-337">For example, if a call to `newContosoChatMessage` fails, the error message in the error object contains "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" Sending detailed error messages to clients in production is not recommended for security reasons, but if you want to enable detailed error messages for troubleshooting purposes, use the following code on the server.</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample48.cs?highlight=2)]

<span data-ttu-id="0d186-338">O exemplo a seguir mostra como adicionar um manipulador para o evento de erro.</span><span class="sxs-lookup"><span data-stu-id="0d186-338">The following example shows how to add a handler for the error event.</span></span>

<span data-ttu-id="0d186-339">**Adicionar um manipulador de erros (com o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-339">**Add an error handler (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample49.js?highlight=1)]

<span data-ttu-id="0d186-340">**Adicionar um manipulador de erro (sem o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-340">**Add an error handler (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample50.js?highlight=2)]

<span data-ttu-id="0d186-341">O exemplo a seguir mostra como manipular um erro de uma invocação de método.</span><span class="sxs-lookup"><span data-stu-id="0d186-341">The following example shows how to handle an error from a method invocation.</span></span>

<span data-ttu-id="0d186-342">**Manipular um erro de uma invocação de método (com o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-342">**Handle an error from a method invocation (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample51.js?highlight=2)]

<span data-ttu-id="0d186-343">**Manipular um erro de uma invocação de método (sem o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-343">**Handle an error from a method invocation (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample52.js?highlight=2)]

<span data-ttu-id="0d186-344">Se uma invocação de método falhar, o `error` também é gerado, de modo que seu código na `error` manipulador do método e, no `.fail` executaria o retorno de chamada de método.</span><span class="sxs-lookup"><span data-stu-id="0d186-344">If a method invocation fails, the `error` event is also raised, so your code in the `error` method handler and in the `.fail` method callback would execute.</span></span>

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a><span data-ttu-id="0d186-345">Como habilitar o registro em log do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="0d186-345">How to enable client-side logging</span></span>

<span data-ttu-id="0d186-346">Para habilitar o registro em log do lado do cliente em uma conexão, defina as `logging` propriedade no objeto de conexão antes de chamar o `start` método para estabelecer a conexão.</span><span class="sxs-lookup"><span data-stu-id="0d186-346">To enable client-side logging on a connection, set the `logging` property on the connection object before you call the `start` method to establish the connection.</span></span>

<span data-ttu-id="0d186-347">**Habilitar registro em log (com o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-347">**Enable logging (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample53.js?highlight=1)]

<span data-ttu-id="0d186-348">**Habilitar registro em log (sem o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="0d186-348">**Enable logging (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample54.js?highlight=2)]

<span data-ttu-id="0d186-349">Para ver os logs, abra Ferramentas de desenvolvedor do navegador e vá para a guia Console. Para obter um tutorial que mostra a tela e instruções passo a passo capturas que mostram como fazer isso, consulte [transmissão de servidor com Signalr do ASP.NET - Enable Logging](../getting-started/tutorial-server-broadcast-with-signalr.md#enable-logging).</span><span class="sxs-lookup"><span data-stu-id="0d186-349">To see the logs, open your browser's developer tools and go to the Console tab. For a tutorial that shows step-by-step instructions and screen shots that show how to do this, see [Server Broadcast with ASP.NET Signalr - Enable Logging](../getting-started/tutorial-server-broadcast-with-signalr.md#enable-logging).</span></span>
