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
<a name="enabling-signalr-tracing"></a>Habilitar o rastreamento do SignalR
====================
por [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este documento descreve como habilitar e configurar o rastreamento de clientes e servidores do SignalR. O rastreamento permite que você exiba informações sobre eventos de diagnóstico em seu aplicativo do SignalR.
>
> Este tópico foi originalmente escrito por Patrick Fletcher.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>Versões de software usadas no tutorial
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET Framework 4.5
> - Versão 2 do SignalR
>
>
>
> ## <a name="questions-and-comments"></a>Perguntas e comentários
>
> Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página. Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


Quando o rastreamento está habilitado, um aplicativo de SignalR cria entradas de log de eventos. Você pode registrar eventos de cliente e o servidor. Rastreamento na conexão de logs do servidor, provedor de expansão e eventos do barramento de mensagem. Rastreamento sobre os cliente registra eventos de conexão. No SignalR 2.1 e posterior, o rastreamento no cliente registra o conteúdo completo das mensagens de invocação de hub.

## <a name="contents"></a>Conteúdo

- [Habilitando o rastreamento no servidor](#server)

    - [Log de eventos do servidor para arquivos de texto](#server_text)
    - [Log de eventos de servidor para o log de eventos](#server_eventlog)
- [Habilitando o rastreamento no cliente .NET (aplicativos da área de trabalho do Windows)](#net_client)

    - [Log de eventos de cliente da área de trabalho para o console](#desktop_console)
    - [Log de eventos de cliente da área de trabalho para um arquivo de texto](#desktop_text)
- [Habilitando o rastreamento em clientes do Windows Phone 8](#phone)

    - [Log de eventos do cliente do Windows Phone na interface do usuário](#phone_ui)
    - [Log de eventos do cliente do Windows Phone para o console de depuração](#phone_debug)
- [Habilitando o rastreamento no cliente JavaScript](#javascript)

<a id="server"></a>
## <a name="enabling-tracing-on-the-server"></a>Habilitando o rastreamento no servidor

Habilitar o rastreamento no servidor dentro do arquivo de configuração do aplicativo (App. config ou Web. config, dependendo do tipo de projeto.) Especificar a quais categorias de eventos que você deseja registrar. No arquivo de configuração, você também especifica se deseja registrar os eventos para um arquivo de texto, o log de eventos do Windows ou um log personalizado usando uma implementação do [TraceListener](https://msdn.microsoft.com/library/system.diagnostics.tracelistener(v=vs.110).aspx).

As categorias de eventos de servidor incluem as seguintes classificações de mensagens:

| Origem | Mensagens |
| --- | --- |
| SignalR.SqlMessageBus | Instalação do provedor de expansão do barramento de mensagem do SQL, a operação de banco de dados, erros e eventos de tempo limite |
| SignalR.ServiceBusMessageBus | Criação de tópico de provedor do serviço do barramento de escala horizontal e assinatura, erros e eventos de mensagens |
| SignalR.RedisMessageBus | Eventos de erro de conexão e desconexão do provedor de expansão de redis |
| SignalR.ScaleoutMessageBus | Eventos de mensagens de expansão |
| SignalR.Transports.WebSocketTransport | Eventos de conexão, desconexão, mensagens e erros de transporte de WebSocket |
| SignalR.Transports.ServerSentEventsTransport | Eventos de conexão, desconexão, mensagens e erros de transporte ServerSentEvents |
| SignalR.Transports.ForeverFrameTransport | Eventos de conexão, desconexão, mensagens e erros de transporte ForeverFrame |
| SignalR.Transports.LongPollingTransport | Eventos de conexão, desconexão, mensagens e erros de transporte LongPolling |
| SignalR.Transports.TransportHeartBeat | Eventos de conexão e desconexão keepalive de transporte |
| SignalR.ReflectedHubDescriptorProvider | Eventos do hub de descoberta |

<a id="server_text"></a>
### <a name="logging-server-events-to-text-files"></a>Log de eventos do servidor para arquivos de texto

O código a seguir mostra como habilitar o rastreamento para cada categoria de evento. Este exemplo configura o aplicativo em log eventos nos arquivos de texto.

**Código de servidor XML para habilitar o rastreamento**

[!code-html[Main](enabling-signalr-tracing/samples/sample1.html)]

No código acima, o `SignalRSwitch` entrada especifica o [TraceLevel](https://msdn.microsoft.com/library/system.diagnostics.tracelevel(v=vs.110).aspx) usada para eventos enviados para o log especificado. Nesse caso, ele é definido como `Verbose` que significa que todos os de depuração e rastreamento de mensagens são registradas.

A saída a seguir mostra as entradas de `transports.log.txt` arquivo para um aplicativo usando o arquivo de configuração acima. Ele mostra uma nova conexão, uma conexão removida e eventos de pulsação do transporte.

[!code-console[Main](enabling-signalr-tracing/samples/sample2.cmd)]

<a id="server_eventlog"></a>
### <a name="logging-server-events-to-the-event-log"></a>Log de eventos de servidor para o log de eventos

Para registrar eventos no log de eventos em vez de um arquivo de texto, altere os valores para as entradas na `sharedListeners` nó. O código a seguir mostra como registrar eventos de servidor para o log de eventos:

**Código de servidor XML para o log de eventos para o log de eventos**

[!code-xml[Main](enabling-signalr-tracing/samples/sample3.xml)]

Os eventos são registrados no log do aplicativo e estão disponíveis por meio do Visualizador de eventos, conforme mostrado abaixo:

![Visualizador de eventos mostrando os logs do SignalR](enabling-signalr-tracing/_static/image1.png)

> [!NOTE]
> Ao usar o log de eventos, defina as **TraceLevel** à **erro** para manter o número de mensagens gerenciáveis.

<a id="net_client"></a>
## <a name="enabling-tracing-in-the-net-client-windows-desktop-apps"></a>Habilitando o rastreamento no cliente .NET (aplicativos da área de trabalho do Windows)

O cliente .NET pode registrar eventos no console, um arquivo de texto ou em um log personalizado usando uma implementação do [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx).

Para habilitar o registro em log no cliente do .NET, defina a conexão `TraceLevel` propriedade para um [TraceLevels](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.tracelevels(v=vs.118).aspx) valor e o `TraceWriter` propriedade platnou [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) instância.

<a id="desktop_console"></a>
### <a name="logging-desktop-client-events-to-the-console"></a>Log de eventos de cliente da área de trabalho para o console

O código c# a seguir mostra como registrar eventos no cliente do .NET no console:

[!code-csharp[Main](enabling-signalr-tracing/samples/sample4.cs?highlight=2-3)]

<a id="desktop_text"></a>
### <a name="logging-desktop-client-events-to-a-text-file"></a>Log de eventos de cliente da área de trabalho para um arquivo de texto

O código c# a seguir mostra como registrar eventos no cliente .NET para um arquivo de texto:

[!code-csharp[Main](enabling-signalr-tracing/samples/sample5.cs?highlight=4-5)]

A saída a seguir mostra as entradas de `ClientLog.txt` arquivo para um aplicativo usando o arquivo de configuração acima. Ele mostra que o cliente se conectar ao servidor e o hub de invocação de um método de cliente chamado `addMessage`:

[!code-console[Main](enabling-signalr-tracing/samples/sample6.cmd)]

<a id="phone"></a>
## <a name="enabling-tracing-in-windows-phone-8-clients"></a>Habilitando o rastreamento em clientes do Windows Phone 8

Aplicativos do SignalR para aplicativos do Windows Phone usam o mesmo cliente do .NET como aplicativos de área de trabalho, mas [console. out](https://msdn.microsoft.com/library/system.console.out(v=vs.110).aspx) e gravar em um arquivo com [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) não estão disponíveis. Em vez disso, você precisará criar uma implementação personalizada de [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) para rastreamento.

<a id="phone_ui"></a>
### <a name="logging-windows-phone-client-events-to-the-ui"></a>Log de eventos do cliente do Windows Phone na interface do usuário

O [SignalR codebase](https://github.com/SignalR/SignalR/archive/master.zip) inclui um exemplo do Windows Phone que grava a saída de rastreamento para um [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) usando um personalizado [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) implementação chamado `TextBlockWriter`. Essa classe pode ser encontrada na **samples/Microsoft.AspNet.SignalR.Client.WP8.Samples** projeto. Ao criar uma instância de `TextBlockWriter`, passe atual [SynchronizationContext](https://msdn.microsoft.com/library/system.threading.synchronizationcontext(v=vs.110).aspx)e uma [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) onde ele criará uma [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) a ser usado para rastreamento saída:

[!code-csharp[Main](enabling-signalr-tracing/samples/sample7.cs)]

A saída de rastreamento, em seguida, será gravada para uma nova [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) criado na [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) passado em:

![](enabling-signalr-tracing/_static/image2.png)

<a id="phone_debug"></a>
### <a name="logging-windows-phone-client-events-to-the-debug-console"></a>Log de eventos do cliente do Windows Phone para o console de depuração

Para enviar a saída para o console de depuração em vez de interface do usuário, criar uma implementação do [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) que grava a janela de depuração e atribuí-lo a sua conexão [TraceWriter](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connection.tracewriter(v=vs.118).aspx) propriedade:

[!code-csharp[Main](enabling-signalr-tracing/samples/sample8.cs)]

Informações de rastreamento, em seguida, serão gravadas na janela de depuração no Visual Studio:

![](enabling-signalr-tracing/_static/image3.png)

<a id="javascript"></a>
## <a name="enabling-tracing-in-the-javascript-client"></a>Habilitando o rastreamento no cliente JavaScript

Para habilitar o registro em log do lado do cliente em uma conexão, defina as `logging` propriedade no objeto de conexão antes de chamar o `start` método para estabelecer a conexão.

**Código de JavaScript do cliente para habilitar o rastreamento para o console do navegador (com o proxy gerado)**

[!code-javascript[Main](enabling-signalr-tracing/samples/sample9.js?highlight=1)]

**Código de JavaScript do cliente para habilitar o rastreamento para o console do navegador (sem o proxy gerado)**

[!code-javascript[Main](enabling-signalr-tracing/samples/sample10.js?highlight=2)]

Quando o rastreamento está habilitado, o cliente JavaScript registra eventos no console do navegador. Para acessar o console do navegador, consulte [transportes monitoramento](../getting-started/introduction-to-signalr.md#MonitoringTransports).

Captura de tela a seguir mostra um cliente SignalR JavaScript com o rastreamento habilitado. Ele mostra a conexão e eventos de invocação de hub no console do navegador:

![Eventos de rastreamento do SignalR no console do navegador](enabling-signalr-tracing/_static/image4.png)
