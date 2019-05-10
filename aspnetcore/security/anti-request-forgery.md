---
title: Ataques de evitar entre solicitação intersite forjada (CSRF/XSRF) no ASP.NET Core
author: steve-smith
description: Descubra como evitar ataques contra aplicativos web em que um site mal-intencionado pode influenciar a interação entre um navegador cliente e o aplicativo.
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2018
uid: security/anti-request-forgery
ms.openlocfilehash: 4498bd88e7188ea6c0063aeec054bc20e13ed5f1
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086436"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="7074c-103">Ataques de evitar entre solicitação intersite forjada (CSRF/XSRF) no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7074c-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="7074c-104">Por [Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7074c-104">By [Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7074c-105">Falsificação de solicitação intersite forjada (também conhecida como XSRF ou CSRF, pronunciado *surfe consulte*) é um ataque contra aplicativos hospedados na web, no qual um aplicativo web mal-intencionado pode influenciar a interação entre um navegador cliente e um aplicativo web que confia que Navegador.</span><span class="sxs-lookup"><span data-stu-id="7074c-105">Cross-site request forgery (also known as XSRF or CSRF, pronounced *see-surf*) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="7074c-106">Esses ataques são possíveis, pois navegadores da web enviam alguns tipos de tokens de autenticação automaticamente com cada solicitação de um site.</span><span class="sxs-lookup"><span data-stu-id="7074c-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="7074c-107">Essa forma de exploração é também conhecido como um *ataque de um clique* ou *sequestro de sessão* porque o ataque aproveita o usuário do autenticado anteriormente sessão.</span><span class="sxs-lookup"><span data-stu-id="7074c-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="7074c-108">Um exemplo de um ataque CSRF:</span><span class="sxs-lookup"><span data-stu-id="7074c-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="7074c-109">Um usuário entra em `www.good-banking-site.com` usando a autenticação de formulários.</span><span class="sxs-lookup"><span data-stu-id="7074c-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="7074c-110">O servidor autentica o usuário e emite uma resposta que inclui um cookie de autenticação.</span><span class="sxs-lookup"><span data-stu-id="7074c-110">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="7074c-111">O site é vulnerável a ataques porque ele confia qualquer solicitação recebida com um cookie de autenticação válido.</span><span class="sxs-lookup"><span data-stu-id="7074c-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="7074c-112">O usuário visitar um site mal-intencionado, `www.bad-crook-site.com`.</span><span class="sxs-lookup"><span data-stu-id="7074c-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="7074c-113">O site mal-intencionado, `www.bad-crook-site.com`, contém um formulário HTML semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="7074c-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="7074c-114">Observe que o formulário `action` postagens para o site vulnerável, não para o site mal-intencionado.</span><span class="sxs-lookup"><span data-stu-id="7074c-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="7074c-115">Essa é a parte de "site cruzado" de CSRF.</span><span class="sxs-lookup"><span data-stu-id="7074c-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="7074c-116">O usuário seleciona o botão Enviar.</span><span class="sxs-lookup"><span data-stu-id="7074c-116">The user selects the submit button.</span></span> <span data-ttu-id="7074c-117">O navegador faz a solicitação e inclui automaticamente o cookie de autenticação para o domínio solicitado, `www.good-banking-site.com`.</span><span class="sxs-lookup"><span data-stu-id="7074c-117">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="7074c-118">A solicitação é executada `www.good-banking-site.com` server com o contexto de autenticação do usuário e pode executar qualquer ação que um usuário autenticado tem permissão para executar.</span><span class="sxs-lookup"><span data-stu-id="7074c-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="7074c-119">Além do cenário em que o usuário seleciona o botão para enviar o formulário, o site mal-intencionado pode:</span><span class="sxs-lookup"><span data-stu-id="7074c-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="7074c-120">Execute um script que envia automaticamente o formulário.</span><span class="sxs-lookup"><span data-stu-id="7074c-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="7074c-121">Envie o envio do formulário como uma solicitação AJAX.</span><span class="sxs-lookup"><span data-stu-id="7074c-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="7074c-122">Oculte o formulário usando CSS.</span><span class="sxs-lookup"><span data-stu-id="7074c-122">Hide the form using CSS.</span></span>

<span data-ttu-id="7074c-123">Esses cenários alternativos não exigem qualquer entrada do usuário que não sejam inicialmente visitar o site mal-intencionado ou ação.</span><span class="sxs-lookup"><span data-stu-id="7074c-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="7074c-124">Usar o HTTPS não impede que um ataque CSRF.</span><span class="sxs-lookup"><span data-stu-id="7074c-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="7074c-125">O site mal-intencionado pode enviar um `https://www.good-banking-site.com/` solicitar tão fácil quanto ele pode enviar uma solicitação não segura.</span><span class="sxs-lookup"><span data-stu-id="7074c-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="7074c-126">Alguns ataques são direcionados a pontos de extremidade que respondem às solicitações GET, neste caso, uma marca de imagem pode ser usada para executar a ação.</span><span class="sxs-lookup"><span data-stu-id="7074c-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="7074c-127">Essa forma de ataque é comum nos sites de Fórum que permitem imagens, mas bloqueiam o JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7074c-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="7074c-128">Aplicativos que alteram o estado em solicitações GET, em que as variáveis ou os recursos são alterados, são vulneráveis a ataques mal-intencionados.</span><span class="sxs-lookup"><span data-stu-id="7074c-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="7074c-129">**Solicitações GET que alteram o estado são inseguras. Uma prática recomendada é nunca alterar o estado em uma solicitação GET.**</span><span class="sxs-lookup"><span data-stu-id="7074c-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="7074c-130">Ataques de CSRF são possíveis em relação a aplicativos web que usam cookies para autenticação porque:</span><span class="sxs-lookup"><span data-stu-id="7074c-130">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="7074c-131">Os navegadores armazenam os cookies emitidos por um aplicativo web.</span><span class="sxs-lookup"><span data-stu-id="7074c-131">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="7074c-132">Armazenado cookies incluem cookies de sessão para usuários autenticados.</span><span class="sxs-lookup"><span data-stu-id="7074c-132">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="7074c-133">Os navegadores enviam a que todos os cookies associados com um domínio para o aplicativo web cada solicitação, independentemente de como a solicitação para o aplicativo foi gerada dentro do navegador.</span><span class="sxs-lookup"><span data-stu-id="7074c-133">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="7074c-134">No entanto, os ataques de CSRF não estão limitadas a exploração de cookies.</span><span class="sxs-lookup"><span data-stu-id="7074c-134">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="7074c-135">Por exemplo, a autenticação básica e Digest também são vulneráveis.</span><span class="sxs-lookup"><span data-stu-id="7074c-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="7074c-136">Depois que um usuário entra com a autenticação básica ou Digest, o navegador automaticamente envia as credenciais até que a sessão&dagger; termina.</span><span class="sxs-lookup"><span data-stu-id="7074c-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="7074c-137">&dagger;Nesse contexto, *sessão* refere-se à sessão do lado do cliente durante o qual o usuário é autenticado.</span><span class="sxs-lookup"><span data-stu-id="7074c-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="7074c-138">Ele é não relacionada a sessões do lado do servidor ou [Middleware de sessão do ASP.NET Core](xref:fundamentals/app-state).</span><span class="sxs-lookup"><span data-stu-id="7074c-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="7074c-139">Os usuários podem se proteger contra vulnerabilidades CSRF tomando precauções:</span><span class="sxs-lookup"><span data-stu-id="7074c-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="7074c-140">Entrar em aplicativos web quando terminar de usá-los.</span><span class="sxs-lookup"><span data-stu-id="7074c-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="7074c-141">Limpar os cookies do navegador periodicamente.</span><span class="sxs-lookup"><span data-stu-id="7074c-141">Clear browser cookies periodically.</span></span>

<span data-ttu-id="7074c-142">No entanto, as vulnerabilidades CSRF são basicamente um problema com o aplicativo web, não pelo usuário final.</span><span class="sxs-lookup"><span data-stu-id="7074c-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="7074c-143">Conceitos básicos de autenticação</span><span class="sxs-lookup"><span data-stu-id="7074c-143">Authentication fundamentals</span></span>

<span data-ttu-id="7074c-144">Autenticação baseada em cookie é uma forma popular de autenticação.</span><span class="sxs-lookup"><span data-stu-id="7074c-144">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="7074c-145">Sistemas de autenticação baseada em token estão crescendo em termos de popularidade, especialmente para aplicativos de página única (SPAs).</span><span class="sxs-lookup"><span data-stu-id="7074c-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="7074c-146">Autenticação baseada em cookie</span><span class="sxs-lookup"><span data-stu-id="7074c-146">Cookie-based authentication</span></span>

<span data-ttu-id="7074c-147">Quando um usuário é autenticado usando o nome de usuário e senha, elas emitidas um token, que contém um tíquete de autenticação que pode ser usado para autenticação e autorização.</span><span class="sxs-lookup"><span data-stu-id="7074c-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="7074c-148">O token é armazenado como um cookie que acompanha cada solicitação, o cliente faz.</span><span class="sxs-lookup"><span data-stu-id="7074c-148">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="7074c-149">Gerar e validar esse cookie é realizada pelo Middleware de autenticação de Cookie.</span><span class="sxs-lookup"><span data-stu-id="7074c-149">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="7074c-150">O [middleware](xref:fundamentals/middleware/index) serializa uma entidade de usuário em um cookie criptografado.</span><span class="sxs-lookup"><span data-stu-id="7074c-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="7074c-151">Nas próximas solicitações, o middleware valida o cookie, recria a entidade de segurança e a atribui a entidade de segurança de [usuário](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) propriedade de [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span><span class="sxs-lookup"><span data-stu-id="7074c-151">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="7074c-152">Autenticação baseada em token</span><span class="sxs-lookup"><span data-stu-id="7074c-152">Token-based authentication</span></span>

<span data-ttu-id="7074c-153">Quando um usuário é autenticado, elas emitidas um token (não um token antifalsificação).</span><span class="sxs-lookup"><span data-stu-id="7074c-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="7074c-154">O token contém informações de usuário na forma de [declarações](/dotnet/framework/security/claims-based-identity-model) ou um token de referência que aponta o aplicativo para o estado do usuário mantido no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7074c-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="7074c-155">Quando um usuário tenta acessar um recurso que requer autenticação, o token é enviado para o aplicativo com um cabeçalho de autorização adicionais na forma de token de portador.</span><span class="sxs-lookup"><span data-stu-id="7074c-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="7074c-156">Isso torna o aplicativo sem monitoração de estado.</span><span class="sxs-lookup"><span data-stu-id="7074c-156">This makes the app stateless.</span></span> <span data-ttu-id="7074c-157">Em cada solicitação subsequente, o token é passado na solicitação para a validação do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="7074c-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="7074c-158">Não é esse token *criptografado*; ele tem *codificado*.</span><span class="sxs-lookup"><span data-stu-id="7074c-158">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="7074c-159">No servidor, o token é decodificado para acessar suas informações.</span><span class="sxs-lookup"><span data-stu-id="7074c-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="7074c-160">Para enviar o token em solicitações subsequentes, armazene o token no armazenamento local do navegador.</span><span class="sxs-lookup"><span data-stu-id="7074c-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="7074c-161">Não fique preocupado sobre vulnerabilidade CSRF, se o token é armazenado no armazenamento local do navegador.</span><span class="sxs-lookup"><span data-stu-id="7074c-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="7074c-162">CSRF é uma preocupação quando o token é armazenado em um cookie.</span><span class="sxs-lookup"><span data-stu-id="7074c-162">CSRF is a concern when the token is stored in a cookie.</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="7074c-163">Vários aplicativos hospedados em um domínio</span><span class="sxs-lookup"><span data-stu-id="7074c-163">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="7074c-164">Ambientes de hospedagem compartilhados são vulneráveis a sequestro de sessão, login CSRF e outros ataques.</span><span class="sxs-lookup"><span data-stu-id="7074c-164">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="7074c-165">Embora `example1.contoso.net` e `example2.contoso.net` são hosts diferentes, há uma relação de confiança implícita entre os hosts sob o `*.contoso.net` domínio.</span><span class="sxs-lookup"><span data-stu-id="7074c-165">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="7074c-166">Essa relação de confiança implícita permite que os hosts potencialmente não confiáveis afetam uns dos outros cookies (as políticas de mesma origem que regem as solicitações AJAX não necessariamente se aplicam aos cookies HTTP).</span><span class="sxs-lookup"><span data-stu-id="7074c-166">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="7074c-167">Ataques que exploram confiáveis cookies entre aplicativos hospedados no mesmo domínio podem ser evitadas por meio do compartilhamento não domínios.</span><span class="sxs-lookup"><span data-stu-id="7074c-167">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="7074c-168">Quando cada aplicativo é hospedado em seu próprio domínio, não há nenhuma relação de confiança implícita de cookie para explorar.</span><span class="sxs-lookup"><span data-stu-id="7074c-168">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="7074c-169">Configuração antifalsificação do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7074c-169">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="7074c-170">ASP.NET Core implementa usando antifalsificação [proteção de dados do ASP.NET Core](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="7074c-170">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="7074c-171">A pilha da proteção de dados deve ser configurada para funcionar em um farm de servidores.</span><span class="sxs-lookup"><span data-stu-id="7074c-171">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="7074c-172">Ver [Configurando a proteção de dados](xref:security/data-protection/configuration/overview) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="7074c-172">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

<span data-ttu-id="7074c-173">No ASP.NET Core 2.0 ou posterior, o [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injeta tokens antifalsificação em elementos de formulário HTML.</span><span class="sxs-lookup"><span data-stu-id="7074c-173">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="7074c-174">A marcação a seguir em um arquivo Razor gera automaticamente os tokens antifalsificação:</span><span class="sxs-lookup"><span data-stu-id="7074c-174">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="7074c-175">Da mesma forma, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) gera tokens antifalsificação por padrão, se o método do formulário não é GET.</span><span class="sxs-lookup"><span data-stu-id="7074c-175">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="7074c-176">A geração automática de tokens antifalsificação para elementos de formulário HTML acontece quando o `<form>` marca contém o `method="post"` atributo e uma das opções a seguir forem verdadeira:</span><span class="sxs-lookup"><span data-stu-id="7074c-176">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="7074c-177">O atributo action está vazio (`action=""`).</span><span class="sxs-lookup"><span data-stu-id="7074c-177">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="7074c-178">O atributo de ação não for fornecido (`<form method="post">`).</span><span class="sxs-lookup"><span data-stu-id="7074c-178">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="7074c-179">Geração automática de tokens antifalsificação para elementos de formulário HTML pode ser desabilitada:</span><span class="sxs-lookup"><span data-stu-id="7074c-179">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="7074c-180">Desabilitar explicitamente tokens antifalsificação com o `asp-antiforgery` atributo:</span><span class="sxs-lookup"><span data-stu-id="7074c-180">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="7074c-181">O elemento de formulário é aceito-out dos auxiliares de marca usando o auxiliar de marca [! recusar símbolo](xref:mvc/views/tag-helpers/intro#opt-out):</span><span class="sxs-lookup"><span data-stu-id="7074c-181">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="7074c-182">Remover o `FormTagHelper` da exibição.</span><span class="sxs-lookup"><span data-stu-id="7074c-182">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="7074c-183">O `FormTagHelper` pode ser removido de uma exibição, adicionando a seguinte diretiva para o modo de exibição do Razor:</span><span class="sxs-lookup"><span data-stu-id="7074c-183">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="7074c-184">[Páginas do Razor](xref:razor-pages/index) são protegidos automaticamente contra XSRF/CSRF.</span><span class="sxs-lookup"><span data-stu-id="7074c-184">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="7074c-185">Para obter mais informações, consulte [XSRF/CSRF e páginas Razor](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="7074c-185">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="7074c-186">A abordagem mais comum para proteger contra ataques CSRF é usar o *padrão de Token do sincronizador* (STP).</span><span class="sxs-lookup"><span data-stu-id="7074c-186">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="7074c-187">STP é usado quando o usuário solicita uma página com os dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="7074c-187">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="7074c-188">O servidor envia um token associado com a atual identidade do usuário para o cliente.</span><span class="sxs-lookup"><span data-stu-id="7074c-188">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="7074c-189">O cliente envia o token para o servidor para verificação.</span><span class="sxs-lookup"><span data-stu-id="7074c-189">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="7074c-190">Se o servidor recebe um token que não corresponde à identidade do usuário autenticado, a solicitação será rejeitada.</span><span class="sxs-lookup"><span data-stu-id="7074c-190">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="7074c-191">O token é exclusivo e imprevisíveis.</span><span class="sxs-lookup"><span data-stu-id="7074c-191">The token is unique and unpredictable.</span></span> <span data-ttu-id="7074c-192">O token também pode ser usado para garantir que o sequenciamento adequado de uma série de solicitações (por exemplo, garantindo a sequência de solicitação de: a página 1 &ndash; página 2 &ndash; página 3).</span><span class="sxs-lookup"><span data-stu-id="7074c-192">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 &ndash; page 2 &ndash; page 3).</span></span> <span data-ttu-id="7074c-193">Todos os formulários em modelos do ASP.NET Core MVC e páginas do Razor geram tokens contra falsificação.</span><span class="sxs-lookup"><span data-stu-id="7074c-193">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="7074c-194">O seguinte par de exemplos do modo de exibição gera tokens contra falsificação:</span><span class="sxs-lookup"><span data-stu-id="7074c-194">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="7074c-195">Adicionar explicitamente um token antifalsificação para um `<form>` elemento sem o uso de auxiliares de marca com o auxiliar HTML [ @Html.AntiForgeryToken ](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span><span class="sxs-lookup"><span data-stu-id="7074c-195">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [@Html.AntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="7074c-196">Em cada um dos casos anteriores, o ASP.NET Core adiciona um campo de formulário oculto semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="7074c-196">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="7074c-197">ASP.NET Core inclui três [filtros](xref:mvc/controllers/filters) para trabalhar com tokens antifalsificação:</span><span class="sxs-lookup"><span data-stu-id="7074c-197">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="7074c-198">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="7074c-198">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="7074c-199">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="7074c-199">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="7074c-200">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="7074c-200">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="7074c-201">Opções de antifalsificação</span><span class="sxs-lookup"><span data-stu-id="7074c-201">Antiforgery options</span></span>

<span data-ttu-id="7074c-202">Personalize [opções antifalsificação](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7074c-202">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="7074c-203">&dagger;Definir a antifalsificação `Cookie` usando as propriedades do objeto de propriedades de [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) classe.</span><span class="sxs-lookup"><span data-stu-id="7074c-203">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="7074c-204">Opção</span><span class="sxs-lookup"><span data-stu-id="7074c-204">Option</span></span> | <span data-ttu-id="7074c-205">Descrição</span><span class="sxs-lookup"><span data-stu-id="7074c-205">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="7074c-206">Cookie</span><span class="sxs-lookup"><span data-stu-id="7074c-206">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="7074c-207">Determina as configurações usadas para criar o cookie antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="7074c-207">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="7074c-208">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="7074c-208">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="7074c-209">O nome do campo de formulário oculto usado pelo sistema antifalsificação para processar tokens antifalsificação nos modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="7074c-209">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="7074c-210">HeaderName</span><span class="sxs-lookup"><span data-stu-id="7074c-210">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="7074c-211">O nome do cabeçalho usado pelo sistema antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="7074c-211">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="7074c-212">Se `null`, o sistema considera apenas os dados de formulário.</span><span class="sxs-lookup"><span data-stu-id="7074c-212">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="7074c-213">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="7074c-213">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="7074c-214">Especifica se deve suprimir a geração do `X-Frame-Options` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="7074c-214">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="7074c-215">Por padrão, o cabeçalho é gerado com um valor de "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="7074c-215">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="7074c-216">Assume o padrão de `false`.</span><span class="sxs-lookup"><span data-stu-id="7074c-216">Defaults to `false`.</span></span> |

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

| <span data-ttu-id="7074c-217">Opção</span><span class="sxs-lookup"><span data-stu-id="7074c-217">Option</span></span> | <span data-ttu-id="7074c-218">Descrição</span><span class="sxs-lookup"><span data-stu-id="7074c-218">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="7074c-219">Cookie</span><span class="sxs-lookup"><span data-stu-id="7074c-219">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="7074c-220">Determina as configurações usadas para criar o cookie antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="7074c-220">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="7074c-221">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="7074c-221">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | <span data-ttu-id="7074c-222">O domínio do cookie.</span><span class="sxs-lookup"><span data-stu-id="7074c-222">The domain of the cookie.</span></span> <span data-ttu-id="7074c-223">Assume o padrão de `null`.</span><span class="sxs-lookup"><span data-stu-id="7074c-223">Defaults to `null`.</span></span> <span data-ttu-id="7074c-224">Essa propriedade está obsoleta e será removida em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="7074c-224">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="7074c-225">A alternativa recomendada é Cookie.Domain.</span><span class="sxs-lookup"><span data-stu-id="7074c-225">The recommended alternative is Cookie.Domain.</span></span> |
| [<span data-ttu-id="7074c-226">CookieName</span><span class="sxs-lookup"><span data-stu-id="7074c-226">CookieName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | <span data-ttu-id="7074c-227">O nome do cookie.</span><span class="sxs-lookup"><span data-stu-id="7074c-227">The name of the cookie.</span></span> <span data-ttu-id="7074c-228">Se não definido, o sistema gera um nome exclusivo que começa com o [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore.Antiforgery.").</span><span class="sxs-lookup"><span data-stu-id="7074c-228">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="7074c-229">Essa propriedade está obsoleta e será removida em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="7074c-229">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="7074c-230">A alternativa recomendada é Cookie.Name.</span><span class="sxs-lookup"><span data-stu-id="7074c-230">The recommended alternative is Cookie.Name.</span></span> |
| [<span data-ttu-id="7074c-231">CookiePath</span><span class="sxs-lookup"><span data-stu-id="7074c-231">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | <span data-ttu-id="7074c-232">O caminho definido no cookie.</span><span class="sxs-lookup"><span data-stu-id="7074c-232">The path set on the cookie.</span></span> <span data-ttu-id="7074c-233">Essa propriedade está obsoleta e será removida em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="7074c-233">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="7074c-234">A alternativa recomendada é Cookie.Path.</span><span class="sxs-lookup"><span data-stu-id="7074c-234">The recommended alternative is Cookie.Path.</span></span> |
| [<span data-ttu-id="7074c-235">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="7074c-235">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="7074c-236">O nome do campo de formulário oculto usado pelo sistema antifalsificação para processar tokens antifalsificação nos modos de exibição.</span><span class="sxs-lookup"><span data-stu-id="7074c-236">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="7074c-237">HeaderName</span><span class="sxs-lookup"><span data-stu-id="7074c-237">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="7074c-238">O nome do cabeçalho usado pelo sistema antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="7074c-238">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="7074c-239">Se `null`, o sistema considera apenas os dados de formulário.</span><span class="sxs-lookup"><span data-stu-id="7074c-239">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="7074c-240">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="7074c-240">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="7074c-241">Especifica se o HTTPS é exigido pelo sistema antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="7074c-241">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="7074c-242">Se `true`, não HTTPS solicitações falham.</span><span class="sxs-lookup"><span data-stu-id="7074c-242">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="7074c-243">Assume o padrão de `false`.</span><span class="sxs-lookup"><span data-stu-id="7074c-243">Defaults to `false`.</span></span> <span data-ttu-id="7074c-244">Essa propriedade está obsoleta e será removida em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="7074c-244">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="7074c-245">A alternativa recomendada é definir Cookie.SecurePolicy.</span><span class="sxs-lookup"><span data-stu-id="7074c-245">The recommended alternative is to set Cookie.SecurePolicy.</span></span> |
| [<span data-ttu-id="7074c-246">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="7074c-246">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="7074c-247">Especifica se deve suprimir a geração do `X-Frame-Options` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="7074c-247">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="7074c-248">Por padrão, o cabeçalho é gerado com um valor de "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="7074c-248">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="7074c-249">Assume o padrão de `false`.</span><span class="sxs-lookup"><span data-stu-id="7074c-249">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="7074c-250">Para obter mais informações, consulte [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="7074c-250">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="7074c-251">Configurar recursos antiforgery com IAntiforgery</span><span class="sxs-lookup"><span data-stu-id="7074c-251">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="7074c-252">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) fornece a API para configurar recursos antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="7074c-252">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="7074c-253">`IAntiforgery` podem ser solicitadas a `Configure` método da `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="7074c-253">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="7074c-254">O exemplo a seguir usa o middleware da home page do aplicativo para gerar um token antifalsificação e enviá-lo na resposta como um cookie (usando a convenção de nomenclatura Angular da padrão descrita mais adiante neste tópico):</span><span class="sxs-lookup"><span data-stu-id="7074c-254">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

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

### <a name="require-antiforgery-validation"></a><span data-ttu-id="7074c-255">Exigir validação antifalsificação</span><span class="sxs-lookup"><span data-stu-id="7074c-255">Require antiforgery validation</span></span>

<span data-ttu-id="7074c-256">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) é um filtro de ação que pode ser aplicado a uma ação individual, um controlador ou globalmente.</span><span class="sxs-lookup"><span data-stu-id="7074c-256">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="7074c-257">As solicitações feitas a ações que têm esse filtro aplicado são bloqueadas, a menos que a solicitação inclui um token antifalsificação válido.</span><span class="sxs-lookup"><span data-stu-id="7074c-257">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

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

<span data-ttu-id="7074c-258">O `ValidateAntiForgeryToken` atributo requer um token para solicitações para os métodos de ação decora, incluindo as solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="7074c-258">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it decorates, including HTTP GET requests.</span></span> <span data-ttu-id="7074c-259">Se o `ValidateAntiForgeryToken` atributo é aplicado em controladores do aplicativo, ele pode ser substituído com o `IgnoreAntiforgeryToken` atributo.</span><span class="sxs-lookup"><span data-stu-id="7074c-259">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="7074c-260">ASP.NET Core não dá suporte a adição de tokens antifalsificação para solicitações GET automaticamente.</span><span class="sxs-lookup"><span data-stu-id="7074c-260">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="7074c-261">Automaticamente validar tokens antifalsificação para métodos HTTP não seguros apenas</span><span class="sxs-lookup"><span data-stu-id="7074c-261">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="7074c-262">Aplicativos ASP.NET Core não geram tokens contra falsificação para métodos HTTP seguros (GET, HEAD, opções e rastreamento).</span><span class="sxs-lookup"><span data-stu-id="7074c-262">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="7074c-263">Em vez de aplicar amplamente a `ValidateAntiForgeryToken` atributo e, em seguida, substituindo-o com `IgnoreAntiforgeryToken` atributos, o [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) atributo pode ser usado.</span><span class="sxs-lookup"><span data-stu-id="7074c-263">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="7074c-264">Esse atributo funciona de maneira idêntica ao `ValidateAntiForgeryToken` de atributo, exceto que ele não exige tokens para solicitações feitas usando os seguintes métodos HTTP:</span><span class="sxs-lookup"><span data-stu-id="7074c-264">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="7074c-265">OBTER</span><span class="sxs-lookup"><span data-stu-id="7074c-265">GET</span></span>
* <span data-ttu-id="7074c-266">HOME</span><span class="sxs-lookup"><span data-stu-id="7074c-266">HEAD</span></span>
* <span data-ttu-id="7074c-267">OPÇÕES</span><span class="sxs-lookup"><span data-stu-id="7074c-267">OPTIONS</span></span>
* <span data-ttu-id="7074c-268">TRACE</span><span class="sxs-lookup"><span data-stu-id="7074c-268">TRACE</span></span>

<span data-ttu-id="7074c-269">É recomendável o uso de `AutoValidateAntiforgeryToken` em larga escala para cenários de não-API.</span><span class="sxs-lookup"><span data-stu-id="7074c-269">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="7074c-270">Isso garante que as ações de POSTAGEM são protegidas por padrão.</span><span class="sxs-lookup"><span data-stu-id="7074c-270">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="7074c-271">A alternativa é ignorar tokens antifalsificação por padrão, a menos que `ValidateAntiForgeryToken` é aplicado a métodos de ação individual.</span><span class="sxs-lookup"><span data-stu-id="7074c-271">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="7074c-272">Ele tem mais provavelmente neste cenário para um método de ação a ser deixado POST desprotegidos por engano, deixando o aplicativo vulnerável a ataques CSRF.</span><span class="sxs-lookup"><span data-stu-id="7074c-272">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="7074c-273">Todas as postagens devem enviar o token antifalsificação.</span><span class="sxs-lookup"><span data-stu-id="7074c-273">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="7074c-274">APIs não têm um mecanismo automático para enviar a parte não-cookie do token.</span><span class="sxs-lookup"><span data-stu-id="7074c-274">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="7074c-275">A implementação provavelmente depende a implementação de código do cliente.</span><span class="sxs-lookup"><span data-stu-id="7074c-275">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="7074c-276">Alguns exemplos são mostrados abaixo:</span><span class="sxs-lookup"><span data-stu-id="7074c-276">Some examples are shown below:</span></span>

<span data-ttu-id="7074c-277">Exemplo de nível de classe:</span><span class="sxs-lookup"><span data-stu-id="7074c-277">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="7074c-278">Exemplo global:</span><span class="sxs-lookup"><span data-stu-id="7074c-278">Global example:</span></span>

```csharp
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="7074c-279">Substituição global ou atributos antifalsificação do controlador</span><span class="sxs-lookup"><span data-stu-id="7074c-279">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="7074c-280">O [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filtro é usado para eliminar a necessidade de um token antifalsificação para uma determinada ação (ou controlador).</span><span class="sxs-lookup"><span data-stu-id="7074c-280">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="7074c-281">Quando aplicado, esse filtro substitui `ValidateAntiForgeryToken` e `AutoValidateAntiforgeryToken` filtros especificados em um nível mais alto (globalmente ou em um controlador).</span><span class="sxs-lookup"><span data-stu-id="7074c-281">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

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

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="7074c-282">Tokens de atualização após a autenticação</span><span class="sxs-lookup"><span data-stu-id="7074c-282">Refresh tokens after authentication</span></span>

<span data-ttu-id="7074c-283">Tokens devem ser atualizados depois que o usuário é autenticado ao redirecionar o usuário para uma exibição ou página do Razor.</span><span class="sxs-lookup"><span data-stu-id="7074c-283">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="7074c-284">JavaScript, AJAX e SPAs</span><span class="sxs-lookup"><span data-stu-id="7074c-284">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="7074c-285">Em aplicativos tradicionais baseados em HTML, tokens antifalsificação são passados para o servidor usando os campos de formulário oculto.</span><span class="sxs-lookup"><span data-stu-id="7074c-285">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="7074c-286">Em aplicativos modernos baseados em JavaScript e SPAs, muitas solicitações são feitas por meio de programação.</span><span class="sxs-lookup"><span data-stu-id="7074c-286">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="7074c-287">Essas solicitações AJAX podem usar outras técnicas (como cabeçalhos de solicitação ou cookies) para enviar o token.</span><span class="sxs-lookup"><span data-stu-id="7074c-287">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="7074c-288">Se os cookies são usados para armazenar tokens de autenticação e para autenticar solicitações de API no servidor, CSRF é um problema potencial.</span><span class="sxs-lookup"><span data-stu-id="7074c-288">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="7074c-289">Se o armazenamento local é usado para armazenar o token, vulnerabilidade CSRF pode ser reduzida porque os valores do armazenamento local não são enviadas automaticamente para o servidor com cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="7074c-289">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="7074c-290">Dessa forma, usando o armazenamento local para armazenar o token antifalsificação no cliente e enviar o token como um cabeçalho de solicitação é uma abordagem recomendada.</span><span class="sxs-lookup"><span data-stu-id="7074c-290">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="7074c-291">JavaScript</span><span class="sxs-lookup"><span data-stu-id="7074c-291">JavaScript</span></span>

<span data-ttu-id="7074c-292">Usando o JavaScript com modos de exibição, o token pode ser criado usando um serviço de dentro da exibição.</span><span class="sxs-lookup"><span data-stu-id="7074c-292">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="7074c-293">Injetar o [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) serviço para o modo de exibição e chame [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span><span class="sxs-lookup"><span data-stu-id="7074c-293">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="7074c-294">Essa abordagem elimina a necessidade de lidar diretamente com definir cookies a partir do servidor ou lê-los a partir do cliente.</span><span class="sxs-lookup"><span data-stu-id="7074c-294">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="7074c-295">O exemplo anterior usa JavaScript para ler o valor do campo oculto para o cabeçalho de POSTAGEM de AJAX.</span><span class="sxs-lookup"><span data-stu-id="7074c-295">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="7074c-296">JavaScript pode também tokens de acesso em cookies e usar o conteúdo do cookie para criar um cabeçalho com o valor do token.</span><span class="sxs-lookup"><span data-stu-id="7074c-296">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="7074c-297">Supondo que o script solicita para enviar o token em um cabeçalho chamado `X-CSRF-TOKEN`, configure o serviço antifalsificação para procurar o `X-CSRF-TOKEN` cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="7074c-297">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="7074c-298">O exemplo a seguir usa JavaScript para fazer uma solicitação AJAX com o cabeçalho apropriado:</span><span class="sxs-lookup"><span data-stu-id="7074c-298">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

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

### <a name="angularjs"></a><span data-ttu-id="7074c-299">AngularJS</span><span class="sxs-lookup"><span data-stu-id="7074c-299">AngularJS</span></span>

<span data-ttu-id="7074c-300">AngularJS usa uma convenção para endereço CSRF.</span><span class="sxs-lookup"><span data-stu-id="7074c-300">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="7074c-301">Se o servidor envia um cookie com o nome `XSRF-TOKEN`, o AngularJS `$http` serviço adiciona o valor do cookie a um cabeçalho quando ele envia uma solicitação ao servidor.</span><span class="sxs-lookup"><span data-stu-id="7074c-301">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="7074c-302">Esse processo é automático.</span><span class="sxs-lookup"><span data-stu-id="7074c-302">This process is automatic.</span></span> <span data-ttu-id="7074c-303">O cabeçalho não precisa ser definido explicitamente no cliente.</span><span class="sxs-lookup"><span data-stu-id="7074c-303">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="7074c-304">O nome do cabeçalho é `X-XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="7074c-304">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="7074c-305">O servidor deve detectar esse cabeçalho e validar seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="7074c-305">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="7074c-306">API do ASP.NET Core trabalhar com essa convenção na inicialização do seu aplicativo:</span><span class="sxs-lookup"><span data-stu-id="7074c-306">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="7074c-307">Configurar seu aplicativo para fornecer um token em um cookie chamado `XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="7074c-307">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="7074c-308">Configurar o serviço antifalsificação para procurar por um cabeçalho chamado `X-XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="7074c-308">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

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

<span data-ttu-id="7074c-309">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7074c-309">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="7074c-310">Estender antifalsificação</span><span class="sxs-lookup"><span data-stu-id="7074c-310">Extend antiforgery</span></span>

<span data-ttu-id="7074c-311">O [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) tipo permite aos desenvolvedores estender o comportamento do sistema anti-CSRF por dados adicionais de ciclo completo em cada token.</span><span class="sxs-lookup"><span data-stu-id="7074c-311">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="7074c-312">O [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) método é chamado sempre que um token de campo é gerado e o valor de retorno é incorporado dentro do token gerado.</span><span class="sxs-lookup"><span data-stu-id="7074c-312">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="7074c-313">Um implementador poderia retornar um carimbo de hora, um nonce ou qualquer outro valor e, em seguida, chame [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) para validar dados quando o token é validado.</span><span class="sxs-lookup"><span data-stu-id="7074c-313">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="7074c-314">Nome de usuário do cliente já está incorporado em tokens gerados, portanto, não há nenhuma necessidade de incluir essas informações.</span><span class="sxs-lookup"><span data-stu-id="7074c-314">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="7074c-315">Se um token inclui dados complementares, mas não `IAntiForgeryAdditionalDataProvider` é configurado, os dados complementares não são validados.</span><span class="sxs-lookup"><span data-stu-id="7074c-315">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7074c-316">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7074c-316">Additional resources</span></span>

* <span data-ttu-id="7074c-317">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) na [Abrir Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span><span class="sxs-lookup"><span data-stu-id="7074c-317">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
