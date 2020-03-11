---
title: Trabalhar com cookies SameSite no ASP.NET Core
author: rick-anderson
description: Saiba como usar o para SameSite cookies no ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Electron
uid: security/samesite
ms.openlocfilehash: eeba2c4403d33312692ed187021a125c22df5d08
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667737"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="84d47-103">Trabalhar com cookies SameSite no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84d47-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="84d47-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="84d47-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="84d47-105">SameSite é um padrão de rascunho de [IETF](https://ietf.org/about/) projetado para fornecer alguma proteção contra ataques CSRF (solicitação entre sites forjada).</span><span class="sxs-lookup"><span data-stu-id="84d47-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="84d47-106">Originalmente rascunho em [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), o rascunho padrão foi atualizado em [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span><span class="sxs-lookup"><span data-stu-id="84d47-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="84d47-107">O padrão atualizado não é compatível com versões anteriores com o padrão anterior, com as diferenças mais perceptíveis a seguir:</span><span class="sxs-lookup"><span data-stu-id="84d47-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="84d47-108">Cookies sem cabeçalho SameSite são tratados como `SameSite=Lax` por padrão.</span><span class="sxs-lookup"><span data-stu-id="84d47-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="84d47-109">`SameSite=None` deve ser usado para permitir o uso de cookies entre sites.</span><span class="sxs-lookup"><span data-stu-id="84d47-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="84d47-110">Os cookies que afirmam `SameSite=None` também devem ser marcados como `Secure`.</span><span class="sxs-lookup"><span data-stu-id="84d47-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="84d47-111">Os aplicativos que usam [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) podem ter problemas com `sameSite=Lax` ou `sameSite=Strict` cookies porque `<iframe>` é tratado como cenários entre sites.</span><span class="sxs-lookup"><span data-stu-id="84d47-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="84d47-112">O valor `SameSite=None` não é permitido pelo [padrão 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) e faz com que algumas implementações tratem cookies como `SameSite=Strict`.</span><span class="sxs-lookup"><span data-stu-id="84d47-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="84d47-113">Consulte [suporte a navegadores mais antigos](#sob) neste documento.</span><span class="sxs-lookup"><span data-stu-id="84d47-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="84d47-114">A configuração `SameSite=Lax` funciona para a maioria dos cookies de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="84d47-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="84d47-115">Algumas formas de autenticação, como o [OpenID Connect](https://openid.net/connect/) (OIDC) e o [WS-Federation,](https://auth0.com/docs/protocols/ws-fed) assumem o padrão de redirecionamentos baseados em post.</span><span class="sxs-lookup"><span data-stu-id="84d47-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="84d47-116">Os redirecionamentos baseados em POST disparam as proteções do navegador SameSite, portanto, o SameSite está desabilitado para esses componentes.</span><span class="sxs-lookup"><span data-stu-id="84d47-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="84d47-117">A maioria dos logons [OAuth](https://oauth.net/) não são afetados devido a diferenças na forma como os fluxos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="84d47-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="84d47-118">Cada componente ASP.NET Core que emite cookies precisa decidir se SameSite é apropriado.</span><span class="sxs-lookup"><span data-stu-id="84d47-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-test-sample-code"></a><span data-ttu-id="84d47-119">Código de exemplo de teste do SameSite</span><span class="sxs-lookup"><span data-stu-id="84d47-119">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="84d47-120">Os seguintes exemplos podem ser baixados e testados:</span><span class="sxs-lookup"><span data-stu-id="84d47-120">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="84d47-121">Amostra</span><span class="sxs-lookup"><span data-stu-id="84d47-121">Sample</span></span>               | <span data-ttu-id="84d47-122">Documento</span><span class="sxs-lookup"><span data-stu-id="84d47-122">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="84d47-123">MVC do .NET Core</span><span class="sxs-lookup"><span data-stu-id="84d47-123">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [<span data-ttu-id="84d47-124">Razor Pages do .NET Core</span><span class="sxs-lookup"><span data-stu-id="84d47-124">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="84d47-125">O exemplo a seguir pode ser baixado e testado:</span><span class="sxs-lookup"><span data-stu-id="84d47-125">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="84d47-126">Amostra</span><span class="sxs-lookup"><span data-stu-id="84d47-126">Sample</span></span>               | <span data-ttu-id="84d47-127">Documento</span><span class="sxs-lookup"><span data-stu-id="84d47-127">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="84d47-128">Razor Pages do .NET Core</span><span class="sxs-lookup"><span data-stu-id="84d47-128">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="84d47-129">Suporte do .NET Core para o atributo sameSite</span><span class="sxs-lookup"><span data-stu-id="84d47-129">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="84d47-130">O .NET Core 2,2 dá suporte ao padrão 2019 de rascunho para SameSite desde o lançamento das atualizações em dezembro de 2019.</span><span class="sxs-lookup"><span data-stu-id="84d47-130">.NET Core 2.2 supports the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="84d47-131">Os desenvolvedores podem controlar programaticamente o valor do atributo sameSite usando a propriedade `HttpCookie.SameSite`.</span><span class="sxs-lookup"><span data-stu-id="84d47-131">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="84d47-132">Definir a propriedade `SameSite` como Strict, Lax ou None resulta na gravação desses valores na rede com o cookie.</span><span class="sxs-lookup"><span data-stu-id="84d47-132">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="84d47-133">Defini-lo como igual a (SameSiteMode) (-1) indica que nenhum atributo sameSite deve ser incluído na rede com o cookie</span><span class="sxs-lookup"><span data-stu-id="84d47-133">Setting it equal to (SameSiteMode)(-1) indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="84d47-134">O .NET Core 3,0 dá suporte aos valores SameSite atualizados e adiciona um valor de enumeração extra, `SameSiteMode.Unspecified` à enumeração `SameSiteMode`.</span><span class="sxs-lookup"><span data-stu-id="84d47-134">.NET Core 3.0 supports the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="84d47-135">Esse novo valor indica que nenhum sameSite deve ser enviado com o cookie.</span><span class="sxs-lookup"><span data-stu-id="84d47-135">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="84d47-136">Alterações de comportamento de patch de dezembro</span><span class="sxs-lookup"><span data-stu-id="84d47-136">December patch behavior changes</span></span>

<span data-ttu-id="84d47-137">A alteração de comportamento específica para .NET Framework e o .NET Core 2,1 é como a propriedade `SameSite` interpreta o valor de `None`.</span><span class="sxs-lookup"><span data-stu-id="84d47-137">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="84d47-138">Antes do patch, um valor de `None` significava "não emitir o atributo de modo algum", após o patch, ele significa "emitir o atributo com um valor de `None`".</span><span class="sxs-lookup"><span data-stu-id="84d47-138">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="84d47-139">Depois do patch, um valor `SameSite` de `(SameSiteMode)(-1)` faz com que o atributo não seja emitido.</span><span class="sxs-lookup"><span data-stu-id="84d47-139">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="84d47-140">O valor padrão de SameSite para autenticação de formulários e cookies de estado de sessão foi alterado de `None` para `Lax`.</span><span class="sxs-lookup"><span data-stu-id="84d47-140">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="84d47-141">Uso da API com SameSite</span><span class="sxs-lookup"><span data-stu-id="84d47-141">API usage with SameSite</span></span>

<span data-ttu-id="84d47-142">[HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) usa como padrão `Unspecified`, o que significa que nenhum atributo SameSite foi adicionado ao cookie e o cliente usará seu comportamento padrão (LAX para novos navegadores, nenhum para os antigos).</span><span class="sxs-lookup"><span data-stu-id="84d47-142">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="84d47-143">O código a seguir mostra como alterar o valor de SameSite do cookie para `SameSiteMode.Lax`:</span><span class="sxs-lookup"><span data-stu-id="84d47-143">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="84d47-144">Todos os componentes de ASP.NET Core que emitem cookies substituem os padrões anteriores pelas configurações apropriadas para seus cenários.</span><span class="sxs-lookup"><span data-stu-id="84d47-144">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="84d47-145">Os valores padrão anteriores substituídos não foram alterados.</span><span class="sxs-lookup"><span data-stu-id="84d47-145">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="84d47-146">Componente</span><span class="sxs-lookup"><span data-stu-id="84d47-146">Component</span></span> | <span data-ttu-id="84d47-147">cookie</span><span class="sxs-lookup"><span data-stu-id="84d47-147">cookie</span></span> | <span data-ttu-id="84d47-148">Padrão</span><span class="sxs-lookup"><span data-stu-id="84d47-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [<span data-ttu-id="84d47-149">SessionOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="84d47-149">SessionOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [<span data-ttu-id="84d47-150">CookieTempDataProviderOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="84d47-150">CookieTempDataProviderOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [<span data-ttu-id="84d47-151">AntiforgeryOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="84d47-151">AntiforgeryOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [<span data-ttu-id="84d47-152">Autenticação de cookie</span><span class="sxs-lookup"><span data-stu-id="84d47-152">Cookie Authentication</span></span>](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [<span data-ttu-id="84d47-153">CookieAuthenticationOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="84d47-153">CookieAuthenticationOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [<span data-ttu-id="84d47-154">Twitteroptions. StateCookie</span><span class="sxs-lookup"><span data-stu-id="84d47-154">TwitterOptions.StateCookie </span></span>](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <span data-ttu-id="84d47-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="84d47-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [<span data-ttu-id="84d47-156">OpenIdConnectOptions.NonceCookie</span><span class="sxs-lookup"><span data-stu-id="84d47-156">OpenIdConnectOptions.NonceCookie</span></span>](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [<span data-ttu-id="84d47-157">HttpContext. Response. cookies. Append</span><span class="sxs-lookup"><span data-stu-id="84d47-157">HttpContext.Response.Cookies.Append</span></span>](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="84d47-158">O ASP.NET Core 3,1 e posterior fornece o seguinte suporte a SameSite:</span><span class="sxs-lookup"><span data-stu-id="84d47-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="84d47-159">Redefine o comportamento de `SameSiteMode.None` para emitir `SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="84d47-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="84d47-160">Adiciona um novo valor `SameSiteMode.Unspecified` para omitir o atributo SameSite.</span><span class="sxs-lookup"><span data-stu-id="84d47-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="84d47-161">Todas as APIs de cookies padrão para `Unspecified`.</span><span class="sxs-lookup"><span data-stu-id="84d47-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="84d47-162">Alguns componentes que usam cookies definem valores mais específicos para seus cenários.</span><span class="sxs-lookup"><span data-stu-id="84d47-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="84d47-163">Consulte a tabela acima para obter exemplos.</span><span class="sxs-lookup"><span data-stu-id="84d47-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="84d47-164">No ASP.NET Core 3,0 e posteriores, os padrões de SameSite foram alterados para evitar conflitos com padrões de cliente inconsistentes.</span><span class="sxs-lookup"><span data-stu-id="84d47-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="84d47-165">As APIs a seguir alteraram o padrão de `SameSiteMode.Lax ` para `-1` para evitar a emissão de um atributo SameSite para esses cookies:</span><span class="sxs-lookup"><span data-stu-id="84d47-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="84d47-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> usado com [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="84d47-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="84d47-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder> usado como uma fábrica para `CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="84d47-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* [<span data-ttu-id="84d47-168">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="84d47-168">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="84d47-169">Histórico e alterações</span><span class="sxs-lookup"><span data-stu-id="84d47-169">History and changes</span></span>

<span data-ttu-id="84d47-170">O suporte a SameSite foi implementado pela primeira vez em ASP.NET Core em 2,0 usando o [padrão de rascunho 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span><span class="sxs-lookup"><span data-stu-id="84d47-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="84d47-171">O padrão 2016 foi aceitar.</span><span class="sxs-lookup"><span data-stu-id="84d47-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="84d47-172">ASP.NET Core aceitou-in definindo vários cookies para `Lax` por padrão.</span><span class="sxs-lookup"><span data-stu-id="84d47-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="84d47-173">Depois de encontrar vários [problemas](https://github.com/aspnet/Announcements/issues/318) com a autenticação, a maior parte do uso do SameSite foi [desabilitada](https://github.com/aspnet/Announcements/issues/348).</span><span class="sxs-lookup"><span data-stu-id="84d47-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="84d47-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) foram emitidos em novembro de 2019 para atualizar do padrão 2016 para o padrão 2019.</span><span class="sxs-lookup"><span data-stu-id="84d47-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="84d47-175">O [rascunho 2019 da especificação de SameSite](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="84d47-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="84d47-176">**Não** é compatível com versões anteriores com o rascunho 2016.</span><span class="sxs-lookup"><span data-stu-id="84d47-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="84d47-177">Para obter mais informações, consulte [dando suporte a navegadores mais antigos](#sob) neste documento.</span><span class="sxs-lookup"><span data-stu-id="84d47-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="84d47-178">Especifica que os cookies são tratados como `SameSite=Lax` por padrão.</span><span class="sxs-lookup"><span data-stu-id="84d47-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="84d47-179">Especifica cookies que declaram explicitamente `SameSite=None` para habilitar a entrega entre sites devem ser marcados como `Secure`.</span><span class="sxs-lookup"><span data-stu-id="84d47-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="84d47-180">`None` é uma nova entrada a ser recusada.</span><span class="sxs-lookup"><span data-stu-id="84d47-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="84d47-181">O é suportado por patches emitidos para o ASP.NET Core 2,1, 2,2 e 3,0.</span><span class="sxs-lookup"><span data-stu-id="84d47-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="84d47-182">ASP.NET Core 3,1 tem suporte adicional a SameSite.</span><span class="sxs-lookup"><span data-stu-id="84d47-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="84d47-183">Está agendado para ser habilitado pelo [Chrome](https://chromestatus.com/feature/5088147346030592) por padrão em [fevereiro de 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span><span class="sxs-lookup"><span data-stu-id="84d47-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="84d47-184">Os navegadores começaram a passar para esse padrão em 2019.</span><span class="sxs-lookup"><span data-stu-id="84d47-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="84d47-185">APIs afetadas pela mudança do padrão de rascunho 2016 SameSite para o padrão de rascunho 2019</span><span class="sxs-lookup"><span data-stu-id="84d47-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="84d47-186">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="84d47-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [<span data-ttu-id="84d47-187">Cookieoptions. SameSite</span><span class="sxs-lookup"><span data-stu-id="84d47-187">CookieOptions.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [<span data-ttu-id="84d47-188">CookieBuilder.SameSite</span><span class="sxs-lookup"><span data-stu-id="84d47-188">CookieBuilder.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [<span data-ttu-id="84d47-189">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="84d47-189">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="84d47-190">Suporte a navegadores mais antigos</span><span class="sxs-lookup"><span data-stu-id="84d47-190">Supporting older browsers</span></span>

<span data-ttu-id="84d47-191">O padrão de 2016 SameSite exigiu que valores desconhecidos devem ser tratados como valores `SameSite=Strict`.</span><span class="sxs-lookup"><span data-stu-id="84d47-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="84d47-192">Os aplicativos acessados de navegadores mais antigos que dão suporte ao padrão de 2016 SameSite podem falhar quando obtêm uma propriedade SameSite com um valor de `None`.</span><span class="sxs-lookup"><span data-stu-id="84d47-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="84d47-193">Os aplicativos Web devem implementar a detecção do navegador se pretenderem oferecer suporte a navegadores mais antigos.</span><span class="sxs-lookup"><span data-stu-id="84d47-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="84d47-194">ASP.NET Core não implementa a detecção de navegador porque os valores dos agentes do usuário são altamente voláteis e mudam com frequência.</span><span class="sxs-lookup"><span data-stu-id="84d47-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="84d47-195">Um ponto de extensão no <xref:Microsoft.AspNetCore.CookiePolicy> permite a conexão da lógica específica do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="84d47-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="84d47-196">Em `Startup.Configure`, adicione o código que chama <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> antes de chamar <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> ou *qualquer* método que grave cookies:</span><span class="sxs-lookup"><span data-stu-id="84d47-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="84d47-197">Em `Startup.ConfigureServices`, adicione um código semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="84d47-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="84d47-198">No exemplo anterior, `MyUserAgentDetectionLib.DisallowsSameSiteNone` é uma biblioteca fornecida pelo usuário que detecta se o agente do usuário não dá suporte a SameSite `None`:</span><span class="sxs-lookup"><span data-stu-id="84d47-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="84d47-199">O código a seguir mostra um exemplo de método de `DisallowsSameSiteNone`:</span><span class="sxs-lookup"><span data-stu-id="84d47-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="84d47-200">O código a seguir é apenas para demonstração:</span><span class="sxs-lookup"><span data-stu-id="84d47-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="84d47-201">Ele não deve ser considerado concluído.</span><span class="sxs-lookup"><span data-stu-id="84d47-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="84d47-202">Ele não é mantido ou tem suporte.</span><span class="sxs-lookup"><span data-stu-id="84d47-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="84d47-203">Testar aplicativos para problemas de SameSite</span><span class="sxs-lookup"><span data-stu-id="84d47-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="84d47-204">Aplicativos que interagem com sites remotos, como por meio de logon de terceiros, precisam:</span><span class="sxs-lookup"><span data-stu-id="84d47-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="84d47-205">Teste a interação em vários navegadores.</span><span class="sxs-lookup"><span data-stu-id="84d47-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="84d47-206">Aplique a [detecção e mitigação do navegador CookiePolicy](#sob) discutidas neste documento.</span><span class="sxs-lookup"><span data-stu-id="84d47-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="84d47-207">Teste os aplicativos Web usando uma versão do cliente que pode aceitar o novo comportamento de SameSite.</span><span class="sxs-lookup"><span data-stu-id="84d47-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="84d47-208">O Chrome, o Firefox e o Chromium Edge têm novos sinalizadores de recurso de aceitação que podem ser usados para teste.</span><span class="sxs-lookup"><span data-stu-id="84d47-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="84d47-209">Depois que seu aplicativo aplicar os patches do SameSite, teste-o com versões mais antigas do cliente, especialmente o Safari.</span><span class="sxs-lookup"><span data-stu-id="84d47-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="84d47-210">Para obter mais informações, consulte [dando suporte a navegadores mais antigos](#sob) neste documento.</span><span class="sxs-lookup"><span data-stu-id="84d47-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="84d47-211">Teste com o Chrome</span><span class="sxs-lookup"><span data-stu-id="84d47-211">Test with Chrome</span></span>

<span data-ttu-id="84d47-212">O Chrome 78 + fornece resultados enganosos porque tem uma mitigação temporária em vigor.</span><span class="sxs-lookup"><span data-stu-id="84d47-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="84d47-213">A mitigação do Chrome 78 + temporária permite cookies com menos de dois minutos.</span><span class="sxs-lookup"><span data-stu-id="84d47-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="84d47-214">O Chrome 76 ou 77 com os sinalizadores de teste apropriados habilitados fornece resultados mais precisos.</span><span class="sxs-lookup"><span data-stu-id="84d47-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="84d47-215">Para testar o novo comportamento de SameSite, alterne `chrome://flags/#same-site-by-default-cookies` para **habilitado**.</span><span class="sxs-lookup"><span data-stu-id="84d47-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="84d47-216">Versões mais antigas do Chrome (75 e inferior) são relatadas para falha com a nova configuração de `None`.</span><span class="sxs-lookup"><span data-stu-id="84d47-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="84d47-217">Consulte [suporte a navegadores mais antigos](#sob) neste documento.</span><span class="sxs-lookup"><span data-stu-id="84d47-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="84d47-218">O Google não disponibiliza versões mais antigas do Chrome.</span><span class="sxs-lookup"><span data-stu-id="84d47-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="84d47-219">Siga as instruções em [baixar o Chromium](https://www.chromium.org/getting-involved/download-chromium) para testar versões anteriores do Chrome.</span><span class="sxs-lookup"><span data-stu-id="84d47-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="84d47-220">**Não** Baixe o Chrome de links fornecidos pela pesquisa de versões mais antigas do Chrome.</span><span class="sxs-lookup"><span data-stu-id="84d47-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="84d47-221">Chromium 76 Win64</span><span class="sxs-lookup"><span data-stu-id="84d47-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="84d47-222">Chromium 74 Win64</span><span class="sxs-lookup"><span data-stu-id="84d47-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="84d47-223">A partir do canário versão `80.0.3975.0`, a mitigação temporária mais completa pode ser desabilitada para fins de teste usando o novo sinalizador `--enable-features=SameSiteDefaultChecksMethodRigorously` para permitir o teste de sites e serviços no estado final eventual do recurso em que a mitigação foi removida.</span><span class="sxs-lookup"><span data-stu-id="84d47-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="84d47-224">Para obter mais informações, consulte o Chromium Projects [SameSite updates](https://www.chromium.org/updates/same-site)</span><span class="sxs-lookup"><span data-stu-id="84d47-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="84d47-225">Teste com o Safari</span><span class="sxs-lookup"><span data-stu-id="84d47-225">Test with Safari</span></span>

<span data-ttu-id="84d47-226">O Safari 12 implementou estritamente o rascunho anterior e falha quando o novo valor de `None` está em um cookie.</span><span class="sxs-lookup"><span data-stu-id="84d47-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="84d47-227">`None` é evitada por meio do código de detecção de navegador que [dá suporte a navegadores mais antigos](#sob) neste documento.</span><span class="sxs-lookup"><span data-stu-id="84d47-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="84d47-228">Teste OS logons de estilo do sistema operacional baseado no Safari 12, Safari 13 e WebKit usando MSAL, ADAL ou qualquer biblioteca que você esteja usando.</span><span class="sxs-lookup"><span data-stu-id="84d47-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="84d47-229">O problema depende da versão subjacente do sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="84d47-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="84d47-230">OSX Mojave (10,14) e iOS 12 são conhecidos por ter problemas de compatibilidade com o novo comportamento de SameSite.</span><span class="sxs-lookup"><span data-stu-id="84d47-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="84d47-231">Atualizar o sistema operacional para OSX Catalina (10,15) ou iOS 13 corrige o problema.</span><span class="sxs-lookup"><span data-stu-id="84d47-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="84d47-232">No momento, o Safari não tem um sinalizador de aceitação para testar o novo comportamento de especificação.</span><span class="sxs-lookup"><span data-stu-id="84d47-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="84d47-233">Testar com o Firefox</span><span class="sxs-lookup"><span data-stu-id="84d47-233">Test with Firefox</span></span>

<span data-ttu-id="84d47-234">O suporte do Firefox para o novo padrão pode ser testado na versão 68 +, optando na página de `about:config` com o sinalizador de recurso `network.cookie.sameSite.laxByDefault`.</span><span class="sxs-lookup"><span data-stu-id="84d47-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="84d47-235">Não há relatórios de problemas de compatibilidade com versões mais antigas do Firefox.</span><span class="sxs-lookup"><span data-stu-id="84d47-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="84d47-236">Testar com o navegador Edge</span><span class="sxs-lookup"><span data-stu-id="84d47-236">Test with Edge browser</span></span>

<span data-ttu-id="84d47-237">O Edge dá suporte ao antigo SameSite padrão.</span><span class="sxs-lookup"><span data-stu-id="84d47-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="84d47-238">A versão 44 do Edge não tem nenhum problema de compatibilidade conhecido com o novo padrão.</span><span class="sxs-lookup"><span data-stu-id="84d47-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="84d47-239">Testar com borda (Chromium)</span><span class="sxs-lookup"><span data-stu-id="84d47-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="84d47-240">Os sinalizadores SameSite são definidos na página `edge://flags/#same-site-by-default-cookies`.</span><span class="sxs-lookup"><span data-stu-id="84d47-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="84d47-241">Nenhum problema de compatibilidade foi descoberto com o Edge Chromium.</span><span class="sxs-lookup"><span data-stu-id="84d47-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="84d47-242">Testar com o</span><span class="sxs-lookup"><span data-stu-id="84d47-242">Test with Electron</span></span>

<span data-ttu-id="84d47-243">As versões do at-SS são versões mais antigas do Chromium.</span><span class="sxs-lookup"><span data-stu-id="84d47-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="84d47-244">Por exemplo, a versão do at-SS usada pelas equipes é Chromium 66, que exibe o comportamento mais antigo.</span><span class="sxs-lookup"><span data-stu-id="84d47-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="84d47-245">Você deve executar seu próprio teste de compatibilidade com a versão do uso de digital que seu produto usa.</span><span class="sxs-lookup"><span data-stu-id="84d47-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="84d47-246">Consulte [suporte a navegadores mais antigos](#sob) na seção a seguir.</span><span class="sxs-lookup"><span data-stu-id="84d47-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84d47-247">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="84d47-247">Additional resources</span></span>

* [<span data-ttu-id="84d47-248">Blog do Chromium: desenvolvedores: Prepare-se para o New SameSite = None; Configurações de cookie seguro</span><span class="sxs-lookup"><span data-stu-id="84d47-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [<span data-ttu-id="84d47-249">SameSite cookies explicados</span><span class="sxs-lookup"><span data-stu-id="84d47-249">SameSite cookies explained</span></span>](https://web.dev/samesite-cookies-explained/)
* [<span data-ttu-id="84d47-250">Patches de novembro de 2019</span><span class="sxs-lookup"><span data-stu-id="84d47-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="84d47-251">Amostra</span><span class="sxs-lookup"><span data-stu-id="84d47-251">Sample</span></span>               | <span data-ttu-id="84d47-252">Documento</span><span class="sxs-lookup"><span data-stu-id="84d47-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="84d47-253">MVC do .NET Core</span><span class="sxs-lookup"><span data-stu-id="84d47-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [<span data-ttu-id="84d47-254">Razor Pages do .NET Core</span><span class="sxs-lookup"><span data-stu-id="84d47-254">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="84d47-255">Amostra</span><span class="sxs-lookup"><span data-stu-id="84d47-255">Sample</span></span>               | <span data-ttu-id="84d47-256">Documento</span><span class="sxs-lookup"><span data-stu-id="84d47-256">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="84d47-257">Razor Pages do .NET Core</span><span class="sxs-lookup"><span data-stu-id="84d47-257">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end
