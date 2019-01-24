---
uid: signalr/overview/older-versions/troubleshooting
title: Solução de problemas do SignalR (SignalR 1.x) | Microsoft Docs
author: bradygaster
description: Este artigo descreve problemas comuns com o desenvolvimento de aplicativos do SignalR.
ms.author: bradyg
ms.date: 06/05/2013
ms.assetid: 347210ba-c452-4feb-886f-b51d89f58971
msc.legacyurl: /signalr/overview/older-versions/troubleshooting
msc.type: authoredcontent
ms.openlocfilehash: d485829fb60b32b063405f37ae9c6c278bf7255b
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836266"
---
<a name="signalr-troubleshooting-signalr-1x"></a><span data-ttu-id="f7191-103">Solução de problemas do SignalR (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="f7191-103">SignalR Troubleshooting (SignalR 1.x)</span></span>
====================
<span data-ttu-id="f7191-104">por [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="f7191-104">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="f7191-105">Este documento descreve a solução de problemas comuns com o SignalR.</span><span class="sxs-lookup"><span data-stu-id="f7191-105">This document describes common troubleshooting issues with SignalR.</span></span>


<span data-ttu-id="f7191-106">Este documento contém as seções a seguir.</span><span class="sxs-lookup"><span data-stu-id="f7191-106">This document contains the following sections.</span></span>

- [<span data-ttu-id="f7191-107">Chamando métodos entre o cliente e servidor silenciosamente falhar</span><span class="sxs-lookup"><span data-stu-id="f7191-107">Calling methods between the client and server silently fails</span></span>](#connection)
- [<span data-ttu-id="f7191-108">Outros problemas de conexão</span><span class="sxs-lookup"><span data-stu-id="f7191-108">Other connection issues</span></span>](#other)
- [<span data-ttu-id="f7191-109">Erros de compilação e do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="f7191-109">Compilation and server-side errors</span></span>](#server)
- [<span data-ttu-id="f7191-110">Problemas do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7191-110">Visual Studio issues</span></span>](#vs)
- [<span data-ttu-id="f7191-111">Problemas de serviços de informações da Internet</span><span class="sxs-lookup"><span data-stu-id="f7191-111">Internet Information Services issues</span></span>](#iis)
- [<span data-ttu-id="f7191-112">Problemas do Azure</span><span class="sxs-lookup"><span data-stu-id="f7191-112">Azure issues</span></span>](#azure)

<a id="connection"></a>

## <a name="calling-methods-between-the-client-and-server-silently-fails"></a><span data-ttu-id="f7191-113">Chamando métodos entre o cliente e servidor silenciosamente falhar</span><span class="sxs-lookup"><span data-stu-id="f7191-113">Calling methods between the client and server silently fails</span></span>

<span data-ttu-id="f7191-114">Esta seção descreve possíveis causas de uma chamada de método entre cliente e servidor falhe sem uma mensagem de erro significativa.</span><span class="sxs-lookup"><span data-stu-id="f7191-114">This section describes possible causes for a method call between client and server to fail without a meaningful error message.</span></span> <span data-ttu-id="f7191-115">Em um aplicativo do SignalR, o servidor não possui informações sobre os métodos que o cliente implementa; Quando o servidor chama um método de cliente, os dados de nome e o parâmetro de método são enviados para o cliente e o método é executado somente se ela existe no formato que o servidor especificado.</span><span class="sxs-lookup"><span data-stu-id="f7191-115">In a SignalR application, the server has no information about the methods that the client implements; when the server invokes a client method, the method name and parameter data are sent to the client, and the method is executed only if it exists in the format that the server specified.</span></span> <span data-ttu-id="f7191-116">Se nenhum método correspondente for encontrado no cliente, nada acontecerá, e nenhuma mensagem de erro é gerada no servidor.</span><span class="sxs-lookup"><span data-stu-id="f7191-116">If no matching method is found on the client, nothing happens, and no error message is raised on the server.</span></span>

<span data-ttu-id="f7191-117">Para investigar ainda mais os métodos de cliente que está sendo chamados não, você pode ativar o log antes de chamar o método start no hub para ver o que o chama são provenientes do servidor.</span><span class="sxs-lookup"><span data-stu-id="f7191-117">To further investigate client methods not getting called, you can turn on logging before the calling the start method on the hub to see what calls are coming from the server.</span></span> <span data-ttu-id="f7191-118">Para habilitar o registro em log em um aplicativo JavaScript, consulte [como habilitar o registro em log do lado do cliente (versão de cliente JavaScript)](../guide-to-the-api/hubs-api-guide-javascript-client.md#logging).</span><span class="sxs-lookup"><span data-stu-id="f7191-118">To enable logging in a JavaScript application, see [How to enable client-side logging (JavaScript client version)](../guide-to-the-api/hubs-api-guide-javascript-client.md#logging).</span></span> <span data-ttu-id="f7191-119">Para habilitar o registro em log em um aplicativo de cliente .NET, consulte [como habilitar o registro em log do lado do cliente (versão de cliente .NET)](../guide-to-the-api/hubs-api-guide-net-client.md#logging).</span><span class="sxs-lookup"><span data-stu-id="f7191-119">To enable logging in a .NET client application, see [How to enable client-side logging (.NET Client version)](../guide-to-the-api/hubs-api-guide-net-client.md#logging).</span></span>

### <a name="misspelled-method-incorrect-method-signature-or-incorrect-hub-name"></a><span data-ttu-id="f7191-120">Método incorreta, assinatura de método incorreto ou nome de hub incorreto</span><span class="sxs-lookup"><span data-stu-id="f7191-120">Misspelled method, incorrect method signature, or incorrect hub name</span></span>

<span data-ttu-id="f7191-121">Se o nome ou assinatura de um método chamado corresponder exatamente a um método apropriado no cliente, a chamada falhará.</span><span class="sxs-lookup"><span data-stu-id="f7191-121">If the name or signature of a called method does not exactly match an appropriate method on the client, the call will fail.</span></span> <span data-ttu-id="f7191-122">Verifique se que o nome do método chamado pelo servidor corresponde ao nome do método no cliente.</span><span class="sxs-lookup"><span data-stu-id="f7191-122">Verify that the method name called by the server matches the name of the method on the client.</span></span> <span data-ttu-id="f7191-123">Além disso, o SignalR cria o proxy do hub usando métodos em camel case, conforme for apropriado em JavaScript, portanto, um método chamado `SendMessage` no servidor seria chamado `sendMessage` no proxy do cliente.</span><span class="sxs-lookup"><span data-stu-id="f7191-123">Also, SignalR creates the hub proxy using camel-cased methods, as is appropriate in JavaScript, so a method called `SendMessage` on the server would be called `sendMessage` in the client proxy.</span></span> <span data-ttu-id="f7191-124">Se você usar o `HubName` atributo no código do lado do servidor, verifique se o nome usado corresponde o nome usado para criar o hub no cliente.</span><span class="sxs-lookup"><span data-stu-id="f7191-124">If you use the `HubName` attribute in your server-side code, verify that the name used matches the name used to create the hub on the client.</span></span> <span data-ttu-id="f7191-125">Se você não usar o `HubName` de atributo, verifique se o nome do hub em um cliente JavaScript camel case, como chatHub em vez de ChatHub.</span><span class="sxs-lookup"><span data-stu-id="f7191-125">If you do not use the `HubName` attribute, verify that the name of the hub in a JavaScript client is camel-cased, such as chatHub instead of ChatHub.</span></span>

### <a name="duplicate-method-name-on-client"></a><span data-ttu-id="f7191-126">Duplicar o nome do método no cliente</span><span class="sxs-lookup"><span data-stu-id="f7191-126">Duplicate method name on client</span></span>

<span data-ttu-id="f7191-127">Verifique se que você não tem um método duplicado no cliente que difere apenas por maiusculas.</span><span class="sxs-lookup"><span data-stu-id="f7191-127">Verify that you do not have a duplicate method on the client that differs only by case.</span></span> <span data-ttu-id="f7191-128">Se seu aplicativo cliente tem um método chamado `sendMessage`, verifique se que há não também um método chamado `SendMessage` também.</span><span class="sxs-lookup"><span data-stu-id="f7191-128">If your client application has a method called `sendMessage`, verify that there isn't also a method called `SendMessage` as well.</span></span>

### <a name="missing-json-parser-on-the-client"></a><span data-ttu-id="f7191-129">Analisador JSON ausente no cliente</span><span class="sxs-lookup"><span data-stu-id="f7191-129">Missing JSON parser on the client</span></span>

<span data-ttu-id="f7191-130">O SignalR requer um analisador JSON devem estar presentes para serializar as chamadas entre o servidor e o cliente.</span><span class="sxs-lookup"><span data-stu-id="f7191-130">SignalR requires a JSON parser to be present to serialize calls between the server and the client.</span></span> <span data-ttu-id="f7191-131">Se seu cliente não tem um analisador JSON interno (por exemplo, o Internet Explorer 7), você precisará incluir um em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f7191-131">If your client doesn't have a built-in JSON parser (such as Internet Explorer 7), you'll need to include one in your application.</span></span> <span data-ttu-id="f7191-132">Você pode baixar o analisador JSON [aqui](http://nuget.org/packages/json2).</span><span class="sxs-lookup"><span data-stu-id="f7191-132">You can download the JSON parser [here](http://nuget.org/packages/json2).</span></span>

### <a name="mixing-hub-and-persistentconnection-syntax"></a><span data-ttu-id="f7191-133">Misturar sintaxe Hub e PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="f7191-133">Mixing Hub and PersistentConnection syntax</span></span>

<span data-ttu-id="f7191-134">O SignalR usa os dois modelos de comunicação: Os hubs e PersistentConnections.</span><span class="sxs-lookup"><span data-stu-id="f7191-134">SignalR uses two communication models: Hubs and PersistentConnections.</span></span> <span data-ttu-id="f7191-135">A sintaxe para chamar esses modelos de dois comunicação é diferente no código do cliente.</span><span class="sxs-lookup"><span data-stu-id="f7191-135">The syntax for calling these two communication models is different in the client code.</span></span> <span data-ttu-id="f7191-136">Se você tiver adicionado um hub no código do servidor, verifique se que todo o código do cliente usa a sintaxe de hub apropriado.</span><span class="sxs-lookup"><span data-stu-id="f7191-136">If you have added a hub in your server code, verify that all of your client code uses the proper hub syntax.</span></span>

<span data-ttu-id="f7191-137">**Código de cliente JavaScript que cria um PersistentConnection em um cliente JavaScript**</span><span class="sxs-lookup"><span data-stu-id="f7191-137">**JavaScript client code that creates a PersistentConnection in a JavaScript client**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample1.js)]

<span data-ttu-id="f7191-138">**Código de cliente JavaScript que cria um Proxy de Hub em um cliente Javascript**</span><span class="sxs-lookup"><span data-stu-id="f7191-138">**JavaScript client code that creates a Hub Proxy in a Javascript client**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample2.js)]

<span data-ttu-id="f7191-139">**Servidor código c# que mapeia uma rota para um PersistentConnection**</span><span class="sxs-lookup"><span data-stu-id="f7191-139">**C# server code that maps a route to a PersistentConnection**</span></span>

[!code-csharp[Main](troubleshooting/samples/sample3.cs)]

<span data-ttu-id="f7191-140">**Servidor código c# que mapeia uma rota para um Hub ou para vários hubs, se você tiver vários aplicativos**</span><span class="sxs-lookup"><span data-stu-id="f7191-140">**C# server code that maps a route to a Hub, or to mulitple hubs if you have multiple applications**</span></span>

[!code-csharp[Main](troubleshooting/samples/sample4.cs)]

### <a name="connection-started-before-subscriptions-are-added"></a><span data-ttu-id="f7191-141">Conexão iniciado antes que as assinaturas forem adicionadas</span><span class="sxs-lookup"><span data-stu-id="f7191-141">Connection started before subscriptions are added</span></span>

<span data-ttu-id="f7191-142">Se a conexão do Hub foi iniciado antes de métodos que podem ser chamados a partir do servidor são adicionados ao proxy, as mensagens não serão recebidas.</span><span class="sxs-lookup"><span data-stu-id="f7191-142">If the Hub's connection is started before methods that can be called from the server are added to the proxy, messages will not be received.</span></span> <span data-ttu-id="f7191-143">O seguinte código JavaScript não iniciará o hub corretamente:</span><span class="sxs-lookup"><span data-stu-id="f7191-143">The following JavaScript code will not start the hub properly:</span></span>

<span data-ttu-id="f7191-144">**Código de cliente JavaScript incorreto que não permitirá que as mensagens dos Hubs sejam recebidas**</span><span class="sxs-lookup"><span data-stu-id="f7191-144">**Incorrect JavaScript client code that will not allow Hubs messages to be received**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample5.js)]

<span data-ttu-id="f7191-145">Em vez disso, adicione as assinaturas de método antes de chamar Start:</span><span class="sxs-lookup"><span data-stu-id="f7191-145">Instead, add the method subscriptions before calling Start:</span></span>

<span data-ttu-id="f7191-146">**Código de cliente de JavaScript que adiciona corretamente as assinaturas para um hub**</span><span class="sxs-lookup"><span data-stu-id="f7191-146">**JavaScript client code that correctly adds subscriptions to a hub**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample6.js)]

### <a name="missing-method-name-on-the-hub-proxy"></a><span data-ttu-id="f7191-147">Nome do método ausente no proxy de hub</span><span class="sxs-lookup"><span data-stu-id="f7191-147">Missing method name on the hub proxy</span></span>

<span data-ttu-id="f7191-148">Verifique se que o método definido no servidor está inscrito no cliente.</span><span class="sxs-lookup"><span data-stu-id="f7191-148">Verify that the method defined on the server is subscribed to on the client.</span></span> <span data-ttu-id="f7191-149">Mesmo que o servidor define o método, ele ainda deve ser adicionado ao proxy de cliente.</span><span class="sxs-lookup"><span data-stu-id="f7191-149">Even though the server defines the method, it must still be added to the client proxy.</span></span> <span data-ttu-id="f7191-150">Métodos podem ser adicionados para o proxy do cliente das seguintes maneiras (Observe que o método é adicionado para o `client` membro do hub, não no hub diretamente):</span><span class="sxs-lookup"><span data-stu-id="f7191-150">Methods can be added to the client proxy in the following ways (Note that the method is added to the `client` member of the hub, not the hub directly):</span></span>

<span data-ttu-id="f7191-151">**Código de cliente JavaScript que adiciona métodos para um proxy de hub**</span><span class="sxs-lookup"><span data-stu-id="f7191-151">**JavaScript client code that adds methods to a hub proxy**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample7.js)]

