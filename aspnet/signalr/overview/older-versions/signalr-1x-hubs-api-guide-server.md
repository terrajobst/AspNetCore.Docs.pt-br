---
uid: signalr/overview/older-versions/signalr-1x-hubs-api-guide-server
title: Guia de API de Hubs do SignalR do ASP.NET – servidor (SignalR 1.x) | Microsoft Docs
author: bradygaster
description: Este documento fornece uma introdução à programação do lado do servidor da API de Hubs de SignalR do ASP.NET para o SignalR versão 1.1, com demonstratin de exemplos de código...
ms.author: bradyg
ms.date: 04/17/2013
ms.assetid: 03e4b9f5-0fea-4d94-959f-014b2762a301
msc.legacyurl: /signalr/overview/older-versions/signalr-1x-hubs-api-guide-server
msc.type: authoredcontent
ms.openlocfilehash: 8d544e81f87998581afb2a1228233b4d374ad70a
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837384"
---
<a name="aspnet-signalr-hubs-api-guide---server-signalr-1x"></a>Guia de API de Hubs do SignalR do ASP.NET – servidor (SignalR 1.x)
====================
by [Patrick Fletcher](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este documento fornece uma introdução à programação do lado do servidor da API de Hubs de SignalR do ASP.NET para o SignalR versão 1.1, com exemplos de código que demonstra as opções comuns.
> 
> A API de Hubs de SignalR permite que você faça chamadas de procedimento remoto (RPCs) de um servidor para clientes conectados e de clientes para o servidor. No código do servidor, você define métodos que podem ser chamados por clientes e você chamar métodos que são executados no cliente. No código do cliente, você define métodos que podem ser chamados a partir do servidor e você chamar métodos que são executados no servidor. O SignalR é responsável por todos os detalhes de cliente-servidor para você.
> 
> O SignalR também oferece uma API de nível inferior chamada conexões persistentes. Para obter uma introdução ao SignalR, Hubs e conexões persistentes, ou para um tutorial que mostra como criar um aplicativo completo do SignalR, consulte [SignalR - Introdução ao](index.md).


## <a name="overview"></a>Visão geral

Este documento contém as seguintes seções:

- [Como registrar o roteiro de SignalR e configurar as opções de SignalR](#route)

    - [A URL de /signalr](#signalrurl)
    - [Configurando as opções de SignalR](#options)
- [Como criar e usar as classes de Hub](#hubclass)

    - [Tempo de vida de objeto de Hub](#transience)
    - [Camel case de nomes de Hub em clientes JavaScript](#hubnames)
    - [Vários Hubs](#multiplehubs)
- [Como definir métodos na classe Hub que os clientes poderão chamar](#hubmethods)

    - [Camel case de nomes de método nos clientes JavaScript](#methodnames)
    - [Quando executado de forma assíncrona](#asyncmethods)
    - [Definindo as sobrecargas](#overloads)
- [Como chamar métodos de cliente da classe Hub](#callfromhub)

    - [Selecionar quais clientes receberão o RPC](#selectingclients)
    - [Nenhuma validação de tempo de compilação para nomes de método](#dynamicmethodnames)
    - [Correspondência de nomes do método diferencia maiusculas de minúsculas](#caseinsensitive)
    - [Execução assíncrona](#asyncclient)
- [Como gerenciar a associação de grupo da classe Hub](#groupsfromhub)

    - [Execução assíncrona dos métodos Add e Remove](#asyncgroupmethods)
    - [Persistência de associação de grupo](#grouppersistence)
    - [Grupos de usuário único](#singleusergroups)
- [Como manipular eventos de tempo de vida de conexão na classe Hub](#connectionlifetime)

    - [Quando são chamados OnConnected, OnDisconnected e OnReconnected](#onreconnected)
    - [Estado do chamador não preenchido](#nocallerstate)
- [Como obter informações sobre o cliente da propriedade de contexto](#contextproperty)
- [Como passar o estado entre clientes e a classe Hub](#passstate)
- [Como tratar erros na classe Hub](#handleErrors)
- [Como chamar métodos de cliente e gerenciar grupos de fora da classe Hub](#callfromoutsidehub)

    - [Chamar métodos de cliente](#callingclientsoutsidehub)
    - [Gerenciar associação de grupo](#managinggroupsoutsidehub)
- [Como habilitar o rastreamento](#tracing)
- [Como personalizar o pipeline de Hubs](#hubpipeline)

Para obter a documentação sobre como os clientes do programa, consulte os seguintes recursos:

- [O SignalR guia da API Hubs – cliente JavaScript](index.md)
- [O SignalR guia da API Hubs – cliente .NET](index.md)

Links para tópicos de referência de API são para a versão 4.5 do .NET da API. Se você estiver usando o .NET 4, consulte [a versão do .NET 4 dos tópicos da API](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).

<a id="route"></a>

## <a name="how-to-register-the-signalr-route-and-configure-signalr-options"></a>Como registrar o roteiro de SignalR e configurar as opções de SignalR

Para definir a rota que os clientes usarão para se conectar ao seu Hub, chame o [MapHubs](https://msdn.microsoft.com/library/system.web.routing.signalrrouteextensions.maphubs(v=vs.111).aspx) método quando o aplicativo é iniciado. `MapHubs` é um [método de extensão](https://msdn.microsoft.com/library/vstudio/bb383977.aspx) para o `System.Web.Routing.RouteCollection` classe. O exemplo a seguir mostra como definir a rota de Hubs do SignalR na *global. asax* arquivo.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample1.cs)]

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample2.cs?highlight=5)]

Se você estiver adicionando funcionalidade SignalR para um aplicativo ASP.NET MVC, certifique-se de que a rota do SignalR é adicionada antes de outras rotas. Para obter mais informações, confira [Tutorial: Introdução ao SignalR e MVC 4](index.md).

<a id="signalrurl"></a>

### <a name="the-signalr-url"></a>A URL de /signalr

Por padrão, a URL da rota que os clientes usarão para se conectar ao seu Hub é "/ signalr". (Não confunda essa URL com a URL "hubs de signalr /", que é para o arquivo JavaScript gerado automaticamente. Para obter mais informações sobre o proxy gerado, consulte [SignalR guia da API Hubs – cliente JavaScript - proxy gerado e o que ele faz para você](index.md).)

Pode haver circunstâncias extraordinários que tornam essa URL base não pode ser usado para o SignalR; Por exemplo, você tem uma pasta em seu projeto chamado *signalr* e você não quiser alterar o nome. Nesse caso, você pode alterar a URL base, conforme mostrado nos exemplos a seguir (substitua "/ signalr" no código de exemplo com a URL desejada).

**Código que especifica a URL do servidor**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample3.cs?highlight=1)]

**Código JavaScript do cliente que especifica a URL (com o proxy gerado)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample4.js?highlight=1)]

**Código JavaScript do cliente que especifica a URL (sem o proxy gerado)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample5.js?highlight=1)]

**Código de cliente .NET que especifica a URL**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample6.cs?highlight=1)]

<a id="options"></a>

### <a name="configuring-signalr-options"></a>Configurando as opções de SignalR

Sobrecargas do `MapHubs` método permitem que você especifique uma URL personalizada, um resolvedor de dependência personalizadas e as opções a seguir:

- Permitir chamadas entre domínios de clientes de navegador.

    Normalmente se o navegador carrega uma página a partir `http://contoso.com`, a conexão do SignalR está no mesmo domínio, no `http://contoso.com/signalr`. Se a página a partir `http://contoso.com` faz uma conexão para `http://fabrikam.com/signalr`, que é uma conexão entre domínios. Por motivos de segurança, conexões entre domínios estão desabilitadas por padrão. Para obter mais informações, consulte [ASP.NET SignalR guia da API Hubs – cliente JavaScript - como estabelecer uma conexão entre domínios](index.md).
- Habilite mensagens de erro detalhadas.

    Quando ocorrem erros, o comportamento padrão do SignalR é enviar aos clientes uma mensagem de notificação sem os detalhes sobre o que aconteceu. Enviar informações de erro detalhadas para clientes não é recomendado em produção, porque usuários mal-intencionados podem ser capazes de usar as informações em ataques contra seu aplicativo. Para solucionar o problema, você pode usar essa opção para habilitar o relatório de erro mais informativo temporariamente.
- Desabilite arquivos de proxy JavaScript gerados automaticamente.

    Por padrão, um arquivo JavaScript com proxies para as suas classes de Hub é gerado em resposta à URL "hubs de signalr /". Se você não quiser usar os proxies JavaScript ou, se você quiser gerar esse arquivo manualmente e se referir a um arquivo físico em seus clientes, você pode usar essa opção para desativar a geração de proxy. Para obter mais informações, consulte [SignalR guia da API Hubs – cliente JavaScript - como criar um arquivo físico para o SignalR gerado proxy](index.md).

O exemplo a seguir mostra como especificar a URL de conexão do SignalR e essas opções em uma chamada para o `MapHubs` método. Para especificar uma URL personalizada, substitua "/ signalr" no exemplo com a URL que você deseja usar.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample7.cs)]

<a id="hubclass"></a>

## <a name="how-to-create-and-use-hub-classes"></a>Como criar e usar as classes de Hub

Para criar um Hub, crie uma classe que deriva de [ASPNET](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx). O exemplo a seguir mostra uma classe simples de Hub para um aplicativo de bate-papo.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample8.cs)]

Neste exemplo, um cliente conectado pode chamar o `NewContosoChatMessage` método, e quando isso acontecer, os dados recebidos são transmitidos para todos os clientes conectados.

<a id="transience"></a>

### <a name="hub-object-lifetime"></a>Tempo de vida de objeto de Hub

Você não criar uma instância da classe Hub ou chamar seus métodos de seu próprio código no servidor. tudo isso é feito para você pelo pipeline de Hubs do SignalR. O SignalR cria uma nova instância da classe de seu Hub cada vez que ele precisa lidar com uma operação de Hub, como quando um cliente se conecta, desconecta ou faz um método de chamada para o servidor.

Como as instâncias da classe Hub são transitórias, você não pode usá-los para manter o estado de uma chamada de método para a próxima. Cada vez que o servidor recebe uma chamada de método em um cliente, uma nova instância de seus processos de classe Hub a mensagem. Para manter o estado por meio de várias conexões e chamadas de método, use algum outro método, como um banco de dados ou uma variável estática na classe Hub ou uma classe diferente que não derivam de `Hub`. Se você mantiver os dados na memória, usando um método como uma variável estática na classe Hub, os dados serão perdidos quando o domínio de aplicativo é reciclado.

Se você quiser enviar mensagens para os clientes do seu próprio código que é executado fora da classe Hub, você não pode fazer isso criando uma instância da classe Hub, mas você pode fazer isso obtendo uma referência ao objeto de contexto SignalR para sua classe Hub. Para obter mais informações, consulte [como chamar métodos de cliente e gerenciar grupos de fora da classe Hub](#callfromoutsidehub) mais adiante neste tópico.

<a id="hubnames"></a>

### <a name="camel-casing-of-hub-names-in-javascript-clients"></a>Camel case de nomes de Hub em clientes JavaScript

Por padrão, os clientes JavaScript consultem Hubs usando uma versão em camel case do nome da classe. O SignalR faz automaticamente essa alteração para que o código JavaScript pode estar em conformidade com as convenções de JavaScript. O exemplo anterior seria conhecido como `contosoChatHub` no código JavaScript.

**Servidor**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample9.cs?highlight=1)]

**Cliente JavaScript usando o proxy gerado**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample10.js?highlight=1)]

Se você deseja especificar um nome diferente para os clientes usar, adicione o `HubName` atributo. Quando você usa um `HubName` de atributo, não há nenhuma alteração de nome para concatenação com maiusculas nos clientes JavaScript.

**Servidor**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample11.cs?highlight=1)]

**Cliente JavaScript usando o proxy gerado**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample12.js?highlight=1)]

<a id="multiplehubs"></a>

### <a name="multiple-hubs"></a>Vários Hubs

Você pode definir várias classes de Hub em um aplicativo. Quando você fizer isso, a conexão é compartilhada, mas os grupos são separados:

- Todos os clientes usarão a mesma URL para estabelecer uma conexão SignalR com o seu serviço ("/ signalr" ou a URL personalizada se você tiver especificado um), e que a conexão é usada para todos os Hubs são definidas pelo serviço.

    Não há nenhuma diferença de desempenho para vários Hubs em comparação à definição de todas as funcionalidades do Hub em uma única classe.
- Todos os Hubs de obtém as mesmas informações de solicitação HTTP.

    Como todos os Hubs compartilham a mesma conexão, as únicas informações de solicitação HTTP que obtém o servidor são o que é fornecido na solicitação HTTP original que estabelece a conexão do SignalR. Se você usar a solicitação de conexão para passar informações do cliente para o servidor, especificando uma cadeia de caracteres de consulta, você não pode fornecer cadeias de caracteres de consulta diferente para os Hubs diferentes. Todos os Hubs receberão as mesmas informações.
- O arquivo de proxies JavaScript gerado conterá proxies para todos os Hubs em um arquivo.

    Para obter informações sobre proxies de JavaScript, consulte [SignalR guia da API Hubs – cliente JavaScript - proxy gerado e o que ele faz para você](index.md).
- Grupos são definidos dentro de Hubs.

    No SignalR, que você pode definir grupos nomeados para difundir a subconjuntos de clientes conectados. Grupos são mantidos separadamente para cada Hub. Por exemplo, um grupo chamado "Administradores" inclui um conjunto de clientes para seus `ContosoChatHub` classe e o mesmo nome de grupo referência a um conjunto diferente de clientes para o seu `StockTickerHub` classe.

<a id="hubmethods"></a>

## <a name="how-to-define-methods-in-the-hub-class-that-clients-can-call"></a>Como definir métodos na classe Hub que os clientes poderão chamar

Para expor um método no Hub que você deseja poder ser chamado do cliente, declare um método público, conforme mostrado nos exemplos a seguir.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample13.cs?highlight=3)]

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample14.cs?highlight=3)]

Você pode especificar um tipo de retorno e parâmetros, incluindo tipos complexos e matrizes, como você faria em qualquer método em c#. Todos os dados que você receber em parâmetros ou retorna ao chamador são comunicados entre o cliente e o servidor usando o JSON e SignalR manipula automaticamente a associação de objetos complexos e matrizes de objetos.

<a id="methodnames"></a>

### <a name="camel-casing-of-method-names-in-javascript-clients"></a>Camel case de nomes de método nos clientes JavaScript

Por padrão, os clientes JavaScript se referir a métodos de Hub usando uma versão em camel case do nome do método. O SignalR faz automaticamente essa alteração para que o código JavaScript pode estar em conformidade com as convenções de JavaScript.

**Servidor**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample15.cs?highlight=1)]

**Cliente JavaScript usando o proxy gerado**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample16.js?highlight=1)]

Se você deseja especificar um nome diferente para os clientes usar, adicione o `HubMethodName` atributo.

**Servidor**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample17.cs?highlight=1)]

**Cliente JavaScript usando o proxy gerado**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample18.js?highlight=1)]

