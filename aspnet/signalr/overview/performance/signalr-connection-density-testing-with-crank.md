---
uid: signalr/overview/performance/signalr-connection-density-testing-with-crank
title: Densidade de Conexão do SignalR testes com Crank | Microsoft Docs
author: bradygaster
description: Densidade de Conexão do SignalR testes com Crank
ms.author: bradyg
ms.date: 02/22/2015
ms.assetid: 148d9ca7-1af1-44b6-a9fb-91e261b9b463
msc.legacyurl: /signalr/overview/performance/signalr-connection-density-testing-with-crank
msc.type: authoredcontent
ms.openlocfilehash: 40c9764f0c47b83df8300553b4b290429937345c
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836539"
---
<a name="signalr-connection-density-testing-with-crank"></a>Densidade de Conexão do SignalR testes com Crank
====================
por [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este artigo descreve como usar a ferramenta manivela para testar um aplicativo com vários clientes simulados.


Quando seu aplicativo estiver em execução no seu ambiente de hospedagem (Azure uma função, IIS, web ou auto-hospedados usando Owin), você pode testar a resposta do aplicativo para um alto nível de densidade de conexão usando a ferramenta manivela. O ambiente de hospedagem pode ser um servidor de serviços de informações da Internet (IIS), um host Owin ou uma função web do Azure. (Observação: Contadores de desempenho não estão disponíveis em aplicativos de Web de serviço de aplicativo do Azure, portanto, não será capaz de obter dados de desempenho de um teste de densidade de conexão.)

Densidade de Conexão refere-se ao número de conexões TCP simultâneas que podem ser estabelecidas em um servidor. Cada conexão TCP resulta em sua própria sobrecarga e abrindo um grande número de conexões ociosas, eventualmente, criar um afunilamento de memória.

[O SignalR codebase](https://github.com/signalr/signalr) inclui uma ferramenta de teste de carga chamada **Crank**. A versão mais recente do manivela pode ser encontrada no [ramificação Dev](https://github.com/SignalR/signalr/tree/dev) no GitHub. Você pode baixar um Zip de base de código do arquivo morto de ramificação de desenvolvimento do SignalR [aqui](https://github.com/SignalR/SignalR/archive/dev.zip).

Crank pode ser usado para saturar totalmente a memória do servidor para calcular o número total de conexões ociosas possíveis no hardware do servidor. Como alternativa, você também pode usar manivela para teste de carga do servidor em uma determinada quantidade de pressão de memória, por aumentando gradualmente conexões até que uma contagem específica ou um limite de memória específica seja atingido.

Durante o teste, é importante usar clientes remotos para evitar qualquer competição por recursos (ou seja, as conexões TCP e memória). Monitore clientes para garantir que eles não estão atingindo gargalos que podem impedir que o servidor atingir sua capacidade total (CPU ou memória). Talvez você precise aumentar o número de clientes para carregar totalmente o servidor.

### <a name="running-a-connection-density-test"></a>Executando um teste de densidade de Conexão

Esta seção descreve as etapas necessárias para executar um teste de densidade de conexão em um aplicativo do SignalR.

1. Baixe e compile o [base de código da ramificação de desenvolvimento do SignalR](https://github.com/SignalR/SignalR/archive/dev.zip). No prompt de comando, navegue até &lt;diretório do projeto&gt;\src\Microsoft.AspNet.SignalR.Crank\bin\debug.
2. Implante seu aplicativo em seu ambiente de hospedagem pretendido. Anote o ponto de extremidade que usa seu aplicativo; Por exemplo, no aplicativo criado na [tutorial de Introdução](../getting-started/tutorial-getting-started-with-signalr.md), o ponto de extremidade `http://<yourhost>:8080/signalr`.
3. Instale [contadores de desempenho do SignalR](signalr-performance.md#perfcounters) no servidor. Se seu aplicativo estiver em execução no Azure, consulte [contadores de desempenho usando o SignalR em uma função da Web do Azure](using-signalr-performance-counters-in-an-azure-web-role.md).

Depois que você baixou e criou a base de código e contadores de desempenho instalados em seu host, a ferramenta de linha de comando manivela pode ser encontrada no `src\Microsoft.AspNet.SignalR.Crank\bin\Debug` pasta.

As opções disponíveis para a ferramenta manivela incluem:

- **/?**: Mostra a tela de Ajuda. As opções disponíveis são exibidas também se o **Url** parâmetro for omitido.
- **/Url**: A URL para conexões do SignalR. Este parâmetro é necessário. Para um aplicativo de SignalR usando o mapeamento padrão, o caminho será encerrada em "/ signalr".
- **/ Transporte**: O nome do transporte usado. O padrão é `auto`, que seleciona o melhor protocolo disponível. As opções incluem `WebSockets`, `ServerSentEvents`, e `LongPolling` (`ForeverFrame` não é uma opção para manivela, desde que o cliente do .NET em vez do Internet Explorer é usado). Para obter mais informações sobre como o SignalR seleciona transportes, consulte [transportes e Fallbacks](../getting-started/introduction-to-signalr.md#transports).
- **/BatchSize**: O número de clientes adicionados em cada lote. O padrão é 50.
- **/ConnectInterval**: O intervalo em milissegundos entre a adição de conexões. O padrão é 500.
- **/ Conexões**: O número de conexões usado para o aplicativo de teste de carga. O padrão é 100.000.
- **/ConnectTimeout**: O tempo limite em segundos antes de anular o teste. O padrão é 300.
- **MinServerMBytes**: Os megabytes mínima do servidor para alcançar. O padrão é 500.
- **SendBytes**: O tamanho da carga enviada para o servidor em bytes. O padrão é 0.
- **SendInterval**: O atraso em milissegundos entre as mensagens para o servidor. O padrão é 500.
- **SendTimeout**: O tempo limite em milissegundos para mensagens para o servidor. O padrão é 300.
- **ControllerUrl**: A Url em que um cliente hospedará um hub de controlador. O padrão é null (hub de controlador). O hub de controlador é iniciado no início da sessão de manivela; Nenhum outro contato entre o hub de controlador e uma manivela é feito.
- **NumClients**: O número de clientes simulados para se conectar ao aplicativo. O padrão é um.
- **Arquivo de log**: O nome do arquivo para o arquivo de log para a execução de teste. O padrão é `crank.csv`.
- **SampleInterval**: O tempo em milissegundos entre as amostras de contador de desempenho. O padrão é 1000.
- **SignalRInstance**: O nome da instância dos contadores de desempenho no servidor. O padrão é usar o estado de conexão do cliente.

### <a name="example"></a>Exemplo

O comando a seguir irá testar um site chamado `pfsignalr` no Azure que hospeda um aplicativo na porta 8080 com um hub chamado "ControllerHub", usando 100 conexões.

`crank /Connections:100 /Url:http://pfsignalr.cloudapp.net:8080/signalr`