### <a name="hub-or-hub-methods-not-declared-as-public"></a><span data-ttu-id="f7191-152">Métodos de hub não declarados como público ou hub</span><span class="sxs-lookup"><span data-stu-id="f7191-152">Hub or hub methods not declared as Public</span></span>

<span data-ttu-id="f7191-153">Para ficar visível no cliente, os métodos e a implementação de hub devem ser declarados como `public`.</span><span class="sxs-lookup"><span data-stu-id="f7191-153">To be visible on the client, the hub implementation and methods must be declared as `public`.</span></span>

### <a name="accessing-hub-from-a-different-application"></a><span data-ttu-id="f7191-154">Acessar o hub de um aplicativo diferente</span><span class="sxs-lookup"><span data-stu-id="f7191-154">Accessing hub from a different application</span></span>

<span data-ttu-id="f7191-155">Os Hubs de SignalR só pode ser acessados por meio de aplicativos que implementam os clientes do SignalR.</span><span class="sxs-lookup"><span data-stu-id="f7191-155">SignalR Hubs can only be accessed through applications that implement SignalR clients.</span></span> <span data-ttu-id="f7191-156">O SignalR não pode interoperar com outras bibliotecas de comunicação (como SOAP ou WCF web services.) Se não houver nenhum cliente SignalR disponível para sua plataforma de destino, você não pode acessar diretamente o ponto de extremidade do servidor.</span><span class="sxs-lookup"><span data-stu-id="f7191-156">SignalR cannot interoperate with other communication libraries (like SOAP or WCF web services.) If there is no SignalR client available for your target platform, you cannot access the server's endpoint directly.</span></span>