<a id="asyncmethods"></a>

### <a name="when-to-execute-asynchronously"></a>Quando executado de forma assíncrona

Se o método será ser longa ou tem que fazer o trabalho seria envolvem espera, como uma pesquisa de banco de dados ou uma chamada de serviço web, tornar o método de Hub assíncrono, retornando um [tarefa](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) (em vez de `void` retornar) ou [ Tarefa&lt;T&gt; ](https://msdn.microsoft.com/library/dd321424.aspx) objeto (em vez de `T` tipo de retorno). Quando você retornar um `Task` objeto do método, o SignalR aguarda o `Task` para ser concluída, e, em seguida, ele envia o resultado desencapsulado para o cliente, portanto, não há nenhuma diferença em como você codificar a chamada de método no cliente.

Tornando um método de Hub assíncronas evita o bloqueio da conexão quando ele usa o transporte de WebSocket. Quando um método de Hub executa de forma síncrona e o transporte de WebSocket, invocações subsequentes dos métodos no Hub do mesmo cliente são bloqueadas até que o método de Hub seja concluída.

A exemplo a seguir mostra o mesmo método codificados para executar de forma síncrona ou assíncrona, seguido pelo código do cliente JavaScript que funcione para chamar uma das versões.

**Síncrona**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample19.cs)]

