---
title: Impedir ataques de solicitação intersite forjada (XSRF/CSRF) no ASP.NET Core
author: steve-smith
description: Descubra como evitar ataques contra aplicativos Web em que um site mal-intencionado pode influenciar a interação entre um navegador cliente e o aplicativo.
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2019
uid: security/anti-request-forgery
ms.openlocfilehash: c3d32a93bcbf9f3897c10e68ba4e43d269a3ea80
ms.sourcegitcommit: b3e1e31e5d8bdd94096cf27444594d4a7b065525
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803364"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="14d5b-103">Impedir ataques de solicitação intersite forjada (XSRF/CSRF) no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="14d5b-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="14d5b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="14d5b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="14d5b-105">A solicitação entre sites forjada (também conhecida como XSRF ou CSRF) é um ataque contra aplicativos hospedados na Web, nos quais um aplicativo Web mal-intencionado pode influenciar a interação entre um navegador cliente e um aplicativo Web que confia nesse navegador.</span><span class="sxs-lookup"><span data-stu-id="14d5b-105">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="14d5b-106">Esses ataques são possíveis porque os navegadores da Web enviam automaticamente alguns tipos de tokens de autenticação com cada solicitação a um site.</span><span class="sxs-lookup"><span data-stu-id="14d5b-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="14d5b-107">Essa forma de exploração também é conhecida como um ataque ou *sessão* de *um clique* , pois o ataque aproveita a sessão autenticada anteriormente do usuário.</span><span class="sxs-lookup"><span data-stu-id="14d5b-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="14d5b-108">Um exemplo de um ataque de CSRF:</span><span class="sxs-lookup"><span data-stu-id="14d5b-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="14d5b-109">Um usuário entra em `www.good-banking-site.com` usando a autenticação de formulários.</span><span class="sxs-lookup"><span data-stu-id="14d5b-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="14d5b-110">O servidor autentica o usuário e emite uma resposta que inclui um cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="14d5b-110">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="14d5b-111">O site está vulnerável a ataques porque confia em qualquer solicitação recebida com um cookie de autenticação válido.</span><span class="sxs-lookup"><span data-stu-id="14d5b-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="14d5b-112">O usuário visita um site mal-intencionado, `www.bad-crook-site.com`.</span><span class="sxs-lookup"><span data-stu-id="14d5b-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="14d5b-113">O site mal-intencionado, `www.bad-crook-site.com`, contém um formulário HTML semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="14d5b-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="14d5b-114">Observe que o `action` do formulário é Postado para o site vulnerável, não para o site mal-intencionado.</span><span class="sxs-lookup"><span data-stu-id="14d5b-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="14d5b-115">Esta é a parte "entre sites" do CSRF.</span><span class="sxs-lookup"><span data-stu-id="14d5b-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="14d5b-116">O usuário seleciona o botão enviar.</span><span class="sxs-lookup"><span data-stu-id="14d5b-116">The user selects the submit button.</span></span> <span data-ttu-id="14d5b-117">O navegador faz a solicitação e inclui automaticamente o cookie de autenticação para o domínio solicitado, `www.good-banking-site.com`.</span><span class="sxs-lookup"><span data-stu-id="14d5b-117">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="14d5b-118">A solicitação é executada no servidor de `www.good-banking-site.com` com o contexto de autenticação do usuário e pode executar qualquer ação que um usuário autenticado tem permissão para executar.</span><span class="sxs-lookup"><span data-stu-id="14d5b-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="14d5b-119">Além do cenário em que o usuário seleciona o botão para enviar o formulário, o site mal-intencionado poderia:</span><span class="sxs-lookup"><span data-stu-id="14d5b-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="14d5b-120">Execute um script que envia automaticamente o formulário.</span><span class="sxs-lookup"><span data-stu-id="14d5b-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="14d5b-121">Envie o envio do formulário como uma solicitação AJAX.</span><span class="sxs-lookup"><span data-stu-id="14d5b-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="14d5b-122">Oculte o formulário usando o CSS.</span><span class="sxs-lookup"><span data-stu-id="14d5b-122">Hide the form using CSS.</span></span>