### <a name="manually-serializing-data"></a><span data-ttu-id="f7191-157">Serialização de dados manualmente</span><span class="sxs-lookup"><span data-stu-id="f7191-157">Manually serializing data</span></span>

<span data-ttu-id="f7191-158">SignalR usará automaticamente JSON para serializar seu método não é preciso de parâmetros lá fazê-lo.</span><span class="sxs-lookup"><span data-stu-id="f7191-158">SignalR will automatically use JSON to serialize your method parameters- there's no need to do it yourself.</span></span>

### <a name="remote-hub-method-not-executed-on-client-in-ondisconnected-function"></a><span data-ttu-id="f7191-159">Método de Hub remoto não é executado no cliente na função OnDisconnected</span><span class="sxs-lookup"><span data-stu-id="f7191-159">Remote Hub method not executed on client in OnDisconnected function</span></span>

<span data-ttu-id="f7191-160">Esse comportamento é padrão.</span><span class="sxs-lookup"><span data-stu-id="f7191-160">This behavior is by design.</span></span> <span data-ttu-id="f7191-161">Quando `OnDisconnected` é chamado, o hub já tiver inserido o `Disconnected` estado, o que não permite mais métodos de hub a ser chamado.</span><span class="sxs-lookup"><span data-stu-id="f7191-161">When `OnDisconnected` is called, the hub has already entered the `Disconnected` state, which does not allow further hub methods to be called.</span></span>

<span data-ttu-id="f7191-162">**C# código do servidor que executa o código no evento OnDisconnected corretamente**</span><span class="sxs-lookup"><span data-stu-id="f7191-162">**C# server code that correctly executes code in the OnDisconnected event**</span></span>

[!code-csharp[Main](troubleshooting/samples/sample8.cs)]

### <a name="connection-limit-reached"></a><span data-ttu-id="f7191-163">Limite de Conexão atingido</span><span class="sxs-lookup"><span data-stu-id="f7191-163">Connection limit reached</span></span>

<span data-ttu-id="f7191-164">Ao usar a versão completa do IIS em um sistema operacional de cliente, como o Windows 7, é imposto um limite de conexão de 10.</span><span class="sxs-lookup"><span data-stu-id="f7191-164">When using the full version of IIS on a client operating system like Windows 7, a 10-connection limit is imposed.</span></span> <span data-ttu-id="f7191-165">Ao usar um sistema operacional cliente, use o IIS Express em vez disso, para evitar esse limite.</span><span class="sxs-lookup"><span data-stu-id="f7191-165">When using a client OS, use IIS Express instead to avoid this limit.</span></span>

### <a name="cross-domain-connection-not-set-up-properly"></a><span data-ttu-id="f7191-166">Conexão de domínio cruzado não configurado corretamente</span><span class="sxs-lookup"><span data-stu-id="f7191-166">Cross-domain connection not set up properly</span></span>

<span data-ttu-id="f7191-167">Se uma conexão entre domínios (uma conexão para o qual a URL do SignalR não está no mesmo domínio que a página de hospedagem) não está configurado corretamente, a conexão pode falhar sem uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="f7191-167">If a cross-domain connection (a connection for which the SignalR URL is not in the same domain as the hosting page) is not set up correctly, the connection may fail without an error message.</span></span> <span data-ttu-id="f7191-168">Para obter informações sobre como habilitar a comunicação entre domínios, consulte [como estabelecer uma conexão entre domínios](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).</span><span class="sxs-lookup"><span data-stu-id="f7191-168">For information on how to enable cross-domain communication, see [How to establish a cross-domain connection](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).</span></span>

### <a name="connection-using-ntlm-active-directory-not-working-in-net-client"></a><span data-ttu-id="f7191-169">Conexão usando NTLM (Active Directory) não está funcionando no cliente do .NET</span><span class="sxs-lookup"><span data-stu-id="f7191-169">Connection using NTLM (Active Directory) not working in .NET client</span></span>

<span data-ttu-id="f7191-170">Uma conexão em um aplicativo de cliente .NET que usa a segurança de domínio poderá falhar se a conexão não está configurado corretamente.</span><span class="sxs-lookup"><span data-stu-id="f7191-170">A connection in a .NET client application that uses Domain security may fail if the connection is not configured properly.</span></span> <span data-ttu-id="f7191-171">Para usar o SignalR em um ambiente de domínio, defina a propriedade de conexão necessárias da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="f7191-171">To use SignalR in a domain environment, set the requisite connection property as follows:</span></span>

<span data-ttu-id="f7191-172">**Cliente código c# que implementa as credenciais de conexão**</span><span class="sxs-lookup"><span data-stu-id="f7191-172">**C# client code that implements connection credentials**</span></span>

[!code-csharp[Main](troubleshooting/samples/sample9.cs)]

<a id="other"></a>

## <a name="other-connection-issues"></a><span data-ttu-id="f7191-173">Outros problemas de conexão</span><span class="sxs-lookup"><span data-stu-id="f7191-173">Other connection issues</span></span>

<span data-ttu-id="f7191-174">Esta seção descreve as causas e soluções para os sintomas específicos ou mensagens de erro que ocorrem durante uma conexão.</span><span class="sxs-lookup"><span data-stu-id="f7191-174">This section describes the causes and solutions for specific symptoms or error messages that occur during a connection.</span></span>