**Assíncrono - ASP.NET 4.5**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample20.cs?highlight=1,7-8)]

**Cliente JavaScript usando o proxy gerado**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample21.js)]

Para obter mais informações sobre como usar métodos assíncronos no ASP.NET 4.5, consulte [usando os métodos assíncronos no ASP.NET MVC 4](../../../mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4.md).

<a id="overloads"></a>

### <a name="defining-overloads"></a>Definindo as sobrecargas

Se você quiser definir sobrecargas para um método, o número de parâmetros em cada sobrecarga deve ser diferente. Se você diferenciar uma sobrecarga apenas especificando tipos de parâmetro diferentes, sua classe Hub será compilado, mas o serviço SignalR lançará uma exceção em tempo de execução quando os clientes tentam para chamada de uma das sobrecargas.

<a id="callfromhub"></a>

## <a name="how-to-call-client-methods-from-the-hub-class"></a>Como chamar métodos de cliente da classe Hub

Para chamar métodos de cliente do servidor, use o `Clients` propriedade em um método em sua classe Hub. O exemplo a seguir mostra o código de servidor que chama `addNewMessageToPage` em clientes tudo conectados e código do cliente que define o método em um cliente JavaScript.

**Servidor**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample22.cs?highlight=5)]

**Cliente JavaScript usando o proxy gerado**

