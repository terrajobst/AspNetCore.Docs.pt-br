---
uid: signalr/overview/older-versions/handling-connection-lifetime-events
title: Noções básicas sobre e manipular eventos de tempo de vida de Conexão no SignalR 1.x | Microsoft Docs
author: pfletcher
description: Este artigo descreve como usar os eventos expostos pela API de Hubs.
ms.author: riande
ms.date: 06/05/2013
ms.assetid: e608e263-264d-448b-b0eb-6eeb77713b22
msc.legacyurl: /signalr/overview/older-versions/handling-connection-lifetime-events
msc.type: authoredcontent
ms.openlocfilehash: f965c38e18c442268f9bb1d7ffb5e98a135efade
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287670"
---
<a name="understanding-and-handling-connection-lifetime-events-in-signalr-1x"></a>Noções básicas sobre e manipular eventos de tempo de vida de Conexão no SignalR 1.x
====================
por [Patrick Fletcher](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este artigo fornece uma visão geral de como os eventos de conexão, a reconexão e a desconexão do SignalR que você pode manipular e configurações de tempo limite e keepalive que você pode configurar.
> 
> O artigo supõe que você já tem algum conhecimento dos eventos de tempo de vida do SignalR e conexão. Para obter uma introdução ao SignalR, consulte [SignalR - visão geral - introdução ao](index.md). Para listas de eventos de tempo de vida de conexão, consulte os seguintes recursos:
> 
> - [Como manipular eventos de tempo de vida de conexão na classe Hub](index.md)
> - [Como manipular eventos de tempo de vida de conexão nos clientes JavaScript](index.md)
> - [Como manipular eventos de tempo de vida de conexão em clientes .NET](index.md)


## <a name="overview"></a>Visão geral

Este artigo contém as seguintes seções:

- [Cenários e a terminologia de tempo de vida da Conexão](#terminology)

    - [Conexões do SignalR, conexões de transporte e conexões físicas](#signalrvstransport)
    - [Cenários de desconexão de transporte](#transportdisconnect)
    - [Cenários de desconexão do cliente](#clientdisconnect)
    - [Cenários de desconexão do servidor](#serverdisconnect)
- [Configurações de tempo limite e keepalive](#timeoutkeepalive)

    - [ConnectionTimeout](#connectiontimeout)
    - [DisconnectTimeout](#disconnecttimeout)
    - [KeepAlive](#keepalive)
    - [Como alterar as configurações de tempo limite e keepalive](#changetimeout)
- [Como notificar o usuário sobre desconexões](#notifydisconnect)
- [Como reconectar continuamente](#continuousreconnect)
- [Como desconectar um cliente no código do servidor](#disconnectclientfromserver)

Links para tópicos de referência de API são para a versão 4.5 do .NET da API. Se você estiver usando o .NET 4, consulte [a versão do .NET 4 dos tópicos da API](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).

<a id="terminology"></a>

## <a name="connection-lifetime-terminology-and-scenarios"></a>Cenários e a terminologia de tempo de vida da Conexão

O `OnReconnected` manipulador de eventos em um SignalR Hub pode executar diretamente após `OnConnected` mas não após `OnDisconnected` para um determinado cliente. O motivo pelo qual que você pode ter uma reconexão sem uma desconexão é que há várias maneiras em que a palavra "conexão" é usada no SignalR.

<a id="signalrvstransport"></a>

### <a name="signalr-connections-transport-connections-and-physical-connections"></a>Conexões do SignalR, conexões de transporte e conexões físicas

Este artigo irá diferenciar *conexões SignalR*, *conexões de transporte*, e *conexões físicas*:

- **Conexão do SignalR** refere-se a uma relação lógica entre um cliente e uma URL do servidor, mantida pela API do SignalR e identificado exclusivamente por uma ID de conexão. Os dados sobre essa relação são mantidos pelo SignalR e são usados para estabelecer uma conexão de transporte. As extremidades da relação e SignalR descarta os dados quando o cliente chama o `Stop` método ou um limite de tempo limite for alcançado enquanto SignalR está tentando restabelecer uma conexão de transporte perdido.
- **Conexão de transporte** refere-se a uma relação lógica entre um cliente e um servidor, mantido por um dos quatro transporte APIs: WebSockets, eventos enviados pelo servidor, para sempre quadro ou sondagem longa. O SignalR usa o transporte de API para criar uma conexão de transporte e a API de transporte depende da existência de uma conexão de rede física para criar a conexão de transporte. A conexão de transporte termina quando termina o SignalR ou quando o transporte API detecta que a conexão física é interrompida.
- **Conexão física** refere-se os links de rede física – fios, sinais sem fio, roteadores, etc. – que facilitam a comunicação entre um computador cliente e um computador do servidor. A conexão física deve estar presente para estabelecer uma conexão de transporte e é necessário estabelecer uma conexão de transporte para estabelecer uma conexão SignalR. No entanto, quebrando a conexão física não ocorrem sempre imediatamente termina a conexão de transporte ou conexão SignalR, conforme será explicado mais adiante neste tópico.

No diagrama a seguir, a conexão do SignalR é representado pela API de Hubs e camada PersistentConnection API SignalR, a conexão de transporte é representado pela camada de transportes e a conexão física é representado por linhas entre o servidor e os clientes.

![Diagrama da arquitetura do SignalR](handling-connection-lifetime-events/_static/image1.png)

Quando você chama o `Start` método em um cliente do SignalR, você está fornecendo o código de cliente do SignalR com todas as informações necessárias para estabelecer uma conexão física em um servidor. Código de cliente SignalR usa essas informações para fazer uma solicitação HTTP e estabelecer uma conexão física que usa um dos métodos de transporte de quatro. Se a conexão de transporte falha ou o servidor falhar, a conexão do SignalR não desaparece imediatamente porque o cliente ainda tem as informações necessárias para restabelecer automaticamente uma nova conexão de transporte para a mesma URL de SignalR. Nesse cenário, nenhuma intervenção do aplicativo do usuário está envolvida e quando o código do cliente SignalR estabelece uma nova conexão de transporte, ele não inicia uma nova conexão do SignalR. A continuidade de conexão o SignalR é refletida no fato de que a ID de conexão, que é criada quando você chama o `Start` método, não é alterado.

O `OnReconnected` manipulador de eventos no Hub é executado quando uma conexão de transporte é restabelecida automaticamente depois de ter sido perdidos. O `OnDisconnected` manipulador de eventos é executado no final de uma conexão SignalR. Uma conexão SignalR pode terminar com qualquer uma das seguintes maneiras:

- Se o cliente chama o `Stop` método, uma mensagem de parada é enviada ao servidor e cliente e servidor terminam a conexão do SignalR imediatamente.
- Depois de conectividade entre cliente e servidor é perdida, o cliente tenta se reconectar e o servidor aguarda o cliente para se reconectar. Se as tentativas de reconexão são malsucedidas e o período de tempo limite de desconexão termina, o cliente e servidor terminam a conexão do SignalR. O cliente para tentar reconectar-se, e descarta o servidor de sua representação de conexão do SignalR.
- Se o cliente será interrompido sem ter a chance de chamar o `Stop` método, o servidor aguarda reconectar-se, o cliente e, em seguida, termina a conexão do SignalR após o período de tempo limite de desconexão.
- Se o servidor para a execução, o cliente tenta se reconectar (recriar a conexão de transporte) e, em seguida, termina a conexão do SignalR após o período de tempo limite de desconexão.

Quando não há nenhum problema de conexão, e o aplicativo de usuário termina a conexão do SignalR chamando o `Stop` método, a conexão do SignalR e a conexão de transporte começam e terminam no aproximadamente ao mesmo tempo. As seções a seguir descrevem mais detalhadamente os outros cenários.

<a id="transportdisconnect"></a>

### <a name="transport-disconnection-scenarios"></a>Cenários de desconexão de transporte

Conexões físicas podem ser lentas ou pode haver interrupções na conectividade. Dependendo de fatores como o comprimento da interrupção, a conexão de transporte pode ser descartado. O SignalR, em seguida, tenta restabelecer a conexão de transporte. Às vezes, a conexão de transporte API detecta a interrupção e descarta a conexão de transporte e SignalR descobre imediatamente que a conexão for perdida. Em outros cenários, nem a API de conexão de transporte nem SignalR fica ciente imediatamente que a conectividade foi perdida. Para todos os transportes, exceto a sondagem longa, o cliente SignalR usa uma função chamada *keepalive* para verificar se há perda de conectividade que o transporte de API não consegue detectar. Para obter informações sobre conexões de sondagem longa, consulte [as configurações de tempo limite e keepalive](#timeoutkeepalive) mais adiante neste tópico.

Quando uma conexão estiver inativo, periodicamente o servidor envia um pacote keepalive ao cliente. A partir da data em que este artigo está sendo escrito, a frequência de padrão é a cada 10 segundos. Através da escuta para esses pacotes, os clientes podem informar se há um problema de conexão. Se um pacote keepalive não for recebido quando esperada, após um curto período o cliente assume que há problemas de conexão como lentidão ou interrupções. Se o keepalive ainda não for recebido após um tempo mais longo, o cliente presume que a conexão foi descartada, e ele começa a tentar reconectar.

O diagrama a seguir ilustra os eventos de cliente e servidor que são gerados em um cenário típico, quando há problemas com a conexão física que não são reconhecidos imediatamente pelo transporte de API. O diagrama se aplica aos seguintes circunstâncias:

- O transporte é WebSockets, para sempre quadro ou eventos enviados pelo servidor.
- Há diferentes períodos de interrupção na conexão de rede física.
- O API de transporte não fica ciente de interrupções, então o SignalR se baseia na funcionalidade keepalive para detectá-los.

![Desconexões de transporte](handling-connection-lifetime-events/_static/image2.png)

Se o cliente entra em modo reconectar-se, mas não é possível estabelecer uma conexão de transporte dentro do limite de tempo limite de desconexão, o servidor encerra a conexão do SignalR. Quando isso acontece, o servidor executa o Hub `OnDisconnected` método e enfileira uma mensagem de desconexão para enviar ao cliente, caso o cliente gerencia conectar mais tarde. Se o cliente, em seguida, reconectar, ele receberá o comando de desconexão e chama o `Stop` método. Nesse cenário, `OnReconnected` não será executado quando o cliente se reconecta, e `OnDisconnected` não será executado quando o cliente chama `Stop`. O diagrama a seguir ilustra esse cenário.

![Interrupções de transporte - tempo limite do servidor](handling-connection-lifetime-events/_static/image3.png)

Os eventos de tempo de vida de conexão do SignalR que poderão ser gerados no cliente são os seguintes:

- `ConnectionSlow` evento de cliente.

    Gerado quando uma predefinição proporção do período de tempo limite de keepalive se passou desde a última mensagem ou keepalive ping foi recebida. O período de aviso de tempo limite de keepalive padrão é 2/3 do tempo limite keepalive. O tempo limite de keepalive é 20 segundos, para que o aviso ocorre em cerca de 13 segundos.

    Por padrão, o servidor envia pings keepalive a cada 10 segundos e o cliente verifica os pings de keepalive sobre cada 2 segundos (um terço da diferença entre o valor de tempo limite de manutenção de atividade e o valor de aviso de tempo limite de keepalive).

    Se o transporte API fica ciente de uma desconexão, SignalR pode ser informados sobre a desconexão antes do tempo limite aviso keepalive passa. Nesse caso, o `ConnectionSlow` evento não será acionado e SignalR deve ir diretamente para o `Reconnecting` eventos.
- `Reconnecting` evento de cliente.

    Gerado quando o (a) o transporte API detecta que a conexão for perdida, ou (b) o período de tempo limite de keepalive se passou desde a última mensagem ou keepalive ping foi recebida. O código do cliente SignalR começa a tentar reconectar. Você pode manipular esse evento, se você quiser que seu aplicativo execute alguma ação quando uma conexão de transporte é perdida. O período de tempo limite padrão keepalive atualmente é de 20 segundos.

    Se seu código de cliente tentar chamar um método de Hub, enquanto o SignalR está em modo reconectar-se, o SignalR tentará enviar o comando. Na maioria das vezes, essas tentativas falhará, mas em algumas circunstâncias que talvez tenham êxito. Para os eventos enviados pelo servidor, para sempre quadro e transportes de sondagem longa, o SignalR usa dois canais de comunicação, um que o cliente usa para enviar mensagens e outro que ele usa para receber mensagens. O canal usado para o recebimento é aquele permanentemente aberto e que é o que é fechada quando a conexão física é interrompida. O canal usado para enviar permanece disponível, portanto, se a conectividade física é restaurada, uma chamada de método de cliente para servidor poderá ser bem-sucedida antes que o canal de recebimento é restabelecido. O valor de retorno não seria recebido até que o SignalR reabre o canal usado para recebimento.
- `Reconnected` evento de cliente.

    Gerado quando a conexão de transporte é restabelecida. O `OnReconnected` executa o manipulador de eventos no Hub.
- `Closed` evento de cliente (`disconnected` eventos em JavaScript).

    Gerado quando o período de tempo limite de desconexão expira enquanto o código de cliente SignalR está tentando se reconectar após perder a conexão de transporte. Desconecte o padrão é de 30 segundos do tempo limite. (Esse evento também é gerado quando a conexão é encerrada porque o `Stop` método é chamado.)

Interrupções de conexão de transporte que não são detectadas pelo transporte de API e não adie a recepção de pings de manutenção de atividade do servidor por mais tempo que o período de aviso de tempo limite de keepalive podem não fazer com que qualquer conexão eventos de tempo de vida a serem gerados.

Alguns ambientes de rede fechar deliberadamente conexões ociosas e outra função dos pacotes keepalive é ajudar a evitar isso, informando a essas redes que uma conexão SignalR está em uso. Em casos extremos a frequência de padrão de pings keepalive pode não ser suficiente para impedir conexões fechadas. Nesse caso, você pode configurar os pings de keepalive sejam enviadas com mais frequência. Para obter mais informações, consulte [as configurações de tempo limite e keepalive](#timeoutkeepalive) mais adiante neste tópico.

> [!NOTE] 
> 
> [!IMPORTANT]
> A sequência de eventos descritas aqui não é garantida. Torna o SignalR cada tentativa de gerar eventos de tempo de vida de conexão de uma maneira previsível de acordo com esse esquema, mas há muitas variações de eventos de rede e de várias maneiras em que estruturas de comunicação subjacente como transporte APIs tratarão-los. Por exemplo, o `Reconnected` evento não pode ser gerado quando o cliente se reconecta, ou o `OnConnected` manipulador no servidor pode ser executados quando a tentativa de estabelecer uma conexão não for bem-sucedida. Este tópico descreve somente os efeitos que normalmente seriam produzidos por determinadas circunstâncias normais.


<a id="clientdisconnect"></a>

### <a name="client-disconnection-scenarios"></a>Cenários de desconexão do cliente

Em um cliente de navegador, o código do cliente SignalR que mantém uma conexão SignalR é executado no contexto de uma página da web JavaScript. Que tem por que a conexão do SignalR tem que encerrar quando você navega de uma página para outro e que por que você tiver várias conexões com a conexão de várias IDs se você se conectar de várias janelas do navegador ou guias. Quando o usuário fecha uma janela ou guia navegador, ou navega para uma nova página ou atualiza a página, a conexão do SignalR terminará imediatamente porque o código de cliente SignalR manipula esse evento de navegador para você e chama o `Stop` método. Nesses cenários, ou em qualquer plataforma de cliente quando o aplicativo chama o `Stop` método, o `OnDisconnected` manipulador de eventos executa imediatamente no servidor e o cliente gera o `Closed` evento (o evento é chamado `disconnected` em JavaScript).

Se um aplicativo cliente ou no computador que está sendo executado falha ou entra em suspensão (por exemplo, quando o usuário fecha o laptop), o servidor não é informado sobre o que aconteceu. Como o servidor sabe, a perda do cliente pode ser devido a interrupção da conectividade e o cliente pode estar tentando se reconectar. Portanto, nesses cenários em que o servidor espera para que o cliente possam se reconectar, e `OnDisconnected` não será executada até que o período de tempo limite de desconexão expire (cerca de 30 segundos por padrão). O diagrama a seguir ilustra esse cenário.

![Falha no computador cliente](handling-connection-lifetime-events/_static/image4.png)

<a id="serverdisconnect"></a>

### <a name="server-disconnection-scenarios"></a>Cenários de desconexão do servidor

Quando um servidor ficar offline – ele é reinicializado, falhar, o domínio do aplicativo recicla, etc. – o resultado pode ser semelhante a uma conexão perdida ou a API de transporte e o SignalR podem saber imediatamente que o servidor não existe mais e SignalR pode começar a tentar reconectar sem Gerando o `ConnectionSlow` eventos. Se o cliente entra em modo reconectar-se e se o servidor recupera ou reinicializações ou um novo servidor é colocado online antes de expira o tempo limite de desconexão, o cliente se reconectará ao servidor novo ou restaurado. Nesse caso, a conexão do SignalR continua no cliente e o `Reconnected` é gerado. No primeiro servidor, `OnDisconnected` nunca será executado e no novo servidor `OnReconnected` é executado, embora `OnConnected` nunca foi executada para esse cliente no servidor antes. (O efeito é o mesmo se o cliente se reconecta ao mesmo servidor após uma reinicialização ou aplicativo reciclagem de domínio, porque quando o servidor reiniciar não tem memória de atividade de conexão anterior). O diagrama a seguir pressupõe que o transporte API fica ciente da conexão perdida imediatamente, portanto, o `ConnectionSlow` não é gerado.

![Falha do servidor e reconexão](handling-connection-lifetime-events/_static/image5.png)

Se um servidor não ficar disponível dentro do período de tempo limite de desconexão, termina a conexão do SignalR. Nesse cenário, o `Closed` evento (`disconnected` nos clientes JavaScript) é gerado no cliente, mas `OnDisconnected` nunca é chamado no servidor. O diagrama a seguir pressupõe que o transporte de API não reconhecem a conexão perdida, para que ele é detectado pelo SignalR keepalive funcionalidade e o `ConnectionSlow` é gerado.

![Tempo limite e falha do servidor](handling-connection-lifetime-events/_static/image6.png)

<a id="timeoutkeepalive"></a>

## <a name="timeout-and-keepalive-settings"></a>Configurações de tempo limite e keepalive

O padrão `ConnectionTimeout`, `DisconnectTimeout`, e `KeepAlive` valores apropriados para a maioria dos cenários, mas pode ser alteradas se seu ambiente tiver necessidades especiais. Por exemplo, se seu ambiente de rede fecha as conexões que estão ociosas por 5 segundos, você terá que diminua o valor de keepalive.

<a id="connectiontimeout"></a>

### <a name="connectiontimeout"></a>ConnectionTimeout

Essa configuração representa a quantidade de tempo para manter uma conexão de transporte abertas e está aguardando uma resposta antes de fechá-lo e abrir uma nova conexão. O valor padrão é de 110 segundos.

Essa configuração se aplica somente quando funcionalidade keepalive está desabilitada, que normalmente se aplica somente à longa transporte de sondagem. O diagrama a seguir ilustra o efeito dessa configuração em um longo conexão de transporte de sondagem.

![Conexão de transporte de sondagem longa](handling-connection-lifetime-events/_static/image7.png)

<a id="disconnecttimeout"></a>

### <a name="disconnecttimeout"></a>DisconnectTimeout

Essa configuração representa a quantidade de tempo a aguardar após uma conexão de transporte é perdida, antes de acionar o `Disconnected` eventos. O valor padrão é 30 segundos. Quando você define `DisconnectTimeout`, `KeepAlive` é definida automaticamente como 1/3 do `DisconnectTimeout` valor.

<a id="keepalive"></a>

### <a name="keepalive"></a>KeepAlive

Essa configuração representa a quantidade de tempo de espera antes de enviar um pacote keepalive ao longo de uma conexão ociosa. O valor padrão é 10 segundos. Esse valor não deve ser a mais de 1/3 do `DisconnectTimeout` valor.

Se você quiser definir ambos `DisconnectTimeout` e `KeepAlive`, defina `KeepAlive` depois `DisconnectTimeout`. Caso contrário, sua `KeepAlive` configuração serão substituídas ao `DisconnectTimeout` define automaticamente `KeepAlive` como 1/3 do valor de tempo limite.

Se você quiser desabilitar a funcionalidade de manutenção de atividade, defina `KeepAlive` como null. Funcionalidade keepalive será desabilitada automaticamente para o long transporte de sondagem.

<a id="changetimeout"></a>

### <a name="how-to-change-timeout-and-keepalive-settings"></a>Como alterar as configurações de tempo limite e keepalive

Para alterar os valores padrão para essas configurações, defini-los no `Application_Start` em seu *global. asax* de arquivos, conforme mostrado no exemplo a seguir. Os valores mostrados no código de exemplo são o mesmo que os valores padrão.

[!code-csharp[Main](handling-connection-lifetime-events/samples/sample1.cs)]

<a id="notifydisconnect"></a>

## <a name="how-to-notify-the-user-about-disconnections"></a>Como notificar o usuário sobre desconexões

Em alguns aplicativos, você talvez queira exibir uma mensagem para o usuário quando houver problemas de conectividade. Você tem várias opções para como e quando fazer isso. Os exemplos de código a seguir são para um cliente JavaScript usando o proxy gerado.

- Manipular o `connectionSlow` evento para exibir uma mensagem assim que o SignalR é fica ciente dos problemas de conexão, antes que ele entra no modo reconectar-se.

    [!code-javascript[Main](handling-connection-lifetime-events/samples/sample2.js)]
- Manipular o `reconnecting` evento para exibir uma mensagem quando o SignalR está ciente de uma desconexão e está indo para reconectar-se o modo.

    [!code-javascript[Main](handling-connection-lifetime-events/samples/sample3.js)]
- Manipular o `disconnected` atingiu o evento para exibir uma mensagem quando tentar se reconectar. Nesse cenário, a única maneira de restabelecer uma conexão com o servidor novamente é reiniciar a conexão do SignalR, chamando o `Start` método, que criará uma nova ID de conexão. O exemplo de código a seguir usa um sinalizador para certificar-se de que você emita a notificação somente depois de um tempo de limite de reconexão, não após um encerramento normal para a conexão do SignalR causado ao chamar o `Stop` método.

    [!code-javascript[Main](handling-connection-lifetime-events/samples/sample4.js)]

<a id="continuousreconnect"></a>

## <a name="how-to-continuously-reconnect"></a>Como reconectar continuamente

Em alguns aplicativos, convém automaticamente restabelecer uma conexão depois que foi perdido e tentar reconectar-se esgotou. Para fazer isso, você pode chamar o `Start` método a partir do seu `Closed` manipulador de eventos (`disconnected` manipulador de eventos em clientes JavaScript). Talvez você queira aguardar um período de tempo antes de chamar `Start` para evitar fazer isso muito frequentemente quando o servidor ou a conexão física não estiverem disponíveis. O exemplo de código a seguir é para um cliente JavaScript usando o proxy gerado.

[!code-javascript[Main](handling-connection-lifetime-events/samples/sample5.js)]

Um problema potencial para estar atento em clientes móveis é que as tentativas de reconexão contínua quando o servidor ou a conexão física não está disponível poderá causar desnecessária de bateria.

<a id="disconnectclientfromserver"></a>

## <a name="how-to-disconnect-a-client-in-server-code"></a>Como desconectar um cliente no código do servidor

O SignalR versão 1.1.1 não tem uma API de servidor interno para desconexão de clientes. Há [planos para adicionar essa funcionalidade no futuro](https://github.com/SignalR/SignalR/issues/2101). Na versão atual do SignalR, a maneira mais simples de se desconectar de um cliente do servidor é implementar um método de desconexão no cliente e chamar esse método do servidor. O exemplo de código a seguir mostra um método de desconexão de um cliente JavaScript usando o proxy gerado.

[!code-javascript[Main](handling-connection-lifetime-events/samples/sample6.js)]

> [!WARNING]
> Security - nem esse método para desconexão de clientes, nem a API interna proposta abordarão o cenário de atacados clientes que estão executando o código mal-intencionado, desde que os clientes podem se reconectar ou o código atacados por um hacker pode remover o `stopClient` método ou alteração o que ele faz. O local adequado para implementar a proteção de (DOS) de negação de serviço com monitoração de estado não está na estrutura ou da camada de servidor, mas em vez disso, na infraestrutura de front-end.
