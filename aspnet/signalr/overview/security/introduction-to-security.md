---
uid: signalr/overview/security/introduction-to-security
title: Introdução à segurança do SignalR | Microsoft Docs
author: bradygaster
description: Descreve os problemas de segurança, que você deve considerar ao desenvolver um aplicativo do SignalR.
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: ed562717-8591-4936-8e10-c7e63dcb570a
msc.legacyurl: /signalr/overview/security/introduction-to-security
msc.type: authoredcontent
ms.openlocfilehash: 311bfb4279b1c919fddcef2c0aed657083f9c34f
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837319"
---
<a name="introduction-to-signalr-security"></a>Introdução à segurança do SignalR
====================
by [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Este artigo descreve os problemas de segurança, que você deve considerar ao desenvolver um aplicativo do SignalR.
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


## <a name="overview"></a>Visão geral

Este documento contém as seguintes seções:

- [Conceitos de segurança do SignalR](#concepts)

    - [Autenticação e autorização](#authentication)
    - [Token de Conexão](#connectiontoken)
    - [Incluir novamente grupos ao reconectar](#rejoingroup)
- [Como o SignalR impede que a falsificação de solicitação entre sites](#csrf)
- [Recomendações de segurança do SignalR](#recommendations)

    - [Protocolo seguro de camadas de soquete (SSL)](#ssl)
    - [Não use grupos como um mecanismo de segurança](#groupsecurity)
    - [Manipulando a entrada de clientes com segurança](#input)
    - [Reconciliando uma alteração no status de usuário com uma conexão ativa](#reconcile)
    - [Arquivos de proxy JavaScript gerados automaticamente](#autogen)
    - [Exceções](#exceptions)

<a id="concepts"></a>

## <a name="signalr-security-concepts"></a>Conceitos de segurança do SignalR

<a id="authentication"></a>

### <a name="authentication-and-authorization"></a>Autenticação e autorização

O SignalR fornece recursos para autenticar usuários. Em vez disso, você integrar os recursos do SignalR a estrutura existente de autenticação para um aplicativo. Você autenticar os usuários, como você faria normalmente em seu aplicativo e trabalhar com os resultados da autenticação no seu SignalR código. Por exemplo, você pode autenticar os usuários com a autenticação de formulários do ASP.NET e, em seguida, no hub, impor que os usuários ou funções têm autorização para chamar um método. Em seu hub, você também pode passar informações de autenticação, como nome de usuário ou se um usuário pertence a uma função, para o cliente.

O SignalR fornece o [autorizar](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) atributo para especificar quais usuários têm acesso a um hub ou método. Você pode aplicar o atributo Authorize para um hub ou determinados métodos em um hub. Sem o atributo Authorize, todos os métodos públicos no hub estão disponíveis para um cliente que está conectado ao hub. Para obter mais informações sobre os hubs, consulte [autenticação e autorização para Hubs do SignalR](hub-authorization.md).

Aplicar o `Authorize` atributo hubs, mas as conexões não persistentes. Para impor regras de autorização ao usar um `PersistentConnection` você deve substituir o `AuthorizeRequest` método. Para obter mais informações sobre conexões persistentes, consulte [autenticação e autorização para conexões persistentes do SignalR](persistent-connection-authorization.md).

<a id="connectiontoken"></a>

### <a name="connection-token"></a>Token de Conexão

O SignalR minimiza o risco da execução de comandos mal-intencionados ao validar a identidade do remetente. Para cada solicitação, o cliente e servidor passam um token de conexão que contém a id de conexão e o nome de usuário para usuários autenticados. A id de conexão identifica exclusivamente todos os clientes conectados. O servidor gera aleatoriamente a id de conexão quando uma nova conexão é criada e persiste essa id durante a conexão. O mecanismo de autenticação para o aplicativo web fornece o nome de usuário. O SignalR usa uma assinatura digital e criptografia para proteger o token de conexão.

![](introduction-to-security/_static/image2.png)

Para cada solicitação, o servidor valida o conteúdo do token para garantir que a solicitação é proveniente do usuário especificado. O nome de usuário deve corresponder à id de conexão. Validando a id de conexão e o nome de usuário, o SignalR impede que um usuário mal-intencionado facilmente representando outro usuário. Se o servidor não é possível validar o token de conexão, a solicitação falhará.

![](introduction-to-security/_static/image4.png)

Porque a id de conexão é parte do processo de verificação, você não deve revelar a id de conexão de um usuário a outros usuários ou armazena o valor no cliente, como em um cookie.

#### <a name="connection-tokens-vs-other-token-types"></a>Tokens de Conexão versus outros tipos de token

Tokens de Conexão são sinalizadas ocasionalmente por ferramentas de segurança porque eles parecem estar tokens de sessão ou tokens de autenticação, que representa um risco de se exposto.

O token de conexão do SignalR não é um token de autenticação. Ele é usado para confirmar que o usuário que fez essa solicitação é o mesmo que criou a conexão. O token de conexão é necessário porque o SignalR do ASP.NET permite conexões para se mover entre servidores. O token associa a conexão com um determinado usuário, mas não declarar a identidade do usuário que faz a solicitação. Para uma solicitação de SignalR ser autenticados corretamente, ele deve ter algum token que afirma a identidade do usuário, como um cookie ou token de portador. No entanto, a conexão de token em si faz nenhuma declaração em que a solicitação foi feita pelo usuário, apenas que a ID de conexão contida no token está associada esse usuário.

Como o token de conexão não fornece nenhuma declaração de autenticação própria, ele não é considerado uma "sessão" ou "autenticação" token. Levando a conexão de um determinado usuário token e substituí-lo em uma solicitação que deve ser autenticado como um usuário diferente (ou uma solicitação não autenticada) falhará, pois a identidade do usuário da solicitação e a identidade armazenada no token não correspondam.

<a id="rejoingroup"></a>

### <a name="rejoining-groups-when-reconnecting"></a>Incluir novamente grupos ao reconectar

Por padrão, o aplicativo SignalR novamente atribuirá automaticamente um usuário aos grupos apropriados ao reconectar-se de uma interrupção temporária, como quando uma conexão será descartada e restabelecida antes que a conexão expire. Ao reconectar, o cliente passa um token de grupo que inclui a id de conexão e os grupos atribuídos. O token de grupo é digitalmente assinado e criptografado. O cliente retém a mesma id de conexão após uma reconexão. Portanto, a id de conexão passada do cliente reconectado deve corresponder a id de conexão anterior usada pelo cliente. Essa verificação impede que um usuário mal-intencionado passar as solicitações para ingressar em grupos de não autorizados ao reconectar.

No entanto, é importante observar que o token de grupo não expira. Se um usuário pertencia a um grupo no passado, mas foi banido do grupo, que o usuário pode ser capaz de simular um token de grupo que inclui o grupo proibido. Se você precisar gerenciar com segurança os usuários que pertencem a quais grupos, você precisa armazenar os dados no servidor, como em um banco de dados. Em seguida, adicione lógica ao seu aplicativo que verifica no servidor, se um usuário pertence a um grupo. Para obter um exemplo de verificação de associação de grupo, consulte [trabalhando com grupos de](../guide-to-the-api/working-with-groups.md).

Automaticamente incluir novamente grupos se aplica somente quando uma conexão é reconectada, após uma interrupção temporária. Se um usuário se desconecta navegar para fora do aplicativo ou o aplicativo ser reiniciado, o aplicativo deve tratar como adicionar esse usuário a grupos corretos. Para obter mais informações, consulte [trabalhando com grupos de](../guide-to-the-api/working-with-groups.md).

<a id="csrf"></a>

## <a name="how-signalr-prevents-cross-site-request-forgery"></a>Como o SignalR impede que a falsificação de solicitação entre sites

Falsificação de solicitação entre sites (CSRF) é um ataque em que um site mal-intencionado envia uma solicitação para um site vulnerável no qual o usuário fez logon. O SignalR impede CSRF, tornando extremamente improvável de um site mal-intencionado para criar uma solicitação válida para o seu aplicativo do SignalR.

### <a name="description-of-csrf-attack"></a>Descrição de ataque CSRF

Aqui está um exemplo de um ataque CSRF:

1. Um usuário faz logon em www.example.com, usando a autenticação de formulários.
2. O servidor autentica o usuário. A resposta do servidor inclui um cookie de autenticação.
3. Sem fazer logoff, o usuário visitar um site mal-intencionado. Este site mal-intencionado contém o formulário HTML a seguir:

    [!code-html[Main](introduction-to-security/samples/sample1.html)]

   Observe que a ação de formulário faz a postagem para o site vulnerável, não para o site mal-intencionado. Essa é a parte de "site cruzado" de CSRF.
4. O usuário clica no botão Enviar. O navegador inclui o cookie de autenticação com a solicitação.
5. A solicitação é executado no servidor de example.com com o contexto de autenticação do usuário e pode fazer qualquer coisa que um usuário autenticado tem permissão para fazer.

Embora este exemplo requer que o usuário clicar no botão do formulário, a página mal-intencionada pode apenas executar facilmente um script que envia uma solicitação AJAX ao seu aplicativo do SignalR. Além disso, usando o SSL não impede que um ataque CSRF, porque o site mal-intencionado pode enviar uma solicitação de "https://".

Normalmente, ataques de CSRF são possíveis em relação a sites da web que usam cookies para autenticação, porque os navegadores enviam a todos os cookies relevantes para o site de destino. No entanto, ataques de CSRF não são limitados a exploração de cookies. Por exemplo, a autenticação básica e Digest também são vulneráveis. Depois que um usuário faz logon com a autenticação básica ou Digest, o navegador automaticamente envia as credenciais até que a sessão termina.

### <a name="csrf-mitigations-taken-by-signalr"></a>Atenuações de CSRF tomadas pelo SignalR

O SignalR realiza as seguintes etapas para impedir que um site mal-intencionado criando solicitações válidas para seu aplicativo. O SignalR usa estas etapas por padrão, você não precisa realizar nenhuma ação em seu código.

- **Desabilitar solicitações de domínio cruzado** SignalR desabilita as solicitações entre domínios para impedir que usuários chamando um ponto de extremidade do SignalR de um domínio externo. O SignalR considera todas as solicitações de um domínio externo para ser inválida e bloqueia a solicitação. É recomendável que você mantenha esse comportamento padrão; Caso contrário, um site mal-intencionado pode enganar os usuários para enviar comandos ao seu site. Se você precisar usar solicitações de domínio cruzado, consulte [como estabelecer uma conexão entre domínios](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain) .
- **Passe o token de conexão na cadeia de caracteres de consulta, não o cookie** SignalR passa o token de conexão como um valor de cadeia de caracteres de consulta, em vez de como um cookie. É seguro armazenar o token de conexão em um cookie, porque o navegador inadvertidamente pode encaminhar o token de conexão quando o código mal-intencionado é encontrado. Além disso, passando o token de conexão na cadeia de caracteres de consulta impede que o token de conexão persistir além da conexão atual. Portanto, um usuário mal-intencionado não pode fazer uma solicitação de credenciais de autenticação de outro usuário.
- **Verifique se o token de conexão** conforme descrito em de [token de Conexão](#connectiontoken) seção, o servidor sabe qual id de conexão é associado a cada usuário autenticado. O servidor não processar qualquer solicitação de uma id de conexão que não coincide com o nome de usuário. É improvável que um usuário mal-intencionado poderia imaginar uma solicitação válida porque o usuário mal-intencionado precisaria saber o nome de usuário e a id de conexão gerada aleatoriamente atual. Essa id de conexão se torna inválido, assim que a conexão será encerrada. Os usuários anônimos não devem ter acesso a informações confidenciais.

<a id="recommendations"></a>

## <a name="signalr-security-recommendations"></a>Recomendações de segurança do SignalR

<a id="ssl"></a>

### <a name="secure-socket-layers-ssl-protocol"></a>Protocolo seguro de camadas de soquete (SSL)

O protocolo SSL usa criptografia para proteger o transporte de dados entre um cliente e servidor. Se seu aplicativo SignalR transmite informações sigilosas entre o cliente e servidor, use o SSL para o transporte. Para obter mais informações sobre como configurar o SSL, consulte [como configurar o SSL no IIS 7](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis).

<a id="groupsecurity"></a>

### <a name="do-not-use-groups-as-a-security-mechanism"></a>Não use grupos como um mecanismo de segurança

Grupos são uma maneira conveniente de coleta de usuários relacionados, mas eles não são um mecanismo seguro para limitar o acesso a informações confidenciais. Isso é especialmente verdadeiro quando os usuários podem automaticamente incluir grupos novamente durante uma reconexão. Em vez disso, considere a adição de usuários com privilégios para uma função e limitando o acesso a um método de hub para somente os membros dessa função. Para obter um exemplo de restringir o acesso com base em uma função, consulte [autenticação e autorização para Hubs do SignalR](hub-authorization.md). Para obter um exemplo de verificação de acesso do usuário aos grupos ao reconectar, consulte [trabalhando com grupos de](../guide-to-the-api/working-with-groups.md).

<a id="input"></a>

### <a name="safely-handling-input-from-clients"></a>Manipulando a entrada de clientes com segurança

Para garantir que um usuário mal-intencionado não enviar script para outros usuários, você deve codificar todas as entradas de clientes que se destina para difusão de outros clientes. Você deve codificar mensagens sobre os clientes de recebimento em vez do servidor, como seu aplicativo SignalR pode ter muitos tipos diferentes de clientes. Portanto, a codificação HTML funciona para um cliente da web, mas não para outros tipos de clientes. Por exemplo, um método de cliente da web para exibir uma mensagem de bate-papo lidaria com segurança com o nome de usuário e a mensagem chamando o `html()` função.

[!code-html[Main](introduction-to-security/samples/sample2.html?highlight=3-4)]

<a id="reconcile"></a>

### <a name="reconciling-a-change-in-user-status-with-an-active-connection"></a>Reconciliando uma alteração no status de usuário com uma conexão ativa

Se o status de autenticação do usuário for alterada enquanto existe uma conexão ativa, o usuário receberá um erro que afirma, "não é possível alterar a identidade do usuário durante uma conexão SignalR Active Directory." Nesse caso, seu aplicativo deve se conectar novamente ao servidor para certificar-se de que a id de conexão e o nome de usuário são coordenados. Por exemplo, se seu aplicativo permite que o usuário faça logoff enquanto existe uma conexão ativa, o nome de usuário para a conexão não corresponderá ao nome que é passado para a próxima solicitação. Você deseja interromper a conexão antes do usuário faz logoff e, em seguida, reiniciá-lo.

No entanto, é importante observar que a maioria dos aplicativos não precisará parar e iniciar a conexão manualmente. Se seu aplicativo redireciona os usuários para uma página separada depois de fazer logon, como o comportamento padrão em um aplicativo de Web Forms ou MVC, ou atualiza a página atual após o logoff, a conexão ativa será automaticamente interrompida e não exigem nenhuma ação adicional.

O exemplo a seguir mostra como parar e iniciar uma conexão quando o status do usuário foi alterado.

[!code-html[Main](introduction-to-security/samples/sample3.html)]

Ou, o status de autenticação do usuário poderá ser alterado se o seu site usa a expiração deslizante com autenticação de formulários, e não há nenhuma atividade para manter o cookie de autenticação válido. Nesse caso, o usuário será desconectado e o nome de usuário não corresponderá ao nome de usuário no token de conexão. Você pode corrigir esse problema adicionando um script que periodicamente solicita um recurso no servidor web para manter o cookie de autenticação válido. O exemplo a seguir mostra como solicitar um recurso a cada 30 minutos.

[!code-javascript[Main](introduction-to-security/samples/sample4.js)]

<a id="autogen"></a>

### <a name="automatically-generated-javascript-proxy-files"></a>Arquivos de proxy JavaScript gerados automaticamente

Se você não deseja incluir todos os hubs e métodos no arquivo de proxy JavaScript para cada usuário, você pode desabilitar a geração automática do arquivo. Você pode escolher essa opção se você tiver vários hubs e métodos, mas não quiser que todos os usuários a serem consideradas todos os métodos. Desativar a geração automática, definindo **EnableJavaScriptProxies** à **falso**.

[!code-csharp[Main](introduction-to-security/samples/sample5.cs)]

Para obter mais informações sobre os arquivos de proxy do JavaScript, consulte [proxy gerado e o que ele faz para você](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy). <a id="exceptions"></a>

### <a name="exceptions"></a>Exceções

Você deve evitar passar objetos de exceção para os clientes porque os objetos podem expor informações confidenciais para os clientes. Em vez disso, chame um método no cliente que exibe a mensagem de erro relevantes.

[!code-csharp[Main](introduction-to-security/samples/sample6.cs)]