[!code-html[Main](signalr-1x-hubs-api-guide-server/samples/sample23.html?highlight=1)]

Não é possível obter um valor de retorno de um método de cliente; sintaxe como `int x = Clients.All.add(1,1)` não funciona.

Você pode especificar os tipos complexos e matrizes como parâmetros. O exemplo a seguir passa um tipo complexo para o cliente em um parâmetro de método.

**Código de servidor que chama um método de cliente usando um objeto complexo**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample24.cs?highlight=3)]

**Código de servidor que define o objeto complexo**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample25.cs?highlight=1)]

**Cliente JavaScript usando o proxy gerado**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample26.js?highlight=2-3)]

<a id="selectingclients"></a>

### <a name="selecting-which-clients-will-receive-the-rpc"></a>Selecionar quais clientes receberão o RPC

A propriedade retorna de clientes uma [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx) objeto que fornece várias opções para especificar quais clientes receberão o RPC:

- Todos os clientes conectados.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample27.cs)]
- Somente o cliente da chamada.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample28.cs)]
- Todos os clientes, exceto o cliente da chamada.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample29.cs)]
- Um cliente específico identificado pela ID de conexão.

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample30.css)]

    Este exemplo chama `addContosoChatMessageToPage` no cliente da chamada e tem o mesmo efeito que usar `Clients.Caller`.
- Todos os clientes conectados, exceto clientes especificados, identificados pela ID de conexão.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample31.cs)]
- Todos os clientes em um grupo especificado de conectados.

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample32.css)]
- Todos os clientes conectados em um grupo especificado, exceto a clientes especificados, identificados pela ID de conexão.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample33.cs)]
- Todos os clientes conectados em um grupo especificado, exceto o cliente da chamada.

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample34.css)]

<a id="dynamicmethodnames"></a>

### <a name="no-compile-time-validation-for-method-names"></a>Nenhuma validação de tempo de compilação para nomes de método

O nome do método que você especificar será interpretado como um objeto dinâmico, o que significa que nenhum IntelliSense ou validação de tempo de compilação para ele. A expressão é avaliada em tempo de execução. Quando a chamada de método é executado, SignalR envia o nome do método e os valores de parâmetro para o cliente e se o cliente tiver um método que corresponde ao nome, que é chamado de método e os valores de parâmetro são passados a ele. Se nenhum método correspondente for encontrado no cliente, nenhum erro será gerado. Para obter informações sobre o formato dos dados que o SignalR transmite para o cliente em segundo plano quando você chama um método de cliente, consulte [Introdução ao SignalR](index.md).