### <a name="start-must-be-called-before-data-can-be-sent-error"></a><span data-ttu-id="f7191-175">Erro de "Início deve ser chamado antes de dados podem ser enviados"</span><span class="sxs-lookup"><span data-stu-id="f7191-175">"Start must be called before data can be sent" error</span></span>

<span data-ttu-id="f7191-176">Esse erro geralmente é visto se o código faz referência a objetos de SignalR antes que a conexão seja iniciada.</span><span class="sxs-lookup"><span data-stu-id="f7191-176">This error is commonly seen if code references SignalR objects before the connection is started.</span></span> <span data-ttu-id="f7191-177">O wireup para manipuladores e assim por diante que irá chamar os métodos definidos no servidor deve ser adicionados após a conexão.</span><span class="sxs-lookup"><span data-stu-id="f7191-177">The wireup for handlers and the like that will call methods defined on the server must be added after the connection completes.</span></span> <span data-ttu-id="f7191-178">Observe que a chamada para `Start` é assíncrona, portanto, o código após a chamada pode ser executada antes que ele for concluído.</span><span class="sxs-lookup"><span data-stu-id="f7191-178">Note that the call to `Start` is asynchronous, so code after the call may be executed before it completes.</span></span> <span data-ttu-id="f7191-179">A melhor maneira de adicionar manipuladores depois que uma conexão é iniciado completamente é colocá-los em uma função de retorno de chamada que é passada como um parâmetro para o método de início:</span><span class="sxs-lookup"><span data-stu-id="f7191-179">The best way to add handlers after a connection starts completely is to put them into a callback function that is passed as a parameter to the start method:</span></span>

<span data-ttu-id="f7191-180">**Código de cliente de JavaScript que adiciona corretamente os manipuladores de eventos que fazem referência a objetos de SignalR**</span><span class="sxs-lookup"><span data-stu-id="f7191-180">**JavaScript client code that correctly adds event handlers that reference SignalR objects**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample10.js?highlight=1)]

<span data-ttu-id="f7191-181">Esse erro também será visto se uma conexão for interrompido enquanto ainda estão sendo referenciados objetos SignalR.</span><span class="sxs-lookup"><span data-stu-id="f7191-181">This error will also be seen if a connection stops while SignalR objects are still being referenced.</span></span>

### <a name="301-moved-permanently-or-302-moved-temporarily-error"></a><span data-ttu-id="f7191-182">"301 movido permanentemente" ou "302 movido temporariamente" Erro</span><span class="sxs-lookup"><span data-stu-id="f7191-182">"301 Moved Permanently" or "302 Moved Temporarily" error</span></span>