<span data-ttu-id="14d5b-123">Esses cenários alternativos não exigem nenhuma ação ou entrada do usuário que não seja a visita inicial do site mal-intencionado.</span><span class="sxs-lookup"><span data-stu-id="14d5b-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="14d5b-124">O uso de HTTPS não impede um ataque de CSRF.</span><span class="sxs-lookup"><span data-stu-id="14d5b-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="14d5b-125">O site mal-intencionado pode enviar uma solicitação de `https://www.good-banking-site.com/` tão facilmente quanto pode enviar uma solicitação insegura.</span><span class="sxs-lookup"><span data-stu-id="14d5b-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="14d5b-126">Alguns ataques têm como alvo pontos de extremidade que respondem a solicitações GET. nesse caso, uma marca de imagem pode ser usada para executar a ação.</span><span class="sxs-lookup"><span data-stu-id="14d5b-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="14d5b-127">Essa forma de ataque é comum em sites de fórum que permitem imagens, mas bloqueiam JavaScript.</span><span class="sxs-lookup"><span data-stu-id="14d5b-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="14d5b-128">Aplicativos que alteram o estado em solicitações GET, em que variáveis ou recursos são alterados, são vulneráveis a ataques mal-intencionados.</span><span class="sxs-lookup"><span data-stu-id="14d5b-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="14d5b-129">**As solicitações GET que alteram o estado são inseguras. Uma prática recomendada é nunca alterar o estado em uma solicitação GET.**</span><span class="sxs-lookup"><span data-stu-id="14d5b-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="14d5b-130">Ataques de CSRF são possíveis em aplicativos Web que usam cookies para autenticação porque:</span><span class="sxs-lookup"><span data-stu-id="14d5b-130">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="14d5b-131">Os navegadores armazenam cookies emitidos por um aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="14d5b-131">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="14d5b-132">Os cookies armazenados incluem cookies de sessão para usuários autenticados.</span><span class="sxs-lookup"><span data-stu-id="14d5b-132">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="14d5b-133">Os navegadores enviam todos os cookies associados a um domínio para o aplicativo Web a cada solicitação, independentemente de como a solicitação para o aplicativo foi gerada no navegador.</span><span class="sxs-lookup"><span data-stu-id="14d5b-133">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="14d5b-134">No entanto, ataques CSRF não são limitados à exploração de cookies.</span><span class="sxs-lookup"><span data-stu-id="14d5b-134">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="14d5b-135">Por exemplo, a autenticação básica e resumida também são vulneráveis.</span><span class="sxs-lookup"><span data-stu-id="14d5b-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="14d5b-136">Depois que um usuário entrar com a autenticação básica ou resumida, o navegador enviará automaticamente as credenciais até que a sessão&dagger; seja encerrada.</span><span class="sxs-lookup"><span data-stu-id="14d5b-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="14d5b-137">&dagger;neste contexto, *Session* refere-se à sessão do lado do cliente durante a qual o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="14d5b-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="14d5b-138">Não está relacionado a sessões do lado do servidor ou ao [middleware de sessão ASP.NET Core](xref:fundamentals/app-state).</span><span class="sxs-lookup"><span data-stu-id="14d5b-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="14d5b-139">Os usuários podem se proteger contra vulnerabilidades do CSRF tomando precauções:</span><span class="sxs-lookup"><span data-stu-id="14d5b-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="14d5b-140">Desconexão de aplicativos Web quando terminar de usá-los.</span><span class="sxs-lookup"><span data-stu-id="14d5b-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="14d5b-141">Limpe os cookies do navegador periodicamente.</span><span class="sxs-lookup"><span data-stu-id="14d5b-141">Clear browser cookies periodically.</span></span>

<span data-ttu-id="14d5b-142">No entanto, as vulnerabilidades do CSRF são fundamentalmente um problema com o aplicativo Web, não com o usuário final.</span><span class="sxs-lookup"><span data-stu-id="14d5b-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="14d5b-143">Conceitos básicos de autenticação</span><span class="sxs-lookup"><span data-stu-id="14d5b-143">Authentication fundamentals</span></span>

<span data-ttu-id="14d5b-144">A autenticação baseada em cookie é uma forma popular de autenticação.</span><span class="sxs-lookup"><span data-stu-id="14d5b-144">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="14d5b-145">Os sistemas de autenticação baseados em token estão crescendo em popularidade, especialmente para aplicativos de página única (SPAs).</span><span class="sxs-lookup"><span data-stu-id="14d5b-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="14d5b-146">Autenticação baseada em cookie</span><span class="sxs-lookup"><span data-stu-id="14d5b-146">Cookie-based authentication</span></span>

