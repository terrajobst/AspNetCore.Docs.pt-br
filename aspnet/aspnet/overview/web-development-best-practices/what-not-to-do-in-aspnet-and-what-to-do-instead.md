---
uid: aspnet/overview/web-development-best-practices/what-not-to-do-in-aspnet-and-what-to-do-instead
title: O que não fazer no ASP.NET e o que fazer em vez disso | Microsoft Docs
author: Rick-Anderson
description: Este tópico descreve vários erros comuns que as pessoas fizer dentro de projetos web ASP.NET. Ele fornece recomendações para que você deve fazer para evitar esses comu...
ms.author: riande
ms.date: 01/28/2019
ms.assetid: c39b9965-545c-4b04-8f55-21be7f28a9e5
msc.legacyurl: /aspnet/overview/web-development-best-practices/what-not-to-do-in-aspnet-and-what-to-do-instead
msc.type: authoredcontent
ms.openlocfilehash: 512d2e2b39467635390fa175546f79d8c9f89f4a
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667704"
---
# <a name="what-not-to-do-in-aspnet-and-what-to-do-instead"></a>O que fazer e o que não fazer em ASP.NET

> Este tópico descreve vários erros comuns que as pessoas fizer dentro de projetos web ASP.NET. Ele fornece recomendações para que você deve fazer para evitar esses erros comuns. Ele se baseia em uma [apresentação](http://vimeo.com/68390507) pela **Damian Edwards** na Norwegian Developers Conference.


## <a name="disclaimer"></a>Aviso de isenção de responsabilidade

Este tópico não se destina como um guia completo para garantir que seu aplicativo é seguro e eficiente. Você ainda precisa seguir as práticas recomendadas para segurança e desempenho que não são descritas neste tópico. Ele apenas sugere como evitar erros comuns relacionados a processos e classes do .NET.

## <a name="overview"></a>Visão geral

Esse tópico contém as seguintes seções:

- [Conformidade com padrões](#standards)

    - [Adaptadores de controle](#adapters)
    - [Propriedades de estilo para controles](#styleprop)
    - [Página e retornos de chamada de controle](#callback)
    - [Detecção de recurso do navegador](#browsercap)
- [Segurança](#security)

    - [Validação de solicitação](#validation)
    - [Sessão e autenticação de formulários sem cookies](#cookieless)
    - [EnableViewStateMac](#viewstatemac)
    - [Nível de confiança médio](#medium)
    - [&lt;appSettings&gt;](#appsettings)
    - [UrlPathEncode](#urlpathencode)
- [Confiabilidade e desempenho](#performance)

    - [PreSendRequestHeaders e PreSendRequestContent](#presend)
    - [Eventos de página assíncrona com Web Forms](#asyncevents)
    - [Disparar e esquecer de trabalho](#fire)
    - [Corpo da entidade de solicitação](#requestentity)
    - [Response. Redirect e Response](#redirect)
    - [EnableViewState e ViewStateMode](#viewstatemode)
    - [SqlMembershipProvider](#sqlprovider)
    - [Solicitações de execução longa (> 110 segundos)](#long)

<a id="standards"></a>

## <a name="standards-compliance"></a>Conformidade com padrões

<a id="adapters"></a>

### <a name="control-adapters"></a>Adaptadores de controle

Recomendação: Parar de usar adaptadores de controle para renderização adaptável e, em vez disso, use consultas de mídia do CSS e HTML compatível com os padrões.

Adaptadores de controles foram introduzidas no .NET 2.0 para processar o código de apresentação que foi personalizado para ambientes e dispositivos diferentes. Agora, essa renderização adaptável pode ser feita com CSS e HTML. Você deve parar de usar adaptadores de controle e converter todos os adaptadores existentes em CSS e HTML.

Para obter mais informações, consulte [consultas de mídia](http://www.w3.org/TR/css3-mediaqueries/) e [How To: Adicionar páginas móveis ao seu Web Forms do ASP.NET / aplicativo MVC](../../../whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application.md).

<a id="styleprop"></a>

### <a name="style-properties-on-controls"></a>Propriedades de estilo para controles

Recomendação: Parar de configurar os valores de estilo na marcação do controle e, em vez disso, defina valores de formatação em folhas de estilo CSS.

Controles de servidor Web com dezenas de propriedades que podem ser usadas para definir propriedades de estilo embutido. Por exemplo, a propriedade ForeColor define a cor do texto para um controle. Você pode fazer o mesmo efeito com mais eficiência por meio de folhas de estilo CSS. Folhas de estilo permitem que você centralize os valores de estilo e evite definir esses valores em todo o aplicativo.

O exemplo a seguir mostra uma classe CSS o texto de conjuntos para vermelho.

[!code-css[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample1.css)]

O exemplo a seguir mostra como aplicar dinamicamente a classe CSS.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample2.cs)]

<a id="callback"></a>

### <a name="page-and-control-callbacks"></a>Retornos de chamada de página e controle

Recomendação: Parar de usar retornos de chamada de página e controle e, em vez disso, use qualquer um dos seguintes: AJAX, UpdatePanel, MVC métodos de ação, API da Web ou SignalR.

Em versões anteriores do ASP.NET, métodos de retorno de chamada de página e controle habilitado atualizar parte da página da web sem atualizar uma página inteira. Agora você pode fazer atualizações parciais de página por meio [AJAX](../../../ajax/index.md), [UpdatePanel](https://msdn.microsoft.com/library/bb386454.aspx), [MVC](../../../mvc/index.md), [API da Web](../../../web-api/index.md) ou [SignalR](../../../signalr/index.md). Você deve interromper o roteamento e usar métodos de retorno de chamada porque podem causar problemas com URLs amigáveis. Por padrão, os controles não permitem que os métodos de retorno de chamada, mas se você habilitar esse recurso em um controle, você deve desabilitá-lo.

<a id="browsercap"></a>

### <a name="browser-capability-detection"></a>Detecção de recurso do navegador

Recomendação: Parar de usar a detecção de recurso estático do navegador e, em vez disso, usar a detecção de recurso dinâmico.

Em versões anteriores do ASP.NET, os recursos com suporte para cada navegador foram armazenados em um arquivo XML. Detectando suporte a recursos por meio de uma pesquisa estática não é a melhor abordagem. Agora, você pode detectar dinamicamente um navegador com suporte do recursos por meio de uma estrutura de detecção de recursos, como [Modernizr](http://modernizr.com/). Detecção de recurso determina o suporte a tentativa de usar um método ou propriedade e, em seguida, verificando para ver se o navegador produziu o resultado desejado. Por padrão, o Modernizr está incluído nos modelos de aplicativo Web.

<a id="security"></a>

## <a name="security"></a>Segurança

<a id="validation"></a>

### <a name="request-validation"></a>Validação de solicitação

Recomendação: Validar a entrada do usuário e codificar a saída dos usuários.

Validação de solicitação é um recurso do ASP.NET que inspeciona cada solicitação e para a solicitação, se uma ameaça percebida for encontrada. Não dependem de validação de solicitação para proteger seu aplicativo contra ataques de script entre sites. Em vez disso, valide todas as entradas de usuários e codificar a saída. Em alguns casos limitados, você pode usar expressões regulares para validar a entrada, mas em casos mais complicados, a que você deve validar entrada do usuário por meio de classes do .NET que determinam se o valor corresponder valores permitidos.

O exemplo a seguir mostra como usar um método estático na classe Uri para determinar se o Uri fornecido por um usuário é válido.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample3.cs)]

No entanto, para verificar suficientemente o Uri, você também deve verificar para certificar-se de que ele especifica `http` ou `https`. O exemplo a seguir usa os métodos de instância para verificar se o Uri é válido.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample4.cs)]

Antes de processar a entrada do usuário como HTML ou incluindo a entrada do usuário em uma consulta SQL, codificar os valores para garantir que o código mal-intencionado não está incluído.

É possível que o HTML codificar o valor na marcação com o &lt;%: %&gt; sintaxe, conforme mostrado abaixo.

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample5.aspx?highlight=1)]

Ou, na sintaxe do Razor, é possível que o HTML codificar com @, conforme mostrado abaixo.

[!code-cshtml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample6.cshtml?highlight=1)]

A exemplo a seguir mostra como HTML codificar um valor no code-behind.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample7.cs)]

Para codificar com segurança um valor para comandos SQL, use parâmetros de comando, como o [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx). <a id="cookieless"></a>

### <a name="cookieless-forms-authentication-and-session"></a>Sessão e autenticação de formulários sem cookies

Recomendação: Requer cookies.

Passar informações de autenticação na cadeia de caracteres de consulta não é seguro. Portanto, requerem cookies quando seu aplicativo inclui autenticação. Se seu cookie armazenar informações confidenciais, considere a exigência de SSL para o cookie.

O exemplo a seguir mostra como especificar no arquivo Web. config que a autenticação de formulários exige um cookie que é transmitido por SSL.

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample8.xml)]

<a id="viewstatemac"></a>

### <a name="enableviewstatemac"></a>EnableViewStateMac

Recomendação: Nunca defina como false.

Por padrão, EnbableViewStateMac é definido como true. Mesmo se seu aplicativo não estiver usando o estado de exibição, não defina EnableViewStateMac como false. Definir esse valor como false fará seu aplicativo vulnerável a XSS.

A partir do ASP.NET 4.5.2, o tempo de execução impõe **EnableViewStateMac = true**. Mesmo se você defini-lo como false, o tempo de execução ignora esse valor e prossegue com o valor definido como true. Para obter mais informações, consulte [ASP.NET 4.5.2 e EnableViewStateMac](https://blogs.msdn.com/b/webdev/archive/2014/05/07/asp-net-4-5-2-and-enableviewstatemac.aspx).

O exemplo a seguir mostra como definir EnableViewStateMac como true. Você não precisa realmente definir esse valor como true, pois ele é verdadeiro por padrão. No entanto, se você tiver definido isso como false em qualquer página em seu aplicativo, você deve corrigir esse valor imediatamente.

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample9.aspx)]

<a id="medium"></a>

### <a name="medium-trust"></a>Nível de confiança médio

Recomendação: Não dependem de confiança médio (ou qualquer outro nível de confiança) como um limite de segurança.

Confiança parcial não proteger adequadamente seu aplicativo e não deve ser usada. Em vez disso, use a confiança total e isolar aplicativos não confiáveis em pools de aplicativos separados. Além disso, execute cada pool de aplicativos com uma identidade exclusiva. Para obter mais informações, consulte [ASP.NET de confiança parcial não garante o isolamento de aplicativo](https://support.microsoft.com/kb/2698981).

<a id="appsettings"></a>

### <a name="ltappsettingsgt"></a>&lt;appSettings&gt;

Recomendação: Não desabilite as configurações de segurança no &lt;appSettings&gt; elemento.

O elemento de appSettings contém muitos valores que são necessários para atualizações de segurança. Você não deve alterar ou desativar esses valores. Se você precisar desabilitar esses valores ao implantar uma atualização, imediatamente habilite novamente depois de concluir a implantação.

Para obter detalhes, consulte [elemento de appSettings do ASP.NET](https://msdn.microsoft.com/library/hh975440.aspx).

<a id="urlpathencode"></a>

### <a name="urlpathencode"></a>UrlPathEncode

Recomendação: Use [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx) em vez disso.

O método UrlPathEncode foi adicionado para o .NET Framework para resolver um problema de compatibilidade do navegador muito específico. Ele não codifica uma URL adequadamente e não proteja seu aplicativo de script entre sites. Você nunca deve usá-lo em seu aplicativo. Em vez disso, use [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx).

O exemplo a seguir mostra como passar uma URL codificada como um parâmetro de cadeia de caracteres de consulta para um controle de hiperlink.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample10.cs)]

<a id="performance"></a>

## <a name="reliability-and-performance"></a>Confiabilidade e desempenho

<a id="presend"></a>

### <a name="presendrequestheaders-and-presendrequestcontent"></a>PreSendRequestHeaders e PreSendRequestContent

Recomendação: Não use esses eventos com módulos gerenciados. Em vez disso, escreva um módulo nativo do IIS para executar a tarefa exigida. Ver [criação de módulos HTTP de código nativo](https://msdn.microsoft.com/library/ms693629.aspx).

Você pode usar o [PreSendRequestHeaders](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestheaders.aspx) e [PreSendRequestContent](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestcontent.aspx) eventos com os módulos IIS nativos.
> [!WARNING]
> Não use `PreSendRequestHeaders` e `PreSendRequestContent` com os módulos gerenciados que implementam `IHttpModule`. Definir essas propriedades pode causar problemas com solicitações assíncronas. A combinação de aplicativo solicitado ARR (roteamento) e o WebSocket pode levar a exceções de violação de acesso que podem fazer com que w3wp falhe. Por exemplo, iiscore! W3_CONTEXT_BASE::GetIsLastNotification + 68 no iiscore.dll causou uma exceção de violação de acesso (0xC0000005).

<a id="asyncevents"></a>

### <a name="asynchronous-page-events-with-web-forms"></a>Eventos de página assíncrona com web forms

Recomendação: No Web Forms, evite escrever async void métodos para eventos de ciclo de vida da página e, em vez disso, use [RegisterAsyncTask](https://msdn.microsoft.com/library/system.web.ui.page.registerasynctask.aspx) para código assíncrono.

Quando você marca um evento de página com **async** e **void**, não é possível determinar quando o código assíncrono foi concluído. Em vez disso, use RegisterAsyncTask para executar o código assíncrono de uma maneira que permite que você controle sua conclusão.

A exemplo a seguir mostra um botão Clique manipulador que contém o código assíncrono. Este exemplo inclui ler um valor de cadeia de caracteres de forma assíncrona, que é fornecido apenas como um exemplo simplificado de uma tarefa assíncrona e não como uma prática recomendada.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample11.cs)]

Se você estiver usando tarefas assíncronas, defina a estrutura de destino do tempo de execução de Http para 4.5 (ou posterior) no arquivo Web. config. Definir a estrutura de destino para 4.5 ativa no novo contexto de sincronização que foi adicionado no .NET 4.5. Esse valor é definido por padrão em novos projetos no Visual Studio, mas é a não ser definida se você estiver trabalhando com um projeto existente.

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample12.xml)]

<a id="fire"></a>

### <a name="fire-and-forget-work"></a>Disparar e esquecer de trabalho

Recomendação: Ao lidar com uma solicitação dentro do ASP.NET, evite iniciar o trabalho de disparar e esquecer (tal chamando o método ThreadPool. QueueUserWorkItem ou criando um temporizador que chama repetidamente um delegado).

Se seu aplicativo tiver trabalho disparar e esquecer que é executado dentro do ASP.NET, seu aplicativo pode ficar fora de sincronizado. A qualquer momento, o domínio de aplicativo pode ser destruído que significa que seu processo contínuo não pode corresponder o estado atual do aplicativo.

Você deve mover esse tipo de trabalho fora do ASP.NET. Você pode usar os trabalhos da Web, serviço do Windows ou uma função de trabalho no Azure para executar um trabalho em andamento e executar esse código de outro processo.

Se for preciso executar este trabalho dentro do ASP.NET, você pode adicionar o pacote do Nuget chamado [WebBackgrounder](http://www.nuget.org/packages/webbackgrounder) para executar o código.

<a id="requestentity"></a>

### <a name="request-entity-body"></a>Corpo da entidade de solicitação

Recomendação: Evite a leitura Request. Form ou Request. InputStream, antes do manipulador de evento de execução.

O mais antigo que você deve ler de Request. Form ou Request. InputStream é durante o manipulador execute eventos. No MVC, o controlador é o manipulador e evento de execução é quando o método de ação é executada. Nos Web Forms, a página é o manipulador e evento de execução é quando o evento Page.Init é acionado. Se você ler o corpo da entidade de solicitação anterior ao evento de execução, você interfere com o processamento da solicitação.

Se você precisar ler o corpo da entidade de solicitação antes do evento de execução, use [Request.GetBufferlessInputStream](https://msdn.microsoft.com/library/ff406798.aspx) ou [Request.GetBufferedInputStream](https://msdn.microsoft.com/library/system.web.httprequest.getbufferedinputstream.aspx). Quando você usa GetBufferlessInputStream, obtenha o fluxo bruto da solicitação e assuma a responsabilidade por processar a solicitação inteira. Depois de chamar GetBufferlessInputStream, Request. Form e Request. InputStream não estão disponíveis porque eles não foram populados pelo ASP.NET. Quando você usa GetBufferedInputStream, você obtém uma cópia do fluxo da solicitação. Request. Form e Request. InputStream ainda estarão disponíveis posteriormente na solicitação porque o ASP.NET preenche a outra cópia.

<a id="redirect"></a>

### <a name="responseredirect-and-responseend"></a>Response. Redirect e Response

Recomendação: Esteja ciente das diferenças em como o thread é manipulado depois de chamar [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx).

O [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx) método chama o método Response. Em um processo síncrono, chamar Request.Redirect faz com que o thread atual anular imediatamente. No entanto, em um processo assíncrono, chamar Response. Redirect não anular o thread atual, portanto, a execução de código continua para a solicitação. Em um processo assíncrono, você deve retornar a tarefa do método para interromper a execução de código.

Em um projeto do MVC, você não deve chamar Response. Redirect. Em vez disso, retorne um RedirectResult.

<a id="viewstatemode"></a>

### <a name="enableviewstate-and-viewstatemode"></a>EnableViewState e ViewStateMode

Recomendação: Use ViewStateMode, em vez de EnableViewState, para fornecer controle granular sobre quais controles usam o estado de exibição.

Quando você define EnableViewState como falso na diretiva Page, o estado de exibição está desabilitado para todos os controles dentro da página e não pode ser habilitado. Se você quiser habilitar o estado de exibição para apenas determinados controles em sua página, defina ViewStateMode como desabilitado para a página.

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample13.aspx)]

Em seguida, defina ViewStateMode para habilitado nos controles que realmente precisam de estado de exibição.

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample14.aspx)]

Habilitando o estado de exibição para os controles que precisam dele, você pode reduzir o tamanho do estado de exibição para suas páginas da web.

<a id="sqlprovider"></a>

### <a name="sqlmembershipprovider"></a>SqlMembershipProvider

Recomendação: Use provedores universais.

Em modelos de projeto atual, SqlMembershipProvider foi substituído por [ASP.NET Universal Providers](http://www.nuget.org/packages/Microsoft.AspNet.Providers), que está disponível como um pacote do NuGet. Se você estiver usando SqlMembershipProvider em um projeto que foi criado com uma versão anterior dos modelos, você deve alternar para provedores universais. Os provedores universais funcionam com todos os bancos de dados que são suportados pelo Entity Framework.

Para obter mais informações, consulte [apresentando o ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx).

<a id="long"></a>

### <a name="long-running-requests-110-seconds"></a>Solicitações de execução longa (> 110 segundos)

Recomendação: Use [WebSockets](https://msdn.microsoft.com/library/system.net.websockets.websocket.aspx) ou [SignalR](../../../signalr/index.md) para clientes conectados e use operações de e/s assíncronas.

Solicitações de execução longa podem causar resultados imprevisíveis e baixo desempenho em seu aplicativo web. A configuração de tempo limite padrão para uma solicitação é de 110 segundos. Se você estiver usando o estado de sessão com uma solicitação de longa execução, o ASP.NET liberará o bloqueio no objeto de sessão depois de 110 segundos. No entanto, seu aplicativo pode ser no meio de uma operação no objeto de sessão quando o bloqueio seja liberado, e a operação pode não ser concluída com êxito. Se uma segunda solicitação do usuário for bloqueada enquanto a primeira solicitação está em execução, a segunda solicitação pode acessar o objeto de sessão em um estado inconsistente.

Se seu aplicativo inclui operações de e/s de bloqueio (ou síncronas), o aplicativo será sem resposta.

Para melhorar o desempenho, use as operações de e/s assíncronas no .NET Framework. Além disso, use o SignalR ou WebSockets para conectar clientes ao servidor. Esses recursos são projetados para manipular solicitações de execução longa.