<a id="caseinsensitive"></a>

### <a name="case-insensitive-method-name-matching"></a>Correspondência de nomes do método diferencia maiusculas de minúsculas

Correspondência de nomes do método diferencia maiusculas de minúsculas. Por exemplo, `Clients.All.addContosoChatMessageToPage` no servidor serão executadas `AddContosoChatMessageToPage`, `addcontosochatmessagetopage`, ou `addContosoChatMessageToPage` no cliente.

<a id="asyncclient"></a>

### <a name="asynchronous-execution"></a>Execução assíncrona

O método que você chamar executa de forma assíncrona. Qualquer código que vem após uma chamada de método para um cliente será executado imediatamente sem aguardar o SignalR concluir a transmissão de dados para os clientes, a menos que você especifica que as linhas subsequentes de código deve aguardar a conclusão do método. Os exemplos a seguir mostram como dois métodos de cliente são executadas sequencialmente, um usando o código que funciona no .NET 4.5 e um usando o código que funciona no .NET 4.

**Exemplo de .NET 4.5**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample35.cs?highlight=1,3)]

**Exemplo de .NET 4**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample36.cs?highlight=3-4)]

Se você usar `await` ou `ContinueWith` para esperar até que um método de cliente seja concluída antes da próxima linha de código ser executado, isso não significa que os clientes, na verdade, receberá a mensagem antes da próxima linha de código ser executado. "Conclusão" de uma chamada de método do cliente significa apenas que o SignalR fez tudo o que é necessário para enviar a mensagem. Se você precisar que os clientes receberam a mensagem de verificação, é preciso programar esse mecanismo por conta própria. Por exemplo, você pode codificar uma `MessageReceived` método no Hub e nos `addContosoChatMessageToPage` método no cliente, você poderia chamar `MessageReceived` depois de fazer qualquer trabalho que você precisa fazer no cliente. No `MessageReceived` no Hub, você pode fazer qualquer trabalho depende de recepção de cliente real e processamento de chamada do método original.

### <a name="how-to-use-a-string-variable-as-the-method-name"></a>Como usar uma variável de cadeia de caracteres como o nome do método

Se você deseja invocar um método de cliente, usando uma variável de cadeia de caracteres como o nome do método, convertido `Clients.All` (ou `Clients.Others`, `Clients.Caller`, etc.) para `IClientProxy` e, em seguida, chamar [Invoke (methodName, args...) ](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.iclientproxy.invoke(v=vs.111).aspx).

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample37.cs)]

<a id="groupsfromhub"></a>

## <a name="how-to-manage-group-membership-from-the-hub-class"></a>Como gerenciar a associação de grupo da classe Hub

Grupos no SignalR fornecem um método para mensagens de difusão para subconjuntos especificados de clientes conectados. Um grupo pode ter qualquer número de clientes e um cliente pode ser um membro de qualquer número de grupos.

Para gerenciar a associação de grupo, use o [Add](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) e [remover](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) métodos fornecidos pelo `Groups` propriedade da classe Hub. A exemplo a seguir mostra a `Groups.Add` e `Groups.Remove` métodos usados em métodos de Hub que são chamados pelo código do cliente, seguida pelo código do cliente JavaScript que os chama.

**Servidor**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample38.cs?highlight=5,10)]

**Cliente JavaScript usando o proxy gerado**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample39.js)]

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample40.js)]

Você não precisa criar explicitamente grupos. Na verdade um grupo é criado automaticamente na primeira vez em que você especifica seu nome em uma chamada para `Groups.Add`, e ela será excluída quando você remove a última conexão de associação contidos nela.

Há uma API para obter uma lista de membros do grupo ou uma lista de grupos. O SignalR envia mensagens para os clientes e grupos com base em um [modelo pub/sub](http://en.wikipedia.org/wiki/Publish/subscribe), e o servidor não mantém listas de grupos ou associações de grupo. Isso ajuda a maximizar a escalabilidade, porque sempre que você adicionar um nó a uma web farm, qualquer estado que mantém o SignalR tem que ser propagadas para o novo nó.

<a id="asyncgroupmethods"></a>

### <a name="asynchronous-execution-of-add-and-remove-methods"></a>Execução assíncrona dos métodos Add e Remove

O `Groups.Add` e `Groups.Remove` métodos são executados de forma assíncrona. Se você quiser adicionar um cliente a um grupo e imediatamente a enviar uma mensagem para o cliente usando o grupo, você precisa certificar-se de que o `Groups.Add` método termina primeiro. Os exemplos de código a seguir mostram como fazer isso, uma usando o código que funciona no .NET 4.5 e outra usando o código que funciona no .NET 4

**Exemplo de .NET 4.5**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample41.cs?highlight=1,3)]

**Exemplo de .NET 4**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample42.cs?highlight=3-4)]

<a id="grouppersistence"></a>

### <a name="group-membership-persistence"></a>Persistência de associação de grupo

