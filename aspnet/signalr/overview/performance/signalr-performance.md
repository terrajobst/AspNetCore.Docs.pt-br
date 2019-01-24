---
uid: signalr/overview/performance/signalr-performance
title: Desempenho do SignalR | Microsoft Docs
author: bradygaster
description: Desempenho do SignalR
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 3751f5e7-59db-4be0-a290-50abc24e5c84
msc.legacyurl: /signalr/overview/performance/signalr-performance
msc.type: authoredcontent
ms.openlocfilehash: 3326c2e600854fc7a4435d96c45b04a6188d3937
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836506"
---
<a name="signalr-performance"></a><span data-ttu-id="9a28d-103">Desempenho do SignalR</span><span class="sxs-lookup"><span data-stu-id="9a28d-103">SignalR Performance</span></span>
====================
<span data-ttu-id="9a28d-104">por [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="9a28d-104">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="9a28d-105">Este tópico descreve como projetar para, medir e melhorar o desempenho em um aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="9a28d-105">This topic describes how to design for, measure, and improve performance in a SignalR application.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="9a28d-106">Versões de software usadas neste tópico</span><span class="sxs-lookup"><span data-stu-id="9a28d-106">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="9a28d-107">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="9a28d-107">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="9a28d-108">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="9a28d-108">.NET 4.5</span></span>
> - <span data-ttu-id="9a28d-109">Versão 2 do SignalR</span><span class="sxs-lookup"><span data-stu-id="9a28d-109">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="9a28d-110">Versões anteriores deste tópico</span><span class="sxs-lookup"><span data-stu-id="9a28d-110">Previous versions of this topic</span></span>
>
> <span data-ttu-id="9a28d-111">Para obter informações sobre versões anteriores do SignalR, consulte [versões mais antigas do SignalR](../older-versions/index.md).</span><span class="sxs-lookup"><span data-stu-id="9a28d-111">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="9a28d-112">Perguntas e comentários</span><span class="sxs-lookup"><span data-stu-id="9a28d-112">Questions and comments</span></span>
>
> <span data-ttu-id="9a28d-113">Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página.</span><span class="sxs-lookup"><span data-stu-id="9a28d-113">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="9a28d-114">Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="9a28d-114">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="9a28d-115">Para obter uma apresentação recente em escala e desempenho do SignalR, consulte [dimensionamento na Web em tempo real com o ASP.NET SignalR](https://channel9.msdn.com/Events/Build/2013/3-502).</span><span class="sxs-lookup"><span data-stu-id="9a28d-115">For a recent presentation on SignalR performance and scaling, see [Scaling the Real-time Web with ASP.NET SignalR](https://channel9.msdn.com/Events/Build/2013/3-502).</span></span>

<span data-ttu-id="9a28d-116">Esse tópico contém as seguintes seções:</span><span class="sxs-lookup"><span data-stu-id="9a28d-116">This topic contains the following sections:</span></span>

- [<span data-ttu-id="9a28d-117">Considerações de design</span><span class="sxs-lookup"><span data-stu-id="9a28d-117">Design considerations</span></span>](#design)
- [<span data-ttu-id="9a28d-118">Ajuste de desempenho do seu servidor SignalR</span><span class="sxs-lookup"><span data-stu-id="9a28d-118">Tuning your SignalR server for performance</span></span>](#tuning)
- [<span data-ttu-id="9a28d-119">Solucionando problemas de desempenho</span><span class="sxs-lookup"><span data-stu-id="9a28d-119">Troubleshooting performance issues</span></span>](#troubleshooting)
- [<span data-ttu-id="9a28d-120">Usando contadores de desempenho do SignalR</span><span class="sxs-lookup"><span data-stu-id="9a28d-120">Using SignalR performance counters</span></span>](#perfcounters)
- [<span data-ttu-id="9a28d-121">Usando outros contadores de desempenho</span><span class="sxs-lookup"><span data-stu-id="9a28d-121">Using other performance counters</span></span>](#othercounters)
- [<span data-ttu-id="9a28d-122">Outros recursos</span><span class="sxs-lookup"><span data-stu-id="9a28d-122">Other resources</span></span>](#otherresources)

<a id="design"></a>

## <a name="design-considerations"></a><span data-ttu-id="9a28d-123">Considerações de design</span><span class="sxs-lookup"><span data-stu-id="9a28d-123">Design considerations</span></span>

<span data-ttu-id="9a28d-124">Esta seção descreve os padrões que podem ser implementados durante o design de um aplicativo do SignalR, para garantir que o desempenho não seja sendo prejudicado, gerando o tráfego de rede desnecessário.</span><span class="sxs-lookup"><span data-stu-id="9a28d-124">This section describes patterns that can be implemented during the design of a SignalR application, to ensure that performance is not being hindered by generating unnecessary network traffic.</span></span>

### <a name="throttling-message-frequency"></a><span data-ttu-id="9a28d-125">Limitação de frequência de mensagens</span><span class="sxs-lookup"><span data-stu-id="9a28d-125">Throttling message frequency</span></span>

<span data-ttu-id="9a28d-126">Mesmo em um aplicativo que envia mensagens a uma alta frequência (por exemplo, um aplicativo de jogos em tempo real), a maioria dos aplicativos não precisa enviar mais de algumas mensagens de um segundo.</span><span class="sxs-lookup"><span data-stu-id="9a28d-126">Even in an application that sends out messages at a high frequency (such as a realtime gaming application), most applications don't need to send more than a few messages a second.</span></span> <span data-ttu-id="9a28d-127">Para reduzir a quantidade de tráfego que gera cada cliente, um loop de mensagem pode ser implementado que filas e envia mensagens não mais frequentemente do que uma taxa fixa (ou seja, até um determinado número de mensagens serão enviados por segundo, se houver mensagens no momento em valo a ser enviado).</span><span class="sxs-lookup"><span data-stu-id="9a28d-127">To reduce the amount of traffic that each client generates, a message loop can be implemented that queues and sends out messages no more frequently than a fixed rate (that is, up to a certain number of messages will be sent every second, if there are messages in that time interval to be sent).</span></span> <span data-ttu-id="9a28d-128">Para um aplicativo de exemplo que limita as mensagens para uma determinada taxa (de cliente e servidor), consulte [em tempo real de alta frequência com SignalR](../getting-started/tutorial-high-frequency-realtime-with-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="9a28d-128">For a sample application that throttles messages to a certain rate (from both client and server), see [High-Frequency Realtime with SignalR](../getting-started/tutorial-high-frequency-realtime-with-signalr.md).</span></span>

### <a name="reducing-message-size"></a><span data-ttu-id="9a28d-129">Reduzindo o tamanho de mensagem</span><span class="sxs-lookup"><span data-stu-id="9a28d-129">Reducing message size</span></span>

<span data-ttu-id="9a28d-130">Você pode reduzir o tamanho de uma mensagem do SignalR, reduzindo o tamanho dos seus objetos serializados.</span><span class="sxs-lookup"><span data-stu-id="9a28d-130">You can reduce the size of a SignalR message by reducing the size of your serialized objects.</span></span> <span data-ttu-id="9a28d-131">No código do servidor, se você estiver enviando um objeto que contém propriedades que não precisam ser transmitidos, impedir que as propriedades que está sendo serializado usando o `JsonIgnore` atributo.</span><span class="sxs-lookup"><span data-stu-id="9a28d-131">In server code, if you're sending an object that contains properties that don't need to be transmitted, prevent those properties from being serialized by using the `JsonIgnore` attribute.</span></span> <span data-ttu-id="9a28d-132">Os nomes das propriedades também são armazenados na mensagem; os nomes das propriedades podem ser reduzidos usando o `JsonProperty` atributo.</span><span class="sxs-lookup"><span data-stu-id="9a28d-132">The names of properties are also stored in the message; the names of properties can be shortened using the `JsonProperty` attribute.</span></span> <span data-ttu-id="9a28d-133">O exemplo de código a seguir demonstra como excluir uma propriedade que está sendo enviada ao cliente e como encurtar os nomes de propriedade:</span><span class="sxs-lookup"><span data-stu-id="9a28d-133">The following code sample demonstrates how to exclude a property from being sent to the client, and how to shorten property names:</span></span>

<span data-ttu-id="9a28d-134">**Código do servidor .NET que demonstra o atributo JsonIgnore para excluir os dados sejam enviados ao cliente e o atributo JsonProperty para reduzir o tamanho de mensagem**</span><span class="sxs-lookup"><span data-stu-id="9a28d-134">**.NET server code that demonstrates the JsonIgnore attribute to exclude data from being sent to the client, and the JsonProperty attribute to reduce message size**</span></span>

[!code-csharp[Main](signalr-performance/samples/sample1.cs?highlight=5,7,10)]

<span data-ttu-id="9a28d-135">Para manter a legibilidade / facilidade de manutenção no código do cliente, os nomes abreviados de propriedade podem ser remapeados para amigável a humanos nomes depois que a mensagem é recebida.</span><span class="sxs-lookup"><span data-stu-id="9a28d-135">In order to retain readability/ maintainability in the client code, the abbreviated property names can be remapped to human-friendly names after the message is received.</span></span> <span data-ttu-id="9a28d-136">O exemplo de código a seguir demonstra uma possível maneira de remapeamento de nomes abreviados para mais longas, definindo um contrato de mensagem (mapeamento) e usando o `reMap` função para aplicar o contrato para a classe de mensagem otimizado:</span><span class="sxs-lookup"><span data-stu-id="9a28d-136">The following code sample demonstrates one possible way of remapping shortened names to longer ones, by defining a message contract (mapping), and using the `reMap` function to apply the contract to the optimized message class:</span></span>

<span data-ttu-id="9a28d-137">**Código de JavaScript do lado do cliente que remapeia reduzido nomes de propriedade para nomes legíveis**</span><span class="sxs-lookup"><span data-stu-id="9a28d-137">**Client-side JavaScript code that remaps shortened property names to human-readable names**</span></span>

[!code-javascript[Main](signalr-performance/samples/sample2.js)]

<span data-ttu-id="9a28d-138">Nomes podem ser reduzidos em mensagens do cliente ao servidor, usando o mesmo método.</span><span class="sxs-lookup"><span data-stu-id="9a28d-138">Names can be shortened in messages from the client to the server as well, using the same method.</span></span>

<span data-ttu-id="9a28d-139">Reduzindo o volume de memória (ou seja, a quantidade de memória usada para a mensagem) da mensagem de objeto também pode melhorar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="9a28d-139">Reducing the memory footprint (that is, the amount of memory used for the message) of the message object can also improve performance.</span></span> <span data-ttu-id="9a28d-140">Por exemplo, se o intervalo completo de um `int` não for necessária, uma `short` ou `byte` pode ser usado em vez disso.</span><span class="sxs-lookup"><span data-stu-id="9a28d-140">For example, if the full range of an `int` is not needed, a `short` or `byte` can be used instead.</span></span>

<span data-ttu-id="9a28d-141">Uma vez que as mensagens são armazenadas no barramento de mensagem na memória do servidor, reduzindo o tamanho de mensagens também pode resolver problemas de memória do servidor.</span><span class="sxs-lookup"><span data-stu-id="9a28d-141">Since messages are stored in the message bus in server memory, reducing the size of messages can also address server memory issues.</span></span>

<a id="tuning"></a>

### <a name="tuning-your-signalr-server-for-performance"></a><span data-ttu-id="9a28d-142">Ajuste de desempenho do seu servidor SignalR</span><span class="sxs-lookup"><span data-stu-id="9a28d-142">Tuning your SignalR server for performance</span></span>

<span data-ttu-id="9a28d-143">As definições de configuração a seguir podem ser usadas para ajustar o servidor para melhorar o desempenho em um aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="9a28d-143">The following configuration settings can be used to tune your server for better performance in a SignalR application.</span></span> <span data-ttu-id="9a28d-144">Para obter informações gerais sobre como melhorar o desempenho em um aplicativo ASP.NET, consulte [melhorando o desempenho do ASP.NET](https://msdn.microsoft.com/library/ff647787.aspx).</span><span class="sxs-lookup"><span data-stu-id="9a28d-144">For general information on how to improve performance in an ASP.NET application, see [Improving ASP.NET Performance](https://msdn.microsoft.com/library/ff647787.aspx).</span></span>

<span data-ttu-id="9a28d-145">**Definições de configuração do SignalR**</span><span class="sxs-lookup"><span data-stu-id="9a28d-145">**SignalR configuration settings**</span></span>

- <span data-ttu-id="9a28d-146">**DefaultMessageBufferSize**: Por padrão, o SignalR retém 1000 mensagens na memória por hub por conexão.</span><span class="sxs-lookup"><span data-stu-id="9a28d-146">**DefaultMessageBufferSize**: By default, SignalR retains 1000 messages in memory per hub per connection.</span></span> <span data-ttu-id="9a28d-147">Se estiverem sendo usada mensagens grandes, isso pode criar problemas de memória que podem ser atenuados por reduzir esse valor.</span><span class="sxs-lookup"><span data-stu-id="9a28d-147">If large messages are being used, this may create memory issues which can be alleviated by reducing this value.</span></span> <span data-ttu-id="9a28d-148">Essa configuração pode ser definida `Application_Start` manipulador de eventos em um aplicativo ASP.NET ou no `Configuration` método de uma classe de inicialização OWIN em um aplicativo hospedado internamente.</span><span class="sxs-lookup"><span data-stu-id="9a28d-148">This setting can be set in the `Application_Start` event handler in an ASP.NET application, or in the `Configuration` method of an OWIN startup class in a self-hosted application.</span></span> <span data-ttu-id="9a28d-149">O exemplo a seguir demonstra como reduzir esse valor para reduzir o volume de memória do seu aplicativo para reduzir a quantidade de memória de servidor usada:</span><span class="sxs-lookup"><span data-stu-id="9a28d-149">The following sample demonstrates how to reduce this value in order to reduce the memory footprint of your application in order to reduce the amount of server memory used:</span></span>

    <span data-ttu-id="9a28d-150">**Código de servidor do .NET em Startup.cs para diminuir o tamanho do buffer de mensagem padrão**</span><span class="sxs-lookup"><span data-stu-id="9a28d-150">**.NET server code in Startup.cs for decreasing default message buffer size**</span></span>

    [!code-csharp[Main](signalr-performance/samples/sample3.cs)]

<span data-ttu-id="9a28d-151">**Definições de configuração do IIS**</span><span class="sxs-lookup"><span data-stu-id="9a28d-151">**IIS configuration settings**</span></span>

- <span data-ttu-id="9a28d-152">**Máximo de solicitações simultâneas por aplicativo**: Aumentar o número de IIS simultâneo solicitações aumentará disponíveis para atender às solicitações de recursos do servidor.</span><span class="sxs-lookup"><span data-stu-id="9a28d-152">**Max concurrent requests per application**: Increasing the number of concurrent IIS requests will increase server resources available for serving requests.</span></span> <span data-ttu-id="9a28d-153">O valor padrão é 5000; para aumentar essa configuração, execute os seguintes comandos no prompt de comando elevado:</span><span class="sxs-lookup"><span data-stu-id="9a28d-153">The default value is 5000; to increase this setting, execute the following commands in an elevated command prompt:</span></span>

    [!code-console[Main](signalr-performance/samples/sample4.cmd)]
- <span data-ttu-id="9a28d-154">**ApplicationPool QueueLength**: Isso é o número máximo de solicitações de filas de HTTP. sys para o pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="9a28d-154">**ApplicationPool QueueLength**: This is the maximum number of requests that Http.sys queues for the application pool.</span></span> <span data-ttu-id="9a28d-155">Quando a fila está cheia, novas solicitações recebem uma resposta "Serviço indisponível" 503.</span><span class="sxs-lookup"><span data-stu-id="9a28d-155">When the queue is full, new requests receive a 503 "Service Unavailable" response.</span></span> <span data-ttu-id="9a28d-156">O valor padrão é 1000.</span><span class="sxs-lookup"><span data-stu-id="9a28d-156">The default value is 1000.</span></span>

    <span data-ttu-id="9a28d-157">Reduzir o comprimento da fila para o processo de trabalho no pool de aplicativos hospedando seu aplicativo para conservar recursos de memória.</span><span class="sxs-lookup"><span data-stu-id="9a28d-157">Shortening the queue length for the worker process in the application pool hosting your application will conserve memory resources.</span></span> <span data-ttu-id="9a28d-158">Para obter mais informações, consulte [Configurando Pools de aplicativos, ajuste e gerenciando](https://technet.microsoft.com/library/cc745955.aspx).</span><span class="sxs-lookup"><span data-stu-id="9a28d-158">For more information, see [Managing, Tuning, and Configuring Application Pools](https://technet.microsoft.com/library/cc745955.aspx).</span></span>

<span data-ttu-id="9a28d-159">**Definições de configuração do ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="9a28d-159">**ASP.NET configuration settings**</span></span>

<span data-ttu-id="9a28d-160">Esta seção inclui definições de configuração que podem ser definidas no `aspnet.config` arquivo.</span><span class="sxs-lookup"><span data-stu-id="9a28d-160">This section includes configuration settings that can be set in the `aspnet.config` file.</span></span> <span data-ttu-id="9a28d-161">Esse arquivo é encontrado em um dos dois locais, dependendo da plataforma:</span><span class="sxs-lookup"><span data-stu-id="9a28d-161">This file is found in one of two locations, depending on platform:</span></span>

- `%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet.config`
- `%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet.config`

<span data-ttu-id="9a28d-162">Configurações do ASP.NET que podem melhorar o desempenho do SignalR incluem o seguinte:</span><span class="sxs-lookup"><span data-stu-id="9a28d-162">ASP.NET settings that may improve SignalR performance include the following:</span></span>

- <span data-ttu-id="9a28d-163">**Máximo de solicitações simultâneas por CPU**: Aumentar essa configuração pode atenuar os gargalos de desempenho.</span><span class="sxs-lookup"><span data-stu-id="9a28d-163">**Maximum concurrent requests per CPU**: Increasing this setting may alleviate performance bottlenecks.</span></span> <span data-ttu-id="9a28d-164">Para aumentar essa configuração, adicione a seguinte definição de configuração para o `aspnet.config` arquivo:</span><span class="sxs-lookup"><span data-stu-id="9a28d-164">To increase this setting, add the following configuration setting to the `aspnet.config` file:</span></span>

    [!code-xml[Main](signalr-performance/samples/sample5.xml?highlight=4)]
- <span data-ttu-id="9a28d-165">**Limite da fila de solicitação**: Quando o número total de conexões excede o `maxConcurrentRequestsPerCPU` definindo, ASP.NET será iniciado usando uma fila de solicitações de limitação.</span><span class="sxs-lookup"><span data-stu-id="9a28d-165">**Request Queue Limit**: When the total number of connections exceeds the `maxConcurrentRequestsPerCPU` setting, ASP.NET will start throttling requests using a queue.</span></span> <span data-ttu-id="9a28d-166">Para aumentar o tamanho da fila, você pode aumentar o `requestQueueLimit` configuração.</span><span class="sxs-lookup"><span data-stu-id="9a28d-166">To increase the size of the queue, you can increase the `requestQueueLimit` setting.</span></span> <span data-ttu-id="9a28d-167">Para fazer isso, adicione a seguinte definição de configuração para o `processModel` nó no `config/machine.config` (em vez de `aspnet.config`):</span><span class="sxs-lookup"><span data-stu-id="9a28d-167">To do this, add the following configuration setting to the `processModel` node in `config/machine.config` (rather than `aspnet.config`):</span></span>

    [!code-xml[Main](signalr-performance/samples/sample6.xml)]

<a id="troubleshooting"></a>

## <a name="troubleshooting-performance-issues"></a><span data-ttu-id="9a28d-168">Solucionando problemas de desempenho</span><span class="sxs-lookup"><span data-stu-id="9a28d-168">Troubleshooting performance issues</span></span>

<span data-ttu-id="9a28d-169">Esta seção descreve maneiras de Localizar gargalos de desempenho em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9a28d-169">This section describes ways to find performance bottlenecks in your application.</span></span>

### <a name="verifying-that-websocket-is-being-used"></a><span data-ttu-id="9a28d-170">Verificando se o WebSocket está sendo usado</span><span class="sxs-lookup"><span data-stu-id="9a28d-170">Verifying that WebSocket is being used</span></span>

<span data-ttu-id="9a28d-171">Enquanto o SignalR pode usar uma variedade de transportes para comunicação entre cliente e servidor, WebSocket oferece uma vantagem de desempenho significativos e deve ser usado se o cliente e servidor dão suporte a ele.</span><span class="sxs-lookup"><span data-stu-id="9a28d-171">While SignalR can use a variety of transports for communication between client and server, WebSocket offers a significant performance advantage, and should be used if the client and server support it.</span></span> <span data-ttu-id="9a28d-172">Para determinar se o seu cliente e servidor atenderem os requisitos do WebSocket, consulte [transportes e Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span><span class="sxs-lookup"><span data-stu-id="9a28d-172">To determine if your client and server meet the requirements for WebSocket, see [Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span></span> <span data-ttu-id="9a28d-173">Para determinar qual transporte está sendo usado em seu aplicativo, você pode usar as ferramentas de desenvolvedor do navegador e examine os logs para ver qual transporte está sendo usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="9a28d-173">To determine what transport is being used in your application, you can use the browser developer tools, and examine the logs to see what transport is being used for the connection.</span></span> <span data-ttu-id="9a28d-174">Para obter informações sobre como usar as ferramentas de desenvolvimento do navegador no Internet Explorer e Chrome, consulte [transportes e Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span><span class="sxs-lookup"><span data-stu-id="9a28d-174">For information on using the browser development tools in Internet Explorer and Chrome, see [Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span></span>

<a id="perfcounters"></a>

## <a name="using-signalr-performance-counters"></a><span data-ttu-id="9a28d-175">Usando contadores de desempenho do SignalR</span><span class="sxs-lookup"><span data-stu-id="9a28d-175">Using SignalR performance counters</span></span>

<span data-ttu-id="9a28d-176">Esta seção descreve como habilitar e usar contadores de desempenho do SignalR, encontrado no `Microsoft.AspNet.SignalR.Utils` pacote.</span><span class="sxs-lookup"><span data-stu-id="9a28d-176">This section describes how to enable and use SignalR performance counters, found in the `Microsoft.AspNet.SignalR.Utils` package.</span></span>

### <a name="installing-signalrexe"></a><span data-ttu-id="9a28d-177">Instalando signalr.exe</span><span class="sxs-lookup"><span data-stu-id="9a28d-177">Installing signalr.exe</span></span>

<span data-ttu-id="9a28d-178">Contadores de desempenho podem ser adicionados ao servidor usando um utilitário chamado SignalR.exe.</span><span class="sxs-lookup"><span data-stu-id="9a28d-178">Performance counters can be added to the server using a utility called SignalR.exe.</span></span> <span data-ttu-id="9a28d-179">Para instalar esse utilitário, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="9a28d-179">To install this utility, follow these steps:</span></span>

1. <span data-ttu-id="9a28d-180">No Visual Studio, selecione **ferramentas** > **Gerenciador de pacotes NuGet** > **gerenciar pacotes NuGet para solução**</span><span class="sxs-lookup"><span data-stu-id="9a28d-180">In Visual Studio, select **Tools** > **NuGet Package Manager** > **Manage NuGet Packages for Solution**</span></span>
2. <span data-ttu-id="9a28d-181">Pesquise **signalr.utils**e selecione instalar.</span><span class="sxs-lookup"><span data-stu-id="9a28d-181">Search for **signalr.utils**, and select Install.</span></span>

    ![](signalr-performance/_static/image1.png)
3. <span data-ttu-id="9a28d-182">Aceite o contrato de licença para instalar o pacote.</span><span class="sxs-lookup"><span data-stu-id="9a28d-182">Accept the license agreement to install the package.</span></span>
4. <span data-ttu-id="9a28d-183">SignalR.exe será instalado para `<project folder>/packages/Microsoft.AspNet.SignalR.Utils.<version>/tools`.</span><span class="sxs-lookup"><span data-stu-id="9a28d-183">SignalR.exe will be installed to `<project folder>/packages/Microsoft.AspNet.SignalR.Utils.<version>/tools`.</span></span>

### <a name="installing-performance-counters-with-signalrexe"></a><span data-ttu-id="9a28d-184">Instalando Contadores de desempenho com SignalR.exe</span><span class="sxs-lookup"><span data-stu-id="9a28d-184">Installing performance counters with SignalR.exe</span></span>

<span data-ttu-id="9a28d-185">Para instalar contadores de desempenho do SignalR, execute SignalR.exe em um prompt de comando com o seguinte parâmetro:</span><span class="sxs-lookup"><span data-stu-id="9a28d-185">To install SignalR performance counters, run SignalR.exe in an elevated command prompt with the following parameter:</span></span>

[!code-console[Main](signalr-performance/samples/sample7.cmd)]

<span data-ttu-id="9a28d-186">Para remover os contadores de desempenho do SignalR, execute SignalR.exe em um prompt de comando com o seguinte parâmetro:</span><span class="sxs-lookup"><span data-stu-id="9a28d-186">To remove SignalR performance counters, run SignalR.exe in an elevated command prompt with the following parameter:</span></span>

[!code-console[Main](signalr-performance/samples/sample8.cmd)]

### <a name="signalr-performance-counters"></a><span data-ttu-id="9a28d-187">Contadores de desempenho do SignalR</span><span class="sxs-lookup"><span data-stu-id="9a28d-187">SignalR Performance counters</span></span>

<span data-ttu-id="9a28d-188">O pacote de utilitários instala os seguintes contadores de desempenho.</span><span class="sxs-lookup"><span data-stu-id="9a28d-188">The utilities package installs the following performance counters.</span></span> <span data-ttu-id="9a28d-189">Os contadores de "Total" medem o número de eventos, pois o pool de aplicativos do último ou o servidor reiniciar.</span><span class="sxs-lookup"><span data-stu-id="9a28d-189">The "Total" counters measure the number of events since the last application pool or server restart.</span></span>

<span data-ttu-id="9a28d-190">**Métricas de Conexão**</span><span class="sxs-lookup"><span data-stu-id="9a28d-190">**Connection metrics**</span></span>

<span data-ttu-id="9a28d-191">As seguintes métricas de medem os eventos de tempo de vida de conexão que ocorrem.</span><span class="sxs-lookup"><span data-stu-id="9a28d-191">The following metrics measure the connection lifetime events that occur.</span></span> <span data-ttu-id="9a28d-192">Para obter mais informações, consulte [Noções básicas e tratamento de eventos de tempo de vida de Conexão](../guide-to-the-api/handling-connection-lifetime-events.md).</span><span class="sxs-lookup"><span data-stu-id="9a28d-192">For more information, see [Understanding and Handling Connection Lifetime Events](../guide-to-the-api/handling-connection-lifetime-events.md).</span></span>

- <span data-ttu-id="9a28d-193">**Conexões conectadas**</span><span class="sxs-lookup"><span data-stu-id="9a28d-193">**Connections Connected**</span></span>
- <span data-ttu-id="9a28d-194">**Conexões reconectadas**</span><span class="sxs-lookup"><span data-stu-id="9a28d-194">**Connections Reconnected**</span></span>
- <span data-ttu-id="9a28d-195">**Conexões desconectadas**</span><span class="sxs-lookup"><span data-stu-id="9a28d-195">**Connections Disconnected**</span></span>
- <span data-ttu-id="9a28d-196">**Conexões atuais**</span><span class="sxs-lookup"><span data-stu-id="9a28d-196">**Connections Current**</span></span>

<span data-ttu-id="9a28d-197">**Métricas de mensagem**</span><span class="sxs-lookup"><span data-stu-id="9a28d-197">**Message metrics**</span></span>

<span data-ttu-id="9a28d-198">As seguintes métricas de medem o tráfego de mensagens gerado pelo SignalR.</span><span class="sxs-lookup"><span data-stu-id="9a28d-198">The following metrics measure the message traffic generated by SignalR.</span></span>

- <span data-ttu-id="9a28d-199">**Total de mensagens recebidas de Conexão**</span><span class="sxs-lookup"><span data-stu-id="9a28d-199">**Connection Messages Received Total**</span></span>
- <span data-ttu-id="9a28d-200">**Total de mensagens de Conexão enviadas**</span><span class="sxs-lookup"><span data-stu-id="9a28d-200">**Connection Messages Sent Total**</span></span>
- <span data-ttu-id="9a28d-201">**Conexão de mensagens recebidos/s**</span><span class="sxs-lookup"><span data-stu-id="9a28d-201">**Connection Messages Received/Sec**</span></span>
- <span data-ttu-id="9a28d-202">**Conexão de mensagens enviadas/s**</span><span class="sxs-lookup"><span data-stu-id="9a28d-202">**Connection Messages Sent/Sec**</span></span>

<span data-ttu-id="9a28d-203">**Métricas do barramento de mensagem**</span><span class="sxs-lookup"><span data-stu-id="9a28d-203">**Message bus metrics**</span></span>

<span data-ttu-id="9a28d-204">As seguintes métricas de medem o tráfego por meio do barramento de mensagem de SignalR interno, a fila na qual todas as mensagens do SignalR entradas e saídas são colocadas.</span><span class="sxs-lookup"><span data-stu-id="9a28d-204">The following metrics measure traffic through the internal SignalR message bus, the queue in which all incoming and outgoing SignalR messages are placed.</span></span> <span data-ttu-id="9a28d-205">É uma mensagem **publicado** quando ele é enviado ou difusão.</span><span class="sxs-lookup"><span data-stu-id="9a28d-205">A message is **Published** when it is sent or broadcast.</span></span> <span data-ttu-id="9a28d-206">Um **assinante** nesse contexto é uma assinatura do barramento de mensagem; isso deve ser igual ao número de clientes mais o próprio servidor.</span><span class="sxs-lookup"><span data-stu-id="9a28d-206">A **Subscriber** in this context is a subscription on the message bus; this should equal the number of clients plus the server itself.</span></span> <span data-ttu-id="9a28d-207">Uma **trabalhador alocado** é um componente que envia dados para as conexões ativas; um **trabalho ocupado** é aquele que está ativamente enviando uma mensagem.</span><span class="sxs-lookup"><span data-stu-id="9a28d-207">An **Allocated Worker** is a component that sends data to active connections; a **Busy Worker** is one that is actively sending a message.</span></span>

- <span data-ttu-id="9a28d-208">**Total recebido de mensagens do barramento de mensagem**</span><span class="sxs-lookup"><span data-stu-id="9a28d-208">**Message Bus Messages Received Total**</span></span>
- <span data-ttu-id="9a28d-209">**Mensagens de barramento de mensagem recebidos/s**</span><span class="sxs-lookup"><span data-stu-id="9a28d-209">**Message Bus Messages Received/Sec**</span></span>
- <span data-ttu-id="9a28d-210">**Mensagens do barramento de mensagem publicado Total**</span><span class="sxs-lookup"><span data-stu-id="9a28d-210">**Message Bus Messages Published Total**</span></span>
- <span data-ttu-id="9a28d-211">**Mensagens de barramento de mensagem publicado/s**</span><span class="sxs-lookup"><span data-stu-id="9a28d-211">**Message Bus Messages Published/Sec**</span></span>
- <span data-ttu-id="9a28d-212">**Atual de assinantes do barramento de mensagem**</span><span class="sxs-lookup"><span data-stu-id="9a28d-212">**Message Bus Subscribers Current**</span></span>
- <span data-ttu-id="9a28d-213">**Total de assinantes do barramento de mensagem**</span><span class="sxs-lookup"><span data-stu-id="9a28d-213">**Message Bus Subscribers Total**</span></span>
- <span data-ttu-id="9a28d-214">**Os assinantes do barramento de mensagem/s**</span><span class="sxs-lookup"><span data-stu-id="9a28d-214">**Message Bus Subscribers/Sec**</span></span>
- <span data-ttu-id="9a28d-215">**Barramento de mensagem alocada trabalhadores**</span><span class="sxs-lookup"><span data-stu-id="9a28d-215">**Message Bus Allocated Workers**</span></span>
- <span data-ttu-id="9a28d-216">**Trabalhos ocupados do barramento de mensagem**</span><span class="sxs-lookup"><span data-stu-id="9a28d-216">**Message Bus Busy Workers**</span></span>
- <span data-ttu-id="9a28d-217">**Atual de tópicos do barramento de mensagem**</span><span class="sxs-lookup"><span data-stu-id="9a28d-217">**Message Bus Topics Current**</span></span>

<span data-ttu-id="9a28d-218">**Métricas de erro**</span><span class="sxs-lookup"><span data-stu-id="9a28d-218">**Error metrics**</span></span>

<span data-ttu-id="9a28d-219">As seguintes métricas de medem erros gerados pelo tráfego de mensagens do SignalR.</span><span class="sxs-lookup"><span data-stu-id="9a28d-219">The following metrics measure errors generated by SignalR message traffic.</span></span> <span data-ttu-id="9a28d-220">**Resolução de hub** erros ocorrem quando um hub ou um método de hub não pode ser resolvido.</span><span class="sxs-lookup"><span data-stu-id="9a28d-220">**Hub Resolution** errors occur when a hub or hub method cannot be resolved.</span></span> <span data-ttu-id="9a28d-221">**Invocação de hub** erros são as exceções geradas durante a invocação de um método de hub.</span><span class="sxs-lookup"><span data-stu-id="9a28d-221">**Hub Invocation** errors are exceptions thrown while invoking a hub method.</span></span> <span data-ttu-id="9a28d-222">**Transporte** erros são erros de conexão gerados durante uma solicitação HTTP ou uma resposta.</span><span class="sxs-lookup"><span data-stu-id="9a28d-222">**Transport** errors are connection errors thrown during an HTTP request or response.</span></span>

- <span data-ttu-id="9a28d-223">**Erros: Total de todos os**</span><span class="sxs-lookup"><span data-stu-id="9a28d-223">**Errors: All Total**</span></span>
- <span data-ttu-id="9a28d-224">**Erros: Todos/s**</span><span class="sxs-lookup"><span data-stu-id="9a28d-224">**Errors: All/Sec**</span></span>
- <span data-ttu-id="9a28d-225">**Erros: Total de resolução de Hub**</span><span class="sxs-lookup"><span data-stu-id="9a28d-225">**Errors: Hub Resolution Total**</span></span>
- <span data-ttu-id="9a28d-226">**Erros: Resolução de Hub/s**</span><span class="sxs-lookup"><span data-stu-id="9a28d-226">**Errors: Hub Resolution/Sec**</span></span>
- <span data-ttu-id="9a28d-227">**Erros: Total de invocação de Hub**</span><span class="sxs-lookup"><span data-stu-id="9a28d-227">**Errors: Hub Invocation Total**</span></span>
- <span data-ttu-id="9a28d-228">**Erros: Invocação de Hub/s**</span><span class="sxs-lookup"><span data-stu-id="9a28d-228">**Errors: Hub Invocation/Sec**</span></span>
- <span data-ttu-id="9a28d-229">**Erros: Total de transporte**</span><span class="sxs-lookup"><span data-stu-id="9a28d-229">**Errors: Transport Total**</span></span>
- <span data-ttu-id="9a28d-230">**Erros: Transporte/s**</span><span class="sxs-lookup"><span data-stu-id="9a28d-230">**Errors: Transport/Sec**</span></span>

<a id="scaleout_metrics"></a>

<span data-ttu-id="9a28d-231">**Métricas de expansão**</span><span class="sxs-lookup"><span data-stu-id="9a28d-231">**Scaleout metrics**</span></span>

<span data-ttu-id="9a28d-232">As seguintes métricas de medem o tráfego e os erros gerados pelo provedor de expansão.</span><span class="sxs-lookup"><span data-stu-id="9a28d-232">The following metrics measure traffic and errors generated by the scaleout provider.</span></span> <span data-ttu-id="9a28d-233">Um **Stream** neste contexto é uma unidade de escala usada pelo provedor de expansão; isso é uma tabela se o SQL Server é usado, um tópico que se o barramento de serviço é usado e uma assinatura se o Redis é usado.</span><span class="sxs-lookup"><span data-stu-id="9a28d-233">A **Stream** in this context is a scale unit used by the scaleout provider; this is a table if SQL Server is used, a Topic if Service Bus is used, and a Subscription if Redis is used.</span></span> <span data-ttu-id="9a28d-234">Garante que cada fluxo ordenada operações de leitura e gravação; um único fluxo é um possível afunilamento de escala, portanto, o número de fluxos que pode ser aumentado para ajudar a reduzir esse gargalo.</span><span class="sxs-lookup"><span data-stu-id="9a28d-234">Each stream ensures ordered read and write operations; a single stream is a potential scale bottleneck, so the number of streams can be increased to help reduce that bottleneck.</span></span> <span data-ttu-id="9a28d-235">Se forem usados vários fluxos, SignalR distribuirá automaticamente mensagens (fragmento) entre esses fluxos de forma que garante que as mensagens enviadas de qualquer determinada conexão estão em ordem.</span><span class="sxs-lookup"><span data-stu-id="9a28d-235">If multiple streams are used, SignalR will automatically distribute (shard) messages across these streams in a way that ensures messages sent from any given connection are in order.</span></span>

<span data-ttu-id="9a28d-236">O [MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx) configuração controla o comprimento da fila de envio de expansão mantida pelo SignalR.</span><span class="sxs-lookup"><span data-stu-id="9a28d-236">The [MaxQueueLength](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.scaleoutconfiguration.maxqueuelength(v=vs.118).aspx) setting controls the length of the scaleout send queue maintained by SignalR.</span></span> <span data-ttu-id="9a28d-237">Configurando-a como um valor maior que 0 colocará todas as mensagens em uma fila de envio para ser enviado um de cada vez para o backplane de mensagem configurado.</span><span class="sxs-lookup"><span data-stu-id="9a28d-237">Setting it to a value greater than 0 will place all messages in a send queue to be sent one at a time to the configured messaging backplane.</span></span> <span data-ttu-id="9a28d-238">Se o tamanho da fila de ultrapassar o tamanho configurado, as chamadas subsequentes para enviar irá falharem imediatamente com uma [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception(v=vs.118).aspx) até que o número de mensagens na fila é menor que a configuração novamente.</span><span class="sxs-lookup"><span data-stu-id="9a28d-238">If the size of the queue goes above the configured length, subsequent calls to send will fail immediately with an [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception(v=vs.118).aspx) until the number of messages in the queue is less than the setting again.</span></span> <span data-ttu-id="9a28d-239">Colocação na fila é desabilitada por padrão, pois os backplanes implementados geralmente têm seus próprios enfileiramento de mensagens ou o controle de fluxo em vigor.</span><span class="sxs-lookup"><span data-stu-id="9a28d-239">Queueing is disabled by default because the implemented backplanes generally have their own queuing or flow-control in place.</span></span> <span data-ttu-id="9a28d-240">No caso do SQL Server, pooling de conexão efetivamente limita o número de envios acontecendo em qualquer momento.</span><span class="sxs-lookup"><span data-stu-id="9a28d-240">In the case of SQL Server, connection pooling effectively limits the number of sends going on at any one time.</span></span>

<span data-ttu-id="9a28d-241">Por padrão, apenas um fluxo é usado para o SQL Server e o Redis, cinco fluxos são usados para o barramento de serviço e enfileiramento está desabilitado, mas essas configurações podem ser alteradas por meio da configuração do SQL Server e do barramento de serviço:</span><span class="sxs-lookup"><span data-stu-id="9a28d-241">By default, only one stream is used for SQL Server and Redis, five streams are used for Service Bus, and queueing is disabled, but these settings can be changed through configuration on SQL Server and Service Bus:</span></span>

<span data-ttu-id="9a28d-242">**Código do servidor .NET para configurar o comprimento da fila e a contagem de tabela para backplane do SQL Server**</span><span class="sxs-lookup"><span data-stu-id="9a28d-242">**.NET Server Code for configuring table count and queue length for SQL Server backplane**</span></span>

[!code-csharp[Main](signalr-performance/samples/sample9.cs)]

<span data-ttu-id="9a28d-243">**Código do servidor .NET para configurar o comprimento de fila e a contagem de tópico para backplane do barramento de serviço**</span><span class="sxs-lookup"><span data-stu-id="9a28d-243">**.NET Server Code for configuring topic count and queue length for Service Bus backplane**</span></span>

[!code-csharp[Main](signalr-performance/samples/sample10.cs)]

<span data-ttu-id="9a28d-244">Um **Buffering** fluxo é aquele que entrou em um estado de falha; quando o fluxo está no estado de falha, todas as mensagens enviadas ao backplane falhará imediatamente até que o fluxo não está em falha.</span><span class="sxs-lookup"><span data-stu-id="9a28d-244">A **Buffering** stream is one that has entered a faulted state; when the stream is in the faulted state, all messages sent to the backplane will fail immediately until the stream is no longer faulting.</span></span> <span data-ttu-id="9a28d-245">O **comprimento da fila de envio** é o número de mensagens que foram lançadas, mas ainda não foram enviados.</span><span class="sxs-lookup"><span data-stu-id="9a28d-245">The **Send Queue Length** is the number of messages that have been posted but not yet sent.</span></span>

- <span data-ttu-id="9a28d-246">**Barramento de mensagem escalável mensagens recebidos/s**</span><span class="sxs-lookup"><span data-stu-id="9a28d-246">**Scaleout Message Bus Messages Received/Sec**</span></span>
- <span data-ttu-id="9a28d-247">**Total de fluxos de expansão**</span><span class="sxs-lookup"><span data-stu-id="9a28d-247">**Scaleout Streams Total**</span></span>
- <span data-ttu-id="9a28d-248">**Fluxos de expansão aberto**</span><span class="sxs-lookup"><span data-stu-id="9a28d-248">**Scaleout Streams Open**</span></span>
- <span data-ttu-id="9a28d-249">**Fluxos de expansão de buffer**</span><span class="sxs-lookup"><span data-stu-id="9a28d-249">**Scaleout Streams Buffering**</span></span>
- <span data-ttu-id="9a28d-250">**Total de erros de expansão**</span><span class="sxs-lookup"><span data-stu-id="9a28d-250">**Scaleout Errors Total**</span></span>
- <span data-ttu-id="9a28d-251">**Erros de expansão/s**</span><span class="sxs-lookup"><span data-stu-id="9a28d-251">**Scaleout Errors/Sec**</span></span>
- <span data-ttu-id="9a28d-252">**Comprimento da fila de envio de expansão**</span><span class="sxs-lookup"><span data-stu-id="9a28d-252">**Scaleout Send Queue Length**</span></span>

<span data-ttu-id="9a28d-253">Para obter mais informações sobre o que está sendo medida esses contadores, consulte [SignalR Scaleout com o Azure Service Bus](scaleout-with-windows-azure-service-bus.md).</span><span class="sxs-lookup"><span data-stu-id="9a28d-253">For more information on what these counters are measuring, see [SignalR Scaleout with Azure Service Bus](scaleout-with-windows-azure-service-bus.md).</span></span>

<a id="othercounters"></a>

## <a name="using-other-performance-counters"></a><span data-ttu-id="9a28d-254">Usando outros contadores de desempenho</span><span class="sxs-lookup"><span data-stu-id="9a28d-254">Using other performance counters</span></span>

<span data-ttu-id="9a28d-255">Os seguintes contadores de desempenho também podem ser útil no monitoramento do desempenho do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9a28d-255">The following performance counters may also be useful in monitoring your application's performance.</span></span>

<span data-ttu-id="9a28d-256">**Memória**</span><span class="sxs-lookup"><span data-stu-id="9a28d-256">**Memory**</span></span>

- <span data-ttu-id="9a28d-257">Memória do .NET CLR\\# bytes em todos os Heaps (para w3wp)</span><span class="sxs-lookup"><span data-stu-id="9a28d-257">.NET CLR Memory\\# bytes in all Heaps (for w3wp)</span></span>

<span data-ttu-id="9a28d-258">**ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="9a28d-258">**ASP.NET**</span></span>

- <span data-ttu-id="9a28d-259">ASP.NET \ solicitações atuais</span><span class="sxs-lookup"><span data-stu-id="9a28d-259">ASP.NET\Requests Current</span></span>
- <span data-ttu-id="9a28d-260">ASP.NET\Queued</span><span class="sxs-lookup"><span data-stu-id="9a28d-260">ASP.NET\Queued</span></span>
- <span data-ttu-id="9a28d-261">ASP.NET\Rejected</span><span class="sxs-lookup"><span data-stu-id="9a28d-261">ASP.NET\Rejected</span></span>

<span data-ttu-id="9a28d-262">**CPU**</span><span class="sxs-lookup"><span data-stu-id="9a28d-262">**CPU**</span></span>

- <span data-ttu-id="9a28d-263">Tempo do processador Information\Processor</span><span class="sxs-lookup"><span data-stu-id="9a28d-263">Processor Information\Processor Time</span></span>

<span data-ttu-id="9a28d-264">**TCP/IP**</span><span class="sxs-lookup"><span data-stu-id="9a28d-264">**TCP/IP**</span></span>

- <span data-ttu-id="9a28d-265">TCPv6/conexões estabelecidas</span><span class="sxs-lookup"><span data-stu-id="9a28d-265">TCPv6/Connections Established</span></span>
- <span data-ttu-id="9a28d-266">TCPv4/conexões estabelecidas</span><span class="sxs-lookup"><span data-stu-id="9a28d-266">TCPv4/Connections Established</span></span>

<span data-ttu-id="9a28d-267">**Serviço Web**</span><span class="sxs-lookup"><span data-stu-id="9a28d-267">**Web Service**</span></span>

- <span data-ttu-id="9a28d-268">Web \ conexões atuais</span><span class="sxs-lookup"><span data-stu-id="9a28d-268">Web Service\Current Connections</span></span>
- <span data-ttu-id="9a28d-269">Conexões de Service\Maximum da Web</span><span class="sxs-lookup"><span data-stu-id="9a28d-269">Web Service\Maximum Connections</span></span>

<span data-ttu-id="9a28d-270">**Threading**</span><span class="sxs-lookup"><span data-stu-id="9a28d-270">**Threading**</span></span>

- <span data-ttu-id="9a28d-271">.NET CLR bloqueios e Threads\\n º de Threads lógicos atuais</span><span class="sxs-lookup"><span data-stu-id="9a28d-271">.NET CLR Locks And Threads\\# of current logical Threads</span></span>
- <span data-ttu-id="9a28d-272">.NET CLR bloqueios e Threads\\# atual de Threads físicos</span><span class="sxs-lookup"><span data-stu-id="9a28d-272">.NET CLR Locks And Threads\\# of current physical Threads</span></span>

<a id="otherresources"></a>

## <a name="other-resources"></a><span data-ttu-id="9a28d-273">Outros recursos</span><span class="sxs-lookup"><span data-stu-id="9a28d-273">Other Resources</span></span>

<span data-ttu-id="9a28d-274">Para obter mais informações sobre monitoramento e ajuste de desempenho do ASP.NET, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="9a28d-274">For more information on ASP.NET performance monitoring and tuning, see the following topics:</span></span>

- <span data-ttu-id="9a28d-275">[Visão geral do desempenho do ASP.NET](https://msdn.microsoft.com/library/cc668225(v=vs.100).aspx)</span><span class="sxs-lookup"><span data-stu-id="9a28d-275">[ASP.NET Performance Overview](https://msdn.microsoft.com/library/cc668225(v=vs.100).aspx)</span></span>
- [<span data-ttu-id="9a28d-276">Uso de threads do ASP.NET no IIS 7.5, IIS 7.0 e IIS 6.0</span><span class="sxs-lookup"><span data-stu-id="9a28d-276">ASP.NET Thread Usage on IIS 7.5, IIS 7.0, and IIS 6.0</span></span>](https://blogs.msdn.com/b/tmarq/archive/2007/07/21/asp-net-thread-usage-on-iis-7-0-and-6-0.aspx)
- [<span data-ttu-id="9a28d-277">&lt;applicationPool&gt; (configurações da Web)</span><span class="sxs-lookup"><span data-stu-id="9a28d-277">&lt;applicationPool&gt; Element (Web Settings)</span></span>](https://msdn.microsoft.com/library/dd560842.aspx)
