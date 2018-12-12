---
uid: signalr/overview/getting-started/introduction-to-signalr
title: Introdução ao SignalR | Microsoft Docs
author: pfletcher
description: Este artigo descreve o que é o SignalR e algumas das soluções que ele foi projetado para criar.
ms.author: riande
ms.date: 06/10/2014
ms.assetid: 0fab5e35-8c1f-43d4-8635-b8aba8766a71
msc.legacyurl: /signalr/overview/getting-started/introduction-to-signalr
msc.type: authoredcontent
ms.openlocfilehash: c865078c14b8615faa278819f86a9dd623a42f36
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287553"
---
<a name="introduction-to-signalr"></a>Introdução ao SignalR
====================

por [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]


> Este artigo descreve o que é o SignalR e algumas das soluções que ele foi projetado para criar. 
> 
> ## <a name="questions-and-comments"></a>Perguntas e comentários
> 
> Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página. Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](https://stackoverflow.com/questions/tagged/signalr).

## <a name="what-is-signalr"></a>O que é o SignalR?

SignalR do ASP.NET é uma biblioteca para desenvolvedores do ASP.NET que simplifica o processo de adicionar a funcionalidade da web em tempo real aos aplicativos. A funcionalidade da web em tempo real é a capacidade de ter conteúdo para clientes conectados imediatamente conforme ficam disponível push de código do servidor, em vez de ter o servidor de esperar que um cliente solicite novos dados.

O SignalR pode ser usado para adicionar qualquer tipo de funcionalidade da web "em tempo real" ao seu aplicativo ASP.NET. Enquanto o bate-papo geralmente é usado como um exemplo, você pode fazer muito mais. Sempre que um usuário atualiza uma página da web para visualizar novos dados ou a página implementa [pesquisa longa](http://en.wikipedia.org/wiki/Push_technology#Long_polling) para recuperar novos dados, ele é um candidato para usar o SignalR. Os exemplos incluem painéis e monitoramento de aplicativos, aplicativos de colaboração (como simultâneas de edição de documentos), atualizações de andamento e trabalho formulários em tempo real.

O SignalR também permite completamente novos tipos de aplicativos web que exigem atualizações de alta frequência do servidor, por exemplo, jogos em tempo real.

O SignalR fornece uma API simples para a criação de chamadas de procedimento remoto do servidor-para-cliente (RPC) que chamam funções JavaScript no cliente de navegadores (e outras plataformas de cliente) do código do lado do servidor .NET. O SignalR também inclui API para o gerenciamento de conexão (por exemplo, conexão e eventos de desconexão) e agrupamento de conexões.

![Chamar métodos com o SignalR](introduction-to-signalr/_static/image1.png)

O SignalR lida com gerenciamento de conexão automaticamente e permite que você as mensagens de difusão para todos os clientes conectados ao mesmo tempo, como uma sala de bate-papo. Você também pode enviar mensagens para clientes específicos. A conexão entre o cliente e servidor é persistente, ao contrário de uma conexão HTTP clássico, que é restabelecida para cada comunicação.

O SignalR dá suporte à funcionalidade de "envio do servidor", no qual o código de servidor pode chamar ao código do cliente no navegador usando chamadas de procedimento remoto (RPC), em vez do modelo de solicitação-resposta comum na web hoje mesmo.

Aplicativos do SignalR podem expandir para milhares de clientes usando o barramento de serviço do SQL Server ou [Redis](http://redis.io).

O SignalR é o código-fonte aberto, acessível por meio [GitHub](https://github.com/signalr).

## <a name="signalr-and-websocket"></a>O SignalR e WebSocket

O SignalR usa o transporte de WebSocket novo onde estiver disponível e voltará a transportes mais antigos onde for necessário. Embora, certamente, você pode escrever seu aplicativo usando o WebSocket diretamente, usando o SignalR significa que muito da funcionalidade extra, que você precisaria implementar já foi feito para você. Mais importante, isso significa que você pode codificar seu aplicativo para tirar proveito do WebSocket sem precisar se preocupar sobre a criação de um caminho de código separado para os clientes mais antigos. O SignalR também protege você de ter de se preocupar sobre atualizações de WebSocket, já que o SignalR é atualizado para dar suporte a alterações no transporte subjacente, fornecendo uma interface consistente de seu aplicativo em versões do WebSocket.

<a id="transports"></a>

## <a name="transports-and-fallbacks"></a>Transportes e fallbacks

O SignalR é uma abstração sobre alguns dos transportes que são necessários para realizar trabalho em tempo real entre cliente e servidor. Uma conexão SignalR é iniciado como HTTP e, em seguida, é promovido para uma conexão WebSocket se ele estiver disponível. WebSocket é o transporte ideal para SignalR, já que ela torna o uso mais eficiente da memória do servidor, tem a menor latência e tem os recursos mais subjacentes (por exemplo, comunicação full-duplex entre cliente e servidor), mas também tem mais rigorosas requisitos: WebSocket exige que o servidor usar o .NET Framework 4.5 ou Windows 8 e Windows Server 2012. Se esses requisitos não forem atendidos, o SignalR tentará usar outros transportes para tornar suas conexões.

### <a name="html-5-transports"></a>Os transportes de HTML 5

Esses transportes dependem de suporte para [HTML 5](http://en.wikipedia.org/wiki/HTML5). Se o navegador do cliente não oferece suporte ao padrão HTML 5, transportes mais antigos serão usados.

- **WebSocket** (se o navegador e o servidor indicam pode dar suporte ao Websocket). WebSocket é o único transporte que estabelece uma conexão true persistente e bidirecional entre cliente e servidor. No entanto, o WebSocket também tem os requisitos mais rígidos; ele é totalmente suportado apenas nas versões mais recentes do Microsoft Internet Explorer, Google Chrome e Mozilla Firefox e tem apenas uma implementação parcial em outros navegadores, como o Opera e Safari.
- **Os eventos enviados do servidor**, também conhecido como EventSource (se o navegador dá suporte a servidor enviado eventos, que é, basicamente, todos os navegadores, exceto o Internet Explorer.)

### <a name="comet-transports"></a>Transportes Comet

Os transportes a seguir se baseiam os [Comet](http://en.wikipedia.org/wiki/Comet_(programming)) modelo de aplicativo da web, na qual um navegador ou outro cliente mantém uma solicitação HTTP mantidos a longo, que o servidor pode usar para enviar dados por push para o cliente sem o cliente especificamente solicitá-la.

- **Para sempre quadro** (para o Internet Explorer apenas). Para sempre quadro cria um IFrame oculto, que faz uma solicitação para um ponto de extremidade no servidor que não for concluída. O servidor, em seguida, envia continuamente script para o cliente que é executado imediatamente, fornecendo uma conexão unidirecional em tempo real do servidor para cliente. A conexão de cliente para servidor usa uma conexão separada do servidor para conexão de cliente e como uma solicitação HTTP padrão, uma nova conexão é criada para cada parte dos dados que precisa ser enviada.
- **AJAX sondagem longa**. Sondagem longa não cria uma conexão persistente, mas em vez disso, sonda o servidor com uma solicitação que permanece aberta até que o servidor responde, o ponto em que a conexão é fechada, e uma nova conexão é solicitado imediatamente. Isso pode introduzir alguma latência enquanto redefine a conexão.

Para obter mais informações sobre quais transportes com suporte em quais configurações, consulte [plataformas com suporte](supported-platforms.md).

### <a name="transport-selection-process"></a>Processo de seleção de transporte

A lista a seguir mostra as etapas que usa o SignalR para decidir qual transporte a ser usado.

1. Se o navegador for o Internet Explorer 8 ou anterior, a sondagem longa é usada.
2. Se JSONP está configurado (ou seja, o `jsonp` parâmetro é definido como `true` quando a conexão é iniciado), a sondagem longa é usada.
3. Se estiver sendo feita uma conexão entre domínios (ou seja, se o ponto de extremidade do SignalR não está no mesmo domínio que a página de hospedagem), WebSocket será usado se os seguintes critérios são atendidos:

   - O cliente oferece suporte a CORS (compartilhamento de recursos entre origens). Para obter detalhes sobre os clientes que suportam CORS, consulte [CORS no caniuse.com](http://www.caniuse.com/CORS).
   - O cliente oferece suporte a WebSocket
   - O servidor dá suporte ao WebSocket

     Se qualquer um desses critérios não forem atendidos, a sondagem longa será usado. Para obter mais informações sobre conexões entre domínios, consulte [como estabelecer uma conexão entre domínios](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).
4. Se JSONP não está configurado e a conexão não está entre domínios, WebSocket será usado se o cliente e o servidor oferecer suporte a ele.
5. Se o cliente ou o servidor não dão suporte a WebSocket, os eventos enviados do servidor é usado se ele estiver disponível.
6. Se os eventos enviados do servidor não estiver disponível, o quadro para sempre é tentado.
7. Se o quadro para sempre falhar, a sondagem longa é usado.

<a id="MonitoringTransports"></a>
### <a name="monitoring-transports"></a>Transportes de monitoramento

Você pode determinar qual transporte que seu aplicativo está usando, habilitando o registro em log em seu hub e abrindo a janela de console em seu navegador.

Para habilitar o log de eventos do seu hub em um navegador, adicione o seguinte comando ao seu aplicativo cliente:

`$.connection.hub.logging = true;`

- No Internet Explorer, abra as ferramentas de desenvolvedor pressionando F12 e clique na guia do Console.

    ![Console do Microsoft Internet Explorer](introduction-to-signalr/_static/image2.png)
- No Chrome, abra o console, pressionando Ctrl + Shift + J.

    ![Console no Google Chrome](introduction-to-signalr/_static/image3.png)

Com o console aberto e o registro em log habilitado, você poderá ver qual transporte está sendo usado pelo SignalR.

![No Internet Explorer, mostrando o transporte de WebSocket do console](introduction-to-signalr/_static/image4.png)

### <a name="specifying-a-transport"></a>Especificar um transporte

Negociar um transporte leva a uma determinada quantidade de tempo e de cliente/servidor recursos. Se os recursos de cliente forem conhecidos, em seguida, um transporte pode ser especificado quando a conexão de cliente é iniciado. O trecho de código a seguir demonstra como iniciar uma conexão usando o transporte de sondagem longa do Ajax, que seriam usadas se ele era conhecido que o cliente não oferecia suporte a qualquer outro protocolo:

`connection.start({ transport: 'longPolling' });`

Você pode especificar uma ordem de fallback, se você quiser que um cliente tentar transportes específicos na ordem. O trecho de código a seguir demonstra a tentativa de WebSocket e caso de falha, indo diretamente para a sondagem longa.

`connection.start({ transport: ['webSockets','longPolling'] });`

As constantes de cadeia de caracteres para especificar os transportes são definidas da seguinte maneira:

- `webSockets`
- `foreverFrame`
- `serverSentEvents`
- `longPolling`

## <a name="connections-and-hubs"></a>Conexões e Hubs

A API do SignalR contém dois modelos para a comunicação entre clientes e servidores: Conexões persistentes e Hubs.

Uma Conexão representa um ponto de extremidade simple para envio de mensagens de difusão, agrupado ou de único destinatário. O oferece API de Conexão persistente (representado pela classe PersistentConnection no código do .NET) o desenvolvedor acesso direto ao protocolo de comunicação de baixo nível que expõe o SignalR. Usando o modelo de comunicação de conexões será familiar aos desenvolvedores acostumados com APIs com base em conexão, como o Windows Communication Foundation.

Um Hub é um pipeline de mais alto nível construído com a API de Conexão que permite que seu cliente e servidor chamar métodos entre si diretamente. O SignalR manipula a expedição entre limites de máquina, como se por mágica, permitindo que os clientes chamar métodos no servidor como facilmente como métodos locais e vice-versa. Usando o modelo de comunicação Hubs será familiar aos desenvolvedores acostumados com a invocação remota APIs, como a comunicação remota do .NET. Usando um Hub também permite que você passar parâmetros com rigidez de tipos para os métodos, permitindo que a associação de modelos.

### <a name="architecture-diagram"></a>Diagrama da arquitetura

O diagrama a seguir mostra a relação entre Hubs, conexões persistentes e as tecnologias subjacentes usadas para transportes.

![Diagrama de arquitetura de SignalR, mostrando as APIs, transportes e clientes](introduction-to-signalr/_static/image5.png)

### <a name="how-hubs-work"></a>Como funcionam os Hubs

Quando o código do lado do servidor chama um método no cliente, um pacote é enviado entre o transporte ativo que contém o nome e parâmetros do método a ser chamado (quando um objeto é enviado como um parâmetro de método, ele é serializado usando JSON). O cliente, em seguida, corresponde ao nome de método para métodos definidos no código do lado do cliente. Se houver uma correspondência, o método do cliente será executado usando os dados de parâmetro desserializado.

A chamada de método pode ser monitorada usando ferramentas como [Fiddler.](http://fiddler2.com/) A imagem a seguir mostra uma chamada de método enviada de um servidor do SignalR para um cliente de navegador da web no painel de Logs do Fiddler. A chamada de método está sendo enviada de um hub chamado `MoveShapeHub`, e o método que está sendo invocado é chamado `updateShape`.

![Exibição do log do Fiddler mostrando o tráfego de SignalR](introduction-to-signalr/_static/image6.png)

Neste exemplo, o nome do hub é identificado com o `H` parâmetro; o método de nome é identificado com o `M` parâmetro e os dados que estão sendo enviados para o método é identificado com o `A` parâmetro. O aplicativo que gerou essa mensagem é criado na [em tempo real de alta frequência](tutorial-high-frequency-realtime-with-signalr.md) tutorial.

### <a name="choosing-a-communication-model"></a>Escolhendo um modelo de comunicação

A maioria dos aplicativos deve usar a API de Hubs. A API de conexões pode ser usada nas seguintes circunstâncias:

- O formato das necessidades reais de mensagem enviada ser especificado.
- O desenvolvedor prefere trabalhar com um modelo de sistema de mensagens e expedição em vez de um modelo de invocação remota.
- Um aplicativo existente que usa um modelo de sistema de mensagens estiver sendo transportado para usar o SignalR.