O SignalR rastreia conexões, não aos usuários, portanto, se você deseja que um usuário estar no mesmo grupo sempre que o usuário estabelece uma conexão, você precisa chamar `Groups.Add` toda vez que o usuário estabelece uma nova conexão.

Após uma perda temporária de conectividade, às vezes, o SignalR pode restaurar a conexão automaticamente. Nesse caso, SignalR está restaurando a mesma conexão, sem estabelecer uma conexão nova e então, a associação de grupo do cliente é automaticamente restaurada. Isso é possível até mesmo quando a interrupção temporária é o resultado de uma reinicialização do servidor ou a falha, porque o estado de conexão para cada cliente, incluindo associações de grupo, é recuperado para o cliente. Se um servidor fica inativo e é substituído por um novo servidor antes da conexão atinge o tempo limite, um cliente pode se reconectar automaticamente ao novo servidor e registrar novamente em grupos, de que ele é um membro.

Quando uma conexão não pode ser restaurado automaticamente após uma perda de conectividade, ou quando a conexão atinge o tempo limite, ou quando o cliente se desconecta (por exemplo, quando um navegador navega para uma nova página), associações de grupo são perdidas. Na próxima vez que o usuário se conecta será uma nova conexão. Para manter as associações de grupo quando o mesmo usuário estabelece uma conexão nova, seu aplicativo tem que controlar as associações entre usuários e grupos e restaurar as associações de grupo sempre que um usuário estabelece uma nova conexão.

Para obter mais informações sobre as conexões e reconexões, consulte [como manipular eventos de tempo de vida de conexão na classe Hub](#connectionlifetime) mais adiante neste tópico.

<a id="singleusergroups"></a>

### <a name="single-user-groups"></a>Grupos de usuário único

Aplicativos que usam o SignalR normalmente têm que acompanhar as associações entre usuários e conexões para saber qual usuário enviou uma mensagem e quais usuários devem receber uma mensagem. Grupos são usados em um dos dois padrões comumente usados para fazer isso.

- Grupos de usuário único.

    Você pode especificar o nome de usuário como o nome do grupo e adicionar a ID de conexão atual ao grupo sempre que o usuário se conecta ou se reconectar. Para enviar mensagens para o usuário que enviar ao grupo. Uma desvantagem desse método é que o grupo não oferece a você uma maneira de descobrir se o usuário está online ou offline.
- Rastrear as associações entre os nomes de usuário e IDs de conexão.

    Você pode armazenar uma associação entre cada nome de usuário e uma ou mais IDs de conexão em um dicionário ou o banco de dados e atualizar os dados armazenados sempre que o usuário se conecta ou desconecta. Para enviar mensagens para o usuário especifique a IDs de conexão. Uma desvantagem desse método é que ele usa mais memória.

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events-in-the-hub-class"></a>Como manipular eventos de tempo de vida de conexão na classe Hub

As razões comuns de manipulação de eventos de tempo de vida de conexão são para controlar se um usuário está conectado ou não e para controlar a associação entre os nomes de usuário e IDs de conexão. Para executar seu próprio código quando os clientes se conectar ou desconectarem, substituir os `OnConnected`, `OnDisconnected`, e `OnReconnected` métodos virtuais do Hub de classe, conforme mostrado no exemplo a seguir.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample43.cs?highlight=3,14,22)]

<a id="onreconnected"></a>

### <a name="when-onconnected-ondisconnected-and-onreconnected-are-called"></a>Quando são chamados OnConnected, OnDisconnected e OnReconnected

Cada vez que um navegador navega para uma nova página, uma nova conexão deve ser estabelecido, que significa que o SignalR executará o `OnDisconnected` método seguido o `OnConnected` método. O SignalR sempre cria uma nova ID de conexão quando uma nova conexão é estabelecida.

O `OnReconnected` método é chamado quando houve uma interrupção temporária na conectividade SignalR pode se recuperar automaticamente, como quando um cabo está temporariamente desconectado e reconectado antes que a conexão atinge o tempo limite. O `OnDisconnected` método é chamado quando o cliente é desconectado e SignalR não é possível reconectar-se automaticamente, como quando um navegador navega para uma nova página. Portanto, é uma sequência de eventos para um determinado cliente de possíveis `OnConnected`, `OnReconnected`, `OnDisconnected`; ou `OnConnected`, `OnDisconnected`. Você não verá a sequência `OnConnected`, `OnDisconnected`, `OnReconnected` para uma determinada conexão.

O `OnDisconnected` método não é chamado em alguns cenários, como quando um servidor ficar inativo ou o domínio de aplicativo é reciclado. Quando outro servidor é fornecido na linha ou o domínio de aplicativo termina seu reciclagem, alguns clientes talvez consiga se reconectar e acionar o `OnReconnected` eventos.

Para obter mais informações, consulte [Noções básicas e tratamento de eventos de tempo de vida de Conexão no SignalR](index.md).

<a id="nocallerstate"></a>

### <a name="caller-state-not-populated"></a>Estado do chamador não preenchido

