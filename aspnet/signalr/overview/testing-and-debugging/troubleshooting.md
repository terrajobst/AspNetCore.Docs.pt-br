---
uid: signalr/overview/testing-and-debugging/troubleshooting
title: Solução de problemas do SignalR | Microsoft Docs
author: pfletcher
description: Este artigo descreve problemas comuns com o desenvolvimento de aplicativos do SignalR.
ms.author: riande
ms.date: 06/10/2014
ms.assetid: 4b559e6c-4fb0-4a04-9812-45cf08ae5779
msc.legacyurl: /signalr/overview/testing-and-debugging/troubleshooting
msc.type: authoredcontent
ms.openlocfilehash: e41061f0310c021b10dc6667a5c3297788213b0a
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287943"
---
<a name="signalr-troubleshooting"></a>Solução de problemas do SignalR
====================
por [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este documento descreve a solução de problemas comuns com o SignalR.
>
> ## <a name="software-versions-used-in-this-topic"></a>Versões de software usadas neste tópico
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - Versão 2 do SignalR
>
>
>
> ## <a name="previous-versions-of-this-topic"></a>Versões anteriores deste tópico
>
> Para obter informações sobre versões anteriores do SignalR, consulte [versões mais antigas do SignalR](../older-versions/index.md).
>
> ## <a name="questions-and-comments"></a>Perguntas e comentários
>
> Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar nos comentários na parte inferior da página. Se você tiver perguntas que não estão diretamente relacionadas para o tutorial, você pode postá-los para o [Fórum do ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


Este documento contém as seções a seguir.

- [Chamando métodos entre o cliente e servidor silenciosamente falhar](#connection)
- [Configurando o IIS do websockets ao ping/pong para detectar um cliente inativo](#pong)
- [Outros problemas de conexão](#other)
- [Erros de compilação e do lado do servidor](#server)
- [Problemas do Visual Studio](#vs)
- [Problemas de serviços de informações da Internet](#iis)
- [Problemas do Microsoft Azure](#azure)

<a id="connection"></a>

## <a name="calling-methods-between-the-client-and-server-silently-fails"></a>Chamando métodos entre o cliente e servidor silenciosamente falhar

Esta seção descreve possíveis causas de uma chamada de método entre cliente e servidor falhe sem uma mensagem de erro significativa. Em um aplicativo do SignalR, o servidor não possui informações sobre os métodos que o cliente implementa; Quando o servidor chama um método de cliente, os dados de nome e o parâmetro de método são enviados para o cliente e o método é executado somente se ela existe no formato que o servidor especificado. Se nenhum método correspondente for encontrado no cliente, nada acontecerá, e nenhuma mensagem de erro é gerada no servidor.

Para investigar ainda mais os métodos de cliente que está sendo chamados não, você pode ativar o log antes de chamar o método start no hub para ver o que o chama são provenientes do servidor. Para habilitar o registro em log em um aplicativo JavaScript, consulte [como habilitar o registro em log do lado do cliente (versão de cliente JavaScript)](../guide-to-the-api/hubs-api-guide-javascript-client.md#logging). Para habilitar o registro em log em um aplicativo de cliente .NET, consulte [como habilitar o registro em log do lado do cliente (versão de cliente .NET)](../guide-to-the-api/hubs-api-guide-net-client.md#logging).

### <a name="misspelled-method-incorrect-method-signature-or-incorrect-hub-name"></a>Método incorreta, assinatura de método incorreto ou nome de hub incorreto

Se o nome ou assinatura de um método chamado corresponder exatamente a um método apropriado no cliente, a chamada falhará. Verifique se que o nome do método chamado pelo servidor corresponde ao nome do método no cliente. Além disso, o SignalR cria o proxy do hub usando métodos em camel case, conforme for apropriado em JavaScript, portanto, um método chamado `SendMessage` no servidor seria chamado `sendMessage` no proxy do cliente. Se você usar o `HubName` atributo no código do lado do servidor, verifique se o nome usado corresponde o nome usado para criar o hub no cliente. Se você não usar o `HubName` de atributo, verifique se o nome do hub em um cliente JavaScript camel case, como chatHub em vez de ChatHub.

### <a name="duplicate-method-name-on-client"></a>Duplicar o nome do método no cliente

Verifique se que você não tem um método duplicado no cliente que difere apenas por maiusculas. Se seu aplicativo cliente tem um método chamado `sendMessage`, verifique se que há não também um método chamado `SendMessage` também.

### <a name="missing-json-parser-on-the-client"></a>Analisador JSON ausente no cliente

O SignalR requer um analisador JSON devem estar presentes para serializar as chamadas entre o servidor e o cliente. Se seu cliente não tem um analisador JSON interno (por exemplo, o Internet Explorer 7), você precisará incluir um em seu aplicativo. Você pode baixar o analisador JSON [aqui](http://nuget.org/packages/json2).

### <a name="mixing-hub-and-persistentconnection-syntax"></a>Misturar sintaxe Hub e PersistentConnection

O SignalR usa os dois modelos de comunicação: Os hubs e PersistentConnections. A sintaxe para chamar esses modelos de dois comunicação é diferente no código do cliente. Se você tiver adicionado um hub no código do servidor, verifique se que todo o código do cliente usa a sintaxe de hub apropriado.

**Código de cliente JavaScript que cria um PersistentConnection em um cliente JavaScript**

[!code-javascript[Main](troubleshooting/samples/sample1.js)]

**Código de cliente JavaScript que cria um Proxy de Hub em um cliente Javascript**

[!code-javascript[Main](troubleshooting/samples/sample2.js)]

**Servidor código c# que mapeia uma rota para um PersistentConnection**

[!code-csharp[Main](troubleshooting/samples/sample3.cs)]

**Servidor código c# que mapeia uma rota para um Hub ou para vários hubs, se você tiver vários aplicativos**

[!code-css[Main](troubleshooting/samples/sample4.css)]

### <a name="connection-started-before-subscriptions-are-added"></a>Conexão iniciado antes que as assinaturas forem adicionadas

Se a conexão do Hub foi iniciado antes de métodos que podem ser chamados a partir do servidor são adicionados ao proxy, as mensagens não serão recebidas. O seguinte código JavaScript não iniciará o hub corretamente:

**Código de cliente JavaScript incorreto que não permitirá que as mensagens dos Hubs sejam recebidas**

[!code-javascript[Main](troubleshooting/samples/sample5.js)]

Em vez disso, adicione as assinaturas de método antes de chamar Start:

**Código de cliente de JavaScript que adiciona corretamente as assinaturas para um hub**

[!code-javascript[Main](troubleshooting/samples/sample6.js)]

### <a name="missing-method-name-on-the-hub-proxy"></a>Nome do método ausente no proxy de hub

Verifique se que o método definido no servidor está inscrito no cliente. Mesmo que o servidor define o método, ele ainda deve ser adicionado ao proxy de cliente. Métodos podem ser adicionados para o proxy do cliente das seguintes maneiras (Observe que o método é adicionado para o `client` membro do hub, não no hub diretamente):

**Código de cliente JavaScript que adiciona métodos para um proxy de hub**

[!code-javascript[Main](troubleshooting/samples/sample7.js)]

### <a name="hub-or-hub-methods-not-declared-as-public"></a>Métodos de hub não declarados como público ou hub

Para ficar visível no cliente, os métodos e a implementação de hub devem ser declarados como `public`.

### <a name="accessing-hub-from-a-different-application"></a>Acessar o hub de um aplicativo diferente

Os Hubs de SignalR só pode ser acessados por meio de aplicativos que implementam os clientes do SignalR. O SignalR não pode interoperar com outras bibliotecas de comunicação (como SOAP ou WCF web services.) Se não houver nenhum cliente SignalR disponível para sua plataforma de destino, você não pode acessar diretamente o ponto de extremidade do servidor.

### <a name="manually-serializing-data"></a>Serialização de dados manualmente

SignalR usará automaticamente JSON para serializar seu método não é preciso de parâmetros lá fazê-lo.

### <a name="remote-hub-method-not-executed-on-client-in-ondisconnected-function"></a>Método de Hub remoto não é executado no cliente na função OnDisconnected

Esse comportamento é padrão. Quando `OnDisconnected` é chamado, o hub já tiver inserido o `Disconnected` estado, o que não permite mais métodos de hub a ser chamado.

**C# código do servidor que executa o código no evento OnDisconnected corretamente**

[!code-csharp[Main](troubleshooting/samples/sample8.cs)]

### <a name="ondisconnect-not-firing-at-consistent-times"></a>OnDisconnect não está sendo disparado em momentos consistentes

Esse comportamento é padrão. Quando um usuário tenta navegar para fora de uma página com uma conexão SignalR Active Directory, o cliente do SignalR, em seguida, fará uma tentativa de melhor esforço para notificar o servidor que a conexão do cliente será interrompido. Se o cliente SignalR do melhor esforço tentativa não conseguir acessar o servidor, o servidor descartará a conexão após um configuráveis `DisconnectTimeout` mais adiante, momento em que o `OnDisconnected` evento será disparado. Se o cliente SignalR do melhor esforço tentativa é bem-sucedida, o `OnDisconnected` evento será acionado imediatamente.

Para obter informações sobre como o `DisconnectTimeout` , consulte [manipulação de eventos de tempo de vida da conexão: DisconnectTimeout](../guide-to-the-api/handling-connection-lifetime-events.md#disconnecttimeout).

### <a name="connection-limit-reached"></a>Limite de Conexão atingido

Ao usar a versão completa do IIS em um sistema operacional de cliente, como o Windows 7, é imposto um limite de conexão de 10. Ao usar um sistema operacional cliente, use o IIS Express em vez disso, para evitar esse limite.

### <a name="cross-domain-connection-not-set-up-properly"></a>Conexão de domínio cruzado não configurado corretamente

Se uma conexão entre domínios (uma conexão para o qual a URL do SignalR não está no mesmo domínio que a página de hospedagem) não está configurado corretamente, a conexão pode falhar sem uma mensagem de erro. Para obter informações sobre como habilitar a comunicação entre domínios, consulte [como estabelecer uma conexão entre domínios](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).

### <a name="connection-using-ntlm-active-directory-not-working-in-net-client"></a>Conexão usando NTLM (Active Directory) não está funcionando no cliente do .NET

Uma conexão em um aplicativo de cliente .NET que usa a segurança de domínio poderá falhar se a conexão não está configurado corretamente. Para usar o SignalR em um ambiente de domínio, defina a propriedade de conexão necessárias da seguinte maneira:

**Cliente código c# que implementa as credenciais de conexão**

[!code-csharp[Main](troubleshooting/samples/sample9.cs)]

<a id="pong"></a>

## <a name="configuring-iis-websockets-to-pingpong-to-detect-a-dead-client"></a>Configurando o IIS do websockets ao ping/pong para detectar um cliente inativo

Servidores de SignalR não sabe se o cliente está inativo ou não e eles dependem de notificação do websocket subjacente para falhas de conexão, ou seja, o `OnClose` retorno de chamada. Uma solução para esse problema é configurar o IIS do websockets para fazer o ping/pong para você. Isso garante que sua conexão será fechado se ele for interrompido inesperadamente. Para obter mais informações, consulte [nesta postagem do stackoverflow](http://stackoverflow.com/questions/19502755/websocket-clients-state-not-changing-on-network-loss).

<a id="other"></a>

## <a name="other-connection-issues"></a>Outros problemas de conexão

Esta seção descreve as causas e soluções para os sintomas específicos ou mensagens de erro que ocorrem durante uma conexão.

### <a name="start-must-be-called-before-data-can-be-sent-error"></a>Erro de "Início deve ser chamado antes de dados podem ser enviados"

Esse erro geralmente é visto se o código faz referência a objetos de SignalR antes que a conexão seja iniciada. O wireup para manipuladores e assim por diante que irá chamar os métodos definidos no servidor deve ser adicionados após a conexão. Observe que a chamada para `Start` é assíncrona, portanto, o código após a chamada pode ser executada antes que ele for concluído. A melhor maneira de adicionar manipuladores depois que uma conexão é iniciado completamente é colocá-los em uma função de retorno de chamada que é passada como um parâmetro para o método de início:

**Código de cliente de JavaScript que adiciona corretamente os manipuladores de eventos que fazem referência a objetos de SignalR**

[!code-javascript[Main](troubleshooting/samples/sample10.js?highlight=1)]

Esse erro também será visto se uma conexão for interrompido enquanto ainda estão sendo referenciados objetos SignalR.

### <a name="301-moved-permanently-or-302-moved-temporarily-error"></a>"301 movido permanentemente" ou "302 movido temporariamente" Erro

Esse erro pode ser visto se o projeto contiver uma pasta chamada SignalR, que irá interferir com o proxy criado automaticamente. Para evitar esse erro, não use uma pasta chamada `SignalR` em seu aplicativo ou a geração de automática de proxy turn off. Ver [o Proxy gerado e o que ele faz para você](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy) para obter mais detalhes.

### <a name="403-forbidden-error-in-net-or-silverlight-client"></a>Erro "403 Proibido" no cliente .NET ou do Silverlight

Esse erro pode ocorrer em ambientes de domínio cruzado, em que a comunicação entre domínios corretamente não está habilitada. Para obter informações sobre como habilitar a comunicação entre domínios, consulte [como estabelecer uma conexão entre domínios](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain). Para estabelecer uma conexão entre domínios em um cliente do Silverlight, consulte [conexões entre domínios de clientes do Silverlight](../guide-to-the-api/hubs-api-guide-net-client.md#slcrossdomain).

### <a name="404-not-found-error"></a>Erro "404 não encontrado"

Há várias causas para esse problema. Verifique se todas as seguintes opções:

- **Referência de endereço de proxy de Hub não está formatada corretamente:** Esse erro geralmente é visto se a referência para o endereço de proxy de hub gerado não está formatada corretamente. Verifique se que a referência para o endereço do hub é feita corretamente. Ver [como referenciar o proxy gerado dinamicamente](../guide-to-the-api/hubs-api-guide-javascript-client.md#dynamicproxy) para obter detalhes.
- **Adicionar rotas ao aplicativo antes de adicionar a rota do hub:** Se seu aplicativo usa outras rotas, verifique se a primeira rota adicionada é a chamada para `MapSignalR`.
- **Usando o IIS 7 ou 7.5 sem a atualização para URLs sem extensão:** Usar o IIS 7 ou 7.5 requer uma atualização para URLs sem extensão para que o servidor possa fornecer acesso às definições de hub no `/signalr/hubs`. A atualização pode ser encontrada [aqui](https://support.microsoft.com/kb/980368).
- **Cache do IIS desatualizado ou corrompido:** Para verificar se o conteúdo de cache não está desatualizado, digite o seguinte comando em uma janela do PowerShell para limpar o cache:

    [!code-powershell[Main](troubleshooting/samples/sample11.ps1)]

### <a name="500-internal-server-error"></a>"Erro de servidor interno 500"

Esse é um erro muito genérico que pode ter uma ampla variedade de causas. Os detalhes do erro devem aparecer no log de eventos do servidor, ou podem ser encontrados usando o servidor de depuração. Informações de erro mais detalhadas podem ser obtidas por meio da ativação erros detalhados no servidor. Para obter mais informações, consulte [como tratar erros na classe Hub](../guide-to-the-api/hubs-api-guide-server.md#handleErrors).

Esse erro também geralmente é visto se um firewall ou proxy não está configurado corretamente, fazendo com que os cabeçalhos de solicitação seja reescrito. A solução é certificar-se de que a porta 80 é habilitada no firewall ou proxy.

### <a name="unexpected-response-code-500"></a>"Código de resposta inesperado: 500"

Esse erro pode ocorrer se a versão do .NET framework usada no aplicativo não coincide com a versão especificada no Web. config. A solução é verificar se o .NET 4.5 é usado em configurações do aplicativo e o arquivo Web. config.

### <a name="typeerror-lthubtypegt-is-undefined-error"></a>"TypeError: &lt;hubType&gt; é indefinido" Erro

Esse erro ocorrerá se a chamada para `MapSignalR` não é feita corretamente. Ver [como registrar o SignalR Middleware e configurar as opções de SignalR](../guide-to-the-api/hubs-api-guide-server.md#route) para obter mais informações.

### <a name="jsonserializationexception-was-unhandled-by-user-code"></a>JsonSerializationException não foi tratada pelo código do usuário

Verifique se que os parâmetros que você enviar a seus métodos não incluem os tipos não serializáveis (como identificadores de arquivos ou conexões de banco de dados). Se você precisar usar os membros em um objeto do lado do servidor que você não deseja ser enviada ao cliente (ou para segurança ou por motivos de serialização), use o `JSONIgnore` atributo.

### <a name="protocol-error-unknown-transport-error"></a>"Erro de protocolo: Erro de transporte desconhecido"

Esse erro pode ocorrer se o cliente não oferece suporte para os transportes que usa o SignalR. Ver [transportes e Fallbacks](../getting-started/introduction-to-signalr.md#transports) para obter informações no qual os navegadores podem ser usados com o SignalR.

### <a name="javascript-hub-proxy-generation-has-been-disabled"></a>"Geração de proxy de JavaScript Hub foi desabilitada."

Esse erro ocorrerá se `DisableJavaScriptProxies` está definida enquanto também inclui uma referência para o proxy gerado dinamicamente no `signalr/hubs`. Para obter mais informações sobre como criar o proxy manualmente, consulte [proxy gerado e o que ele faz para você](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy).

### <a name="the-connection-id-is-in-the-incorrect-format-or-the-user-identity-cannot-change-during-an-active-signalr-connection-error"></a>"A ID de conexão está no formato incorreto" ou "a identidade do usuário não pode alterar durante uma conexão SignalR active" Erro

Esse erro pode ser visto se a autenticação está sendo usada, e o cliente é desconectado antes que a conexão é interrompida. A solução é interromper a conexão do SignalR antes de sair do cliente.

### <a name="uncaught-error-signalr-jquery-not-found-please-ensure-jquery-is-referenced-before-the-signalrjs-file-error"></a>"Não capturada erro: SignalR: jQuery não foi encontrado. Verifique se o jQuery é referenciado antes do arquivo SignalR.js"Erro

O cliente SignalR JavaScript exige jQuery para ser executado. Verifique se sua referência para o jQuery está correta, se o caminho usado é válido e que a referência para o jQuery é antes da referência ao SignalR.

### <a name="uncaught-typeerror-cannot-read-property-ltpropertygt-of-undefined-error"></a>"Não capturada TypeError: Não é possível ler a propriedade '&lt;propriedade&gt;' indefinido "Erro

Esse erro resulta da falta jQuery ou o proxy de hubs referenciado corretamente. Verifique se sua referência para o jQuery e o proxy de hubs está correta, se o caminho usado é válido e que a referência para o jQuery é antes da referência para o proxy de hubs. A referência ao proxy hubs deve ser semelhante ao seguinte:

**Código do lado do cliente HTML que referencia corretamente o proxy de Hubs**

[!code-html[Main](troubleshooting/samples/sample12.html)]

### <a name="runtimebinderexception-was-unhandled-by-user-code-error"></a>Erro de "RuntimeBinderException não foi tratada pelo código do usuário"

Esse erro pode ocorrer quando a sobrecarga incorreta de `Hub.On` é usado. Se o método tiver um valor de retorno, o tipo de retorno deve ser especificado como um parâmetro de tipo genérico:

**Método definido no cliente (sem proxy gerada)**

[!code-html[Main](troubleshooting/samples/sample13.html?highlight=1)]

### <a name="connection-id-is-inconsistent-or-connection-breaks-between-page-loads"></a>ID de Conexão é inconsistente ou quebras de conexão entre os carregamentos de página

Esse comportamento é padrão. Uma vez que o objeto de hub está hospedado no objeto de página, o hub é destruído quando a página for atualizada. Precisa de um aplicativo de várias página manter a associação entre usuários e IDs de conexão para que eles serão consistentes entre os carregamentos de página. A IDs de conexão pode ser armazenado no servidor em qualquer um uma `ConcurrentDictionary` objeto ou um banco de dados.

### <a name="value-cannot-be-null-error"></a>Erro "O valor não pode ser nulo"

Atualmente, não há suporte para métodos do lado do servidor com parâmetros opcionais; Se o parâmetro opcional for omitido, o método falhará. Para obter mais informações, consulte [parâmetros opcionais](https://github.com/SignalR/SignalR/issues/324).

### <a name="firefox-cant-establish-a-connection-to-the-server-at-ltaddressgt-error-in-firebug"></a>"Firefox não é possível estabelecer uma conexão ao servidor em &lt;endereço&gt;" Erro no Firebug

Se a negociação de transporte de WebSocket falhar e outro transporte é usada em vez disso, essa mensagem de erro pode ser vista no Firebug. Esse comportamento é padrão.

### <a name="the-remote-certificate-is-invalid-according-to-the-validation-procedure-error-in-net-client-application"></a>Erro "o certificado remoto é inválido de acordo com o procedimento de validação" no aplicativo cliente do .NET

Se o servidor exigir certificados de cliente personalizadas, em seguida, você pode adicionar um x509certificate para a conexão antes da solicitação é feita. Adicionar o certificado para a conexão usando `Connection.AddClientCertificate`.

### <a name="connection-drops-after-authentication-times-out"></a>Conexão cair após a autenticação expira

Esse comportamento é padrão. As credenciais de autenticação não podem ser modificadas enquanto uma conexão estiver ativa; Para atualizar as credenciais, a conexão deve ser interrompido e reiniciado.

### <a name="onconnected-gets-called-twice-when-using-jquery-mobile"></a>OnConnected é chamado duas vezes ao usar o jQuery Mobile

jQuery Mobile `initializePage` função força os scripts em cada página para ser executado novamente, criando assim uma segunda conexão. As soluções para esse problema incluem:

- Inclua a referência a jQuery Mobile antes de seu arquivo JavaScript.
- Desabilitar a `initializePage` função definindo `$.mobile.autoInitializePage = false`.
- Aguarde até a página termine a inicialização antes de iniciar a conexão.

### <a name="messages-are-delayed-in-silverlight-applications-using-server-sent-events"></a>As mensagens estão atrasadas em aplicativos do Silverlight usando eventos enviados do servidor

As mensagens estão atrasadas quando usando servidor enviado eventos em Silverlight. Para forçar o longo de sondagem para ser usado em vez disso, use o seguinte ao iniciar a conexão:

[!code-css[Main](troubleshooting/samples/sample14.css)]

### <a name="permission-denied-using-forever-frame-protocol"></a>Para sempre "Permissão Denied" usando protocolo de quadro

Esse é um problema conhecido, descrito [aqui](https://github.com/SignalR/SignalR/issues/1963). Este sintoma pode ser visto usando a biblioteca mais recente de JQuery; a solução alternativa é fazer o downgrade do seu aplicativo para o JQuery 1.8.2.

### <a name="invalidoperationexception-not-a-valid-web-socket-request"></a>"InvalidOperationException: Não uma solicitação do soquete da web válido.

Esse erro pode ocorrer se o protocolo WebSocket é usado, mas o proxy de rede está modificando os cabeçalhos de solicitação. A solução é configurar o proxy para permitir que o WebSocket na porta 80.

### <a name="exception-ltmethod-namegt-method-could-not-be-resolved-when-client-calls-method-on-server"></a>"Exceção: &lt;nome do método&gt; método não pôde ser resolvido" quando o cliente chama o método no servidor

Esse erro pode resultar do uso de tipos de dados que não podem ser descobertos em uma carga JSON, como matriz. A solução alternativa é usar um tipo de dados que pode ser descoberto por JSON, como IList. Para obter mais informações, consulte [não é possível chamar métodos de hub com parâmetros da matriz de cliente .NET](https://github.com/SignalR/SignalR/issues/2672).

<a id="server"></a>

## <a name="compilation-and-server-side-errors"></a>Erros de compilação e do lado do servidor

 A seção a seguir contém as soluções possíveis para o compilador e erros de tempo de execução do lado do servidor.

### <a name="reference-to-hub-instance-is-null"></a>Referência à instância de Hub é nula

Uma vez que uma instância de hub é criada para cada conexão, é possível criar uma instância de um hub em seu código por conta própria. Para chamar métodos em um hub de fora do próprio hub, consulte [como chamar métodos de cliente e gerenciar grupos de fora da classe Hub](../guide-to-the-api/hubs-api-guide-server.md#callfromoutsidehub) para saber como obter uma referência para o contexto do hub.

### <a name="httpcontextcurrentsession-is-null"></a>HTTPContext.Current.Session é nulo

Esse comportamento é padrão. SignalR não oferece suporte para o estado de sessão do ASP.NET, como habilitar o estado de sessão interrompe de mensagens duplex.

### <a name="no-suitable-method-to-override"></a>Nenhum método adequado para substituição

Você poderá ver esse erro se você estiver usando o código de documentação mais antiga ou blogs. Verifique se que você não está referenciando os nomes de métodos que foram alterados ou preteridos (como `OnConnectedAsync`).

### <a name="hostcontextextensionswebsocketserverurl-is-null"></a>HostContextExtensions.WebSocketServerUrl é nulo

Esse comportamento é padrão. Este membro foi preterido e não deve ser usado.

### <a name="a-route-named-signalrhubs-is-already-in-the-route-collection-error"></a>Erro de "uma rota denominada 'signalr.hubs' já está na coleção de rotas"

Esse erro será exibido se `MapSignalR` é chamado duas vezes por seu aplicativo. Alguns aplicativos de exemplo chamada `MapSignalR` diretamente na classe de inicialização; outros fazer a chamada em uma classe wrapper. Certifique-se de que seu aplicativo não ambos.

### <a name="websocket-is-not-used"></a>WebSocket não é usado.

Se você verificou que o seu servidor e clientes atender os requisitos do WebSocket (listados na [plataformas com suporte](../getting-started/supported-platforms.md) documento), você precisará habilitar o WebSocket em seu servidor. Instruções sobre como fazer isso pode ser encontrados [aqui](https://www.iis.net/learn/get-started/whats-new-in-iis-8/iis-80-websocket-protocol-support).

### <a name="connection-is-undefined"></a>$.connection é indefinido

Esse erro indica que os scripts em uma página não estão sendo carregados corretamente ou que o proxy de hub não está acessível ou está sendo acessado incorretamente. Verifique se que as referências de script em sua página correspondem aos scripts carregados em seu projeto e que /SignalR/hubs. podem ser acessados em um navegador quando o servidor está em execução.

### <a name="one-or-more-types-required-to-compile-a-dynamic-expression-cannot-be-found"></a>Não não possível encontrar um ou mais tipos necessários para compilar uma expressão dinâmica

Esse erro indica que o `Microsoft.CSharp` biblioteca está ausente. Adicioná-lo na **Assemblies -&gt;Framework** guia.

### <a name="caller-state-cannot-be-accessed-from-clientscaller-in-visual-basic-or-in-a-strongly-typed-hub-conversion-from-type-taskof-object-to-type-string-is-not-valid-error"></a>Estado do chamador não pode ser acessado de Clients.Caller no Visual Basic ou em um hub com rigidez de tipos; Erro "A conversão de tipo"Task (Of Object)"para o tipo 'String' não é válida"

Para acessar o estado do chamador no Visual Basic ou em um hub com rigidez de tipos, use o `Clients.CallerState` propriedade (introduzida no SignalR 2.1) em vez de `Clients.Caller`.

<a id="vs"></a>

## <a name="visual-studio-issues"></a>Problemas do Visual Studio

Esta seção descreve os problemas encontrados no Visual Studio.

### <a name="script-documents-node-does-not-appear-in-solution-explorer"></a>Nó de documentos de script não aparece no Gerenciador de soluções

Alguns dos nossos tutoriais de direcioná-lo para o nó de "Documentos de Script" no Gerenciador de soluções durante a depuração. Esse nó é produzido pelo depurador JavaScript e só será exibida durante a depuração de clientes de navegador no Internet Explorer; o nó não aparecerão se forem usado Chrome ou Firefox. O depurador JavaScript também não será executado se outro depurador de cliente é executado, como o depurador do Silverlight.

### <a name="signalr-does-not-work-on-visual-studio-2008-or-earlier"></a>O SignalR não funciona no Visual Studio 2008 ou anterior

Esse comportamento é padrão. O SignalR requer o .NET Framework 4 ou posterior; Isso exige que os aplicativos do SignalR ser desenvolvidos no Visual Studio 2010 ou posterior. O componente de servidor do SignalR requer o .NET Framework 4.5.

<a id="iis"></a>

## <a name="iis-issues"></a>Problemas do IIS

Esta seção contém problemas com os serviços de informações da Internet.

### <a name="signalr-works-on-visual-studio-development-server-but-not-in-iis"></a>O SignalR funciona no servidor de desenvolvimento do Visual Studio, mas não no IIS

O SignalR é compatível com o IIS 7.0 e 7.5, mas o suporte para URLs sem extensão devem ser adicionadas. Para adicionar suporte para URLs sem extensão, consulte [https://support.microsoft.com/kb/980368](https://support.microsoft.com/kb/980368)

O SignalR exige o ASP.NET ser instalado no servidor (ASP.NET não está instalado no IIS por padrão). Para instalar o ASP.NET, consulte [Downloads do ASP.NET](https://www.asp.net/downloads).

<a id="azure"></a>

## <a name="microsoft-azure-issues"></a>Problemas do Microsoft Azure

Esta seção contém problemas com o Microsoft Azure.

### <a name="fileloadexception-when-hosting-signalr-in-an-azure-worker-role"></a>FileLoadException ao hospedar o SignalR em uma função de trabalho do Azure

Hospedar o SignalR em uma função de trabalho do Azure pode resultar na exceção "não foi possível carregar arquivo ou assembly ' Microsoft. owin, versão = 2.0.0.0". Esse é um problema conhecido com o NuGet; Redirecionamentos de associação não são adicionados automaticamente em projetos de função de trabalho do Azure. Para corrigir isso, você pode adicionar manualmente os redirecionamentos de associação. Adicione as seguintes linhas para o `app.config` arquivo do seu projeto de função de trabalho.

[!code-xml[Main](troubleshooting/samples/sample15.xml)]

### <a name="messages-are-not-received-through-the-azure-backplane-after-altering-topic-names"></a>As mensagens não são recebidas por meio do backplane do Azure após a alteração de nomes de tópico

Os tópicos usados pelo Azure backplane são mantidos internamente; elas não pretendem ser configurável pelo usuário.
