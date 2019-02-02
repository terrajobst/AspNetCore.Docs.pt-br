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
# <a name="what-not-to-do-in-aspnet-and-what-to-do-instead"></a><span data-ttu-id="f5bac-104">O que fazer e o que não fazer em ASP.NET</span><span class="sxs-lookup"><span data-stu-id="f5bac-104">What not to do in ASP.NET, and what to do instead</span></span>

> <span data-ttu-id="f5bac-105">Este tópico descreve vários erros comuns que as pessoas fizer dentro de projetos web ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="f5bac-105">This topic describes several common mistakes people make within ASP.NET web projects.</span></span> <span data-ttu-id="f5bac-106">Ele fornece recomendações para que você deve fazer para evitar esses erros comuns.</span><span class="sxs-lookup"><span data-stu-id="f5bac-106">It provides recommendations for what you should do to avoid these common mistakes.</span></span> <span data-ttu-id="f5bac-107">Ele se baseia em uma [apresentação](http://vimeo.com/68390507) pela **Damian Edwards** na Norwegian Developers Conference.</span><span class="sxs-lookup"><span data-stu-id="f5bac-107">It is based on a [presentation](http://vimeo.com/68390507) by **Damian Edwards** at Norwegian Developers Conference.</span></span>


## <a name="disclaimer"></a><span data-ttu-id="f5bac-108">Aviso de isenção de responsabilidade</span><span class="sxs-lookup"><span data-stu-id="f5bac-108">Disclaimer</span></span>

<span data-ttu-id="f5bac-109">Este tópico não se destina como um guia completo para garantir que seu aplicativo é seguro e eficiente.</span><span class="sxs-lookup"><span data-stu-id="f5bac-109">This topic is not intended as a complete guide to ensure your application is secure and efficient.</span></span> <span data-ttu-id="f5bac-110">Você ainda precisa seguir as práticas recomendadas para segurança e desempenho que não são descritas neste tópico.</span><span class="sxs-lookup"><span data-stu-id="f5bac-110">You still need to follow best practices for security and performance that are not outlined in this topic.</span></span> <span data-ttu-id="f5bac-111">Ele apenas sugere como evitar erros comuns relacionados a processos e classes do .NET.</span><span class="sxs-lookup"><span data-stu-id="f5bac-111">It only suggests how to avoid common mistakes related to .NET classes and processes.</span></span>

## <a name="overview"></a><span data-ttu-id="f5bac-112">Visão geral</span><span class="sxs-lookup"><span data-stu-id="f5bac-112">Overview</span></span>

<span data-ttu-id="f5bac-113">Esse tópico contém as seguintes seções:</span><span class="sxs-lookup"><span data-stu-id="f5bac-113">This topic contains the following sections:</span></span>

- [<span data-ttu-id="f5bac-114">Conformidade com padrões</span><span class="sxs-lookup"><span data-stu-id="f5bac-114">Standards Compliance</span></span>](#standards)

    - [<span data-ttu-id="f5bac-115">Adaptadores de controle</span><span class="sxs-lookup"><span data-stu-id="f5bac-115">Control Adapters</span></span>](#adapters)
    - [<span data-ttu-id="f5bac-116">Propriedades de estilo para controles</span><span class="sxs-lookup"><span data-stu-id="f5bac-116">Style Properties on Controls</span></span>](#styleprop)
    - [<span data-ttu-id="f5bac-117">Página e retornos de chamada de controle</span><span class="sxs-lookup"><span data-stu-id="f5bac-117">Page and Control Callbacks</span></span>](#callback)
    - [<span data-ttu-id="f5bac-118">Detecção de recurso do navegador</span><span class="sxs-lookup"><span data-stu-id="f5bac-118">Browser Capability Detection</span></span>](#browsercap)
- [<span data-ttu-id="f5bac-119">Segurança</span><span class="sxs-lookup"><span data-stu-id="f5bac-119">Security</span></span>](#security)

    - [<span data-ttu-id="f5bac-120">Validação de solicitação</span><span class="sxs-lookup"><span data-stu-id="f5bac-120">Request Validation</span></span>](#validation)
    - [<span data-ttu-id="f5bac-121">Sessão e autenticação de formulários sem cookies</span><span class="sxs-lookup"><span data-stu-id="f5bac-121">Cookieless Forms Authentication and Session</span></span>](#cookieless)
    - [<span data-ttu-id="f5bac-122">EnableViewStateMac</span><span class="sxs-lookup"><span data-stu-id="f5bac-122">EnableViewStateMac</span></span>](#viewstatemac)
    - [<span data-ttu-id="f5bac-123">Nível de confiança médio</span><span class="sxs-lookup"><span data-stu-id="f5bac-123">Medium Trust</span></span>](#medium)
    - [<span data-ttu-id="f5bac-124">&lt;appSettings&gt;</span><span class="sxs-lookup"><span data-stu-id="f5bac-124">&lt;appSettings&gt;</span></span>](#appsettings)
    - [<span data-ttu-id="f5bac-125">UrlPathEncode</span><span class="sxs-lookup"><span data-stu-id="f5bac-125">UrlPathEncode</span></span>](#urlpathencode)
- [<span data-ttu-id="f5bac-126">Confiabilidade e desempenho</span><span class="sxs-lookup"><span data-stu-id="f5bac-126">Reliability and Performance</span></span>](#performance)

    - [<span data-ttu-id="f5bac-127">PreSendRequestHeaders e PreSendRequestContent</span><span class="sxs-lookup"><span data-stu-id="f5bac-127">PreSendRequestHeaders and PreSendRequestContent</span></span>](#presend)
    - [<span data-ttu-id="f5bac-128">Eventos de página assíncrona com Web Forms</span><span class="sxs-lookup"><span data-stu-id="f5bac-128">Asynchronous Page Events with Web Forms</span></span>](#asyncevents)
    - [<span data-ttu-id="f5bac-129">Disparar e esquecer de trabalho</span><span class="sxs-lookup"><span data-stu-id="f5bac-129">Fire-and-Forget Work</span></span>](#fire)
    - [<span data-ttu-id="f5bac-130">Corpo da entidade de solicitação</span><span class="sxs-lookup"><span data-stu-id="f5bac-130">Request Entity Body</span></span>](#requestentity)
    - [<span data-ttu-id="f5bac-131">Response. Redirect e Response</span><span class="sxs-lookup"><span data-stu-id="f5bac-131">Response.Redirect and Response.End</span></span>](#redirect)
    - [<span data-ttu-id="f5bac-132">EnableViewState e ViewStateMode</span><span class="sxs-lookup"><span data-stu-id="f5bac-132">EnableViewState and ViewStateMode</span></span>](#viewstatemode)
    - [<span data-ttu-id="f5bac-133">SqlMembershipProvider</span><span class="sxs-lookup"><span data-stu-id="f5bac-133">SqlMembershipProvider</span></span>](#sqlprovider)
    - [<span data-ttu-id="f5bac-134">Solicitações de execução longa (> 110 segundos)</span><span class="sxs-lookup"><span data-stu-id="f5bac-134">Long Running Requests (>110 seconds)</span></span>](#long)

<a id="standards"></a>

## <a name="standards-compliance"></a><span data-ttu-id="f5bac-135">Conformidade com padrões</span><span class="sxs-lookup"><span data-stu-id="f5bac-135">Standards Compliance</span></span>

<a id="adapters"></a>

### <a name="control-adapters"></a><span data-ttu-id="f5bac-136">Adaptadores de controle</span><span class="sxs-lookup"><span data-stu-id="f5bac-136">Control adapters</span></span>

<span data-ttu-id="f5bac-137">Recomendação: Parar de usar adaptadores de controle para renderização adaptável e, em vez disso, use consultas de mídia do CSS e HTML compatível com os padrões.</span><span class="sxs-lookup"><span data-stu-id="f5bac-137">Recommendation: Stop using control adapters for adaptive rendering, and instead use CSS media queries and standards-compliant HTML.</span></span>

<span data-ttu-id="f5bac-138">Adaptadores de controles foram introduzidas no .NET 2.0 para processar o código de apresentação que foi personalizado para ambientes e dispositivos diferentes.</span><span class="sxs-lookup"><span data-stu-id="f5bac-138">Controls Adapters were introduced in .NET 2.0 to render presentation code that was customized for different devices and environments.</span></span> <span data-ttu-id="f5bac-139">Agora, essa renderização adaptável pode ser feita com CSS e HTML.</span><span class="sxs-lookup"><span data-stu-id="f5bac-139">Now, this adaptive rendering can be accomplished with CSS and HTML.</span></span> <span data-ttu-id="f5bac-140">Você deve parar de usar adaptadores de controle e converter todos os adaptadores existentes em CSS e HTML.</span><span class="sxs-lookup"><span data-stu-id="f5bac-140">You should stop using Control Adapters and convert any existing adapters to CSS and HTML.</span></span>

<span data-ttu-id="f5bac-141">Para obter mais informações, consulte [consultas de mídia](http://www.w3.org/TR/css3-mediaqueries/) e [How To: Adicionar páginas móveis ao seu Web Forms do ASP.NET / aplicativo MVC](../../../whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application.md).</span><span class="sxs-lookup"><span data-stu-id="f5bac-141">For more information, see [Media Queries](http://www.w3.org/TR/css3-mediaqueries/) and [How To: Add Mobile Pages to Your ASP.NET Web Forms / MVC Application](../../../whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application.md).</span></span>

<a id="styleprop"></a>

### <a name="style-properties-on-controls"></a><span data-ttu-id="f5bac-142">Propriedades de estilo para controles</span><span class="sxs-lookup"><span data-stu-id="f5bac-142">Style properties on controls</span></span>

<span data-ttu-id="f5bac-143">Recomendação: Parar de configurar os valores de estilo na marcação do controle e, em vez disso, defina valores de formatação em folhas de estilo CSS.</span><span class="sxs-lookup"><span data-stu-id="f5bac-143">Recommendation: Stop setting style values in the control markup, and instead set formatting values in CSS stylesheets.</span></span>

<span data-ttu-id="f5bac-144">Controles de servidor Web com dezenas de propriedades que podem ser usadas para definir propriedades de estilo embutido.</span><span class="sxs-lookup"><span data-stu-id="f5bac-144">Web server controls contain dozens of properties which can be used to set in-line style properties.</span></span> <span data-ttu-id="f5bac-145">Por exemplo, a propriedade ForeColor define a cor do texto para um controle.</span><span class="sxs-lookup"><span data-stu-id="f5bac-145">For example, the ForeColor property sets the color of the text for a control.</span></span> <span data-ttu-id="f5bac-146">Você pode fazer o mesmo efeito com mais eficiência por meio de folhas de estilo CSS.</span><span class="sxs-lookup"><span data-stu-id="f5bac-146">You can accomplish this same effect more efficiently through CSS stylesheets.</span></span> <span data-ttu-id="f5bac-147">Folhas de estilo permitem que você centralize os valores de estilo e evite definir esses valores em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f5bac-147">Stylesheets enable you to centralize style values and avoid setting these values throughout your application.</span></span>

<span data-ttu-id="f5bac-148">O exemplo a seguir mostra uma classe CSS o texto de conjuntos para vermelho.</span><span class="sxs-lookup"><span data-stu-id="f5bac-148">The following example shows a CSS class the sets text to red.</span></span>

[!code-css[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample1.css)]

<span data-ttu-id="f5bac-149">O exemplo a seguir mostra como aplicar dinamicamente a classe CSS.</span><span class="sxs-lookup"><span data-stu-id="f5bac-149">The next example shows how to dynamically apply the CSS class.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample2.cs)]

<a id="callback"></a>

### <a name="page-and-control-callbacks"></a><span data-ttu-id="f5bac-150">Retornos de chamada de página e controle</span><span class="sxs-lookup"><span data-stu-id="f5bac-150">Page and control callbacks</span></span>

<span data-ttu-id="f5bac-151">Recomendação: Parar de usar retornos de chamada de página e controle e, em vez disso, use qualquer um dos seguintes: AJAX, UpdatePanel, MVC métodos de ação, API da Web ou SignalR.</span><span class="sxs-lookup"><span data-stu-id="f5bac-151">Recommendation: Stop using page and control callbacks, and instead use any of the following: AJAX, UpdatePanel, MVC action methods, Web API, or SignalR.</span></span>

<span data-ttu-id="f5bac-152">Em versões anteriores do ASP.NET, métodos de retorno de chamada de página e controle habilitado atualizar parte da página da web sem atualizar uma página inteira.</span><span class="sxs-lookup"><span data-stu-id="f5bac-152">In earlier versions of ASP.NET, Page and Control callback methods enabled you to update part of the web page without refreshing an entire page.</span></span> <span data-ttu-id="f5bac-153">Agora você pode fazer atualizações parciais de página por meio [AJAX](../../../ajax/index.md), [UpdatePanel](https://msdn.microsoft.com/library/bb386454.aspx), [MVC](../../../mvc/index.md), [API da Web](../../../web-api/index.md) ou [SignalR](../../../signalr/index.md).</span><span class="sxs-lookup"><span data-stu-id="f5bac-153">You can now accomplish partial-page updates through [AJAX](../../../ajax/index.md), [UpdatePanel](https://msdn.microsoft.com/library/bb386454.aspx), [MVC](../../../mvc/index.md), [Web API](../../../web-api/index.md) or [SignalR](../../../signalr/index.md).</span></span> <span data-ttu-id="f5bac-154">Você deve interromper o roteamento e usar métodos de retorno de chamada porque podem causar problemas com URLs amigáveis.</span><span class="sxs-lookup"><span data-stu-id="f5bac-154">You should stop using callback methods because they can cause issues with friendly URLs and routing.</span></span> <span data-ttu-id="f5bac-155">Por padrão, os controles não permitem que os métodos de retorno de chamada, mas se você habilitar esse recurso em um controle, você deve desabilitá-lo.</span><span class="sxs-lookup"><span data-stu-id="f5bac-155">By default, controls do not enable callback methods, but if you enabled this feature in a control, you should disable it.</span></span>

<a id="browsercap"></a>

### <a name="browser-capability-detection"></a><span data-ttu-id="f5bac-156">Detecção de recurso do navegador</span><span class="sxs-lookup"><span data-stu-id="f5bac-156">Browser capability detection</span></span>

<span data-ttu-id="f5bac-157">Recomendação: Parar de usar a detecção de recurso estático do navegador e, em vez disso, usar a detecção de recurso dinâmico.</span><span class="sxs-lookup"><span data-stu-id="f5bac-157">Recommendation: Stop using static browser capability detection, and instead use dynamic feature detection.</span></span>

<span data-ttu-id="f5bac-158">Em versões anteriores do ASP.NET, os recursos com suporte para cada navegador foram armazenados em um arquivo XML.</span><span class="sxs-lookup"><span data-stu-id="f5bac-158">In earlier versions of ASP.NET, the supported features for each browser were stored in an XML file.</span></span> <span data-ttu-id="f5bac-159">Detectando suporte a recursos por meio de uma pesquisa estática não é a melhor abordagem.</span><span class="sxs-lookup"><span data-stu-id="f5bac-159">Detecting feature support through a static lookup is not the best approach.</span></span> <span data-ttu-id="f5bac-160">Agora, você pode detectar dinamicamente um navegador com suporte do recursos por meio de uma estrutura de detecção de recursos, como [Modernizr](http://modernizr.com/).</span><span class="sxs-lookup"><span data-stu-id="f5bac-160">Now, you can dynamically detect a browser's supported features by using a feature detection framework, such as [Modernizr](http://modernizr.com/).</span></span> <span data-ttu-id="f5bac-161">Detecção de recurso determina o suporte a tentativa de usar um método ou propriedade e, em seguida, verificando para ver se o navegador produziu o resultado desejado.</span><span class="sxs-lookup"><span data-stu-id="f5bac-161">Feature detection determines support by attempting to use a method or property and then checking to see if the browser produced the desired result.</span></span> <span data-ttu-id="f5bac-162">Por padrão, o Modernizr está incluído nos modelos de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="f5bac-162">By default, Modernizr is included in the Web application templates.</span></span>

<a id="security"></a>

## <a name="security"></a><span data-ttu-id="f5bac-163">Segurança</span><span class="sxs-lookup"><span data-stu-id="f5bac-163">Security</span></span>

<a id="validation"></a>

### <a name="request-validation"></a><span data-ttu-id="f5bac-164">Validação de solicitação</span><span class="sxs-lookup"><span data-stu-id="f5bac-164">Request validation</span></span>

<span data-ttu-id="f5bac-165">Recomendação: Validar a entrada do usuário e codificar a saída dos usuários.</span><span class="sxs-lookup"><span data-stu-id="f5bac-165">Recommendation: Validate user input, and encode output from users.</span></span>

<span data-ttu-id="f5bac-166">Validação de solicitação é um recurso do ASP.NET que inspeciona cada solicitação e para a solicitação, se uma ameaça percebida for encontrada.</span><span class="sxs-lookup"><span data-stu-id="f5bac-166">Request validation is a feature of ASP.NET that inspects each request and stops the request if a perceived threat is found.</span></span> <span data-ttu-id="f5bac-167">Não dependem de validação de solicitação para proteger seu aplicativo contra ataques de script entre sites.</span><span class="sxs-lookup"><span data-stu-id="f5bac-167">Do not depend on request validation for securing your application against cross-site scripting attacks.</span></span> <span data-ttu-id="f5bac-168">Em vez disso, valide todas as entradas de usuários e codificar a saída.</span><span class="sxs-lookup"><span data-stu-id="f5bac-168">Instead, validate all input from users and encode the output.</span></span> <span data-ttu-id="f5bac-169">Em alguns casos limitados, você pode usar expressões regulares para validar a entrada, mas em casos mais complicados, a que você deve validar entrada do usuário por meio de classes do .NET que determinam se o valor corresponder valores permitidos.</span><span class="sxs-lookup"><span data-stu-id="f5bac-169">In some limited cases, you can use regular expressions to validate the input, but in more complicated cases you should validate user input by using .NET classes that determine if the value matches allowed values.</span></span>

<span data-ttu-id="f5bac-170">O exemplo a seguir mostra como usar um método estático na classe Uri para determinar se o Uri fornecido por um usuário é válido.</span><span class="sxs-lookup"><span data-stu-id="f5bac-170">The following example shows how to use a static method in the Uri class to determine whether the Uri provided by a user is valid.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample3.cs)]

<span data-ttu-id="f5bac-171">No entanto, para verificar suficientemente o Uri, você também deve verificar para certificar-se de que ele especifica `http` ou `https`.</span><span class="sxs-lookup"><span data-stu-id="f5bac-171">However, to sufficiently verify the Uri, you should also check to make sure it specifies `http` or `https`.</span></span> <span data-ttu-id="f5bac-172">O exemplo a seguir usa os métodos de instância para verificar se o Uri é válido.</span><span class="sxs-lookup"><span data-stu-id="f5bac-172">The following example uses instance methods to verify that the Uri is valid.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample4.cs)]

<span data-ttu-id="f5bac-173">Antes de processar a entrada do usuário como HTML ou incluindo a entrada do usuário em uma consulta SQL, codificar os valores para garantir que o código mal-intencionado não está incluído.</span><span class="sxs-lookup"><span data-stu-id="f5bac-173">Before rendering user input as HTML or including user input in a SQL query, encode the values to ensure malicious code is not included.</span></span>

<span data-ttu-id="f5bac-174">É possível que o HTML codificar o valor na marcação com o &lt;%: %&gt; sintaxe, conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="f5bac-174">You can HTML encode the value in markup with the &lt;%: %&gt; syntax, as shown below.</span></span>

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample5.aspx?highlight=1)]

<span data-ttu-id="f5bac-175">Ou, na sintaxe do Razor, é possível que o HTML codificar com @, conforme mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="f5bac-175">Or, in Razor syntax, you can HTML encode with @, as shown below.</span></span>

[!code-cshtml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample6.cshtml?highlight=1)]

<span data-ttu-id="f5bac-176">A exemplo a seguir mostra como HTML codificar um valor no code-behind.</span><span class="sxs-lookup"><span data-stu-id="f5bac-176">The next example shows how to HTML encode a value in code-behind.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample7.cs)]

<span data-ttu-id="f5bac-177">Para codificar com segurança um valor para comandos SQL, use parâmetros de comando, como o [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx).</span><span class="sxs-lookup"><span data-stu-id="f5bac-177">To safely encode a value for SQL commands, use command parameters such as the [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx).</span></span> <a id="cookieless"></a>

### <a name="cookieless-forms-authentication-and-session"></a><span data-ttu-id="f5bac-178">Sessão e autenticação de formulários sem cookies</span><span class="sxs-lookup"><span data-stu-id="f5bac-178">Cookieless forms authentication and session</span></span>

<span data-ttu-id="f5bac-179">Recomendação: Requer cookies.</span><span class="sxs-lookup"><span data-stu-id="f5bac-179">Recommendation: Require cookies.</span></span>

<span data-ttu-id="f5bac-180">Passar informações de autenticação na cadeia de caracteres de consulta não é seguro.</span><span class="sxs-lookup"><span data-stu-id="f5bac-180">Passing authentication information in the query string is not secure.</span></span> <span data-ttu-id="f5bac-181">Portanto, requerem cookies quando seu aplicativo inclui autenticação.</span><span class="sxs-lookup"><span data-stu-id="f5bac-181">Therefore, require cookies when your application includes authentication.</span></span> <span data-ttu-id="f5bac-182">Se seu cookie armazenar informações confidenciais, considere a exigência de SSL para o cookie.</span><span class="sxs-lookup"><span data-stu-id="f5bac-182">If your cookie stores sensitive information, consider requiring SSL for the cookie.</span></span>

<span data-ttu-id="f5bac-183">O exemplo a seguir mostra como especificar no arquivo Web. config que a autenticação de formulários exige um cookie que é transmitido por SSL.</span><span class="sxs-lookup"><span data-stu-id="f5bac-183">The following example shows how to specify in the Web.config file that Forms Authentication requires a cookie that is transmitted over SSL.</span></span>

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample8.xml)]

<a id="viewstatemac"></a>

### <a name="enableviewstatemac"></a><span data-ttu-id="f5bac-184">EnableViewStateMac</span><span class="sxs-lookup"><span data-stu-id="f5bac-184">EnableViewStateMac</span></span>

<span data-ttu-id="f5bac-185">Recomendação: Nunca defina como false.</span><span class="sxs-lookup"><span data-stu-id="f5bac-185">Recommendation: Never set to false.</span></span>

<span data-ttu-id="f5bac-186">Por padrão, EnbableViewStateMac é definido como true.</span><span class="sxs-lookup"><span data-stu-id="f5bac-186">By default, EnbableViewStateMac is set to true.</span></span> <span data-ttu-id="f5bac-187">Mesmo se seu aplicativo não estiver usando o estado de exibição, não defina EnableViewStateMac como false.</span><span class="sxs-lookup"><span data-stu-id="f5bac-187">Even if your application is not using view state, do not set EnableViewStateMac to false.</span></span> <span data-ttu-id="f5bac-188">Definir esse valor como false fará seu aplicativo vulnerável a XSS.</span><span class="sxs-lookup"><span data-stu-id="f5bac-188">Setting this value to false will make your application vulnerable to cross-site scripting.</span></span>

<span data-ttu-id="f5bac-189">A partir do ASP.NET 4.5.2, o tempo de execução impõe **EnableViewStateMac = true**.</span><span class="sxs-lookup"><span data-stu-id="f5bac-189">Starting with ASP.NET 4.5.2, the runtime enforces **EnableViewStateMac=true**.</span></span> <span data-ttu-id="f5bac-190">Mesmo se você defini-lo como false, o tempo de execução ignora esse valor e prossegue com o valor definido como true.</span><span class="sxs-lookup"><span data-stu-id="f5bac-190">Even if you set it to false, the runtime ignores this value and proceeds with the value set to true.</span></span> <span data-ttu-id="f5bac-191">Para obter mais informações, consulte [ASP.NET 4.5.2 e EnableViewStateMac](https://blogs.msdn.com/b/webdev/archive/2014/05/07/asp-net-4-5-2-and-enableviewstatemac.aspx).</span><span class="sxs-lookup"><span data-stu-id="f5bac-191">For more information, see [ASP.NET 4.5.2 and EnableViewStateMac](https://blogs.msdn.com/b/webdev/archive/2014/05/07/asp-net-4-5-2-and-enableviewstatemac.aspx).</span></span>

<span data-ttu-id="f5bac-192">O exemplo a seguir mostra como definir EnableViewStateMac como true.</span><span class="sxs-lookup"><span data-stu-id="f5bac-192">The following example shows how to set EnableViewStateMac to true.</span></span> <span data-ttu-id="f5bac-193">Você não precisa realmente definir esse valor como true, pois ele é verdadeiro por padrão.</span><span class="sxs-lookup"><span data-stu-id="f5bac-193">You do not need to actually set this value to true because it is true by default.</span></span> <span data-ttu-id="f5bac-194">No entanto, se você tiver definido isso como false em qualquer página em seu aplicativo, você deve corrigir esse valor imediatamente.</span><span class="sxs-lookup"><span data-stu-id="f5bac-194">However, if you have set it to false on any page in your application, you must immediately correct this value.</span></span>

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample9.aspx)]

<a id="medium"></a>

### <a name="medium-trust"></a><span data-ttu-id="f5bac-195">Nível de confiança médio</span><span class="sxs-lookup"><span data-stu-id="f5bac-195">Medium trust</span></span>

<span data-ttu-id="f5bac-196">Recomendação: Não dependem de confiança médio (ou qualquer outro nível de confiança) como um limite de segurança.</span><span class="sxs-lookup"><span data-stu-id="f5bac-196">Recommendation: Do not depend on Medium Trust (or any other trust level) as a security boundary.</span></span>

<span data-ttu-id="f5bac-197">Confiança parcial não proteger adequadamente seu aplicativo e não deve ser usada.</span><span class="sxs-lookup"><span data-stu-id="f5bac-197">Partial trust does not adequately protect your application and should not be used.</span></span> <span data-ttu-id="f5bac-198">Em vez disso, use a confiança total e isolar aplicativos não confiáveis em pools de aplicativos separados.</span><span class="sxs-lookup"><span data-stu-id="f5bac-198">Instead, use Full Trust, and isolate untrusted applications in separate application pools.</span></span> <span data-ttu-id="f5bac-199">Além disso, execute cada pool de aplicativos com uma identidade exclusiva.</span><span class="sxs-lookup"><span data-stu-id="f5bac-199">Also, run each application pool under a unique identity.</span></span> <span data-ttu-id="f5bac-200">Para obter mais informações, consulte [ASP.NET de confiança parcial não garante o isolamento de aplicativo](https://support.microsoft.com/kb/2698981).</span><span class="sxs-lookup"><span data-stu-id="f5bac-200">For more information, see [ASP.NET Partial Trust does not guarantee application isolation](https://support.microsoft.com/kb/2698981).</span></span>

<a id="appsettings"></a>

### <a name="ltappsettingsgt"></a><span data-ttu-id="f5bac-201">&lt;appSettings&gt;</span><span class="sxs-lookup"><span data-stu-id="f5bac-201">&lt;appSettings&gt;</span></span>

<span data-ttu-id="f5bac-202">Recomendação: Não desabilite as configurações de segurança no &lt;appSettings&gt; elemento.</span><span class="sxs-lookup"><span data-stu-id="f5bac-202">Recommendation: Do not disable security settings in &lt;appSettings&gt; element.</span></span>

<span data-ttu-id="f5bac-203">O elemento de appSettings contém muitos valores que são necessários para atualizações de segurança.</span><span class="sxs-lookup"><span data-stu-id="f5bac-203">The appSettings element contains many values which are required for security updates.</span></span> <span data-ttu-id="f5bac-204">Você não deve alterar ou desativar esses valores.</span><span class="sxs-lookup"><span data-stu-id="f5bac-204">You should not change or disable these values.</span></span> <span data-ttu-id="f5bac-205">Se você precisar desabilitar esses valores ao implantar uma atualização, imediatamente habilite novamente depois de concluir a implantação.</span><span class="sxs-lookup"><span data-stu-id="f5bac-205">If you must disable these values when deploying an update, immediately re-enable after completing deployment.</span></span>

<span data-ttu-id="f5bac-206">Para obter detalhes, consulte [elemento de appSettings do ASP.NET](https://msdn.microsoft.com/library/hh975440.aspx).</span><span class="sxs-lookup"><span data-stu-id="f5bac-206">For details, see [ASP.NET appSettings Element](https://msdn.microsoft.com/library/hh975440.aspx).</span></span>

<a id="urlpathencode"></a>

### <a name="urlpathencode"></a><span data-ttu-id="f5bac-207">UrlPathEncode</span><span class="sxs-lookup"><span data-stu-id="f5bac-207">UrlPathEncode</span></span>

<span data-ttu-id="f5bac-208">Recomendação: Use [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx) em vez disso.</span><span class="sxs-lookup"><span data-stu-id="f5bac-208">Recommendation: Use [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx) instead.</span></span>

<span data-ttu-id="f5bac-209">O método UrlPathEncode foi adicionado para o .NET Framework para resolver um problema de compatibilidade do navegador muito específico.</span><span class="sxs-lookup"><span data-stu-id="f5bac-209">The UrlPathEncode method was added to the .NET Framework to resolve a very specific browser compatibility problem.</span></span> <span data-ttu-id="f5bac-210">Ele não codifica uma URL adequadamente e não proteja seu aplicativo de script entre sites.</span><span class="sxs-lookup"><span data-stu-id="f5bac-210">It does not adequately encode a URL, and does not protect your application from cross-site scripting.</span></span> <span data-ttu-id="f5bac-211">Você nunca deve usá-lo em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f5bac-211">You should never use it in your application.</span></span> <span data-ttu-id="f5bac-212">Em vez disso, use [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx).</span><span class="sxs-lookup"><span data-stu-id="f5bac-212">Instead, use [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx).</span></span>

<span data-ttu-id="f5bac-213">O exemplo a seguir mostra como passar uma URL codificada como um parâmetro de cadeia de caracteres de consulta para um controle de hiperlink.</span><span class="sxs-lookup"><span data-stu-id="f5bac-213">The following example shows how to pass an encoded URL as a query string parameter for a hyperlink control.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample10.cs)]

<a id="performance"></a>

## <a name="reliability-and-performance"></a><span data-ttu-id="f5bac-214">Confiabilidade e desempenho</span><span class="sxs-lookup"><span data-stu-id="f5bac-214">Reliability and performance</span></span>

<a id="presend"></a>

### <a name="presendrequestheaders-and-presendrequestcontent"></a><span data-ttu-id="f5bac-215">PreSendRequestHeaders e PreSendRequestContent</span><span class="sxs-lookup"><span data-stu-id="f5bac-215">PreSendRequestHeaders and PreSendRequestContent</span></span>

<span data-ttu-id="f5bac-216">Recomendação: Não use esses eventos com módulos gerenciados.</span><span class="sxs-lookup"><span data-stu-id="f5bac-216">Recommendation: Do not use these events with managed modules.</span></span> <span data-ttu-id="f5bac-217">Em vez disso, escreva um módulo nativo do IIS para executar a tarefa exigida.</span><span class="sxs-lookup"><span data-stu-id="f5bac-217">Instead, write a native IIS module to perform the required task.</span></span> <span data-ttu-id="f5bac-218">Ver [criação de módulos HTTP de código nativo](https://msdn.microsoft.com/library/ms693629.aspx).</span><span class="sxs-lookup"><span data-stu-id="f5bac-218">See [Creating Native-Code HTTP Modules](https://msdn.microsoft.com/library/ms693629.aspx).</span></span>

<span data-ttu-id="f5bac-219">Você pode usar o [PreSendRequestHeaders](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestheaders.aspx) e [PreSendRequestContent](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestcontent.aspx) eventos com os módulos IIS nativos.</span><span class="sxs-lookup"><span data-stu-id="f5bac-219">You can use the [PreSendRequestHeaders](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestheaders.aspx) and [PreSendRequestContent](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestcontent.aspx) events with native IIS modules.</span></span>
> [!WARNING]
> <span data-ttu-id="f5bac-220">Não use `PreSendRequestHeaders` e `PreSendRequestContent` com os módulos gerenciados que implementam `IHttpModule`.</span><span class="sxs-lookup"><span data-stu-id="f5bac-220">Do not use `PreSendRequestHeaders` and `PreSendRequestContent` with managed modules that implement `IHttpModule`.</span></span> <span data-ttu-id="f5bac-221">Definir essas propriedades pode causar problemas com solicitações assíncronas.</span><span class="sxs-lookup"><span data-stu-id="f5bac-221">Setting these properties can cause issues with asynchronous requests.</span></span> <span data-ttu-id="f5bac-222">A combinação de aplicativo solicitado ARR (roteamento) e o WebSocket pode levar a exceções de violação de acesso que podem fazer com que w3wp falhe.</span><span class="sxs-lookup"><span data-stu-id="f5bac-222">The combination of Application Requested Routing (ARR) and websockets might lead to access violation exceptions that can cause w3wp to crash.</span></span> <span data-ttu-id="f5bac-223">Por exemplo, iiscore! W3_CONTEXT_BASE::GetIsLastNotification + 68 no iiscore.dll causou uma exceção de violação de acesso (0xC0000005).</span><span class="sxs-lookup"><span data-stu-id="f5bac-223">For example, iiscore!W3_CONTEXT_BASE::GetIsLastNotification+68 in iiscore.dll has caused an access violation exception (0xC0000005).</span></span>

<a id="asyncevents"></a>

### <a name="asynchronous-page-events-with-web-forms"></a><span data-ttu-id="f5bac-224">Eventos de página assíncrona com web forms</span><span class="sxs-lookup"><span data-stu-id="f5bac-224">Asynchronous page events with web forms</span></span>

<span data-ttu-id="f5bac-225">Recomendação: No Web Forms, evite escrever async void métodos para eventos de ciclo de vida da página e, em vez disso, use [RegisterAsyncTask](https://msdn.microsoft.com/library/system.web.ui.page.registerasynctask.aspx) para código assíncrono.</span><span class="sxs-lookup"><span data-stu-id="f5bac-225">Recommendation: In Web Forms, avoid writing async void methods for Page lifecycle events, and instead use [Page.RegisterAsyncTask](https://msdn.microsoft.com/library/system.web.ui.page.registerasynctask.aspx) for asynchronous code.</span></span>

<span data-ttu-id="f5bac-226">Quando você marca um evento de página com **async** e **void**, não é possível determinar quando o código assíncrono foi concluído.</span><span class="sxs-lookup"><span data-stu-id="f5bac-226">When you mark a page event with **async** and **void**, you cannot determine when the asynchronous code has finished.</span></span> <span data-ttu-id="f5bac-227">Em vez disso, use RegisterAsyncTask para executar o código assíncrono de uma maneira que permite que você controle sua conclusão.</span><span class="sxs-lookup"><span data-stu-id="f5bac-227">Instead, use Page.RegisterAsyncTask to run the asynchronous code in a way that enables you to track its completion.</span></span>

<span data-ttu-id="f5bac-228">A exemplo a seguir mostra um botão Clique manipulador que contém o código assíncrono.</span><span class="sxs-lookup"><span data-stu-id="f5bac-228">The following example shows a button click handler that contains asynchronous code.</span></span> <span data-ttu-id="f5bac-229">Este exemplo inclui ler um valor de cadeia de caracteres de forma assíncrona, que é fornecido apenas como um exemplo simplificado de uma tarefa assíncrona e não como uma prática recomendada.</span><span class="sxs-lookup"><span data-stu-id="f5bac-229">This example includes reading a string value asynchronously, which is provided only as a simplified example of an asynchronous task and not as a recommended practice.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample11.cs)]

<span data-ttu-id="f5bac-230">Se você estiver usando tarefas assíncronas, defina a estrutura de destino do tempo de execução de Http para 4.5 (ou posterior) no arquivo Web. config.</span><span class="sxs-lookup"><span data-stu-id="f5bac-230">If you are using asynchronous Tasks, set the Http runtime target framework to 4.5 (or later) in the Web.config file.</span></span> <span data-ttu-id="f5bac-231">Definir a estrutura de destino para 4.5 ativa no novo contexto de sincronização que foi adicionado no .NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="f5bac-231">Setting the target framework to 4.5 turns on the new synchronization context that was added in .NET 4.5.</span></span> <span data-ttu-id="f5bac-232">Esse valor é definido por padrão em novos projetos no Visual Studio, mas é a não ser definida se você estiver trabalhando com um projeto existente.</span><span class="sxs-lookup"><span data-stu-id="f5bac-232">This value is set by default in new projects in Visual Studio, but is not be set if you are working with an existing project.</span></span>

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample12.xml)]

<a id="fire"></a>

### <a name="fire-and-forget-work"></a><span data-ttu-id="f5bac-233">Disparar e esquecer de trabalho</span><span class="sxs-lookup"><span data-stu-id="f5bac-233">Fire-and-forget work</span></span>

<span data-ttu-id="f5bac-234">Recomendação: Ao lidar com uma solicitação dentro do ASP.NET, evite iniciar o trabalho de disparar e esquecer (tal chamando o método ThreadPool. QueueUserWorkItem ou criando um temporizador que chama repetidamente um delegado).</span><span class="sxs-lookup"><span data-stu-id="f5bac-234">Recommendation: When handling a request within ASP.NET, avoid launching fire-and-forget work (such calling the ThreadPool.QueueUserWorkItem method or creating a timer that repeatedly calls a delegate).</span></span>

<span data-ttu-id="f5bac-235">Se seu aplicativo tiver trabalho disparar e esquecer que é executado dentro do ASP.NET, seu aplicativo pode ficar fora de sincronizado. A qualquer momento, o domínio de aplicativo pode ser destruído que significa que seu processo contínuo não pode corresponder o estado atual do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f5bac-235">If your application has fire-and-forget work that runs within ASP.NET, your application can get out of sync. At any time, the app domain can be destroyed which means your ongoing process may no longer match the current state of the application.</span></span>

<span data-ttu-id="f5bac-236">Você deve mover esse tipo de trabalho fora do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="f5bac-236">You should move this type of work outside of ASP.NET.</span></span> <span data-ttu-id="f5bac-237">Você pode usar os trabalhos da Web, serviço do Windows ou uma função de trabalho no Azure para executar um trabalho em andamento e executar esse código de outro processo.</span><span class="sxs-lookup"><span data-stu-id="f5bac-237">You can use a Web Jobs, Windows Service or a Worker role in Azure to perform ongoing work, and run that code from another process.</span></span>

<span data-ttu-id="f5bac-238">Se for preciso executar este trabalho dentro do ASP.NET, você pode adicionar o pacote do Nuget chamado [WebBackgrounder](http://www.nuget.org/packages/webbackgrounder) para executar o código.</span><span class="sxs-lookup"><span data-stu-id="f5bac-238">If you must perform this work within ASP.NET, you can add the Nuget package called [WebBackgrounder](http://www.nuget.org/packages/webbackgrounder) to run the code.</span></span>

<a id="requestentity"></a>

### <a name="request-entity-body"></a><span data-ttu-id="f5bac-239">Corpo da entidade de solicitação</span><span class="sxs-lookup"><span data-stu-id="f5bac-239">Request entity body</span></span>

<span data-ttu-id="f5bac-240">Recomendação: Evite a leitura Request. Form ou Request. InputStream, antes do manipulador de evento de execução.</span><span class="sxs-lookup"><span data-stu-id="f5bac-240">Recommendation: Avoid reading Request.Form or Request.InputStream before the handler's execute event.</span></span>

<span data-ttu-id="f5bac-241">O mais antigo que você deve ler de Request. Form ou Request. InputStream é durante o manipulador execute eventos.</span><span class="sxs-lookup"><span data-stu-id="f5bac-241">The earliest you should read from Request.Form or Request.InputStream is during the handler's execute event.</span></span> <span data-ttu-id="f5bac-242">No MVC, o controlador é o manipulador e evento de execução é quando o método de ação é executada.</span><span class="sxs-lookup"><span data-stu-id="f5bac-242">In MVC, the Controller is the handler and the execute event is when the action method runs.</span></span> <span data-ttu-id="f5bac-243">Nos Web Forms, a página é o manipulador e evento de execução é quando o evento Page.Init é acionado.</span><span class="sxs-lookup"><span data-stu-id="f5bac-243">In Web Forms, the Page is the handler and the execute event is when the Page.Init event fires.</span></span> <span data-ttu-id="f5bac-244">Se você ler o corpo da entidade de solicitação anterior ao evento de execução, você interfere com o processamento da solicitação.</span><span class="sxs-lookup"><span data-stu-id="f5bac-244">If you read the request entity body earlier than the execute event, you interfere with the processing of the request.</span></span>

<span data-ttu-id="f5bac-245">Se você precisar ler o corpo da entidade de solicitação antes do evento de execução, use [Request.GetBufferlessInputStream](https://msdn.microsoft.com/library/ff406798.aspx) ou [Request.GetBufferedInputStream](https://msdn.microsoft.com/library/system.web.httprequest.getbufferedinputstream.aspx).</span><span class="sxs-lookup"><span data-stu-id="f5bac-245">If you need to read the request entity body before the execute event, use either [Request.GetBufferlessInputStream](https://msdn.microsoft.com/library/ff406798.aspx) or [Request.GetBufferedInputStream](https://msdn.microsoft.com/library/system.web.httprequest.getbufferedinputstream.aspx).</span></span> <span data-ttu-id="f5bac-246">Quando você usa GetBufferlessInputStream, obtenha o fluxo bruto da solicitação e assuma a responsabilidade por processar a solicitação inteira.</span><span class="sxs-lookup"><span data-stu-id="f5bac-246">When you use GetBufferlessInputStream, you get the raw stream from the request, and assume responsibility for processing the entire request.</span></span> <span data-ttu-id="f5bac-247">Depois de chamar GetBufferlessInputStream, Request. Form e Request. InputStream não estão disponíveis porque eles não foram populados pelo ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="f5bac-247">After calling GetBufferlessInputStream, Request.Form and Request.InputStream are not available because they have not been populated by ASP.NET.</span></span> <span data-ttu-id="f5bac-248">Quando você usa GetBufferedInputStream, você obtém uma cópia do fluxo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="f5bac-248">When you use GetBufferedInputStream, you get a copy of the stream from the request.</span></span> <span data-ttu-id="f5bac-249">Request. Form e Request. InputStream ainda estarão disponíveis posteriormente na solicitação porque o ASP.NET preenche a outra cópia.</span><span class="sxs-lookup"><span data-stu-id="f5bac-249">Request.Form and Request.InputStream are still available later in the request because ASP.NET populates the other copy.</span></span>

<a id="redirect"></a>

### <a name="responseredirect-and-responseend"></a><span data-ttu-id="f5bac-250">Response. Redirect e Response</span><span class="sxs-lookup"><span data-stu-id="f5bac-250">Response.Redirect and Response.End</span></span>

<span data-ttu-id="f5bac-251">Recomendação: Esteja ciente das diferenças em como o thread é manipulado depois de chamar [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx).</span><span class="sxs-lookup"><span data-stu-id="f5bac-251">Recommendation: Be aware of differences in how thread is handled after calling [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx).</span></span>

<span data-ttu-id="f5bac-252">O [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx) método chama o método Response.</span><span class="sxs-lookup"><span data-stu-id="f5bac-252">The [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx) method calls the Response.End method.</span></span> <span data-ttu-id="f5bac-253">Em um processo síncrono, chamar Request.Redirect faz com que o thread atual anular imediatamente.</span><span class="sxs-lookup"><span data-stu-id="f5bac-253">In a synchronous process, calling Request.Redirect causes the current thread to immediately abort.</span></span> <span data-ttu-id="f5bac-254">No entanto, em um processo assíncrono, chamar Response. Redirect não anular o thread atual, portanto, a execução de código continua para a solicitação.</span><span class="sxs-lookup"><span data-stu-id="f5bac-254">However, in an asynchronous process, calling Response.Redirect does not abort the current thread, so code execution continues for the request.</span></span> <span data-ttu-id="f5bac-255">Em um processo assíncrono, você deve retornar a tarefa do método para interromper a execução de código.</span><span class="sxs-lookup"><span data-stu-id="f5bac-255">In an asynchronous process, you must return the Task from the method to stop the code execution.</span></span>

<span data-ttu-id="f5bac-256">Em um projeto do MVC, você não deve chamar Response. Redirect.</span><span class="sxs-lookup"><span data-stu-id="f5bac-256">In an MVC project, you should not call Response.Redirect.</span></span> <span data-ttu-id="f5bac-257">Em vez disso, retorne um RedirectResult.</span><span class="sxs-lookup"><span data-stu-id="f5bac-257">Instead, return a RedirectResult.</span></span>

<a id="viewstatemode"></a>

### <a name="enableviewstate-and-viewstatemode"></a><span data-ttu-id="f5bac-258">EnableViewState e ViewStateMode</span><span class="sxs-lookup"><span data-stu-id="f5bac-258">EnableViewState and ViewStateMode</span></span>

<span data-ttu-id="f5bac-259">Recomendação: Use ViewStateMode, em vez de EnableViewState, para fornecer controle granular sobre quais controles usam o estado de exibição.</span><span class="sxs-lookup"><span data-stu-id="f5bac-259">Recommendation: Use ViewStateMode, instead of EnableViewState, to provide granular control over which controls use view state.</span></span>

<span data-ttu-id="f5bac-260">Quando você define EnableViewState como falso na diretiva Page, o estado de exibição está desabilitado para todos os controles dentro da página e não pode ser habilitado.</span><span class="sxs-lookup"><span data-stu-id="f5bac-260">When you set EnableViewState to false in the Page directive, view state is disabled for all controls within the page and cannot be enabled.</span></span> <span data-ttu-id="f5bac-261">Se você quiser habilitar o estado de exibição para apenas determinados controles em sua página, defina ViewStateMode como desabilitado para a página.</span><span class="sxs-lookup"><span data-stu-id="f5bac-261">If you want to enable view state for only certain controls in your page, set ViewStateMode to Disabled for the Page.</span></span>

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample13.aspx)]

<span data-ttu-id="f5bac-262">Em seguida, defina ViewStateMode para habilitado nos controles que realmente precisam de estado de exibição.</span><span class="sxs-lookup"><span data-stu-id="f5bac-262">Then, set ViewStateMode to Enabled on only the controls that actually need view state.</span></span>

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample14.aspx)]

<span data-ttu-id="f5bac-263">Habilitando o estado de exibição para os controles que precisam dele, você pode reduzir o tamanho do estado de exibição para suas páginas da web.</span><span class="sxs-lookup"><span data-stu-id="f5bac-263">By enabling view state for only the controls that need it, you can shrink the size of the view state for your web pages.</span></span>

<a id="sqlprovider"></a>

### <a name="sqlmembershipprovider"></a><span data-ttu-id="f5bac-264">SqlMembershipProvider</span><span class="sxs-lookup"><span data-stu-id="f5bac-264">SqlMembershipProvider</span></span>

<span data-ttu-id="f5bac-265">Recomendação: Use provedores universais.</span><span class="sxs-lookup"><span data-stu-id="f5bac-265">Recommendation: Use Universal Providers.</span></span>

<span data-ttu-id="f5bac-266">Em modelos de projeto atual, SqlMembershipProvider foi substituído por [ASP.NET Universal Providers](http://www.nuget.org/packages/Microsoft.AspNet.Providers), que está disponível como um pacote do NuGet.</span><span class="sxs-lookup"><span data-stu-id="f5bac-266">In the current project templates, SqlMembershipProvider has been replaced by [ASP.NET Universal Providers](http://www.nuget.org/packages/Microsoft.AspNet.Providers), which is available as a NuGet package.</span></span> <span data-ttu-id="f5bac-267">Se você estiver usando SqlMembershipProvider em um projeto que foi criado com uma versão anterior dos modelos, você deve alternar para provedores universais.</span><span class="sxs-lookup"><span data-stu-id="f5bac-267">If you are using SqlMembershipProvider in a project that was built with an earlier version of the templates, you should switch to Universal Providers.</span></span> <span data-ttu-id="f5bac-268">Os provedores universais funcionam com todos os bancos de dados que são suportados pelo Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="f5bac-268">The Universal Providers work with all databases that are supported by Entity Framework.</span></span>

<span data-ttu-id="f5bac-269">Para obter mais informações, consulte [apresentando o ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx).</span><span class="sxs-lookup"><span data-stu-id="f5bac-269">For more information, see [Introducing ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx).</span></span>

<a id="long"></a>

### <a name="long-running-requests-110-seconds"></a><span data-ttu-id="f5bac-270">Solicitações de execução longa (> 110 segundos)</span><span class="sxs-lookup"><span data-stu-id="f5bac-270">Long-running requests (>110 seconds)</span></span>

<span data-ttu-id="f5bac-271">Recomendação: Use [WebSockets](https://msdn.microsoft.com/library/system.net.websockets.websocket.aspx) ou [SignalR](../../../signalr/index.md) para clientes conectados e use operações de e/s assíncronas.</span><span class="sxs-lookup"><span data-stu-id="f5bac-271">Recommendation: Use [WebSockets](https://msdn.microsoft.com/library/system.net.websockets.websocket.aspx) or [SignalR](../../../signalr/index.md) for connected clients, and use asynchronous I/O operations.</span></span>

<span data-ttu-id="f5bac-272">Solicitações de execução longa podem causar resultados imprevisíveis e baixo desempenho em seu aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="f5bac-272">Long-running requests can cause unpredictable results and poor performance in your web application.</span></span> <span data-ttu-id="f5bac-273">A configuração de tempo limite padrão para uma solicitação é de 110 segundos.</span><span class="sxs-lookup"><span data-stu-id="f5bac-273">The default timeout setting for a request is 110 seconds.</span></span> <span data-ttu-id="f5bac-274">Se você estiver usando o estado de sessão com uma solicitação de longa execução, o ASP.NET liberará o bloqueio no objeto de sessão depois de 110 segundos.</span><span class="sxs-lookup"><span data-stu-id="f5bac-274">If you are using session state with a long-running request, ASP.NET will release the lock on the Session object after 110 seconds.</span></span> <span data-ttu-id="f5bac-275">No entanto, seu aplicativo pode ser no meio de uma operação no objeto de sessão quando o bloqueio seja liberado, e a operação pode não ser concluída com êxito.</span><span class="sxs-lookup"><span data-stu-id="f5bac-275">However, your application might be in the middle of an operation on the Session object when the lock is released, and the operation might not complete successfully.</span></span> <span data-ttu-id="f5bac-276">Se uma segunda solicitação do usuário for bloqueada enquanto a primeira solicitação está em execução, a segunda solicitação pode acessar o objeto de sessão em um estado inconsistente.</span><span class="sxs-lookup"><span data-stu-id="f5bac-276">If a second request from the user is blocked while the first request is running, the second request might access the Session object in an inconsistent state.</span></span>

<span data-ttu-id="f5bac-277">Se seu aplicativo inclui operações de e/s de bloqueio (ou síncronas), o aplicativo será sem resposta.</span><span class="sxs-lookup"><span data-stu-id="f5bac-277">If your application includes blocking (or synchronous) I/O operations, the application will be unresponsive.</span></span>

<span data-ttu-id="f5bac-278">Para melhorar o desempenho, use as operações de e/s assíncronas no .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f5bac-278">To improve performance, use the asynchronous I/O operations in the .NET Framework.</span></span> <span data-ttu-id="f5bac-279">Além disso, use o SignalR ou WebSockets para conectar clientes ao servidor.</span><span class="sxs-lookup"><span data-stu-id="f5bac-279">Also, use WebSockets or SignalR for connecting clients to the server.</span></span> <span data-ttu-id="f5bac-280">Esses recursos são projetados para manipular solicitações de execução longa.</span><span class="sxs-lookup"><span data-stu-id="f5bac-280">These features are designed to efficiently handle long-running requests.</span></span>
