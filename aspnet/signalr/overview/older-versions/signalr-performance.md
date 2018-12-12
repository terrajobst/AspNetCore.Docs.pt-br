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
<a name="signalr-performance-signalr-1x"></a>Desempenho do SignalR (SignalR 1.x)
====================
por [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este tópico descreve como projetar para, medir e melhorar o desempenho em um aplicativo do SignalR.


Para obter uma apresentação recente em escala e desempenho do SignalR, consulte [dimensionamento na Web em tempo real com o ASP.NET SignalR](https://channel9.msdn.com/Events/Build/2013/3-502).

Esse tópico contém as seguintes seções:

- [Considerações de design](#design)
- [Ajuste de desempenho do seu servidor SignalR](#tuning)
- [Solucionando problemas de desempenho](#troubleshooting)
- [Usando contadores de desempenho do SignalR](#perfcounters)
- [Usando outros contadores de desempenho](#othercounters)
- [Outros recursos](#otherresources)

<a id="design"></a>

## <a name="design-considerations"></a>Considerações de design

Esta seção descreve os padrões que podem ser implementados durante o design de um aplicativo do SignalR, para garantir que o desempenho não seja sendo prejudicado, gerando o tráfego de rede desnecessário.

### <a name="throttling-message-frequency"></a>Limitação de frequência de mensagens

Mesmo em um aplicativo que envia mensagens a uma alta frequência (por exemplo, um aplicativo de jogos em tempo real), a maioria dos aplicativos não precisa enviar mais de algumas mensagens de um segundo. Para reduzir a quantidade de tráfego que gera cada cliente, um loop de mensagem pode ser implementado que filas e envia mensagens não mais frequentemente do que uma taxa fixa (ou seja, até um determinado número de mensagens serão enviados por segundo, se houver mensagens no momento em valo a ser enviado). Para um aplicativo de exemplo que limita as mensagens para uma determinada taxa (de cliente e servidor), consulte [em tempo real de alta frequência com SignalR](../getting-started/tutorial-high-frequency-realtime-with-signalr.md).

### <a name="reducing-message-size"></a>Reduzindo o tamanho de mensagem

Você pode reduzir o tamanho de uma mensagem do SignalR, reduzindo o tamanho dos seus objetos serializados. No código do servidor, se você estiver enviando um objeto que contém propriedades que não precisam ser transmitidos, impedir que as propriedades que está sendo serializado usando o `JsonIgnore` atributo. Os nomes das propriedades também são armazenados na mensagem; os nomes das propriedades podem ser reduzidos usando o `JsonProperty` atributo. O exemplo de código a seguir demonstra como excluir uma propriedade que está sendo enviada ao cliente e como encurtar os nomes de propriedade:

**Código do servidor .NET que demonstra o atributo JsonIgnore para excluir os dados sejam enviados ao cliente e o atributo JsonProperty para reduzir o tamanho de mensagem**

[!code-csharp[Main](signalr-performance/samples/sample1.cs?highlight=5,7,10)]

Para manter a legibilidade / facilidade de manutenção no código do cliente, os nomes abreviados de propriedade podem ser remapeados para amigável a humanos nomes depois que a mensagem é recebida. O exemplo de código a seguir demonstra uma possível maneira de remapeamento de nomes abreviados para mais longas, definindo um contrato de mensagem (mapeamento) e usando o `reMap` função para aplicar o contrato para a classe de mensagem otimizado:

**Código de JavaScript do lado do cliente que remapeia reduzido nomes de propriedade para nomes legíveis**

[!code-javascript[Main](signalr-performance/samples/sample2.js)]

Nomes podem ser reduzidos em mensagens do cliente ao servidor, usando o mesmo método.

Reduzindo o volume de memória (ou seja, a quantidade de memória usada para a mensagem) da mensagem de objeto também pode melhorar o desempenho. Por exemplo, se o intervalo completo de um `int` não for necessária, uma `short` ou `byte` pode ser usado em vez disso.

Uma vez que as mensagens são armazenadas no barramento de mensagem na memória do servidor, reduzindo o tamanho de mensagens também pode resolver problemas de memória do servidor.

<a id="tuning"></a>

### <a name="tuning-your-signalr-server-for-performance"></a>Ajuste de desempenho do seu servidor SignalR

As definições de configuração a seguir podem ser usadas para ajustar o servidor para melhorar o desempenho em um aplicativo do SignalR. Para obter informações gerais sobre como melhorar o desempenho em um aplicativo ASP.NET, consulte [melhorando o desempenho do ASP.NET](https://msdn.microsoft.com/library/ff647787.aspx).

**Definições de configuração do SignalR**

- **DefaultMessageBufferSize**: Por padrão, o SignalR retém 1000 mensagens na memória por hub por conexão. Se estiverem sendo usada mensagens grandes, isso pode criar problemas de memória que podem ser atenuados por reduzir esse valor. Essa configuração pode ser definida `Application_Start` manipulador de eventos em um aplicativo ASP.NET ou no `Configuration` método de uma classe de inicialização OWIN em um aplicativo hospedado internamente. O exemplo a seguir demonstra como reduzir esse valor para reduzir o volume de memória do seu aplicativo para reduzir a quantidade de memória de servidor usada:

    **Código de servidor do .NET no global. asax para diminuir o tamanho do buffer de mensagem padrão**

    [!code-csharp[Main](signalr-performance/samples/sample3.cs)]

**Definições de configuração do IIS**

- **Máximo de solicitações simultâneas por aplicativo**: Aumentar o número de IIS simultâneo solicitações aumentará disponíveis para atender às solicitações de recursos do servidor. O valor padrão é 5000; para aumentar essa configuração, execute os seguintes comandos no prompt de comando elevado:

    [!code-console[Main](signalr-performance/samples/sample4.cmd)]

**Definições de configuração do ASP.NET**

Esta seção inclui definições de configuração que podem ser definidas no `aspnet.config` arquivo. Esse arquivo é encontrado em um dos dois locais, dependendo da plataforma:

- `%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet.config`
- `%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet.config`

Configurações do ASP.NET que podem melhorar o desempenho do SignalR incluem o seguinte:

- **Máximo de solicitações simultâneas por CPU**: Aumentar essa configuração pode atenuar os gargalos de desempenho. Para aumentar essa configuração, adicione a seguinte definição de configuração para o `aspnet.config` arquivo:

    [!code-xml[Main](signalr-performance/samples/sample5.xml?highlight=4)]
- **Limite da fila de solicitação**: Quando o número total de conexões excede o `maxConcurrentRequestsPerCPU` definindo, ASP.NET será iniciado usando uma fila de solicitações de limitação. Para aumentar o tamanho da fila, você pode aumentar o `requestQueueLimit` configuração. Para fazer isso, adicione a seguinte definição de configuração para o `processModel` nó no `config/machine.config` (em vez de `aspnet.config`):

    [!code-xml[Main](signalr-performance/samples/sample6.xml)]

<a id="troubleshooting"></a>

## <a name="troubleshooting-performance-issues"></a>Solucionando problemas de desempenho

Esta seção descreve maneiras de Localizar gargalos de desempenho em seu aplicativo.

### <a name="verifying-that-websocket-is-being-used"></a>Verificando se o WebSocket está sendo usado

Enquanto o SignalR pode usar uma variedade de transportes para comunicação entre cliente e servidor, WebSocket oferece uma vantagem de desempenho significativos e deve ser usado se o cliente e servidor dão suporte a ele. Para determinar se o seu cliente e servidor atenderem os requisitos do WebSocket, consulte [transportes e Fallbacks](../getting-started/introduction-to-signalr.md#transports). Para determinar qual transporte está sendo usado em seu aplicativo, você pode usar as ferramentas de desenvolvedor do navegador e examine os logs para ver qual transporte está sendo usado para a conexão. Para obter informações sobre como usar as ferramentas de desenvolvimento do navegador no Internet Explorer e Chrome, consulte [transportes e Fallbacks](../getting-started/introduction-to-signalr.md#transports).

<a id="perfcounters"></a>

## <a name="using-signalr-performance-counters"></a>Usando contadores de desempenho do SignalR

Esta seção descreve como habilitar e usar contadores de desempenho do SignalR, encontrado no `Microsoft.AspNet.SignalR.Utils` pacote.

### <a name="installing-signalrexe"></a>Instalando signalr.exe

Contadores de desempenho podem ser adicionados ao servidor usando um utilitário chamado SignalR.exe. Para instalar esse utilitário, siga estas etapas:

1. No Visual Studio, selecione **ferramentas** > **Gerenciador de pacotes NuGet** > **gerenciar pacotes NuGet para solução**
2. Pesquise **signalr.utils**e selecione instalar.

    ![](signalr-performance/_static/image1.png)
3. Aceite o contrato de licença para instalar o pacote.
4. SignalR.exe será instalado para `<project folder>/packages/Microsoft.AspNet.SignalR.Utils.<version>/tools`.

### <a name="installing-performance-counters-with-signalrexe"></a>Instalando Contadores de desempenho com SignalR.exe

Para instalar contadores de desempenho do SignalR, execute SignalR.exe em um prompt de comando com o seguinte parâmetro:

[!code-console[Main](signalr-performance/samples/sample7.cmd)]

Para remover os contadores de desempenho do SignalR, execute SignalR.exe em um prompt de comando com o seguinte parâmetro:

[!code-console[Main](signalr-performance/samples/sample8.cmd)]

### <a name="signalr-performance-counters"></a>Contadores de desempenho do SignalR

O pacote de utilitários instala os seguintes contadores de desempenho. Os contadores de "Total" medem o número de eventos, pois o pool de aplicativos do último ou o servidor reiniciar.

**Métricas de Conexão**

As seguintes métricas de medem os eventos de tempo de vida de conexão que ocorrem. Para obter mais informações, consulte [Noções básicas e tratamento de eventos de tempo de vida de Conexão](../guide-to-the-api/handling-connection-lifetime-events.md).

- **Conexões conectadas**
- **Conexões reconectadas**
- **Conexões Disonnected**
- **Conexões atuais**

**Métricas de mensagem**

As seguintes métricas de medem o tráfego de mensagens gerado pelo SignalR.

- **Total de mensagens recebidas de Conexão**
- **Total de mensagens de Conexão enviadas**
- **Conexão de mensagens recebidos/s**
- **Conexão de mensagens enviadas/s**

**Métricas do barramento de mensagem**

As seguintes métricas de medem o tráfego por meio do barramento de mensagem de SignalR interno, a fila na qual todas as mensagens do SignalR entradas e saídas são colocadas. É uma mensagem **publicado** quando ele é enviado ou difusão. Um **assinante** nesse contexto é uma assinatura do barramento de mensagem; isso deve ser igual ao número de clientes mais o próprio servidor. Uma **trabalhador alocado** é um componente que envia dados para as conexões ativas; um **trabalho ocupado** é aquele que está ativamente enviando uma mensagem.

- **Total recebido de mensagens do barramento de mensagem**
- **Mensagens de barramento de mensagem recebidos/s**
- **Mensagens do barramento de mensagem publicado Total**
- **Mensagens de barramento de mensagem publicado/s**
- **Atual de assinantes do barramento de mensagem**
- **Total de assinantes do barramento de mensagem**
- **Os assinantes do barramento de mensagem/s**
- **Barramento de mensagem alocada trabalhadores**
- **Trabalhos ocupados do barramento de mensagem**
- **Atual de tópicos do barramento de mensagem**

**Métricas de erro**

As seguintes métricas de medem erros gerados pelo tráfego de mensagens do SignalR. **Resolução de hub** erros ocorrem quando um hub ou um método de hub não pode ser resolvido. **Invocação de hub** erros são as exceções geradas durante a invocação de um método de hub. **Transporte** erros são erros de conexão gerados durante uma solicitação HTTP ou uma resposta.

- **Erros: Total de todos os**
- **Erros: Todos/s**
- **Erros: Total de resolução de Hub**
- **Erros: Resolução de Hub/s**
- **Erros: Total de invocação de Hub**
- **Erros: Invocação de Hub/s**
- **Erros: Total de transporte**
- **Erros: Transporte/s**

**Métricas de expansão**

As seguintes métricas de medem o tráfego e os erros gerados pelo provedor de expansão. Um **Stream** neste contexto é uma unidade de escala usada pelo provedor de expansão; isso é uma tabela se o SQL Server é usado, um tópico que se o barramento de serviço é usado e uma assinatura se o Redis é usado. Por padrão, apenas um fluxo é usado, mas isso pode ser aumentado por meio da configuração do SQL Server e do barramento de serviço. Um **Buffering** fluxo é aquele que entrou em um estado de falha; quando o fluxo está no estado de falha, todas as mensagens enviadas ao backplane falhará imediatamente até que o fluxo não está em falha. O **comprimento da fila de envio** é o número de mensagens que foram lançadas, mas ainda não foram enviados.

- **Barramento de mensagem escalável mensagens recebidos/s**
- **Total de fluxos de expansão**
- **Fluxos de expansão aberto**
- **Fluxos de expansão de buffer**
- **Total de erros de expansão**
- **Erros de expansão/s**
- **Comprimento da fila de envio de expansão**

Para obter mais informações sobre o que está sendo medida esses contadores, consulte [SignalR Scaleout com o Azure Service Bus](scaleout-with-windows-azure-service-bus.md).

<a id="othercounters"></a>

## <a name="using-other-performance-counters"></a>Usando outros contadores de desempenho

Os seguintes contadores de desempenho também podem ser útil no monitoramento do desempenho do seu aplicativo.

**Memória**

- .NET CLR memória n º de bytes em todos os Heaps (para w3wp)

**ASP.NET**

- ASP.NET \ solicitações atuais
- ASP.NET\Queued
- ASP.NET\Rejected

**CPU**

- Tempo do processador Information\Processor

**TCP/IP**

- TCPv6/conexões estabelecidas
- TCPv4/conexões estabelecidas

**Serviço Web**

- Web \ conexões atuais
- Conexões de Service\Maximum da Web

**Threading**

- .NET CLR LocksAndThreads\# de Threads lógicos atuais
- Threads do .NET CLR LocksAnd\# atual de Threads físicos

<a id="otherresources"></a>

## <a name="other-resources"></a>Outros recursos

Para obter mais informações sobre monitoramento e ajuste de desempenho do ASP.NET, consulte os tópicos a seguir:

- [Visão geral do desempenho do ASP.NET](https://msdn.microsoft.com/library/cc668225(v=vs.100).aspx)
- [Uso de threads do ASP.NET no IIS 7.5, IIS 7.0 e IIS 6.0](https://blogs.msdn.com/b/tmarq/archive/2007/07/21/asp-net-thread-usage-on-iis-7-0-and-6-0.aspx)
- [&lt;applicationPool&gt; (configurações da Web)](https://msdn.microsoft.com/library/dd560842.aspx)
