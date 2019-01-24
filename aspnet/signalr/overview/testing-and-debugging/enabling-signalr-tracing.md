---
uid: signalr/overview/testing-and-debugging/enabling-signalr-tracing
title: Habilitar o rastreamento do SignalR | Microsoft Docs
author: bradygaster
description: Este documento descreve como habilitar e configurar o rastreamento de clientes e servidores do SignalR. O rastreamento permite que você exiba informações sobre eventos de diagnóstico...
ms.author: bradyg
ms.date: 08/08/2014
ms.assetid: 30060acb-be3e-4347-996f-3870f0c37829
msc.legacyurl: /signalr/overview/testing-and-debugging/enabling-signalr-tracing
msc.type: authoredcontent
ms.openlocfilehash: c6515e3653798ef50e2d2dcb7354ffed407a190e
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836461"
---
<a name="enabling-signalr-tracing"></a><span data-ttu-id="22fcc-104">Habilitar o rastreamento do SignalR</span><span class="sxs-lookup"><span data-stu-id="22fcc-104">Enabling SignalR Tracing</span></span>
====================
<span data-ttu-id="22fcc-105">por [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="22fcc-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="22fcc-106">Este documento descreve como habilitar e configurar o rastreamento de clientes e servidores do SignalR.</span><span class="sxs-lookup"><span data-stu-id="22fcc-106">This document describes how to enable and configure tracing for SignalR servers and clients.</span></span> <span data-ttu-id="22fcc-107">O rastreamento permite que você exiba informações sobre eventos de diagnóstico em seu aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="22fcc-107">Tracing enables you to view diagnostic information about events in your SignalR application.</span></span>
>
> <span data-ttu-id="22fcc-108">Este tópico foi originalmente escrito por Patrick Fletcher.</span><span class="sxs-lookup"><span data-stu-id="22fcc-108">This topic was originally written by Patrick Fletcher.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="22fcc-109">Versões de software usadas no tutorial</span><span class="sxs-lookup"><span data-stu-id="22fcc-109">Software versions used in the tutorial</span></span>
>
>
> - [<span data-ttu-id="22fcc-110">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="22fcc-110">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="22fcc-111">.NET Framework 4.5</span><span class="sxs-lookup"><span data-stu-id="22fcc-111">.NET Framework 4.5</span></span>
> - <span data-ttu-id="22fcc-112">Versão 2 do SignalR</span><span class="sxs-lookup"><span data-stu-id="22fcc-112">SignalR version 2</span></span>
>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="22fcc-113">Perguntas e comentários</span><span class="sxs-lookup"><span data-stu-id="22fcc-113">Questions and comments</span></span>
>
> <span data-ttu-id="22fcc-114">Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="22fcc-114">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="22fcc-115">Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="22fcc-115">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="22fcc-116">Quando o rastreamento está habilitado, um aplicativo de SignalR cria entradas de log de eventos.</span><span class="sxs-lookup"><span data-stu-id="22fcc-116">When tracing is enabled, a SignalR application creates log entries for events.</span></span> <span data-ttu-id="22fcc-117">Você pode registrar eventos de cliente e o servidor.</span><span class="sxs-lookup"><span data-stu-id="22fcc-117">You can log events from both the client and the server.</span></span> <span data-ttu-id="22fcc-118">Rastreamento na conexão de logs do servidor, provedor de expansão e eventos do barramento de mensagem.</span><span class="sxs-lookup"><span data-stu-id="22fcc-118">Tracing on the server logs connection, scaleout provider, and message bus events.</span></span> <span data-ttu-id="22fcc-119">Rastreamento sobre os cliente registra eventos de conexão.</span><span class="sxs-lookup"><span data-stu-id="22fcc-119">Tracing on the client logs connection events.</span></span> <span data-ttu-id="22fcc-120">No SignalR 2.1 e posterior, o rastreamento no cliente registra o conteúdo completo das mensagens de invocação de hub.</span><span class="sxs-lookup"><span data-stu-id="22fcc-120">In SignalR 2.1 and later, tracing on the client logs the full content of hub invocation messages.</span></span>

## <a name="contents"></a><span data-ttu-id="22fcc-121">Conteúdo</span><span class="sxs-lookup"><span data-stu-id="22fcc-121">Contents</span></span>

- [<span data-ttu-id="22fcc-122">Habilitando o rastreamento no servidor</span><span class="sxs-lookup"><span data-stu-id="22fcc-122">Enabling tracing on the server</span></span>](#server)

    - [<span data-ttu-id="22fcc-123">Log de eventos do servidor para arquivos de texto</span><span class="sxs-lookup"><span data-stu-id="22fcc-123">Logging server events to text files</span></span>](#server_text)
    - [<span data-ttu-id="22fcc-124">Log de eventos de servidor para o log de eventos</span><span class="sxs-lookup"><span data-stu-id="22fcc-124">Logging server events to the event log</span></span>](#server_eventlog)
- [<span data-ttu-id="22fcc-125">Habilitando o rastreamento no cliente .NET (aplicativos da área de trabalho do Windows)</span><span class="sxs-lookup"><span data-stu-id="22fcc-125">Enabling tracing in the .NET client (Windows Desktop apps)</span></span>](#net_client)

    - [<span data-ttu-id="22fcc-126">Log de eventos de cliente da área de trabalho para o console</span><span class="sxs-lookup"><span data-stu-id="22fcc-126">Logging Desktop client events to the console</span></span>](#desktop_console)
    - [<span data-ttu-id="22fcc-127">Log de eventos de cliente da área de trabalho para um arquivo de texto</span><span class="sxs-lookup"><span data-stu-id="22fcc-127">Logging Desktop client events to a text file</span></span>](#desktop_text)
- [<span data-ttu-id="22fcc-128">Habilitando o rastreamento em clientes do Windows Phone 8</span><span class="sxs-lookup"><span data-stu-id="22fcc-128">Enabling tracing in Windows Phone 8 clients</span></span>](#phone)

    - [<span data-ttu-id="22fcc-129">Log de eventos do cliente do Windows Phone na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="22fcc-129">Logging Windows Phone client events to the UI</span></span>](#phone_ui)
    - [<span data-ttu-id="22fcc-130">Log de eventos do cliente do Windows Phone para o console de depuração</span><span class="sxs-lookup"><span data-stu-id="22fcc-130">Logging Windows Phone client events to the debug console</span></span>](#phone_debug)
- [<span data-ttu-id="22fcc-131">Habilitando o rastreamento no cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="22fcc-131">Enabling tracing in the JavaScript client</span></span>](#javascript)

<a id="server"></a>
## <a name="enabling-tracing-on-the-server"></a><span data-ttu-id="22fcc-132">Habilitando o rastreamento no servidor</span><span class="sxs-lookup"><span data-stu-id="22fcc-132">Enabling tracing on the server</span></span>

<span data-ttu-id="22fcc-133">Habilitar o rastreamento no servidor dentro do arquivo de configuração do aplicativo (App. config ou Web. config, dependendo do tipo de projeto.) Especificar a quais categorias de eventos que você deseja registrar.</span><span class="sxs-lookup"><span data-stu-id="22fcc-133">You enable tracing on the server within the application's configuration file (either App.config or Web.config depending on the type of project.) You specify which categories of events you want to log.</span></span> <span data-ttu-id="22fcc-134">No arquivo de configuração, você também especifica se deseja registrar os eventos para um arquivo de texto, o log de eventos do Windows ou um log personalizado usando uma implementação do [TraceListener](https://msdn.microsoft.com/library/system.diagnostics.tracelistener(v=vs.110).aspx).</span><span class="sxs-lookup"><span data-stu-id="22fcc-134">In the configuration file, you also specify whether to log the events to a text file, the Windows event log, or a custom log using an implementation of [TraceListener](https://msdn.microsoft.com/library/system.diagnostics.tracelistener(v=vs.110).aspx).</span></span>

<span data-ttu-id="22fcc-135">As categorias de eventos de servidor incluem as seguintes classificações de mensagens:</span><span class="sxs-lookup"><span data-stu-id="22fcc-135">The server event categories include the following sorts of messages:</span></span>

| <span data-ttu-id="22fcc-136">Origem</span><span class="sxs-lookup"><span data-stu-id="22fcc-136">Source</span></span> | <span data-ttu-id="22fcc-137">Mensagens</span><span class="sxs-lookup"><span data-stu-id="22fcc-137">Messages</span></span> |
| --- | --- |
| <span data-ttu-id="22fcc-138">SignalR.SqlMessageBus</span><span class="sxs-lookup"><span data-stu-id="22fcc-138">SignalR.SqlMessageBus</span></span> | <span data-ttu-id="22fcc-139">Instalação do provedor de expansão do barramento de mensagem do SQL, a operação de banco de dados, erros e eventos de tempo limite</span><span class="sxs-lookup"><span data-stu-id="22fcc-139">SQL Message Bus scaleout provider setup, database operation, error, and timeout events</span></span> |
| <span data-ttu-id="22fcc-140">SignalR.ServiceBusMessageBus</span><span class="sxs-lookup"><span data-stu-id="22fcc-140">SignalR.ServiceBusMessageBus</span></span> | <span data-ttu-id="22fcc-141">Criação de tópico de provedor do serviço do barramento de escala horizontal e assinatura, erros e eventos de mensagens</span><span class="sxs-lookup"><span data-stu-id="22fcc-141">Service bus scaleout provider topic creation and subscription, error, and messaging events</span></span> |
| <span data-ttu-id="22fcc-142">SignalR.RedisMessageBus</span><span class="sxs-lookup"><span data-stu-id="22fcc-142">SignalR.RedisMessageBus</span></span> | <span data-ttu-id="22fcc-143">Eventos de erro de conexão e desconexão do provedor de expansão de redis</span><span class="sxs-lookup"><span data-stu-id="22fcc-143">Redis scaleout provider connection, disconnection, and error events</span></span> |
| <span data-ttu-id="22fcc-144">SignalR.ScaleoutMessageBus</span><span class="sxs-lookup"><span data-stu-id="22fcc-144">SignalR.ScaleoutMessageBus</span></span> | <span data-ttu-id="22fcc-145">Eventos de mensagens de expansão</span><span class="sxs-lookup"><span data-stu-id="22fcc-145">Scaleout messaging events</span></span> |
| <span data-ttu-id="22fcc-146">SignalR.Transports.WebSocketTransport</span><span class="sxs-lookup"><span data-stu-id="22fcc-146">SignalR.Transports.WebSocketTransport</span></span> | <span data-ttu-id="22fcc-147">Eventos de conexão, desconexão, mensagens e erros de transporte de WebSocket</span><span class="sxs-lookup"><span data-stu-id="22fcc-147">WebSocket transport connection, disconnection, messaging, and error events</span></span> |
| <span data-ttu-id="22fcc-148">SignalR.Transports.ServerSentEventsTransport</span><span class="sxs-lookup"><span data-stu-id="22fcc-148">SignalR.Transports.ServerSentEventsTransport</span></span> | <span data-ttu-id="22fcc-149">Eventos de conexão, desconexão, mensagens e erros de transporte ServerSentEvents</span><span class="sxs-lookup"><span data-stu-id="22fcc-149">ServerSentEvents transport connection, disconnection, messaging, and error events</span></span> |
| <span data-ttu-id="22fcc-150">SignalR.Transports.ForeverFrameTransport</span><span class="sxs-lookup"><span data-stu-id="22fcc-150">SignalR.Transports.ForeverFrameTransport</span></span> | <span data-ttu-id="22fcc-151">Eventos de conexão, desconexão, mensagens e erros de transporte ForeverFrame</span><span class="sxs-lookup"><span data-stu-id="22fcc-151">ForeverFrame transport connection, disconnection, messaging, and error events</span></span> |
| <span data-ttu-id="22fcc-152">SignalR.Transports.LongPollingTransport</span><span class="sxs-lookup"><span data-stu-id="22fcc-152">SignalR.Transports.LongPollingTransport</span></span> | <span data-ttu-id="22fcc-153">Eventos de conexão, desconexão, mensagens e erros de transporte LongPolling</span><span class="sxs-lookup"><span data-stu-id="22fcc-153">LongPolling transport connection, disconnection, messaging, and error events</span></span> |
| <span data-ttu-id="22fcc-154">SignalR.Transports.TransportHeartBeat</span><span class="sxs-lookup"><span data-stu-id="22fcc-154">SignalR.Transports.TransportHeartBeat</span></span> | <span data-ttu-id="22fcc-155">Eventos de conexão e desconexão keepalive de transporte</span><span class="sxs-lookup"><span data-stu-id="22fcc-155">Transport connection, disconnection, and keepalive events</span></span> |
| <span data-ttu-id="22fcc-156">SignalR.ReflectedHubDescriptorProvider</span><span class="sxs-lookup"><span data-stu-id="22fcc-156">SignalR.ReflectedHubDescriptorProvider</span></span> | <span data-ttu-id="22fcc-157">Eventos do hub de descoberta</span><span class="sxs-lookup"><span data-stu-id="22fcc-157">Hub discovery events</span></span> |

<a id="server_text"></a>
### <a name="logging-server-events-to-text-files"></a><span data-ttu-id="22fcc-158">Log de eventos do servidor para arquivos de texto</span><span class="sxs-lookup"><span data-stu-id="22fcc-158">Logging server events to text files</span></span>

<span data-ttu-id="22fcc-159">O código a seguir mostra como habilitar o rastreamento para cada categoria de evento.</span><span class="sxs-lookup"><span data-stu-id="22fcc-159">The following code shows how to enable tracing for each category of event.</span></span> <span data-ttu-id="22fcc-160">Este exemplo configura o aplicativo em log eventos nos arquivos de texto.</span><span class="sxs-lookup"><span data-stu-id="22fcc-160">This sample configures the application to log events to text files.</span></span>

<span data-ttu-id="22fcc-161">**Código de servidor XML para habilitar o rastreamento**</span><span class="sxs-lookup"><span data-stu-id="22fcc-161">**XML server code for enabling tracing**</span></span>

[!code-html[Main](enabling-signalr-tracing/samples/sample1.html)]

<span data-ttu-id="22fcc-162">No código acima, o `SignalRSwitch` entrada especifica o [TraceLevel](https://msdn.microsoft.com/library/system.diagnostics.tracelevel(v=vs.110).aspx) usada para eventos enviados para o log especificado.</span><span class="sxs-lookup"><span data-stu-id="22fcc-162">In the code above, the `SignalRSwitch` entry specifies the [TraceLevel](https://msdn.microsoft.com/library/system.diagnostics.tracelevel(v=vs.110).aspx) used for events sent to the specified log.</span></span> <span data-ttu-id="22fcc-163">Nesse caso, ele é definido como `Verbose` que significa que todos os de depuração e rastreamento de mensagens são registradas.</span><span class="sxs-lookup"><span data-stu-id="22fcc-163">In this case, it is set to `Verbose` which means all debugging and tracing messages are logged.</span></span>

<span data-ttu-id="22fcc-164">A saída a seguir mostra as entradas de `transports.log.txt` arquivo para um aplicativo usando o arquivo de configuração acima.</span><span class="sxs-lookup"><span data-stu-id="22fcc-164">The following output shows entries from the `transports.log.txt` file for an application using the above configuration file.</span></span> <span data-ttu-id="22fcc-165">Ele mostra uma nova conexão, uma conexão removida e eventos de pulsação do transporte.</span><span class="sxs-lookup"><span data-stu-id="22fcc-165">It shows a new connection, a removed connection, and transport heartbeat events.</span></span>

[!code-console[Main](enabling-signalr-tracing/samples/sample2.cmd)]

<a id="server_eventlog"></a>
### <a name="logging-server-events-to-the-event-log"></a><span data-ttu-id="22fcc-166">Log de eventos de servidor para o log de eventos</span><span class="sxs-lookup"><span data-stu-id="22fcc-166">Logging server events to the event log</span></span>

<span data-ttu-id="22fcc-167">Para registrar eventos no log de eventos em vez de um arquivo de texto, altere os valores para as entradas na `sharedListeners` nó.</span><span class="sxs-lookup"><span data-stu-id="22fcc-167">To log events to the event log rather than a text file, change the values for the entries in the `sharedListeners` node.</span></span> <span data-ttu-id="22fcc-168">O código a seguir mostra como registrar eventos de servidor para o log de eventos:</span><span class="sxs-lookup"><span data-stu-id="22fcc-168">The following code shows how to log server events to the event log:</span></span>

<span data-ttu-id="22fcc-169">**Código de servidor XML para o log de eventos para o log de eventos**</span><span class="sxs-lookup"><span data-stu-id="22fcc-169">**XML server code for logging events to the event log**</span></span>

[!code-xml[Main](enabling-signalr-tracing/samples/sample3.xml)]

<span data-ttu-id="22fcc-170">Os eventos são registrados no log do aplicativo e estão disponíveis por meio do Visualizador de eventos, conforme mostrado abaixo:</span><span class="sxs-lookup"><span data-stu-id="22fcc-170">The events are logged in the Application log, and are available through the Event Viewer, as shown below:</span></span>

![Visualizador de eventos mostrando os logs do SignalR](enabling-signalr-tracing/_static/image1.png)

> [!NOTE]
> <span data-ttu-id="22fcc-172">Ao usar o log de eventos, defina as **TraceLevel** à **erro** para manter o número de mensagens gerenciáveis.</span><span class="sxs-lookup"><span data-stu-id="22fcc-172">When using the event log, set the **TraceLevel** to **Error** to keep the number of messages manageable.</span></span>

<a id="net_client"></a>
## <a name="enabling-tracing-in-the-net-client-windows-desktop-apps"></a><span data-ttu-id="22fcc-173">Habilitando o rastreamento no cliente .NET (aplicativos da área de trabalho do Windows)</span><span class="sxs-lookup"><span data-stu-id="22fcc-173">Enabling tracing in the .NET client (Windows Desktop apps)</span></span>

<span data-ttu-id="22fcc-174">O cliente .NET pode registrar eventos no console, um arquivo de texto ou em um log personalizado usando uma implementação do [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx).</span><span class="sxs-lookup"><span data-stu-id="22fcc-174">The .NET client can log events to the console, a text file, or to a custom log using an implementation of [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx).</span></span>

<span data-ttu-id="22fcc-175">Para habilitar o registro em log no cliente do .NET, defina a conexão `TraceLevel` propriedade para um [TraceLevels](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.tracelevels(v=vs.118).aspx) valor e o `TraceWriter` propriedade platnou [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) instância.</span><span class="sxs-lookup"><span data-stu-id="22fcc-175">To enable logging in the .NET client, set the connection's `TraceLevel` property to a [TraceLevels](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.tracelevels(v=vs.118).aspx) value, and the `TraceWriter` property to a valid [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) instance.</span></span>

<a id="desktop_console"></a>
### <a name="logging-desktop-client-events-to-the-console"></a><span data-ttu-id="22fcc-176">Log de eventos de cliente da área de trabalho para o console</span><span class="sxs-lookup"><span data-stu-id="22fcc-176">Logging Desktop client events to the console</span></span>

<span data-ttu-id="22fcc-177">O código c# a seguir mostra como registrar eventos no cliente do .NET no console:</span><span class="sxs-lookup"><span data-stu-id="22fcc-177">The following C# code shows how to log events in the .NET client to the console:</span></span>

[!code-csharp[Main](enabling-signalr-tracing/samples/sample4.cs?highlight=2-3)]

<a id="desktop_text"></a>
### <a name="logging-desktop-client-events-to-a-text-file"></a><span data-ttu-id="22fcc-178">Log de eventos de cliente da área de trabalho para um arquivo de texto</span><span class="sxs-lookup"><span data-stu-id="22fcc-178">Logging Desktop client events to a text file</span></span>

<span data-ttu-id="22fcc-179">O código c# a seguir mostra como registrar eventos no cliente .NET para um arquivo de texto:</span><span class="sxs-lookup"><span data-stu-id="22fcc-179">The following C# code shows how to log events in the .NET client to a text file:</span></span>

[!code-csharp[Main](enabling-signalr-tracing/samples/sample5.cs?highlight=4-5)]

<span data-ttu-id="22fcc-180">A saída a seguir mostra as entradas de `ClientLog.txt` arquivo para um aplicativo usando o arquivo de configuração acima.</span><span class="sxs-lookup"><span data-stu-id="22fcc-180">The following output shows entries from the `ClientLog.txt` file for an application using the above configuration file.</span></span> <span data-ttu-id="22fcc-181">Ele mostra que o cliente se conectar ao servidor e o hub de invocação de um método de cliente chamado `addMessage`:</span><span class="sxs-lookup"><span data-stu-id="22fcc-181">It shows the client connecting to the server, and the hub invoking a client method called `addMessage`:</span></span>

[!code-console[Main](enabling-signalr-tracing/samples/sample6.cmd)]

<a id="phone"></a>
## <a name="enabling-tracing-in-windows-phone-8-clients"></a><span data-ttu-id="22fcc-182">Habilitando o rastreamento em clientes do Windows Phone 8</span><span class="sxs-lookup"><span data-stu-id="22fcc-182">Enabling tracing in Windows Phone 8 clients</span></span>

<span data-ttu-id="22fcc-183">Aplicativos do SignalR para aplicativos do Windows Phone usam o mesmo cliente do .NET como aplicativos de área de trabalho, mas [console. out](https://msdn.microsoft.com/library/system.console.out(v=vs.110).aspx) e gravar em um arquivo com [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) não estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="22fcc-183">SignalR applications for Windows Phone apps use the same .NET client as desktop apps, but [Console.Out](https://msdn.microsoft.com/library/system.console.out(v=vs.110).aspx) and writing to a file with [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) are not available.</span></span> <span data-ttu-id="22fcc-184">Em vez disso, você precisará criar uma implementação personalizada de [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) para rastreamento.</span><span class="sxs-lookup"><span data-stu-id="22fcc-184">Instead, you need to create a custom implementation of [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) for tracing.</span></span>

<a id="phone_ui"></a>
### <a name="logging-windows-phone-client-events-to-the-ui"></a><span data-ttu-id="22fcc-185">Log de eventos do cliente do Windows Phone na interface do usuário</span><span class="sxs-lookup"><span data-stu-id="22fcc-185">Logging Windows Phone client events to the UI</span></span>

<span data-ttu-id="22fcc-186">O [SignalR codebase](https://github.com/SignalR/SignalR/archive/master.zip) inclui um exemplo do Windows Phone que grava a saída de rastreamento para um [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) usando um personalizado [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) implementação chamado `TextBlockWriter`.</span><span class="sxs-lookup"><span data-stu-id="22fcc-186">The [SignalR codebase](https://github.com/SignalR/SignalR/archive/master.zip) includes a Windows Phone sample that writes trace output to a [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) using a custom [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) implementation called `TextBlockWriter`.</span></span> <span data-ttu-id="22fcc-187">Essa classe pode ser encontrada na **samples/Microsoft.AspNet.SignalR.Client.WP8.Samples** projeto.</span><span class="sxs-lookup"><span data-stu-id="22fcc-187">This class can be found in the **samples/Microsoft.AspNet.SignalR.Client.WP8.Samples** project.</span></span> <span data-ttu-id="22fcc-188">Ao criar uma instância de `TextBlockWriter`, passe atual [SynchronizationContext](https://msdn.microsoft.com/library/system.threading.synchronizationcontext(v=vs.110).aspx)e uma [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) onde ele criará uma [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) a ser usado para rastreamento saída:</span><span class="sxs-lookup"><span data-stu-id="22fcc-188">When creating an instance of `TextBlockWriter`, pass in the current [SynchronizationContext](https://msdn.microsoft.com/library/system.threading.synchronizationcontext(v=vs.110).aspx), and a [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) where it will create a [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) to use for trace output:</span></span>

[!code-csharp[Main](enabling-signalr-tracing/samples/sample7.cs)]

<span data-ttu-id="22fcc-189">A saída de rastreamento, em seguida, será gravada para uma nova [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) criado na [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) passado em:</span><span class="sxs-lookup"><span data-stu-id="22fcc-189">The trace output will then be written to a new [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) created in the [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) you passed in:</span></span>

![](enabling-signalr-tracing/_static/image2.png)

<a id="phone_debug"></a>
### <a name="logging-windows-phone-client-events-to-the-debug-console"></a><span data-ttu-id="22fcc-190">Log de eventos do cliente do Windows Phone para o console de depuração</span><span class="sxs-lookup"><span data-stu-id="22fcc-190">Logging Windows Phone client events to the debug console</span></span>

<span data-ttu-id="22fcc-191">Para enviar a saída para o console de depuração em vez de interface do usuário, criar uma implementação do [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) que grava a janela de depuração e atribuí-lo a sua conexão [TraceWriter](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connection.tracewriter(v=vs.118).aspx) propriedade:</span><span class="sxs-lookup"><span data-stu-id="22fcc-191">To send output to the debug console rather than the UI, create an implementation of [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) that writes to the debug window, and assign it to your connection's [TraceWriter](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connection.tracewriter(v=vs.118).aspx) property:</span></span>

[!code-csharp[Main](enabling-signalr-tracing/samples/sample8.cs)]

<span data-ttu-id="22fcc-192">Informações de rastreamento, em seguida, serão gravadas na janela de depuração no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="22fcc-192">Trace information will then be written to the debug window in Visual Studio:</span></span>

![](enabling-signalr-tracing/_static/image3.png)

<a id="javascript"></a>
## <a name="enabling-tracing-in-the-javascript-client"></a><span data-ttu-id="22fcc-193">Habilitando o rastreamento no cliente JavaScript</span><span class="sxs-lookup"><span data-stu-id="22fcc-193">Enabling tracing in the JavaScript client</span></span>

<span data-ttu-id="22fcc-194">Para habilitar o registro em log do lado do cliente em uma conexão, defina as `logging` propriedade no objeto de conexão antes de chamar o `start` método para estabelecer a conexão.</span><span class="sxs-lookup"><span data-stu-id="22fcc-194">To enable client-side logging on a connection, set the `logging` property on the connection object before you call the `start` method to establish the connection.</span></span>

<span data-ttu-id="22fcc-195">**Código de JavaScript do cliente para habilitar o rastreamento para o console do navegador (com o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="22fcc-195">**Client JavaScript code for enabling tracing to the browser console (with the generated proxy)**</span></span>

[!code-javascript[Main](enabling-signalr-tracing/samples/sample9.js?highlight=1)]

<span data-ttu-id="22fcc-196">**Código de JavaScript do cliente para habilitar o rastreamento para o console do navegador (sem o proxy gerado)**</span><span class="sxs-lookup"><span data-stu-id="22fcc-196">**Client JavaScript code for enabling tracing to the browser console (without the generated proxy)**</span></span>

[!code-javascript[Main](enabling-signalr-tracing/samples/sample10.js?highlight=2)]

<span data-ttu-id="22fcc-197">Quando o rastreamento está habilitado, o cliente JavaScript registra eventos no console do navegador.</span><span class="sxs-lookup"><span data-stu-id="22fcc-197">When tracing is enabled, the JavaScript client logs events to the browser console.</span></span> <span data-ttu-id="22fcc-198">Para acessar o console do navegador, consulte [transportes monitoramento](../getting-started/introduction-to-signalr.md#MonitoringTransports).</span><span class="sxs-lookup"><span data-stu-id="22fcc-198">To access the browser console, see [Monitoring Transports](../getting-started/introduction-to-signalr.md#MonitoringTransports).</span></span>

<span data-ttu-id="22fcc-199">Captura de tela a seguir mostra um cliente SignalR JavaScript com o rastreamento habilitado.</span><span class="sxs-lookup"><span data-stu-id="22fcc-199">The following screenshot shows a SignalR JavaScript client with tracing enabled.</span></span> <span data-ttu-id="22fcc-200">Ele mostra a conexão e eventos de invocação de hub no console do navegador:</span><span class="sxs-lookup"><span data-stu-id="22fcc-200">It shows connection and hub invocation events in the browser console:</span></span>

![Eventos de rastreamento do SignalR no console do navegador](enabling-signalr-tracing/_static/image4.png)