<span data-ttu-id="14d5b-147">Quando um usuário é autenticado usando seu nome de usuário e senha, ele recebe um token que contém um tíquete de autenticação que pode ser usado para autenticação e autorização.</span><span class="sxs-lookup"><span data-stu-id="14d5b-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="14d5b-148">O token é armazenado como um cookie que acompanha cada solicitação que o cliente faz.</span><span class="sxs-lookup"><span data-stu-id="14d5b-148">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="14d5b-149">Gerar e validar esse cookie é executado pelo middleware de autenticação de cookie.</span><span class="sxs-lookup"><span data-stu-id="14d5b-149">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="14d5b-150">O [middleware](xref:fundamentals/middleware/index) serializa uma entidade de usuário em um cookie criptografado.</span><span class="sxs-lookup"><span data-stu-id="14d5b-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="14d5b-151">Em solicitações subsequentes, o middleware valida o cookie, recria a entidade de segurança e atribui a entidade de segurança à propriedade de [usuário](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) de [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span><span class="sxs-lookup"><span data-stu-id="14d5b-151">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="14d5b-152">Autenticação baseada em token</span><span class="sxs-lookup"><span data-stu-id="14d5b-152">Token-based authentication</span></span>

<span data-ttu-id="14d5b-153">Quando um usuário é autenticado, ele recebe um token (não um token de falsificação).</span><span class="sxs-lookup"><span data-stu-id="14d5b-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="14d5b-154">O token contém informações de usuário na forma de [declarações](/dotnet/framework/security/claims-based-identity-model) ou um token de referência que aponta o aplicativo para o estado do usuário mantido no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="14d5b-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="14d5b-155">Quando um usuário tenta acessar um recurso que requer autenticação, o token é enviado ao aplicativo com um cabeçalho de autorização adicional na forma de token de portador.</span><span class="sxs-lookup"><span data-stu-id="14d5b-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="14d5b-156">Isso torna o aplicativo sem estado.</span><span class="sxs-lookup"><span data-stu-id="14d5b-156">This makes the app stateless.</span></span> <span data-ttu-id="14d5b-157">Em cada solicitação subsequente, o token é passado na solicitação de validação do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="14d5b-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="14d5b-158">Esse token não está *criptografado*; Ele está *codificado*.</span><span class="sxs-lookup"><span data-stu-id="14d5b-158">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="14d5b-159">No servidor, o token é decodificado para acessar suas informações.</span><span class="sxs-lookup"><span data-stu-id="14d5b-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="14d5b-160">Para enviar o token em solicitações subsequentes, armazene o token no armazenamento local do navegador.</span><span class="sxs-lookup"><span data-stu-id="14d5b-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="14d5b-161">Não se preocupe com a vulnerabilidade de CSRF se o token for armazenado no armazenamento local do navegador.</span><span class="sxs-lookup"><span data-stu-id="14d5b-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="14d5b-162">CSRF é uma preocupação quando o token é armazenado em um cookie.</span><span class="sxs-lookup"><span data-stu-id="14d5b-162">CSRF is a concern when the token is stored in a cookie.</span></span> <span data-ttu-id="14d5b-163">Para obter mais informações, consulte o exemplo de código de spa problema do GitHub [adiciona dois cookies](https://github.com/aspnet/AspNetCore.Docs/issues/13369).</span><span class="sxs-lookup"><span data-stu-id="14d5b-163">For more information, see the GitHub issue [SPA code sample adds two cookies](https://github.com/aspnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="14d5b-164">Vários aplicativos hospedados em um domínio</span><span class="sxs-lookup"><span data-stu-id="14d5b-164">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="14d5b-165">Os ambientes de hospedagem compartilhados são vulneráveis ao seqüestro de sessão, CSRF de logon e outros ataques.</span><span class="sxs-lookup"><span data-stu-id="14d5b-165">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="14d5b-166">Embora `example1.contoso.net` e `example2.contoso.net` sejam hosts diferentes, há uma relação de confiança implícita entre os hosts no domínio de `*.contoso.net`.</span><span class="sxs-lookup"><span data-stu-id="14d5b-166">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="14d5b-167">Essa relação de confiança implícita permite que hosts potencialmente não confiáveis afetem os cookies uns dos outros (as políticas de mesma origem que regem as solicitações AJAX não necessariamente se aplicam a cookies HTTP).</span><span class="sxs-lookup"><span data-stu-id="14d5b-167">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="14d5b-168">Os ataques que exploram cookies confiáveis entre aplicativos hospedados no mesmo domínio podem ser impedidos por não compartilhar domínios.</span><span class="sxs-lookup"><span data-stu-id="14d5b-168">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="14d5b-169">Quando cada aplicativo é hospedado em seu próprio domínio, não há nenhuma relação de confiança de cookie implícita para explorar.</span><span class="sxs-lookup"><span data-stu-id="14d5b-169">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="14d5b-170">Configuração de antifalsificação de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="14d5b-170">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="14d5b-171">ASP.NET Core implementa antifalsificação usando a [proteção de dados do ASP.NET Core](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="14d5b-171">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="14d5b-172">A pilha de proteção de dados deve ser configurada para funcionar em um farm de servidores.</span><span class="sxs-lookup"><span data-stu-id="14d5b-172">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="14d5b-173">Consulte [Configurando a proteção de dados](xref:security/data-protection/configuration/overview) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="14d5b-173">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="14d5b-174">O middleware antifalsificação é adicionado ao contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) quando uma das seguintes APIs é chamada no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="14d5b-174">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="14d5b-175">O middleware antifalsificação é adicionado ao contêiner de [injeção de dependência](xref:fundamentals/dependency-injection) quando <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> é chamado em `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="14d5b-175">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="14d5b-176">No ASP.NET Core 2,0 ou posterior, o [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injeta tokens de antifalsificação em elementos de formulário HTML.</span><span class="sxs-lookup"><span data-stu-id="14d5b-176">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="14d5b-177">A seguinte marcação em um arquivo Razor gera automaticamente tokens de antifalsificação:</span><span class="sxs-lookup"><span data-stu-id="14d5b-177">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="14d5b-178">Da mesma forma, [IHtmlHelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) gera tokens antifalsificação por padrão se o método do formulário não for Get.</span><span class="sxs-lookup"><span data-stu-id="14d5b-178">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="14d5b-179">A geração automática de tokens antifalsificação para elementos de formulário HTML acontece quando a marca de `<form>` contém o atributo `method="post"` e qualquer uma das seguintes opções é verdadeira:</span><span class="sxs-lookup"><span data-stu-id="14d5b-179">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="14d5b-180">O atributo action está vazio (`action=""`).</span><span class="sxs-lookup"><span data-stu-id="14d5b-180">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="14d5b-181">O atributo action não é fornecido (`<form method="post">`).</span><span class="sxs-lookup"><span data-stu-id="14d5b-181">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="14d5b-182">A geração automática de tokens antifalsificação para elementos de formulário HTML pode ser desabilitada:</span><span class="sxs-lookup"><span data-stu-id="14d5b-182">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="14d5b-183">Desabilite explicitamente os tokens antifalsificação com o atributo `asp-antiforgery`:</span><span class="sxs-lookup"><span data-stu-id="14d5b-183">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="14d5b-184">O elemento form é aceito por auxiliares de marca usando o símbolo de desativação auxiliar de marca [!](xref:mvc/views/tag-helpers/intro#opt-out):</span><span class="sxs-lookup"><span data-stu-id="14d5b-184">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="14d5b-185">Remova a `FormTagHelper` da exibição.</span><span class="sxs-lookup"><span data-stu-id="14d5b-185">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="14d5b-186">O `FormTagHelper` pode ser removido de um modo de exibição adicionando a seguinte diretiva ao modo de exibição do Razor:</span><span class="sxs-lookup"><span data-stu-id="14d5b-186">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="14d5b-187">[Razor Pages](xref:razor-pages/index) são automaticamente protegidos de XSRF/CSRF.</span><span class="sxs-lookup"><span data-stu-id="14d5b-187">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="14d5b-188">Para obter mais informações, consulte [XSRF/CSRF e Razor Pages](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="14d5b-188">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="14d5b-189">A abordagem mais comum para a defesa contra ataques CSRF é usar o *padrão de token do sincronizador* (STP).</span><span class="sxs-lookup"><span data-stu-id="14d5b-189">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="14d5b-190">O STP é usado quando o usuário solicita uma página com dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="14d5b-190">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="14d5b-191">O servidor envia um token associado à identidade do usuário atual para o cliente.</span><span class="sxs-lookup"><span data-stu-id="14d5b-191">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="14d5b-192">O cliente envia de volta o token para o servidor para verificação.</span><span class="sxs-lookup"><span data-stu-id="14d5b-192">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="14d5b-193">Se o servidor receber um token que não corresponda à identidade do usuário autenticado, a solicitação será rejeitada.</span><span class="sxs-lookup"><span data-stu-id="14d5b-193">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="14d5b-194">O token é exclusivo e imprevisível.</span><span class="sxs-lookup"><span data-stu-id="14d5b-194">The token is unique and unpredictable.</span></span> <span data-ttu-id="14d5b-195">O token também pode ser usado para garantir o sequenciamento adequado de uma série de solicitações (por exemplo, garantindo a sequência de solicitação de: Página 1 &ndash; página 2 &ndash; página 3).</span><span class="sxs-lookup"><span data-stu-id="14d5b-195">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 &ndash; page 2 &ndash; page 3).</span></span> <span data-ttu-id="14d5b-196">Todos os formulários em modelos ASP.NET Core MVC e Razor Pages geram tokens antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="14d5b-196">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="14d5b-197">O seguinte par de exemplos de exibição gera tokens antifalsificação:</span><span class="sxs-lookup"><span data-stu-id="14d5b-197">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="14d5b-198">Adicione explicitamente um token de antifalsificação a um elemento `<form>` sem usar auxiliares de marcas com o HTML helper [@Html.AntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span><span class="sxs-lookup"><span data-stu-id="14d5b-198">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [@Html.AntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="14d5b-199">Em cada um dos casos anteriores, ASP.NET Core adiciona um campo de formulário oculto semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="14d5b-199">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="14d5b-200">ASP.NET Core inclui três [filtros](xref:mvc/controllers/filters) para trabalhar com tokens antifalsificação:</span><span class="sxs-lookup"><span data-stu-id="14d5b-200">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="14d5b-201">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="14d5b-201">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="14d5b-202">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="14d5b-202">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="14d5b-203">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="14d5b-203">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="14d5b-204">Opções de antifalsificação</span><span class="sxs-lookup"><span data-stu-id="14d5b-204">Antiforgery options</span></span>

<span data-ttu-id="14d5b-205">Personalize [as opções de antifalsificação](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="14d5b-205">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="14d5b-206">&dagger;definir as propriedades de `Cookie` antifalsificação usando as propriedades da classe [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) .</span><span class="sxs-lookup"><span data-stu-id="14d5b-206">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="14d5b-207">Opção</span><span class="sxs-lookup"><span data-stu-id="14d5b-207">Option</span></span> | <span data-ttu-id="14d5b-208">Descrição</span><span class="sxs-lookup"><span data-stu-id="14d5b-208">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="14d5b-209">Cookie</span><span class="sxs-lookup"><span data-stu-id="14d5b-209">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="14d5b-210">Determina as configurações usadas para criar os cookies antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="14d5b-210">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="14d5b-211">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="14d5b-211">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="14d5b-212">O nome do campo de formulário oculto usado pelo sistema antifalsificação para renderizar tokens de antifalsificação em exibições.</span><span class="sxs-lookup"><span data-stu-id="14d5b-212">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="14d5b-213">HeaderName</span><span class="sxs-lookup"><span data-stu-id="14d5b-213">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="14d5b-214">O nome do cabeçalho usado pelo sistema antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="14d5b-214">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="14d5b-215">Se `null`, o sistema considerará apenas os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="14d5b-215">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="14d5b-216">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="14d5b-216">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="14d5b-217">Especifica se a geração do cabeçalho de `X-Frame-Options` deve ser suprimida.</span><span class="sxs-lookup"><span data-stu-id="14d5b-217">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="14d5b-218">Por padrão, o cabeçalho é gerado com um valor de "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="14d5b-218">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="14d5b-219">Assume o padrão de `false`.</span><span class="sxs-lookup"><span data-stu-id="14d5b-219">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="14d5b-220">Opção</span><span class="sxs-lookup"><span data-stu-id="14d5b-220">Option</span></span> | <span data-ttu-id="14d5b-221">Descrição</span><span class="sxs-lookup"><span data-stu-id="14d5b-221">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="14d5b-222">Cookie</span><span class="sxs-lookup"><span data-stu-id="14d5b-222">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="14d5b-223">Determina as configurações usadas para criar os cookies antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="14d5b-223">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="14d5b-224">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="14d5b-224">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | <span data-ttu-id="14d5b-225">O domínio do cookie.</span><span class="sxs-lookup"><span data-stu-id="14d5b-225">The domain of the cookie.</span></span> <span data-ttu-id="14d5b-226">Assume o padrão de `null`.</span><span class="sxs-lookup"><span data-stu-id="14d5b-226">Defaults to `null`.</span></span> <span data-ttu-id="14d5b-227">Esta propriedade é obsoleta e será removida em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="14d5b-227">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="14d5b-228">A alternativa recomendada é cookie. domain.</span><span class="sxs-lookup"><span data-stu-id="14d5b-228">The recommended alternative is Cookie.Domain.</span></span> |
| [<span data-ttu-id="14d5b-229">CookieName</span><span class="sxs-lookup"><span data-stu-id="14d5b-229">CookieName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | <span data-ttu-id="14d5b-230">O nome do cookie.</span><span class="sxs-lookup"><span data-stu-id="14d5b-230">The name of the cookie.</span></span> <span data-ttu-id="14d5b-231">Se não estiver definido, o sistema gera um nome exclusivo que começa com o [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore. antifalsificação. ").</span><span class="sxs-lookup"><span data-stu-id="14d5b-231">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="14d5b-232">Esta propriedade é obsoleta e será removida em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="14d5b-232">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="14d5b-233">A alternativa recomendada é Cookie.Name.</span><span class="sxs-lookup"><span data-stu-id="14d5b-233">The recommended alternative is Cookie.Name.</span></span> |
| [<span data-ttu-id="14d5b-234">CookiePath</span><span class="sxs-lookup"><span data-stu-id="14d5b-234">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | <span data-ttu-id="14d5b-235">O caminho definido no cookie.</span><span class="sxs-lookup"><span data-stu-id="14d5b-235">The path set on the cookie.</span></span> <span data-ttu-id="14d5b-236">Esta propriedade é obsoleta e será removida em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="14d5b-236">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="14d5b-237">A alternativa recomendada é cookie. Path.</span><span class="sxs-lookup"><span data-stu-id="14d5b-237">The recommended alternative is Cookie.Path.</span></span> |
| [<span data-ttu-id="14d5b-238">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="14d5b-238">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="14d5b-239">O nome do campo de formulário oculto usado pelo sistema antifalsificação para renderizar tokens de antifalsificação em exibições.</span><span class="sxs-lookup"><span data-stu-id="14d5b-239">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="14d5b-240">HeaderName</span><span class="sxs-lookup"><span data-stu-id="14d5b-240">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="14d5b-241">O nome do cabeçalho usado pelo sistema antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="14d5b-241">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="14d5b-242">Se `null`, o sistema considerará apenas os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="14d5b-242">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="14d5b-243">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="14d5b-243">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="14d5b-244">Especifica se o HTTPS é exigido pelo sistema antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="14d5b-244">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="14d5b-245">Se `true`, as solicitações não-HTTPS falharão.</span><span class="sxs-lookup"><span data-stu-id="14d5b-245">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="14d5b-246">Assume o padrão de `false`.</span><span class="sxs-lookup"><span data-stu-id="14d5b-246">Defaults to `false`.</span></span> <span data-ttu-id="14d5b-247">Esta propriedade é obsoleta e será removida em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="14d5b-247">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="14d5b-248">A alternativa recomendada é definir cookie. SecurePolicy.</span><span class="sxs-lookup"><span data-stu-id="14d5b-248">The recommended alternative is to set Cookie.SecurePolicy.</span></span> |
| [<span data-ttu-id="14d5b-249">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="14d5b-249">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="14d5b-250">Especifica se a geração do cabeçalho de `X-Frame-Options` deve ser suprimida.</span><span class="sxs-lookup"><span data-stu-id="14d5b-250">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="14d5b-251">Por padrão, o cabeçalho é gerado com um valor de "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="14d5b-251">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="14d5b-252">Assume o padrão de `false`.</span><span class="sxs-lookup"><span data-stu-id="14d5b-252">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="14d5b-253">Para obter mais informações, consulte [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="14d5b-253">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="14d5b-254">Configurar recursos antifalsificação com IAntiforgery</span><span class="sxs-lookup"><span data-stu-id="14d5b-254">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="14d5b-255">O [IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) fornece a API para configurar recursos antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="14d5b-255">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="14d5b-256">`IAntiforgery` pode ser solicitado no método `Configure` da classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="14d5b-256">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="14d5b-257">O exemplo a seguir usa o middleware do home page do aplicativo para gerar um token de antifalsificação e enviá-lo na resposta como um cookie (usando a Convenção de nomenclatura angular padrão descrita mais adiante neste tópico):</span><span class="sxs-lookup"><span data-stu-id="14d5b-257">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="14d5b-258">Exigir validação de antifalsificação</span><span class="sxs-lookup"><span data-stu-id="14d5b-258">Require antiforgery validation</span></span>

<span data-ttu-id="14d5b-259">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) é um filtro de ação que pode ser aplicado a uma ação individual, a um controlador ou globalmente.</span><span class="sxs-lookup"><span data-stu-id="14d5b-259">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="14d5b-260">As solicitações feitas às ações que têm esse filtro aplicado são bloqueadas, a menos que a solicitação inclua um token antifalsificação válido.</span><span class="sxs-lookup"><span data-stu-id="14d5b-260">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="14d5b-261">O atributo `ValidateAntiForgeryToken` requer um token para solicitações para os métodos de ação que ele decora, incluindo solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="14d5b-261">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it decorates, including HTTP GET requests.</span></span> <span data-ttu-id="14d5b-262">Se o atributo `ValidateAntiForgeryToken` for aplicado nos controladores do aplicativo, ele poderá ser substituído pelo atributo `IgnoreAntiforgeryToken`.</span><span class="sxs-lookup"><span data-stu-id="14d5b-262">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="14d5b-263">ASP.NET Core não dá suporte à adição de tokens antifalsificação para obter solicitações automaticamente.</span><span class="sxs-lookup"><span data-stu-id="14d5b-263">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="14d5b-264">Validar automaticamente tokens antifalsificação somente para métodos HTTP inseguros</span><span class="sxs-lookup"><span data-stu-id="14d5b-264">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="14d5b-265">ASP.NET Core aplicativos não geram tokens antifalsificação para métodos HTTP seguros (GET, cabeçalho, opções e rastreamento).</span><span class="sxs-lookup"><span data-stu-id="14d5b-265">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="14d5b-266">Em vez de aplicar amplamente o atributo `ValidateAntiForgeryToken` e, em seguida, substituí-lo por `IgnoreAntiforgeryToken` atributos, o atributo [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="14d5b-266">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="14d5b-267">Esse atributo funciona de forma idêntica ao atributo `ValidateAntiForgeryToken`, exceto pelo fato de que ele não requer tokens para solicitações feitas usando os seguintes métodos HTTP:</span><span class="sxs-lookup"><span data-stu-id="14d5b-267">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="14d5b-268">OBTER</span><span class="sxs-lookup"><span data-stu-id="14d5b-268">GET</span></span>
* <span data-ttu-id="14d5b-269">HOME</span><span class="sxs-lookup"><span data-stu-id="14d5b-269">HEAD</span></span>
* <span data-ttu-id="14d5b-270">OPÇÕES</span><span class="sxs-lookup"><span data-stu-id="14d5b-270">OPTIONS</span></span>
* <span data-ttu-id="14d5b-271">TRACE</span><span class="sxs-lookup"><span data-stu-id="14d5b-271">TRACE</span></span>

<span data-ttu-id="14d5b-272">Recomendamos o uso de `AutoValidateAntiforgeryToken` amplamente para cenários que não são da API.</span><span class="sxs-lookup"><span data-stu-id="14d5b-272">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="14d5b-273">Isso garante que as ações de POSTAgem sejam protegidas por padrão.</span><span class="sxs-lookup"><span data-stu-id="14d5b-273">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="14d5b-274">A alternativa é ignorar os tokens antifalsificação por padrão, a menos que `ValidateAntiForgeryToken` seja aplicado a métodos de ação individuais.</span><span class="sxs-lookup"><span data-stu-id="14d5b-274">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="14d5b-275">É mais provável que esse cenário para um método POST Action seja deixado desprotegido por engano, deixando o aplicativo vulnerável a ataques CSRF.</span><span class="sxs-lookup"><span data-stu-id="14d5b-275">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="14d5b-276">Todas as postagens devem enviar o token de antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="14d5b-276">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="14d5b-277">As APIs não têm um mecanismo automático para enviar a parte não cookie do token.</span><span class="sxs-lookup"><span data-stu-id="14d5b-277">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="14d5b-278">A implementação provavelmente depende da implementação do código do cliente.</span><span class="sxs-lookup"><span data-stu-id="14d5b-278">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="14d5b-279">Alguns exemplos são mostrados abaixo:</span><span class="sxs-lookup"><span data-stu-id="14d5b-279">Some examples are shown below:</span></span>

<span data-ttu-id="14d5b-280">Exemplo de nível de classe:</span><span class="sxs-lookup"><span data-stu-id="14d5b-280">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="14d5b-281">Exemplo global:</span><span class="sxs-lookup"><span data-stu-id="14d5b-281">Global example:</span></span>

```csharp
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="14d5b-282">Substituir atributos globais ou de antifalsificação do controlador</span><span class="sxs-lookup"><span data-stu-id="14d5b-282">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="14d5b-283">O filtro [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) é usado para eliminar a necessidade de um token de antifalsificação para uma determinada ação (ou controlador).</span><span class="sxs-lookup"><span data-stu-id="14d5b-283">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="14d5b-284">Quando aplicado, esse filtro substitui `ValidateAntiForgeryToken` e `AutoValidateAntiforgeryToken` filtros especificados em um nível mais alto (globalmente ou em um controlador).</span><span class="sxs-lookup"><span data-stu-id="14d5b-284">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="14d5b-285">Atualizar tokens após a autenticação</span><span class="sxs-lookup"><span data-stu-id="14d5b-285">Refresh tokens after authentication</span></span>

<span data-ttu-id="14d5b-286">Os tokens devem ser atualizados depois que o usuário é autenticado redirecionando o usuário para uma página de exibição ou Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="14d5b-286">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="14d5b-287">JavaScript, AJAX e SPAs</span><span class="sxs-lookup"><span data-stu-id="14d5b-287">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="14d5b-288">Em aplicativos baseados em HTML tradicionais, os tokens antifalsificação são passados para o servidor usando campos de formulário ocultos.</span><span class="sxs-lookup"><span data-stu-id="14d5b-288">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="14d5b-289">Em aplicativos modernos baseados em JavaScript e SPAs, muitas solicitações são feitas programaticamente.</span><span class="sxs-lookup"><span data-stu-id="14d5b-289">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="14d5b-290">Essas solicitações AJAX podem usar outras técnicas (como cabeçalhos de solicitação ou cookies) para enviar o token.</span><span class="sxs-lookup"><span data-stu-id="14d5b-290">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="14d5b-291">Se os cookies forem usados para armazenar tokens de autenticação e autenticar solicitações de API no servidor, o CSRF será um problema potencial.</span><span class="sxs-lookup"><span data-stu-id="14d5b-291">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="14d5b-292">Se o armazenamento local for usado para armazenar o token, a vulnerabilidade CSRF poderá ser reduzida porque os valores do armazenamento local não serão enviados automaticamente para o servidor com cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="14d5b-292">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="14d5b-293">Portanto, o uso do armazenamento local para armazenar o token de antifalsificação no cliente e o envio do token como um cabeçalho de solicitação é uma abordagem recomendada.</span><span class="sxs-lookup"><span data-stu-id="14d5b-293">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="14d5b-294">JavaScript</span><span class="sxs-lookup"><span data-stu-id="14d5b-294">JavaScript</span></span>

<span data-ttu-id="14d5b-295">Usando JavaScript com exibições, o token pode ser criado usando um serviço de dentro da exibição.</span><span class="sxs-lookup"><span data-stu-id="14d5b-295">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="14d5b-296">Insira o serviço [Microsoft. AspNetCore. antifalsificating. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) na exibição e chame [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span><span class="sxs-lookup"><span data-stu-id="14d5b-296">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="14d5b-297">Essa abordagem elimina a necessidade de lidar diretamente com a configuração de cookies do servidor ou leitura deles a partir do cliente.</span><span class="sxs-lookup"><span data-stu-id="14d5b-297">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="14d5b-298">O exemplo anterior usa JavaScript para ler o valor do campo oculto para o cabeçalho de POSTAgem AJAX.</span><span class="sxs-lookup"><span data-stu-id="14d5b-298">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="14d5b-299">O JavaScript também pode acessar tokens em cookies e usar o conteúdo do cookie para criar um cabeçalho com o valor do token.</span><span class="sxs-lookup"><span data-stu-id="14d5b-299">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="14d5b-300">Supondo que as solicitações de script enviem o token em um cabeçalho chamado `X-CSRF-TOKEN`, configure o serviço antifalsificação para procurar o cabeçalho `X-CSRF-TOKEN`:</span><span class="sxs-lookup"><span data-stu-id="14d5b-300">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="14d5b-301">O exemplo a seguir usa JavaScript para fazer uma solicitação AJAX com o cabeçalho apropriado:</span><span class="sxs-lookup"><span data-stu-id="14d5b-301">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="14d5b-302">AngularJS</span><span class="sxs-lookup"><span data-stu-id="14d5b-302">AngularJS</span></span>

<span data-ttu-id="14d5b-303">O AngularJS usa uma Convenção para tratar CSRF.</span><span class="sxs-lookup"><span data-stu-id="14d5b-303">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="14d5b-304">Se o servidor enviar um cookie com o nome `XSRF-TOKEN`, o serviço AngularJS `$http` adicionará o valor de cookie a um cabeçalho ao enviar uma solicitação ao servidor.</span><span class="sxs-lookup"><span data-stu-id="14d5b-304">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="14d5b-305">Esse processo é automático.</span><span class="sxs-lookup"><span data-stu-id="14d5b-305">This process is automatic.</span></span> <span data-ttu-id="14d5b-306">O cabeçalho não precisa ser definido explicitamente no cliente.</span><span class="sxs-lookup"><span data-stu-id="14d5b-306">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="14d5b-307">O nome do cabeçalho é `X-XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="14d5b-307">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="14d5b-308">O servidor deve detectar esse cabeçalho e validar seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="14d5b-308">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="14d5b-309">Para ASP.NET Core API funcione com essa convenção na inicialização do seu aplicativo:</span><span class="sxs-lookup"><span data-stu-id="14d5b-309">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="14d5b-310">Configure seu aplicativo para fornecer um token em um cookie chamado `XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="14d5b-310">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="14d5b-311">Configure o serviço antifalsificação para procurar um cabeçalho chamado `X-XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="14d5b-311">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="14d5b-312">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="14d5b-312">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="14d5b-313">Estender antifalsificação</span><span class="sxs-lookup"><span data-stu-id="14d5b-313">Extend antiforgery</span></span>

<span data-ttu-id="14d5b-314">O tipo [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) permite que os desenvolvedores estendam o comportamento do sistema CSRF por meio da ida e volta de dados adicionais em cada token.</span><span class="sxs-lookup"><span data-stu-id="14d5b-314">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="14d5b-315">O método [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) é chamado cada vez que um token de campo é gerado e o valor de retorno é inserido no token gerado.</span><span class="sxs-lookup"><span data-stu-id="14d5b-315">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="14d5b-316">Um implementador poderia retornar um carimbo de data/hora, um nonce ou qualquer outro valor e, em seguida, chamar [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) para validar esses dados quando o token for validado.</span><span class="sxs-lookup"><span data-stu-id="14d5b-316">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="14d5b-317">O nome de usuário do cliente já está inserido nos tokens gerados, portanto, não é necessário incluir essas informações.</span><span class="sxs-lookup"><span data-stu-id="14d5b-317">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="14d5b-318">Se um token incluir dados complementares, mas nenhum `IAntiForgeryAdditionalDataProvider` estiver configurado, os dados complementares não serão validados.</span><span class="sxs-lookup"><span data-stu-id="14d5b-318">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14d5b-319">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="14d5b-319">Additional resources</span></span>

* <span data-ttu-id="14d5b-320">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) em [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span><span class="sxs-lookup"><span data-stu-id="14d5b-320">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
