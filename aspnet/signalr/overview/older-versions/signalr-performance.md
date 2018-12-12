---
uid: signalr/overview/older-versions/signalr-performance
title: Desempenho do SignalR (SignalR 1.x) | Microsoft Docs
author: pfletcher
description: Desempenho do SignalR
ms.author: riande
ms.date: 07/03/2013
ms.assetid: 9594d644-66b6-4223-acdd-23e29a6e4c46
msc.legacyurl: /signalr/overview/older-versions/signalr-performance
msc.type: authoredcontent
ms.openlocfilehash: ea2d3908544ac8b3ea17ceceaf1d2905c5c6f322
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287554"
---
<a name="signalr-performance-signalr-1x"></a><span data-ttu-id="8cffa-103">Desempenho do SignalR (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="8cffa-103">SignalR Performance (SignalR 1.x)</span></span>
====================
<span data-ttu-id="8cffa-104">por [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="8cffa-104">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="8cffa-105">Este tópico descreve como projetar para, medir e melhorar o desempenho em um aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="8cffa-105">This topic describes how to design for, measure, and improve performance in a SignalR application.</span></span>


<span data-ttu-id="8cffa-106">Para obter uma apresentação recente em escala e desempenho do SignalR, consulte [dimensionamento na Web em tempo real com o ASP.NET SignalR](https://channel9.msdn.com/Events/Build/2013/3-502).</span><span class="sxs-lookup"><span data-stu-id="8cffa-106">For a recent presentation on SignalR performance and scaling, see [Scaling the Real-time Web with ASP.NET SignalR](https://channel9.msdn.com/Events/Build/2013/3-502).</span></span>

<span data-ttu-id="8cffa-107">Esse tópico contém as seguintes seções:</span><span class="sxs-lookup"><span data-stu-id="8cffa-107">This topic contains the following sections:</span></span>

- [<span data-ttu-id="8cffa-108">Considerações de design</span><span class="sxs-lookup"><span data-stu-id="8cffa-108">Design considerations</span></span>](#design)
- [<span data-ttu-id="8cffa-109">Ajuste de desempenho do seu servidor SignalR</span><span class="sxs-lookup"><span data-stu-id="8cffa-109">Tuning your SignalR server for performance</span></span>](#tuning)
- [<span data-ttu-id="8cffa-110">Solucionando problemas de desempenho</span><span class="sxs-lookup"><span data-stu-id="8cffa-110">Troubleshooting performance issues</span></span>](#troubleshooting)
- [<span data-ttu-id="8cffa-111">Usando contadores de desempenho do SignalR</span><span class="sxs-lookup"><span data-stu-id="8cffa-111">Using SignalR performance counters</span></span>](#perfcounters)
- [<span data-ttu-id="8cffa-112">Usando outros contadores de desempenho</span><span class="sxs-lookup"><span data-stu-id="8cffa-112">Using other performance counters</span></span>](#othercounters)
- [<span data-ttu-id="8cffa-113">Outros recursos</span><span class="sxs-lookup"><span data-stu-id="8cffa-113">Other resources</span></span>](#otherresources)

<a id="design"></a>

## <a name="design-considerations"></a><span data-ttu-id="8cffa-114">Considerações de design</span><span class="sxs-lookup"><span data-stu-id="8cffa-114">Design considerations</span></span>

<span data-ttu-id="8cffa-115">Esta seção descreve os padrões que podem ser implementados durante o design de um aplicativo do SignalR, para garantir que o desempenho não seja sendo prejudicado, gerando o tráfego de rede desnecessário.</span><span class="sxs-lookup"><span data-stu-id="8cffa-115">This section describes patterns that can be implemented during the design of a SignalR application, to ensure that performance is not being hindered by generating unnecessary network traffic.</span></span>

### <a name="throttling-message-frequency"></a><span data-ttu-id="8cffa-116">Limitação de frequência de mensagens</span><span class="sxs-lookup"><span data-stu-id="8cffa-116">Throttling message frequency</span></span>

<span data-ttu-id="8cffa-117">Mesmo em um aplicativo que envia mensagens a uma alta frequência (por exemplo, um aplicativo de jogos em tempo real), a maioria dos aplicativos não precisa enviar mais de algumas mensagens de um segundo.</span><span class="sxs-lookup"><span data-stu-id="8cffa-117">Even in an application that sends out messages at a high frequency (such as a realtime gaming application), most applications don't need to send more than a few messages a second.</span></span> <span data-ttu-id="8cffa-118">Para reduzir a quantidade de tráfego que gera cada cliente, um loop de mensagem pode ser implementado que filas e envia mensagens não mais frequentemente do que uma taxa fixa (ou seja, até um determinado número de mensagens serão enviados por segundo, se houver mensagens no momento em valo a ser enviado).</span><span class="sxs-lookup"><span data-stu-id="8cffa-118">To reduce the amount of traffic that each client generates, a message loop can be implemented that queues and sends out messages no more frequently than a fixed rate (that is, up to a certain number of messages will be sent every second, if there are messages in that time interval to be sent).</span></span> <span data-ttu-id="8cffa-119">Para um aplicativo de exemplo que limita as mensagens para uma determinada taxa (de cliente e servidor), consulte [em tempo real de alta frequência com SignalR](../getting-started/tutorial-high-frequency-realtime-with-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="8cffa-119">For a sample application that throttles messages to a certain rate (from both client and server), see [High-Frequency Realtime with SignalR](../getting-started/tutorial-high-frequency-realtime-with-signalr.md).</span></span>

### <a name="reducing-message-size"></a><span data-ttu-id="8cffa-120">Reduzindo o tamanho de mensagem</span><span class="sxs-lookup"><span data-stu-id="8cffa-120">Reducing message size</span></span>

<span data-ttu-id="8cffa-121">Você pode reduzir o tamanho de uma mensagem do SignalR, reduzindo o tamanho dos seus objetos serializados.</span><span class="sxs-lookup"><span data-stu-id="8cffa-121">You can reduce the size of a SignalR message by reducing the size of your serialized objects.</span></span> <span data-ttu-id="8cffa-122">No código do servidor, se você estiver enviando um objeto que contém propriedades que não precisam ser transmitidos, impedir que as propriedades que está sendo serializado usando o `JsonIgnore` atributo.</span><span class="sxs-lookup"><span data-stu-id="8cffa-122">In server code, if you're sending an object that contains properties that don't need to be transmitted, prevent those properties from being serialized by using the `JsonIgnore` attribute.</span></span> <span data-ttu-id="8cffa-123">Os nomes das propriedades também são armazenados na mensagem; os nomes das propriedades podem ser reduzidos usando o `JsonProperty` atributo.</span><span class="sxs-lookup"><span data-stu-id="8cffa-123">The names of properties are also stored in the message; the names of properties can be shortened using the `JsonProperty` attribute.</span></span> <span data-ttu-id="8cffa-124">O exemplo de código a seguir demonstra como excluir uma propriedade que está sendo enviada ao cliente e como encurtar os nomes de propriedade:</span><span class="sxs-lookup"><span data-stu-id="8cffa-124">The following code sample demonstrates how to exclude a property from being sent to the client, and how to shorten property names:</span></span>

<span data-ttu-id="8cffa-125">**Código do servidor .NET que demonstra o atributo JsonIgnore para excluir os dados sejam enviados ao cliente e o atributo JsonProperty para reduzir o tamanho de mensagem**</span><span class="sxs-lookup"><span data-stu-id="8cffa-125">**.NET server code that demonstrates the JsonIgnore attribute to exclude data from being sent to the client, and the JsonProperty attribute to reduce message size**</span></span>

[!code-csharp[Main](signalr-performance/samples/sample1.cs?highlight=5,7,10)]

<span data-ttu-id="8cffa-126">Para manter a legibilidade / facilidade de manutenção no código do cliente, os nomes abreviados de propriedade podem ser remapeados para amigável a humanos nomes depois que a mensagem é recebida.</span><span class="sxs-lookup"><span data-stu-id="8cffa-126">In order to retain readability/ maintainability in the client code, the abbreviated property names can be remapped to human-friendly names after the message is received.</span></span> <span data-ttu-id="8cffa-127">O exemplo de código a seguir demonstra uma possível maneira de remapeamento de nomes abreviados para mais longas, definindo um contrato de mensagem (mapeamento) e usando o `reMap` função para aplicar o contrato para a classe de mensagem otimizado:</span><span class="sxs-lookup"><span data-stu-id="8cffa-127">The following code sample demonstrates one possible way of remapping shortened names to longer ones, by defining a message contract (mapping), and using the `reMap` function to apply the contract to the optimized message class:</span></span>

<span data-ttu-id="8cffa-128">**Código de JavaScript do lado do cliente que remapeia reduzido nomes de propriedade para nomes legíveis**</span><span class="sxs-lookup"><span data-stu-id="8cffa-128">**Client-side JavaScript code that remaps shortened property names to human-readable names**</span></span>

[!code-javascript[Main](signalr-performance/samples/sample2.js)]

<span data-ttu-id="8cffa-129">Nomes podem ser reduzidos em mensagens do cliente ao servidor, usando o mesmo método.</span><span class="sxs-lookup"><span data-stu-id="8cffa-129">Names can be shortened in messages from the client to the server as well, using the same method.</span></span>

<span data-ttu-id="8cffa-130">Reduzindo o volume de memória (ou seja, a quantidade de memória usada para a mensagem) da mensagem de objeto também pode melhorar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="8cffa-130">Reducing the memory footprint (that is, the amount of memory used for the message) of the message object can also improve performance.</span></span> <span data-ttu-id="8cffa-131">Por exemplo, se o intervalo completo de um `int` não for necessária, uma `short` ou `byte` pode ser usado em vez disso.</span><span class="sxs-lookup"><span data-stu-id="8cffa-131">For example, if the full range of an `int` is not needed, a `short` or `byte` can be used instead.</span></span>

<span data-ttu-id="8cffa-132">Uma vez que as mensagens são armazenadas no barramento de mensagem na memória do servidor, reduzindo o tamanho de mensagens também pode resolver problemas de memória do servidor.</span><span class="sxs-lookup"><span data-stu-id="8cffa-132">Since messages are stored in the message bus in server memory, reducing the size of messages can also address server memory issues.</span></span>

<a id="tuning"></a>

### <a name="tuning-your-signalr-server-for-performance"></a><span data-ttu-id="8cffa-133">Ajuste de desempenho do seu servidor SignalR</span><span class="sxs-lookup"><span data-stu-id="8cffa-133">Tuning your SignalR server for performance</span></span>

<span data-ttu-id="8cffa-134">As definições de configuração a seguir podem ser usadas para ajustar o servidor para melhorar o desempenho em um aplicativo do SignalR.</span><span class="sxs-lookup"><span data-stu-id="8cffa-134">The following configuration settings can be used to tune your server for better performance in a SignalR application.</span></span> <span data-ttu-id="8cffa-135">Para obter informações gerais sobre como melhorar o desempenho em um aplicativo ASP.NET, consulte [melhorando o desempenho do ASP.NET](https://msdn.microsoft.com/library/ff647787.aspx).</span><span class="sxs-lookup"><span data-stu-id="8cffa-135">For general information on how to improve performance in an ASP.NET application, see [Improving ASP.NET Performance](https://msdn.microsoft.com/library/ff647787.aspx).</span></span>

<span data-ttu-id="8cffa-136">**Definições de configuração do SignalR**</span><span class="sxs-lookup"><span data-stu-id="8cffa-136">**SignalR configuration settings**</span></span>

- <span data-ttu-id="8cffa-137">**DefaultMessageBufferSize**: Por padrão, o SignalR retém 1000 mensagens na memória por hub por conexão.</span><span class="sxs-lookup"><span data-stu-id="8cffa-137">**DefaultMessageBufferSize**: By default, SignalR retains 1000 messages in memory per hub per connection.</span></span> <span data-ttu-id="8cffa-138">Se estiverem sendo usada mensagens grandes, isso pode criar problemas de memória que podem ser atenuados por reduzir esse valor.</span><span class="sxs-lookup"><span data-stu-id="8cffa-138">If large messages are being used, this may create memory issues which can be alleviated by reducing this value.</span></span> <span data-ttu-id="8cffa-139">Essa configuração pode ser definida `Application_Start` manipulador de eventos em um aplicativo ASP.NET ou no `Configuration` método de uma classe de inicialização OWIN em um aplicativo hospedado internamente.</span><span class="sxs-lookup"><span data-stu-id="8cffa-139">This setting can be set in the `Application_Start` event handler in an ASP.NET application, or in the `Configuration` method of an OWIN startup class in a self-hosted application.</span></span> <span data-ttu-id="8cffa-140">O exemplo a seguir demonstra como reduzir esse valor para reduzir o volume de memória do seu aplicativo para reduzir a quantidade de memória de servidor usada:</span><span class="sxs-lookup"><span data-stu-id="8cffa-140">The following sample demonstrates how to reduce this value in order to reduce the memory footprint of your application in order to reduce the amount of server memory used:</span></span>

    <span data-ttu-id="8cffa-141">**Código de servidor do .NET no global. asax para diminuir o tamanho do buffer de mensagem padrão**</span><span class="sxs-lookup"><span data-stu-id="8cffa-141">**.NET server code in Global.asax for decreasing default message buffer size**</span></span>

    [!code-csharp[Main](signalr-performance/samples/sample3.cs)]

<span data-ttu-id="8cffa-142">**Definições de configuração do IIS**</span><span class="sxs-lookup"><span data-stu-id="8cffa-142">**IIS configuration settings**</span></span>

- <span data-ttu-id="8cffa-143">**Máximo de solicitações simultâneas por aplicativo**: Aumentar o número de IIS simultâneo solicitações aumentará disponíveis para atender às solicitações de recursos do servidor.</span><span class="sxs-lookup"><span data-stu-id="8cffa-143">**Max concurrent requests per application**: Increasing the number of concurrent IIS requests will increase server resources available for serving requests.</span></span> <span data-ttu-id="8cffa-144">O valor padrão é 5000; para aumentar essa configuração, execute os seguintes comandos no prompt de comando elevado:</span><span class="sxs-lookup"><span data-stu-id="8cffa-144">The default value is 5000; to increase this setting, execute the following commands in an elevated command prompt:</span></span>

    [!code-console[Main](signalr-performance/samples/sample4.cmd)]

<span data-ttu-id="8cffa-145">**Definições de configuração do ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="8cffa-145">**ASP.NET configuration settings**</span></span>

<span data-ttu-id="8cffa-146">Esta seção inclui definições de configuração que podem ser definidas no `aspnet.config` arquivo.</span><span class="sxs-lookup"><span data-stu-id="8cffa-146">This section includes configuration settings that can be set in the `aspnet.config` file.</span></span> <span data-ttu-id="8cffa-147">Esse arquivo é encontrado em um dos dois locais, dependendo da plataforma:</span><span class="sxs-lookup"><span data-stu-id="8cffa-147">This file is found in one of two locations, depending on platform:</span></span>

- `%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet.config`
- `%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet.config`

<span data-ttu-id="8cffa-148">Configurações do ASP.NET que podem melhorar o desempenho do SignalR incluem o seguinte:</span><span class="sxs-lookup"><span data-stu-id="8cffa-148">ASP.NET settings that may improve SignalR performance include the following:</span></span>

- <span data-ttu-id="8cffa-149">**Máximo de solicitações simultâneas por CPU**: Aumentar essa configuração pode atenuar os gargalos de desempenho.</span><span class="sxs-lookup"><span data-stu-id="8cffa-149">**Maximum concurrent requests per CPU**: Increasing this setting may alleviate performance bottlenecks.</span></span> <span data-ttu-id="8cffa-150">Para aumentar essa configuração, adicione a seguinte definição de configuração para o `aspnet.config` arquivo:</span><span class="sxs-lookup"><span data-stu-id="8cffa-150">To increase this setting, add the following configuration setting to the `aspnet.config` file:</span></span>

    [!code-xml[Main](signalr-performance/samples/sample5.xml?highlight=4)]
- <span data-ttu-id="8cffa-151">**Limite da fila de solicitação**: Quando o número total de conexões excede o `maxConcurrentRequestsPerCPU` definindo, ASP.NET será iniciado usando uma fila de solicitações de limitação.</span><span class="sxs-lookup"><span data-stu-id="8cffa-151">**Request Queue Limit**: When the total number of connections exceeds the `maxConcurrentRequestsPerCPU` setting, ASP.NET will start throttling requests using a queue.</span></span> <span data-ttu-id="8cffa-152">Para aumentar o tamanho da fila, você pode aumentar o `requestQueueLimit` configuração.</span><span class="sxs-lookup"><span data-stu-id="8cffa-152">To increase the size of the queue, you can increase the `requestQueueLimit` setting.</span></span> <span data-ttu-id="8cffa-153">Para fazer isso, adicione a seguinte definição de configuração para o `processModel` nó no `config/machine.config` (em vez de `aspnet.config`):</span><span class="sxs-lookup"><span data-stu-id="8cffa-153">To do this, add the following configuration setting to the `processModel` node in `config/machine.config` (rather than `aspnet.config`):</span></span>

    [!code-xml[Main](signalr-performance/samples/sample6.xml)]

<a id="troubleshooting"></a>

## <a name="troubleshooting-performance-issues"></a><span data-ttu-id="8cffa-154">Solucionando problemas de desempenho</span><span class="sxs-lookup"><span data-stu-id="8cffa-154">Troubleshooting performance issues</span></span>

<span data-ttu-id="8cffa-155">Esta seção descreve maneiras de Localizar gargalos de desempenho em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8cffa-155">This section describes ways to find performance bottlenecks in your application.</span></span>

### <a name="verifying-that-websocket-is-being-used"></a><span data-ttu-id="8cffa-156">Verificando se o WebSocket está sendo usado</span><span class="sxs-lookup"><span data-stu-id="8cffa-156">Verifying that WebSocket is being used</span></span>

<span data-ttu-id="8cffa-157">Enquanto o SignalR pode usar uma variedade de transportes para comunicação entre cliente e servidor, WebSocket oferece uma vantagem de desempenho significativos e deve ser usado se o cliente e servidor dão suporte a ele.</span><span class="sxs-lookup"><span data-stu-id="8cffa-157">While SignalR can use a variety of transports for communication between client and server, WebSocket offers a significant performance advantage, and should be used if the client and server support it.</span></span> <span data-ttu-id="8cffa-158">Para determinar se o seu cliente e servidor atenderem os requisitos do WebSocket, consulte [transportes e Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span><span class="sxs-lookup"><span data-stu-id="8cffa-158">To determine if your client and server meet the requirements for WebSocket, see [Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span></span> <span data-ttu-id="8cffa-159">Para determinar qual transporte está sendo usado em seu aplicativo, você pode usar as ferramentas de desenvolvedor do navegador e examine os logs para ver qual transporte está sendo usado para a conexão.</span><span class="sxs-lookup"><span data-stu-id="8cffa-159">To determine what transport is being used in your application, you can use the browser developer tools, and examine the logs to see what transport is being used for the connection.</span></span> <span data-ttu-id="8cffa-160">Para obter informações sobre como usar as ferramentas de desenvolvimento do navegador no Internet Explorer e Chrome, consulte [transportes e Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span><span class="sxs-lookup"><span data-stu-id="8cffa-160">For information on using the browser development tools in Internet Explorer and Chrome, see [Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span></span>

<a id="perfcounters"></a>

## <a name="using-signalr-performance-counters"></a><span data-ttu-id="8cffa-161">Usando contadores de desempenho do SignalR</span><span class="sxs-lookup"><span data-stu-id="8cffa-161">Using SignalR performance counters</span></span>

<span data-ttu-id="8cffa-162">Esta seção descreve como habilitar e usar contadores de desempenho do SignalR, encontrado no `Microsoft.AspNet.SignalR.Utils` pacote.</span><span class="sxs-lookup"><span data-stu-id="8cffa-162">This section describes how to enable and use SignalR performance counters, found in the `Microsoft.AspNet.SignalR.Utils` package.</span></span>

### <a name="installing-signalrexe"></a><span data-ttu-id="8cffa-163">Instalando signalr.exe</span><span class="sxs-lookup"><span data-stu-id="8cffa-163">Installing signalr.exe</span></span>

<span data-ttu-id="8cffa-164">Contadores de desempenho podem ser adicionados ao servidor usando um utilitário chamado SignalR.exe.</span><span class="sxs-lookup"><span data-stu-id="8cffa-164">Peformance counters can be added to the server using a utility called SignalR.exe.</span></span> <span data-ttu-id="8cffa-165">Para instalar esse utilitário, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="8cffa-165">To install this utility, follow these steps:</span></span>

1. <span data-ttu-id="8cffa-166">No Visual Studio, selecione **ferramentas** > **Gerenciador de pacotes NuGet** > **gerenciar pacotes NuGet para solução**</span><span class="sxs-lookup"><span data-stu-id="8cffa-166">In Visual Studio, select **Tools** > **NuGet Package Manager** > **Manage NuGet Packages for Solution**</span></span>
2. <span data-ttu-id="8cffa-167">Pesquise **signalr.utils**e selecione instalar.</span><span class="sxs-lookup"><span data-stu-id="8cffa-167">Search for **signalr.utils**, and select Install.</span></span>

    ![](signalr-performance/_static/image1.png)
3. <span data-ttu-id="8cffa-168">Aceite o contrato de licença para instalar o pacote.</span><span class="sxs-lookup"><span data-stu-id="8cffa-168">Accept the license agreement to install the package.</span></span>
4. <span data-ttu-id="8cffa-169">SignalR.exe será instalado para `<project folder>/packages/Microsoft.AspNet.SignalR.Utils.<version>/tools`.</span><span class="sxs-lookup"><span data-stu-id="8cffa-169">SignalR.exe will be installed to `<project folder>/packages/Microsoft.AspNet.SignalR.Utils.<version>/tools`.</span></span>

### <a name="installing-performance-counters-with-signalrexe"></a><span data-ttu-id="8cffa-170">Instalando Contadores de desempenho com SignalR.exe</span><span class="sxs-lookup"><span data-stu-id="8cffa-170">Installing performance counters with SignalR.exe</span></span>

<span data-ttu-id="8cffa-171">Para instalar contadores de desempenho do SignalR, execute SignalR.exe em um prompt de comando com o seguinte parâmetro:</span><span class="sxs-lookup"><span data-stu-id="8cffa-171">To install SignalR performance counters, run SignalR.exe in an elevated command prompt with the following parameter:</span></span>

[!code-console[Main](signalr-performance/samples/sample7.cmd)]

<span data-ttu-id="8cffa-172">Para remover os contadores de desempenho do SignalR, execute SignalR.exe em um prompt de comando com o seguinte parâmetro:</span><span class="sxs-lookup"><span data-stu-id="8cffa-172">To remove SignalR performance counters, run SignalR.exe in an elevated command prompt with the following parameter:</span></span>

[!code-console[Main](signalr-performance/samples/sample8.cmd)]

### <a name="signalr-performance-counters"></a><span data-ttu-id="8cffa-173">Contadores de desempenho do SignalR</span><span class="sxs-lookup"><span data-stu-id="8cffa-173">SignalR Performance counters</span></span>

<span data-ttu-id="8cffa-174">O pacote de utilitários instala os seguintes contadores de desempenho.</span><span class="sxs-lookup"><span data-stu-id="8cffa-174">The utilites package installs the following performance counters.</span></span> <span data-ttu-id="8cffa-175">Os contadores de "Total" medem o número de eventos, pois o pool de aplicativos do último ou o servidor reiniciar.</span><span class="sxs-lookup"><span data-stu-id="8cffa-175">The "Total" counters measure the number of events since the last application pool or server restart.</span></span>

<span data-ttu-id="8cffa-176">**Métricas de Conexão**</span><span class="sxs-lookup"><span data-stu-id="8cffa-176">**Connection metrics**</span></span>

<span data-ttu-id="8cffa-177">As seguintes métricas de medem os eventos de tempo de vida de conexão que ocorrem.</span><span class="sxs-lookup"><span data-stu-id="8cffa-177">The following metrics measure the connection lifetime events that occur.</span></span> <span data-ttu-id="8cffa-178">Para obter mais informações, consulte [Noções básicas e tratamento de eventos de tempo de vida de Conexão](../guide-to-the-api/handling-connection-lifetime-events.md).</span><span class="sxs-lookup"><span data-stu-id="8cffa-178">For more information, see [Understanding and Handling Connection Lifetime Events](../guide-to-the-api/handling-connection-lifetime-events.md).</span></span>

- <span data-ttu-id="8cffa-179">**Conexões conectadas**</span><span class="sxs-lookup"><span data-stu-id="8cffa-179">**Connections Connected**</span></span>
- <span data-ttu-id="8cffa-180">**Conexões reconectadas**</span><span class="sxs-lookup"><span data-stu-id="8cffa-180">**Connections Reconnected**</span></span>
- <span data-ttu-id="8cffa-181">**Conexões Disonnected**</span><span class="sxs-lookup"><span data-stu-id="8cffa-181">**Connections Disonnected**</span></span>
- <span data-ttu-id="8cffa-182">**Conexões atuais**</span><span class="sxs-lookup"><span data-stu-id="8cffa-182">**Connections Current**</span></span>

<span data-ttu-id="8cffa-183">**Métricas de mensagem**</span><span class="sxs-lookup"><span data-stu-id="8cffa-183">**Message metrics**</span></span>

<span data-ttu-id="8cffa-184">As seguintes métricas de medem o tráfego de mensagens gerado pelo SignalR.</span><span class="sxs-lookup"><span data-stu-id="8cffa-184">The following metrics measure the message traffic generated by SignalR.</span></span>

- <span data-ttu-id="8cffa-185">**Total de mensagens recebidas de Conexão**</span><span class="sxs-lookup"><span data-stu-id="8cffa-185">**Connection Messages Received Total**</span></span>
- <span data-ttu-id="8cffa-186">**Total de mensagens de Conexão enviadas**</span><span class="sxs-lookup"><span data-stu-id="8cffa-186">**Connection Messages Sent Total**</span></span>
- <span data-ttu-id="8cffa-187">**Conexão de mensagens recebidos/s**</span><span class="sxs-lookup"><span data-stu-id="8cffa-187">**Connection Messages Received/Sec**</span></span>
- <span data-ttu-id="8cffa-188">**Conexão de mensagens enviadas/s**</span><span class="sxs-lookup"><span data-stu-id="8cffa-188">**Connection Messages Sent/Sec**</span></span>

<span data-ttu-id="8cffa-189">**Métricas do barramento de mensagem**</span><span class="sxs-lookup"><span data-stu-id="8cffa-189">**Message bus metrics**</span></span>

<span data-ttu-id="8cffa-190">As seguintes métricas de medem o tráfego por meio do barramento de mensagem de SignalR interno, a fila na qual todas as mensagens do SignalR entradas e saídas são colocadas.</span><span class="sxs-lookup"><span data-stu-id="8cffa-190">The following metrics measure traffic through the internal SignalR message bus, the queue in which all incoming and outgoing SignalR messages are placed.</span></span> <span data-ttu-id="8cffa-191">É uma mensagem **publicado** quando ele é enviado ou difusão.</span><span class="sxs-lookup"><span data-stu-id="8cffa-191">A message is **Published** when it is sent or broadcast.</span></span> <span data-ttu-id="8cffa-192">Um **assinante** nesse contexto é uma assinatura do barramento de mensagem; isso deve ser igual ao número de clientes mais o próprio servidor.</span><span class="sxs-lookup"><span data-stu-id="8cffa-192">A **Subscriber** in this context is a subscription on the message bus; this should equal the number of clients plus the server itself.</span></span> <span data-ttu-id="8cffa-193">Uma **trabalhador alocado** é um componente que envia dados para as conexões ativas; um **trabalho ocupado** é aquele que está ativamente enviando uma mensagem.</span><span class="sxs-lookup"><span data-stu-id="8cffa-193">An **Allocated Worker** is a component that sends data to active connections; a **Busy Worker** is one that is actively sending a message.</span></span>

- <span data-ttu-id="8cffa-194">**Total recebido de mensagens do barramento de mensagem**</span><span class="sxs-lookup"><span data-stu-id="8cffa-194">**Message Bus Messages Received Total**</span></span>
- <span data-ttu-id="8cffa-195">**Mensagens de barramento de mensagem recebidos/s**</span><span class="sxs-lookup"><span data-stu-id="8cffa-195">**Message Bus Messages Received/Sec**</span></span>
- <span data-ttu-id="8cffa-196">**Mensagens do barramento de mensagem publicado Total**</span><span class="sxs-lookup"><span data-stu-id="8cffa-196">**Message Bus Messages Published Total**</span></span>
- <span data-ttu-id="8cffa-197">**Mensagens de barramento de mensagem publicado/s**</span><span class="sxs-lookup"><span data-stu-id="8cffa-197">**Message Bus Messages Published/Sec**</span></span>
- <span data-ttu-id="8cffa-198">**Atual de assinantes do barramento de mensagem**</span><span class="sxs-lookup"><span data-stu-id="8cffa-198">**Message Bus Subscribers Current**</span></span>
- <span data-ttu-id="8cffa-199">**Total de assinantes do barramento de mensagem**</span><span class="sxs-lookup"><span data-stu-id="8cffa-199">**Message Bus Subscribers Total**</span></span>
- <span data-ttu-id="8cffa-200">**Os assinantes do barramento de mensagem/s**</span><span class="sxs-lookup"><span data-stu-id="8cffa-200">**Message Bus Subscribers/Sec**</span></span>
- <span data-ttu-id="8cffa-201">**Barramento de mensagem alocada trabalhadores**</span><span class="sxs-lookup"><span data-stu-id="8cffa-201">**Message Bus Allocated Workers**</span></span>
- <span data-ttu-id="8cffa-202">**Trabalhos ocupados do barramento de mensagem**</span><span class="sxs-lookup"><span data-stu-id="8cffa-202">**Message Bus Busy Workers**</span></span>
- <span data-ttu-id="8cffa-203">**Atual de tópicos do barramento de mensagem**</span><span class="sxs-lookup"><span data-stu-id="8cffa-203">**Message Bus Topics Current**</span></span>

<span data-ttu-id="8cffa-204">**Métricas de erro**</span><span class="sxs-lookup"><span data-stu-id="8cffa-204">**Error metrics**</span></span>

<span data-ttu-id="8cffa-205">As seguintes métricas de medem erros gerados pelo tráfego de mensagens do SignalR.</span><span class="sxs-lookup"><span data-stu-id="8cffa-205">The following metrics measure errors generated by SignalR message traffic.</span></span> <span data-ttu-id="8cffa-206">**Resolução de hub** erros ocorrem quando um hub ou um método de hub não pode ser resolvido.</span><span class="sxs-lookup"><span data-stu-id="8cffa-206">**Hub Resolution** errors occur when a hub or hub method cannot be resolved.</span></span> <span data-ttu-id="8cffa-207">**Invocação de hub** erros são as exceções geradas durante a invocação de um método de hub.</span><span class="sxs-lookup"><span data-stu-id="8cffa-207">**Hub Invocation** errors are exceptions thrown while invoking a hub method.</span></span> <span data-ttu-id="8cffa-208">**Transporte** erros são erros de conexão gerados durante uma solicitação HTTP ou uma resposta.</span><span class="sxs-lookup"><span data-stu-id="8cffa-208">**Transport** errors are connection errors thrown during an HTTP request or response.</span></span>

- <span data-ttu-id="8cffa-209">**Erros: Total de todos os**</span><span class="sxs-lookup"><span data-stu-id="8cffa-209">**Errors: All Total**</span></span>
- <span data-ttu-id="8cffa-210">**Erros: Todos/s**</span><span class="sxs-lookup"><span data-stu-id="8cffa-210">**Errors: All/Sec**</span></span>
- <span data-ttu-id="8cffa-211">**Erros: Total de resolução de Hub**</span><span class="sxs-lookup"><span data-stu-id="8cffa-211">**Errors: Hub Resolution Total**</span></span>
- <span data-ttu-id="8cffa-212">**Erros: Resolução de Hub/s**</span><span class="sxs-lookup"><span data-stu-id="8cffa-212">**Errors: Hub Resolution/Sec**</span></span>
- <span data-ttu-id="8cffa-213">**Erros: Total de invocação de Hub**</span><span class="sxs-lookup"><span data-stu-id="8cffa-213">**Errors: Hub Invocation Total**</span></span>
- <span data-ttu-id="8cffa-214">**Erros: Invocação de Hub/s**</span><span class="sxs-lookup"><span data-stu-id="8cffa-214">**Errors: Hub Invocation/Sec**</span></span>
- <span data-ttu-id="8cffa-215">**Erros: Total de transporte**</span><span class="sxs-lookup"><span data-stu-id="8cffa-215">**Errors: Transport Total**</span></span>
- <span data-ttu-id="8cffa-216">**Erros: Transporte/s**</span><span class="sxs-lookup"><span data-stu-id="8cffa-216">**Errors: Transport/Sec**</span></span>

<span data-ttu-id="8cffa-217">**Métricas de expansão**</span><span class="sxs-lookup"><span data-stu-id="8cffa-217">**Scaleout metrics**</span></span>

<span data-ttu-id="8cffa-218">As seguintes métricas de medem o tráfego e os erros gerados pelo provedor de expansão.</span><span class="sxs-lookup"><span data-stu-id="8cffa-218">The following metrics measure traffic and errors generated by the scaleout provider.</span></span> <span data-ttu-id="8cffa-219">Um **Stream** neste contexto é uma unidade de escala usada pelo provedor de expansão; isso é uma tabela se o SQL Server é usado, um tópico que se o barramento de serviço é usado e uma assinatura se o Redis é usado.</span><span class="sxs-lookup"><span data-stu-id="8cffa-219">A **Stream** in this context is a scale unit used by the scaleout provider; this is a table if SQL Server is used, a Topic if Service Bus is used, and a Subscription if Redis is used.</span></span> <span data-ttu-id="8cffa-220">Por padrão, apenas um fluxo é usado, mas isso pode ser aumentado por meio da configuração do SQL Server e do barramento de serviço.</span><span class="sxs-lookup"><span data-stu-id="8cffa-220">By default, only one stream is used, but this can be increased through configuration on SQL Server and Service Bus.</span></span> <span data-ttu-id="8cffa-221">Um **Buffering** fluxo é aquele que entrou em um estado de falha; quando o fluxo está no estado de falha, todas as mensagens enviadas ao backplane falhará imediatamente até que o fluxo não está em falha.</span><span class="sxs-lookup"><span data-stu-id="8cffa-221">A **Buffering** stream is one that has entered a faulted state; when the stream is in the faulted state, all messages sent to the backplane will fail immediately until the stream is no longer faulting.</span></span> <span data-ttu-id="8cffa-222">O **comprimento da fila de envio** é o número de mensagens que foram lançadas, mas ainda não foram enviados.</span><span class="sxs-lookup"><span data-stu-id="8cffa-222">The **Send Queue Length** is the number of messages that have been posted but not yet sent.</span></span>

- <span data-ttu-id="8cffa-223">**Barramento de mensagem escalável mensagens recebidos/s**</span><span class="sxs-lookup"><span data-stu-id="8cffa-223">**Scaleout Message Bus Messages Received/Sec**</span></span>
- <span data-ttu-id="8cffa-224">**Total de fluxos de expansão**</span><span class="sxs-lookup"><span data-stu-id="8cffa-224">**Scaleout Streams Total**</span></span>
- <span data-ttu-id="8cffa-225">**Fluxos de expansão aberto**</span><span class="sxs-lookup"><span data-stu-id="8cffa-225">**Scaleout Streams Open**</span></span>
- <span data-ttu-id="8cffa-226">**Fluxos de expansão de buffer**</span><span class="sxs-lookup"><span data-stu-id="8cffa-226">**Scaleout Streams Buffering**</span></span>
- <span data-ttu-id="8cffa-227">**Total de erros de expansão**</span><span class="sxs-lookup"><span data-stu-id="8cffa-227">**Scaleout Errors Total**</span></span>
- <span data-ttu-id="8cffa-228">**Erros de expansão/s**</span><span class="sxs-lookup"><span data-stu-id="8cffa-228">**Scaleout Errors/Sec**</span></span>
- <span data-ttu-id="8cffa-229">**Comprimento da fila de envio de expansão**</span><span class="sxs-lookup"><span data-stu-id="8cffa-229">**Scaleout Send Queue Length**</span></span>

<span data-ttu-id="8cffa-230">Para obter mais informações sobre o que está sendo medida esses contadores, consulte [SignalR Scaleout com o Azure Service Bus](scaleout-with-windows-azure-service-bus.md).</span><span class="sxs-lookup"><span data-stu-id="8cffa-230">For more information on what these counters are measuring, see [SignalR Scaleout with Azure Service Bus](scaleout-with-windows-azure-service-bus.md).</span></span>

<a id="othercounters"></a>

## <a name="using-other-performance-counters"></a><span data-ttu-id="8cffa-231">Usando outros contadores de desempenho</span><span class="sxs-lookup"><span data-stu-id="8cffa-231">Using other performance counters</span></span>

<span data-ttu-id="8cffa-232">Os seguintes contadores de desempenho também podem ser útil no monitoramento do desempenho do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8cffa-232">The following performance counters may also be useful in monitoring your application's performance.</span></span>

<span data-ttu-id="8cffa-233">**Memória**</span><span class="sxs-lookup"><span data-stu-id="8cffa-233">**Memory**</span></span>

- <span data-ttu-id="8cffa-234">.NET CLR memória n º de bytes em todos os Heaps (para w3wp)</span><span class="sxs-lookup"><span data-stu-id="8cffa-234">.NET CLR Memory# bytes in all Heaps (for w3wp)</span></span>

<span data-ttu-id="8cffa-235">**ASP.NET**</span><span class="sxs-lookup"><span data-stu-id="8cffa-235">**ASP.NET**</span></span>

- <span data-ttu-id="8cffa-236">ASP.NET \ solicitações atuais</span><span class="sxs-lookup"><span data-stu-id="8cffa-236">ASP.NET\Requests Current</span></span>
- <span data-ttu-id="8cffa-237">ASP.NET\Queued</span><span class="sxs-lookup"><span data-stu-id="8cffa-237">ASP.NET\Queued</span></span>
- <span data-ttu-id="8cffa-238">ASP.NET\Rejected</span><span class="sxs-lookup"><span data-stu-id="8cffa-238">ASP.NET\Rejected</span></span>

<span data-ttu-id="8cffa-239">**CPU**</span><span class="sxs-lookup"><span data-stu-id="8cffa-239">**CPU**</span></span>

- <span data-ttu-id="8cffa-240">Tempo do processador Information\Processor</span><span class="sxs-lookup"><span data-stu-id="8cffa-240">Processor Information\Processor Time</span></span>

<span data-ttu-id="8cffa-241">**TCP/IP**</span><span class="sxs-lookup"><span data-stu-id="8cffa-241">**TCP/IP**</span></span>

- <span data-ttu-id="8cffa-242">TCPv6/conexões estabelecidas</span><span class="sxs-lookup"><span data-stu-id="8cffa-242">TCPv6/Connections Established</span></span>
- <span data-ttu-id="8cffa-243">TCPv4/conexões estabelecidas</span><span class="sxs-lookup"><span data-stu-id="8cffa-243">TCPv4/Connections Established</span></span>

<span data-ttu-id="8cffa-244">**Serviço Web**</span><span class="sxs-lookup"><span data-stu-id="8cffa-244">**Web Service**</span></span>

- <span data-ttu-id="8cffa-245">Web \ conexões atuais</span><span class="sxs-lookup"><span data-stu-id="8cffa-245">Web Service\Current Connections</span></span>
- <span data-ttu-id="8cffa-246">Conexões de Service\Maximum da Web</span><span class="sxs-lookup"><span data-stu-id="8cffa-246">Web Service\Maximum Connections</span></span>

<span data-ttu-id="8cffa-247">**Threading**</span><span class="sxs-lookup"><span data-stu-id="8cffa-247">**Threading**</span></span>

- <span data-ttu-id="8cffa-248">.NET CLR LocksAndThreads\# de Threads lógicos atuais</span><span class="sxs-lookup"><span data-stu-id="8cffa-248">.NET CLR LocksAndThreads\# of current logical Threads</span></span>
- <span data-ttu-id="8cffa-249">Threads do .NET CLR LocksAnd\# atual de Threads físicos</span><span class="sxs-lookup"><span data-stu-id="8cffa-249">.NET CLR LocksAnd Threads\# of current physical Threads</span></span>

<a id="otherresources"></a>

## <a name="other-resources"></a><span data-ttu-id="8cffa-250">Outros recursos</span><span class="sxs-lookup"><span data-stu-id="8cffa-250">Other Resources</span></span>

<span data-ttu-id="8cffa-251">Para obter mais informações sobre monitoramento e ajuste de desempenho do ASP.NET, consulte os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="8cffa-251">For more information on ASP.NET performance monitoring and tuning, see the following topics:</span></span>

- <span data-ttu-id="8cffa-252">[Visão geral do desempenho do ASP.NET](https://msdn.microsoft.com/library/cc668225(v=vs.100).aspx)</span><span class="sxs-lookup"><span data-stu-id="8cffa-252">[ASP.NET Performance Overview](https://msdn.microsoft.com/library/cc668225(v=vs.100).aspx)</span></span>
- [<span data-ttu-id="8cffa-253">Uso de threads do ASP.NET no IIS 7.5, IIS 7.0 e IIS 6.0</span><span class="sxs-lookup"><span data-stu-id="8cffa-253">ASP.NET Thread Usage on IIS 7.5, IIS 7.0, and IIS 6.0</span></span>](https://blogs.msdn.com/b/tmarq/archive/2007/07/21/asp-net-thread-usage-on-iis-7-0-and-6-0.aspx)
- [<span data-ttu-id="8cffa-254">&lt;applicationPool&gt; (configurações da Web)</span><span class="sxs-lookup"><span data-stu-id="8cffa-254">&lt;applicationPool&gt; Element (Web Settings)</span></span>](https://msdn.microsoft.com/library/dd560842.aspx)