<span data-ttu-id="f7191-183">Esse erro pode ser visto se o projeto contiver uma pasta chamada SignalR, que irá interferir com o proxy criado automaticamente.</span><span class="sxs-lookup"><span data-stu-id="f7191-183">This error may be seen if the project contains a folder called SignalR, which will interfere with the automatically-created proxy.</span></span> <span data-ttu-id="f7191-184">Para evitar esse erro, não use uma pasta chamada `SignalR` em seu aplicativo ou a geração de automática de proxy turn off.</span><span class="sxs-lookup"><span data-stu-id="f7191-184">To avoid this error, do not use a folder called `SignalR` in your application, or turn automatic proxy generation off.</span></span> <span data-ttu-id="f7191-185">Ver [o Proxy gerado e o que ele faz para você](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="f7191-185">See [The Generated Proxy and what it does for you](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy) for more details.</span></span>

### <a name="403-forbidden-error-in-net-or-silverlight-client"></a><span data-ttu-id="f7191-186">Erro "403 Proibido" no cliente .NET ou do Silverlight</span><span class="sxs-lookup"><span data-stu-id="f7191-186">"403 Forbidden" error in .NET or Silverlight client</span></span>

<span data-ttu-id="f7191-187">Esse erro pode ocorrer em ambientes de domínio cruzado, em que a comunicação entre domínios corretamente não está habilitada.</span><span class="sxs-lookup"><span data-stu-id="f7191-187">This error may occur in cross-domain environments where cross-domain communication is not properly enabled.</span></span> <span data-ttu-id="f7191-188">Para obter informações sobre como habilitar a comunicação entre domínios, consulte [como estabelecer uma conexão entre domínios](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).</span><span class="sxs-lookup"><span data-stu-id="f7191-188">For information on how to enable cross-domain communication, see [How to establish a cross-domain connection](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).</span></span> <span data-ttu-id="f7191-189">Para estabelecer uma conexão entre domínios em um cliente do Silverlight, consulte [conexões entre domínios de clientes do Silverlight](../guide-to-the-api/hubs-api-guide-net-client.md#slcrossdomain).</span><span class="sxs-lookup"><span data-stu-id="f7191-189">To establish a cross-domain connection in a Silverlight client, see [Cross-domain connections from Silverlight clients](../guide-to-the-api/hubs-api-guide-net-client.md#slcrossdomain).</span></span>

### <a name="404-not-found-error"></a><span data-ttu-id="f7191-190">Erro "404 não encontrado"</span><span class="sxs-lookup"><span data-stu-id="f7191-190">"404 Not Found" error</span></span>

<span data-ttu-id="f7191-191">Há várias causas para esse problema.</span><span class="sxs-lookup"><span data-stu-id="f7191-191">There are several causes for this issue.</span></span> <span data-ttu-id="f7191-192">Verifique se todas as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="f7191-192">Verify all of the following:</span></span>

- <span data-ttu-id="f7191-193">**Referência de endereço de proxy de Hub não está formatada corretamente:** Esse erro geralmente é visto se a referência para o endereço de proxy de hub gerado não está formatada corretamente.</span><span class="sxs-lookup"><span data-stu-id="f7191-193">**Hub proxy address reference not formatted correctly:** This error is commonly seen if the reference to the generated hub proxy address is not formatted correctly.</span></span> <span data-ttu-id="f7191-194">Verifique se que a referência para o endereço do hub é feita corretamente.</span><span class="sxs-lookup"><span data-stu-id="f7191-194">Verify that the reference to the hub address is made properly.</span></span> <span data-ttu-id="f7191-195">Ver [como referenciar o proxy gerado dinamicamente](../guide-to-the-api/hubs-api-guide-javascript-client.md#dynamicproxy) para obter detalhes.</span><span class="sxs-lookup"><span data-stu-id="f7191-195">See [How to reference the dynamically generated proxy](../guide-to-the-api/hubs-api-guide-javascript-client.md#dynamicproxy) for details.</span></span>
- <span data-ttu-id="f7191-196">**Adicionar rotas ao aplicativo antes de adicionar a rota do hub:** Se seu aplicativo usa outras rotas, verifique se a primeira rota adicionada é a chamada para `MapHubs`.</span><span class="sxs-lookup"><span data-stu-id="f7191-196">**Adding routes to application before adding the hub route:** If your application uses other routes, verify that the first route added is the call to `MapHubs`.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="f7191-197">"Erro de servidor interno 500"</span><span class="sxs-lookup"><span data-stu-id="f7191-197">"500 Internal Server Error"</span></span>

<span data-ttu-id="f7191-198">Esse é um erro muito genérico que pode ter uma ampla variedade de causas.</span><span class="sxs-lookup"><span data-stu-id="f7191-198">This is a very generic error that could have a wide variety of causes.</span></span> <span data-ttu-id="f7191-199">Os detalhes do erro devem aparecer no log de eventos do servidor, ou podem ser encontrados usando o servidor de depuração.</span><span class="sxs-lookup"><span data-stu-id="f7191-199">The details of the error should appear in the server's event log, or can be found through debugging the server.</span></span> <span data-ttu-id="f7191-200">Informações de erro mais detalhadas podem ser obtidas por meio da ativação erros detalhados no servidor.</span><span class="sxs-lookup"><span data-stu-id="f7191-200">More detailed error information may be obtained by turning on detailed errors on the server.</span></span> <span data-ttu-id="f7191-201">Para obter mais informações, consulte [como tratar erros na classe Hub](../guide-to-the-api/hubs-api-guide-server.md#handleErrors).</span><span class="sxs-lookup"><span data-stu-id="f7191-201">For more information, see [How to handle errors in the Hub class](../guide-to-the-api/hubs-api-guide-server.md#handleErrors).</span></span>

### <a name="typeerror-lthubtypegt-is-undefined-error"></a><span data-ttu-id="f7191-202">"TypeError: &lt;hubType&gt; é indefinido" Erro</span><span class="sxs-lookup"><span data-stu-id="f7191-202">"TypeError: &lt;hubType&gt; is undefined" error</span></span>

<span data-ttu-id="f7191-203">Esse erro ocorrerá se a chamada para `MapHubs` não é feita corretamente.</span><span class="sxs-lookup"><span data-stu-id="f7191-203">This error will result if the call to `MapHubs` is not made properly.</span></span> <span data-ttu-id="f7191-204">Ver [como registrar o roteiro de SignalR e configurar as opções de SignalR](../guide-to-the-api/hubs-api-guide-server.md#route) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="f7191-204">See [How to register the SignalR route and configure SignalR options](../guide-to-the-api/hubs-api-guide-server.md#route) for more information.</span></span>

### <a name="jsonserializationexception-was-unhandled-by-user-code"></a><span data-ttu-id="f7191-205">JsonSerializationException não foi tratada pelo código do usuário</span><span class="sxs-lookup"><span data-stu-id="f7191-205">JsonSerializationException was unhandled by user code</span></span>

<span data-ttu-id="f7191-206">Verifique se que os parâmetros que você enviar a seus métodos não incluem os tipos não serializáveis (como identificadores de arquivos ou conexões de banco de dados).</span><span class="sxs-lookup"><span data-stu-id="f7191-206">Verify that the parameters you send to your methods do not include non-serializable types (like file handles or database connections).</span></span> <span data-ttu-id="f7191-207">Se você precisar usar os membros em um objeto do lado do servidor que você não deseja ser enviada ao cliente (ou para segurança ou por motivos de serialização), use o `JSONIgnore` atributo.</span><span class="sxs-lookup"><span data-stu-id="f7191-207">If you need to use members on a server-side object that you don't want to be sent to the client (either for security or for reasons of serialization), use the `JSONIgnore` attribute.</span></span>

### <a name="protocol-error-unknown-transport-error"></a><span data-ttu-id="f7191-208">"Erro de protocolo: Erro de transporte desconhecido"</span><span class="sxs-lookup"><span data-stu-id="f7191-208">"Protocol error: Unknown transport" error</span></span>

<span data-ttu-id="f7191-209">Esse erro pode ocorrer se o cliente não oferece suporte para os transportes que usa o SignalR.</span><span class="sxs-lookup"><span data-stu-id="f7191-209">This error may occur if the client does not support the transports that SignalR uses.</span></span> <span data-ttu-id="f7191-210">Ver [transportes e Fallbacks](../getting-started/introduction-to-signalr.md#transports) para obter informações no qual os navegadores podem ser usados com o SignalR.</span><span class="sxs-lookup"><span data-stu-id="f7191-210">See [Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports) for information on which browsers can be used with SignalR.</span></span>

### <a name="javascript-hub-proxy-generation-has-been-disabled"></a><span data-ttu-id="f7191-211">"Geração de proxy de JavaScript Hub foi desabilitada."</span><span class="sxs-lookup"><span data-stu-id="f7191-211">"JavaScript Hub proxy generation has been disabled."</span></span>

<span data-ttu-id="f7191-212">Esse erro ocorrerá se `DisableJavaScriptProxies` está definida enquanto também inclui uma referência para o proxy gerado dinamicamente no `signalr/hubs`.</span><span class="sxs-lookup"><span data-stu-id="f7191-212">This error will occur if `DisableJavaScriptProxies` is set while also including a reference to the dynamically generated proxy at `signalr/hubs`.</span></span> <span data-ttu-id="f7191-213">Para obter mais informações sobre como criar o proxy manualmente, consulte [proxy gerado e o que ele faz para você](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy).</span><span class="sxs-lookup"><span data-stu-id="f7191-213">For more information on creating the proxy manually, see [The generated proxy and what it does for you](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy).</span></span>

### <a name="the-connection-id-is-in-the-incorrect-format-or-the-user-identity-cannot-change-during-an-active-signalr-connection-error"></a><span data-ttu-id="f7191-214">"A ID de conexão está no formato incorreto" ou "a identidade do usuário não pode alterar durante uma conexão SignalR active" Erro</span><span class="sxs-lookup"><span data-stu-id="f7191-214">"The connection ID is in the incorrect format" or "The user identity cannot change during an active SignalR connection" error</span></span>

<span data-ttu-id="f7191-215">Esse erro pode ser visto se a autenticação está sendo usada, e o cliente é desconectado antes que a conexão é interrompida.</span><span class="sxs-lookup"><span data-stu-id="f7191-215">This error may be seen if authentication is being used, and the client is logged out before the connection is stopped.</span></span> <span data-ttu-id="f7191-216">A solução é interromper a conexão do SignalR antes de sair do cliente.</span><span class="sxs-lookup"><span data-stu-id="f7191-216">The solution is to stop the SignalR connection before logging the client out.</span></span>

### <a name="uncaught-error-signalr-jquery-not-found-please-ensure-jquery-is-referenced-before-the-signalrjs-file-error"></a><span data-ttu-id="f7191-217">"Não capturada erro: SignalR: jQuery não foi encontrado.</span><span class="sxs-lookup"><span data-stu-id="f7191-217">"Uncaught Error: SignalR: jQuery not found.</span></span> <span data-ttu-id="f7191-218">Verifique se o jQuery é referenciado antes do arquivo SignalR.js"Erro</span><span class="sxs-lookup"><span data-stu-id="f7191-218">Please ensure jQuery is referenced before the SignalR.js file" error</span></span>

<span data-ttu-id="f7191-219">O cliente SignalR JavaScript exige jQuery para ser executado.</span><span class="sxs-lookup"><span data-stu-id="f7191-219">The SignalR JavaScript client requires jQuery to run.</span></span> <span data-ttu-id="f7191-220">Verifique se sua referência para o jQuery está correta, se o caminho usado é válido e que a referência para o jQuery é antes da referência ao SignalR.</span><span class="sxs-lookup"><span data-stu-id="f7191-220">Verify that your reference to jQuery is correct, that the path used is valid, and that the reference to jQuery is before the reference to SignalR.</span></span>

### <a name="uncaught-typeerror-cannot-read-property-ltpropertygt-of-undefined-error"></a><span data-ttu-id="f7191-221">"Não capturada TypeError: Não é possível ler a propriedade '&lt;propriedade&gt;' indefinido "Erro</span><span class="sxs-lookup"><span data-stu-id="f7191-221">"Uncaught TypeError: Cannot read property '&lt;property&gt;' of undefined" error</span></span>

<span data-ttu-id="f7191-222">Esse erro resulta da falta jQuery ou o proxy de hubs referenciado corretamente.</span><span class="sxs-lookup"><span data-stu-id="f7191-222">This error results from not having jQuery or the hubs proxy referenced properly.</span></span> <span data-ttu-id="f7191-223">Verifique se sua referência para o jQuery e o proxy de hubs está correta, se o caminho usado é válido e que a referência para o jQuery é antes da referência para o proxy de hubs.</span><span class="sxs-lookup"><span data-stu-id="f7191-223">Verify that your reference to jQuery and the hubs proxy is correct, that the path used is valid, and that the reference to jQuery is before the reference to the hubs proxy.</span></span> <span data-ttu-id="f7191-224">A referência ao proxy hubs deve ser semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="f7191-224">The default reference to the hubs proxy should look like the following:</span></span>

<span data-ttu-id="f7191-225">**Código do lado do cliente HTML que referencia corretamente o proxy de Hubs**</span><span class="sxs-lookup"><span data-stu-id="f7191-225">**HTML client-side code that correctly references the Hubs proxy**</span></span>

[!code-html[Main](troubleshooting/samples/sample11.html)]

### <a name="runtimebinderexception-was-unhandled-by-user-code-error"></a><span data-ttu-id="f7191-226">Erro de "RuntimeBinderException não foi tratada pelo código do usuário"</span><span class="sxs-lookup"><span data-stu-id="f7191-226">"RuntimeBinderException was unhandled by user code" error</span></span>

<span data-ttu-id="f7191-227">Esse erro pode ocorrer quando a sobrecarga incorreta de `Hub.On` é usado.</span><span class="sxs-lookup"><span data-stu-id="f7191-227">This error may occur when the incorrect overload of `Hub.On` is used.</span></span> <span data-ttu-id="f7191-228">Se o método tiver um valor de retorno, o tipo de retorno deve ser especificado como um parâmetro de tipo genérico:</span><span class="sxs-lookup"><span data-stu-id="f7191-228">If the method has a return value, the return type must be specified as a generic type parameter:</span></span>

<span data-ttu-id="f7191-229">**Método definido no cliente (sem proxy gerada)**</span><span class="sxs-lookup"><span data-stu-id="f7191-229">**Method defined on the client (without generated proxy)**</span></span>

[!code-html[Main](troubleshooting/samples/sample12.html?highlight=1)]

### <a name="connection-id-is-inconsistent-or-connection-breaks-between-page-loads"></a><span data-ttu-id="f7191-230">ID de Conexão é inconsistente ou quebras de conexão entre os carregamentos de página</span><span class="sxs-lookup"><span data-stu-id="f7191-230">Connection ID is inconsistent or connection breaks between page loads</span></span>

<span data-ttu-id="f7191-231">Esse comportamento é padrão.</span><span class="sxs-lookup"><span data-stu-id="f7191-231">This behavior is by design.</span></span> <span data-ttu-id="f7191-232">Uma vez que o objeto de hub está hospedado no objeto de página, o hub é destruído quando a página for atualizada.</span><span class="sxs-lookup"><span data-stu-id="f7191-232">Since the hub object is hosted in the page object, the hub is destroyed when the page refreshes.</span></span> <span data-ttu-id="f7191-233">Precisa de um aplicativo de várias página manter a associação entre usuários e IDs de conexão para que eles serão consistentes entre os carregamentos de página.</span><span class="sxs-lookup"><span data-stu-id="f7191-233">A multi-page application needs to maintain the association between users and connection IDs so that they will be consistent between page loads.</span></span> <span data-ttu-id="f7191-234">A IDs de conexão pode ser armazenado no servidor em qualquer um uma `ConcurrentDictionary` objeto ou um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f7191-234">The connection IDs can be stored on the server in either a `ConcurrentDictionary` object or a database.</span></span>

### <a name="value-cannot-be-null-error"></a><span data-ttu-id="f7191-235">Erro "O valor não pode ser nulo"</span><span class="sxs-lookup"><span data-stu-id="f7191-235">"Value cannot be null" error</span></span>

<span data-ttu-id="f7191-236">Atualmente, não há suporte para métodos do lado do servidor com parâmetros opcionais; Se o parâmetro opcional for omitido, o método falhará.</span><span class="sxs-lookup"><span data-stu-id="f7191-236">Server-side methods with optional parameters are not currently supported; if the optional parameter is omitted, the method will fail.</span></span> <span data-ttu-id="f7191-237">Para obter mais informações, consulte [parâmetros opcionais](https://github.com/SignalR/SignalR/issues/324).</span><span class="sxs-lookup"><span data-stu-id="f7191-237">For more information, see [Optional Parameters](https://github.com/SignalR/SignalR/issues/324).</span></span>

### <a name="firefox-cant-establish-a-connection-to-the-server-at-ltaddressgt-error-in-firebug"></a><span data-ttu-id="f7191-238">"Firefox não é possível estabelecer uma conexão ao servidor em &lt;endereço&gt;" Erro no Firebug</span><span class="sxs-lookup"><span data-stu-id="f7191-238">"Firefox can't establish a connection to the server at &lt;address&gt;" error in Firebug</span></span>

<span data-ttu-id="f7191-239">Se a negociação de transporte de WebSocket falhar e outro transporte é usada em vez disso, essa mensagem de erro pode ser vista no Firebug.</span><span class="sxs-lookup"><span data-stu-id="f7191-239">This error message can be seen in Firebug if negotiation of the WebSocket transport fails and another transport is used instead.</span></span> <span data-ttu-id="f7191-240">Esse comportamento é padrão.</span><span class="sxs-lookup"><span data-stu-id="f7191-240">This behavior is by design.</span></span>

### <a name="the-remote-certificate-is-invalid-according-to-the-validation-procedure-error-in-net-client-application"></a><span data-ttu-id="f7191-241">Erro "o certificado remoto é inválido de acordo com o procedimento de validação" no aplicativo cliente do .NET</span><span class="sxs-lookup"><span data-stu-id="f7191-241">"The remote certificate is invalid according to the validation procedure" error in .NET client application</span></span>

<span data-ttu-id="f7191-242">Se o servidor exigir certificados de cliente personalizadas, em seguida, você pode adicionar um x509certificate para a conexão antes da solicitação é feita.</span><span class="sxs-lookup"><span data-stu-id="f7191-242">If your server requires custom client certificates, then you can add an x509certificate to the connection before the request is made.</span></span> <span data-ttu-id="f7191-243">Adicionar o certificado para a conexão usando `Connection.AddClientCertificate`.</span><span class="sxs-lookup"><span data-stu-id="f7191-243">Add the certificate to the connection using `Connection.AddClientCertificate`.</span></span>

### <a name="connection-drops-after-authentication-times-out"></a><span data-ttu-id="f7191-244">Conexão cair após a autenticação expira</span><span class="sxs-lookup"><span data-stu-id="f7191-244">Connection drops after authentication times out</span></span>

<span data-ttu-id="f7191-245">Esse comportamento é padrão.</span><span class="sxs-lookup"><span data-stu-id="f7191-245">This behavior is by design.</span></span> <span data-ttu-id="f7191-246">As credenciais de autenticação não podem ser modificadas enquanto uma conexão estiver ativa; Para atualizar as credenciais, a conexão deve ser interrompido e reiniciado.</span><span class="sxs-lookup"><span data-stu-id="f7191-246">Authentication credentials cannot be modified while a connection is active; to refresh credentials, the connection must be stopped and restarted.</span></span>

### <a name="onconnected-gets-called-twice-when-using-jquery-mobile"></a><span data-ttu-id="f7191-247">OnConnected é chamado duas vezes ao usar o jQuery Mobile</span><span class="sxs-lookup"><span data-stu-id="f7191-247">OnConnected gets called twice when using jQuery Mobile</span></span>

<span data-ttu-id="f7191-248">jQuery Mobile `initializePage` função força os scripts em cada página para ser executado novamente, criando assim uma segunda conexão.</span><span class="sxs-lookup"><span data-stu-id="f7191-248">jQuery Mobile's `initializePage` function forces the scripts in each page to be re-executed, thus creating a second connection.</span></span> <span data-ttu-id="f7191-249">As soluções para esse problema incluem:</span><span class="sxs-lookup"><span data-stu-id="f7191-249">Solutions for this issue include:</span></span>

- <span data-ttu-id="f7191-250">Inclua a referência a jQuery Mobile antes de seu arquivo JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f7191-250">Include the reference to jQuery Mobile before your JavaScript file.</span></span>
- <span data-ttu-id="f7191-251">Desabilitar a `initializePage` função definindo `$.mobile.autoInitializePage = false`.</span><span class="sxs-lookup"><span data-stu-id="f7191-251">Disable the `initializePage` function by setting `$.mobile.autoInitializePage = false`.</span></span>
- <span data-ttu-id="f7191-252">Aguarde até a página termine a inicialização antes de iniciar a conexão.</span><span class="sxs-lookup"><span data-stu-id="f7191-252">Wait for the page to finish initializing before starting the connection.</span></span>

### <a name="messages-are-delayed-in-silverlight-applications-using-server-sent-events"></a><span data-ttu-id="f7191-253">As mensagens estão atrasadas em aplicativos do Silverlight usando eventos enviados do servidor</span><span class="sxs-lookup"><span data-stu-id="f7191-253">Messages are delayed in Silverlight applications using Server Sent Events</span></span>

<span data-ttu-id="f7191-254">As mensagens estão atrasadas quando usando servidor enviado eventos em Silverlight.</span><span class="sxs-lookup"><span data-stu-id="f7191-254">Messages are delayed when using server sent events on Silverlight.</span></span> <span data-ttu-id="f7191-255">Para forçar o longo de sondagem para ser usado em vez disso, use o seguinte ao iniciar a conexão:</span><span class="sxs-lookup"><span data-stu-id="f7191-255">To force long polling to be used instead, use the following when starting the connection:</span></span>

[!code-css[Main](troubleshooting/samples/sample13.css)]

### <a name="permission-denied-using-forever-frame-protocol"></a><span data-ttu-id="f7191-256">Para sempre "Permissão Denied" usando protocolo de quadro</span><span class="sxs-lookup"><span data-stu-id="f7191-256">"Permission Denied" using Forever Frame protocol</span></span>

<span data-ttu-id="f7191-257">Esse é um problema conhecido, descrito [aqui](https://github.com/SignalR/SignalR/issues/1963).</span><span class="sxs-lookup"><span data-stu-id="f7191-257">This is a known issue, described [here](https://github.com/SignalR/SignalR/issues/1963).</span></span> <span data-ttu-id="f7191-258">Este sintoma pode ser visto usando a biblioteca mais recente de JQuery; a solução alternativa é fazer o downgrade do seu aplicativo para o JQuery 1.8.2.</span><span class="sxs-lookup"><span data-stu-id="f7191-258">This symptom may be seen using the latest JQuery library; the workaround is to downgrade your application to JQuery 1.8.2.</span></span>

<a id="server"></a>

## <a name="compilation-and-server-side-errors"></a><span data-ttu-id="f7191-259">Erros de compilação e do lado do servidor</span><span class="sxs-lookup"><span data-stu-id="f7191-259">Compilation and server-side errors</span></span>

 <span data-ttu-id="f7191-260">A seção a seguir contém as soluções possíveis para o compilador e erros de tempo de execução do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="f7191-260">The following section contains possible solutions to compiler and server-side runtime errors.</span></span> 

### <a name="reference-to-hub-instance-is-null"></a><span data-ttu-id="f7191-261">Referência à instância de Hub é nula</span><span class="sxs-lookup"><span data-stu-id="f7191-261">Reference to Hub instance is null</span></span>

<span data-ttu-id="f7191-262">Uma vez que uma instância de hub é criada para cada conexão, é possível criar uma instância de um hub em seu código por conta própria.</span><span class="sxs-lookup"><span data-stu-id="f7191-262">Since a hub instance is created for each connection, you can't create an instance of a hub in your code yourself.</span></span> <span data-ttu-id="f7191-263">Para chamar métodos em um hub de fora do próprio hub, consulte [como chamar métodos de cliente e gerenciar grupos de fora da classe Hub](../guide-to-the-api/hubs-api-guide-server.md#callfromoutsidehub) para saber como obter uma referência para o contexto do hub.</span><span class="sxs-lookup"><span data-stu-id="f7191-263">To call methods on a hub from outside the hub itself, see [How to call client methods and manage groups from outside the Hub class](../guide-to-the-api/hubs-api-guide-server.md#callfromoutsidehub) for how to obtain a reference to the hub context.</span></span>

### <a name="httpcontextcurrentsession-is-null"></a><span data-ttu-id="f7191-264">HTTPContext.Current.Session é nulo</span><span class="sxs-lookup"><span data-stu-id="f7191-264">HTTPContext.Current.Session is null</span></span>

<span data-ttu-id="f7191-265">Esse comportamento é padrão.</span><span class="sxs-lookup"><span data-stu-id="f7191-265">This behavior is by design.</span></span> <span data-ttu-id="f7191-266">SignalR não oferece suporte para o estado de sessão do ASP.NET, como habilitar o estado de sessão interrompe de mensagens duplex.</span><span class="sxs-lookup"><span data-stu-id="f7191-266">SignalR does not support the ASP.NET session state, since enabling the session state would break duplex messaging.</span></span>

### <a name="no-suitable-method-to-override"></a><span data-ttu-id="f7191-267">Nenhum método adequado para substituição</span><span class="sxs-lookup"><span data-stu-id="f7191-267">No suitable method to override</span></span>

<span data-ttu-id="f7191-268">Você poderá ver esse erro se você estiver usando o código de documentação mais antiga ou blogs.</span><span class="sxs-lookup"><span data-stu-id="f7191-268">You may see this error if you are using code from older documentation or blogs.</span></span> <span data-ttu-id="f7191-269">Verifique se que você não está referenciando os nomes de métodos que foram alterados ou preteridos (como `OnConnectedAsync`).</span><span class="sxs-lookup"><span data-stu-id="f7191-269">Verify that you are not referencing names of methods that have been changed or deprecated (like `OnConnectedAsync`).</span></span>

### <a name="hostcontextextensionswebsocketserverurl-is-null"></a><span data-ttu-id="f7191-270">HostContextExtensions.WebSocketServerUrl é nulo</span><span class="sxs-lookup"><span data-stu-id="f7191-270">HostContextExtensions.WebSocketServerUrl is null</span></span>

<span data-ttu-id="f7191-271">Esse comportamento é padrão.</span><span class="sxs-lookup"><span data-stu-id="f7191-271">This behavior is by design.</span></span> <span data-ttu-id="f7191-272">Este membro foi preterido e não deve ser usado.</span><span class="sxs-lookup"><span data-stu-id="f7191-272">This member is deprecated and should not be used.</span></span>

### <a name="a-route-named-signalrhubs-is-already-in-the-route-collection-error"></a><span data-ttu-id="f7191-273">Erro de "uma rota denominada 'signalr.hubs' já está na coleção de rotas"</span><span class="sxs-lookup"><span data-stu-id="f7191-273">"A route named 'signalr.hubs' is already in the route collection" error</span></span>

<span data-ttu-id="f7191-274">Esse erro será exibido se `MapHubs` é chamado duas vezes por seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f7191-274">This error will be seen if `MapHubs` is called twice by your application.</span></span> <span data-ttu-id="f7191-275">Alguns aplicativos de exemplo chamada `MapHubs` diretamente no arquivo de aplicativo global; outros fazer a chamada em uma classe wrapper.</span><span class="sxs-lookup"><span data-stu-id="f7191-275">Some example applications call `MapHubs` directly in the global application file; others make the call in a wrapper class.</span></span> <span data-ttu-id="f7191-276">Certifique-se de que seu aplicativo não ambos.</span><span class="sxs-lookup"><span data-stu-id="f7191-276">Ensure that your application does not do both.</span></span>

<a id="vs"></a>

## <a name="visual-studio-issues"></a><span data-ttu-id="f7191-277">Problemas do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7191-277">Visual Studio issues</span></span>

<span data-ttu-id="f7191-278">Esta seção descreve os problemas encontrados no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f7191-278">This section describes issues encountered in Visual Studio.</span></span>

### <a name="script-documents-node-does-not-appear-in-solution-explorer"></a><span data-ttu-id="f7191-279">Nó de documentos de script não aparece no Gerenciador de soluções</span><span class="sxs-lookup"><span data-stu-id="f7191-279">Script Documents node does not appear in Solution Explorer</span></span>

<span data-ttu-id="f7191-280">Alguns dos nossos tutoriais de direcioná-lo para o nó de "Documentos de Script" no Gerenciador de soluções durante a depuração.</span><span class="sxs-lookup"><span data-stu-id="f7191-280">Some of our tutorials direct you to the "Script Documents" node in Solution Explorer while debugging.</span></span> <span data-ttu-id="f7191-281">Esse nó é produzido pelo depurador JavaScript e só será exibida durante a depuração de clientes de navegador no Internet Explorer; o nó não aparecerão se forem usado Chrome ou Firefox.</span><span class="sxs-lookup"><span data-stu-id="f7191-281">This node is produced by the JavaScript debugger, and will only appear while debugging browser clients in Internet Explorer; the node will not appear if Chrome or Firefox are used.</span></span> <span data-ttu-id="f7191-282">O depurador JavaScript também não será executado se outro depurador de cliente é executado, como o depurador do Silverlight.</span><span class="sxs-lookup"><span data-stu-id="f7191-282">The JavaScript debugger will also not run if another client debugger is running, such as the Silverlight debugger.</span></span>

### <a name="signalr-does-not-work-on-visual-studio-2008-or-earlier"></a><span data-ttu-id="f7191-283">O SignalR não funciona no Visual Studio 2008 ou anterior</span><span class="sxs-lookup"><span data-stu-id="f7191-283">SignalR does not work on Visual Studio 2008 or earlier</span></span>

<span data-ttu-id="f7191-284">Esse comportamento é padrão.</span><span class="sxs-lookup"><span data-stu-id="f7191-284">This behavior is by design.</span></span> <span data-ttu-id="f7191-285">O SignalR requer o .NET Framework 4 ou posterior; Isso exige que os aplicativos do SignalR ser desenvolvidos no Visual Studio 2010 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="f7191-285">SignalR requires .NET Framework 4 or later; this requires that SignalR applications be developed in Visual Studio 2010 or later.</span></span>

<a id="iis"></a>

## <a name="iis-issues"></a><span data-ttu-id="f7191-286">Problemas do IIS</span><span class="sxs-lookup"><span data-stu-id="f7191-286">IIS issues</span></span>

<span data-ttu-id="f7191-287">Esta seção contém problemas com os serviços de informações da Internet.</span><span class="sxs-lookup"><span data-stu-id="f7191-287">This section contains issues with Internet Information Services.</span></span>

### <a name="web-site-crashes-after-maphubs-call"></a><span data-ttu-id="f7191-288">Site da Web falha depois de chamar MapHubs</span><span class="sxs-lookup"><span data-stu-id="f7191-288">Web site crashes after MapHubs call</span></span>

<span data-ttu-id="f7191-289">Esse problema foi corrigido na versão mais recente do SignalR.</span><span class="sxs-lookup"><span data-stu-id="f7191-289">This issue has been fixed in the latest version of SignalR.</span></span> <span data-ttu-id="f7191-290">Verifique se você está usando a versão lançada mais recente do SignalR ao atualizar sua instalação usando o NuGet.</span><span class="sxs-lookup"><span data-stu-id="f7191-290">Verify that you are using the latest released version of SignalR by updating your installation using NuGet.</span></span>

<a id="azure"></a>

## <a name="azure-issues"></a><span data-ttu-id="f7191-291">Problemas do Azure</span><span class="sxs-lookup"><span data-stu-id="f7191-291">Azure issues</span></span>

<span data-ttu-id="f7191-292">Esta seção contém problemas com o Microsoft Azure.</span><span class="sxs-lookup"><span data-stu-id="f7191-292">This section contains issues with Microsoft Azure.</span></span>

### <a name="messages-are-not-received-through-the-azure-backplane-after-altering-topic-names"></a><span data-ttu-id="f7191-293">As mensagens não são recebidas por meio do backplane do Azure após a alteração de nomes de tópico</span><span class="sxs-lookup"><span data-stu-id="f7191-293">Messages are not received through the Azure backplane after altering topic names</span></span>

<span data-ttu-id="f7191-294">Os tópicos usados pelo Azure backplane não pretendem ser configurável pelo usuário.</span><span class="sxs-lookup"><span data-stu-id="f7191-294">The topics used by the Azure backplane are not intended to be user-configurable.</span></span>