Os métodos de manipulador de eventos de tempo de vida de conexão são chamados de servidor, o que significa que qualquer estado que você colocar nas `state` objeto no cliente não será preenchido no `Caller` propriedade no servidor. Para obter informações sobre o `state` objeto e o `Caller` propriedade, consulte [como passar o estado entre clientes e a classe Hub](#passstate) mais adiante neste tópico.

<a id="contextproperty"></a>

## <a name="how-to-get-information-about-the-client-from-the-context-property"></a>Como obter informações sobre o cliente da propriedade de contexto

Para obter informações sobre o cliente, use o `Context` propriedade da classe Hub. O `Context` propriedade retorna um [HubCallerContext](https://msdn.microsoft.com/library/jj890883(v=vs.111).aspx) objeto que fornece acesso às seguintes informações:

- A ID de conexão do cliente da chamada.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample44.cs?highlight=1)]

    A ID de conexão é um GUID que é atribuído pelo SignalR (não é possível especificar o valor em seu próprio código). Há uma ID de conexão para cada conexão e a mesma conexão que ID é usada por todos os Hubs, se você tiver vários Hubs em seu aplicativo.
- Dados de cabeçalho HTTP.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample45.cs?highlight=1)]

    Você também pode obter os cabeçalhos HTTP de `Context.Headers`. O motivo para várias referências à mesma coisa é que `Context.Headers` foi criado pela primeira vez, o `Context.Request` propriedade foi adicionada posteriormente, e `Context.Headers` foi mantido para compatibilidade com versões anteriores.
- Dados de cadeia de caracteres de consulta.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample46.cs?highlight=1)]

    Você também pode obter dados de cadeia de caracteres de consulta de `Context.QueryString`.

    A cadeia de caracteres de consulta que você obtém nessa propriedade é aquele que foi usado com a solicitação HTTP que é estabelecer a conexão do SignalR. Você pode adicionar parâmetros de cadeia de caracteres de consulta no cliente por meio da configuração de conexão, que é uma maneira conveniente para passar dados sobre o cliente do cliente para o servidor. O exemplo a seguir mostra uma maneira de adicionar uma cadeia de caracteres de consulta em um cliente JavaScript ao usar o proxy gerado.

    [!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample47.js?highlight=1)]

    Para obter mais informações sobre como definir parâmetros de cadeia de caracteres de consulta, consulte os guias de API para o [JavaScript](index.md) e [.NET](index.md) clientes.

    Você pode encontrar o método de transporte usado para a conexão nos dados de cadeia de caracteres de consulta, juntamente com alguns outros valores usados internamente pelo SignalR:

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample48.cs)]

    O valor de `transportMethod` será "webSockets", "serverSentEvents", "foreverFrame" ou "longPolling". Observe que, se você verificar esse valor `OnConnected` método do manipulador de eventos, em alguns cenários, você pode obter inicialmente um valor de transporte que não é o método de transporte negociada final para a conexão. Nesse caso, o método lançará uma exceção e será chamado novamente mais tarde quando o método de transporte final é estabelecido.
- Cookies.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample49.cs?highlight=1)]

    Você também pode obter cookies do `Context.RequestCookies`.
- Informações do usuário.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample50.cs?highlight=1)]
- O objeto HttpContext para a solicitação:

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample51.cs?highlight=1)]

    Use esse método em vez de obter `HttpContext.Current` para obter o `HttpContext` objeto para a conexão do SignalR.

<a id="passstate"></a>

## <a name="how-to-pass-state-between-clients-and-the-hub-class"></a>Como passar o estado entre clientes e a classe Hub

O proxy de cliente fornece um `state` objeto no qual você pode armazenar dados que você deseja ser transmitida para o servidor com cada chamada de método. No servidor, você pode acessar esses dados no `Clients.Caller` propriedade nos métodos de Hub que são chamados pelos clientes. O `Clients.Caller` propriedade não é populada para os métodos de manipulador de eventos de tempo de vida de conexão `OnConnected`, `OnDisconnected`, e `OnReconnected`.

Criar ou atualizar dados na `state` objeto e o `Clients.Caller` propriedade funciona em ambas as direções. Você pode atualizar os valores no servidor e eles são passados de volta ao cliente.

O exemplo a seguir mostra o código JavaScript do cliente que armazena o estado para a transmissão para o servidor com cada chamada de método.

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample52.js?highlight=1-2)]

O exemplo a seguir mostra o código equivalente em um cliente .NET.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample53.cs?highlight=1-2)]

Em sua classe Hub, você pode acessar esses dados no `Clients.Caller` propriedade. O exemplo a seguir mostra o código que recupera o estado conhecido no exemplo anterior.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample54.cs?highlight=3-4)]

> [!NOTE]
> Esse mecanismo para persistir o estado não é destinado para grandes quantidades de dados, desde que tudo o que você colocar nas `state` ou `Clients.Caller` propriedade é recuperado com cada invocação de método. É útil para itens menores, como nomes de usuário ou contadores.


<a id="handleErrors"></a>

## <a name="how-to-handle-errors-in-the-hub-class"></a>Como tratar erros na classe Hub

Para tratar erros que ocorrem em seus métodos de classe Hub, use um ou ambos dos seguintes métodos:

- Encapsular o código do método em blocos try-catch e o objeto de exceção de log. Para fins de depuração, você pode enviar a exceção para o cliente, mas para segurança motivos enviando informações detalhadas para clientes em produção não são recomendados.
- Criar um módulo de pipeline de Hubs que lida com o [OnIncomingError](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubpipelinemodule.onincomingerror(v=vs.111).aspx) método. O exemplo a seguir mostra um módulo de pipeline que registra erros, seguidos do código no global. asax que injeta o módulo no pipeline de Hubs.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample55.cs)]

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample56.cs?highlight=3)]

Para obter mais informações sobre os módulos do pipeline de Hub, consulte [como personalizar o pipeline de Hubs](#hubpipeline) mais adiante neste tópico.

<a id="tracing"></a>

## <a name="how-to-enable-tracing"></a>Como habilitar o rastreamento

Para habilitar o rastreamento do lado do servidor, adicione um elemento System. Diagnostics ao seu arquivo Web. config, conforme mostrado neste exemplo:

[!code-html[Main](signalr-1x-hubs-api-guide-server/samples/sample57.html?highlight=17-72)]

Quando você executa o aplicativo no Visual Studio, você pode exibir os logs na **saída** janela.

<a id="callfromoutsidehub"></a>

## <a name="how-to-call-client-methods-and-manage-groups-from-outside-the-hub-class"></a>Como chamar métodos de cliente e gerenciar grupos de fora da classe Hub

Para chamar métodos de cliente de uma classe diferente de sua classe Hub, obtenha uma referência para o objeto de contexto do SignalR para o Hub e usá-la para chamar métodos no cliente ou gerenciar grupos.

O exemplo a seguir `StockTicker` classe obtém o objeto de contexto, armazena em uma instância da classe, armazena a instância da classe em uma propriedade estática e usa o contexto da instância da classe singleton para chamar o `updateStockPrice` método nos clientes que estão conectado a um Hub chamado `StockTickerHub`.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample58.cs?highlight=8,24)]

Se você precisar usar os contexto várias vezes em um objeto de longa duração, obter a referência de uma vez e salvá-lo em vez de fazê-lo novamente cada vez. Obtendo o contexto de uma vez, você garante que o SignalR envia mensagens para os clientes na mesma sequência em que seus métodos de Hub tornar cliente invocações de método. Para obter um tutorial que mostra como usar o contexto do SignalR para um Hub, consulte [transmissão de servidor com SignalR do ASP.NET](index.md).

<a id="callingclientsoutsidehub"></a>

### <a name="calling-client-methods"></a>Chamar métodos de cliente

Você pode especificar quais clientes receberão o RPC, mas você tem menos opções do que quando você chama a partir de uma classe de Hub. O motivo para isso é que o contexto não está associado uma chamada específica de um cliente, portanto, todos os métodos que exigem o conhecimento da ID da conexão atual, como `Clients.Others`, ou `Clients.Caller`, ou `Clients.OthersInGroup`, não estão disponíveis. As seguintes opções estão disponíveis:

- Todos os clientes conectados.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample59.cs)]
- Um cliente específico identificado pela ID de conexão.

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample60.css)]
- Todos os clientes conectados, exceto clientes especificados, identificados pela ID de conexão.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample61.cs)]
- Todos os clientes em um grupo especificado de conectados.

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample62.css)]
- Clientes de todos os conectados em um grupo especificado, exceto clientes especificados, identificado pela ID de conexão.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample63.cs)]

Se você estiver chamando em sua classe não-Hub dos métodos na classe Hub, você pode passar a ID de conexão atual e usá-lo com `Clients.Client`, `Clients.AllExcept`, ou `Clients.Group` simular `Clients.Caller`, `Clients.Others`, ou `Clients.OthersInGroup`. No exemplo a seguir, o `MoveShapeHub` classe passa a ID de conexão para o `Broadcaster` classe, de modo que o `Broadcaster` classe pode simular `Clients.Others`.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample64.cs?highlight=12,36)]

<a id="managinggroupsoutsidehub"></a>

### <a name="managing-group-membership"></a>Gerenciar associação de grupo

Para gerenciar os grupos, você tem as mesmas opções como faria em uma classe de Hub.

- Adicionar um cliente a um grupo

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample65.cs)]
- Remover um cliente de um grupo

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample66.css)]

<a id="hubpipeline"></a>

## <a name="how-to-customize-the-hubs-pipeline"></a>Como personalizar o pipeline de Hubs

O SignalR permite injetar seu próprio código no pipeline do Hub. O exemplo a seguir mostra um módulo personalizado de pipeline de Hub que registra cada chamada de método de entrada recebida do cliente e saída chamada do método invocado no cliente:

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample67.cs)]

O código a seguir na *global. asax* arquivo registra o módulo a ser executados no pipeline de Hub:

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample68.cs?highlight=3)]

Há muitos métodos diferentes que podem ser substituídos. Para obter uma lista completa, consulte [HubPipelineModule métodos](https://msdn.microsoft.com/library/jj918633(v=vs.111).aspx).
