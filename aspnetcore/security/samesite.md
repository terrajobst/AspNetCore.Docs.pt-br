---
title: Trabalhar com cookies SameSite no ASP.NET Core
author: rick-anderson
description: Saiba como usar o para SameSite cookies no ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: security/samesite
ms.openlocfilehash: b344ed8f539979210980b3421659207edd513f32
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146427"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a>Trabalhar com cookies SameSite no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

[SameSite](https://tools.ietf.org/html/draft-west-first-party-cookies-07) é um rascunho de [IETF](https://ietf.org/about/) projetado para fornecer uma proteção contra ataques de CSRF (solicitação entre sites forjados). O [rascunho do SameSite 2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00):

* Trata cookies como `SameSite=Lax` por padrão.
* Os cookies de Estados que declaram explicitamente `SameSite=None` para habilitar a entrega entre sites devem ser marcados como `Secure`.

`Lax` funciona para a maioria dos cookies de aplicativo. Algumas formas de autenticação, como o [OpenID Connect](https://openid.net/connect/) (OIDC) e o [WS-Federation,](https://auth0.com/docs/protocols/ws-fed) assumem o padrão de redirecionamentos baseados em post. Os redirecionamentos baseados em POST disparam as proteções do navegador SameSite, portanto, o SameSite está desabilitado para esses componentes. A maioria dos logons [OAuth](https://oauth.net/) não são afetados devido a diferenças na forma como os fluxos de solicitação.

O parâmetro `None` causa problemas de compatibilidade com clientes que implementaram o padrão anterior de rascunho 2016 (por exemplo, iOS 12). Consulte [suporte a navegadores mais antigos](#sob) neste documento.

Cada componente ASP.NET Core que emite cookies precisa decidir se SameSite é apropriado.

## <a name="api-usage-with-samesite"></a>Uso da API com SameSite

[HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) usa como padrão `Unspecified`, o que significa que nenhum atributo SameSite foi adicionado ao cookie e o cliente usará seu comportamento padrão (LAX para novos navegadores, nenhum para os antigos). O código a seguir mostra como alterar o valor de SameSite do cookie para `SameSiteMode.Lax`:

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

Todos os componentes de ASP.NET Core que emitem cookies substituem os padrões anteriores pelas configurações apropriadas para seus cenários. Os valores padrão anteriores substituídos não foram alterados.

| Componente | cookie | Padrão |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [SessionOptions. cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [CookieTempDataProviderOptions. cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [AntiforgeryOptions. cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [Autenticação de cookie](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [CookieAuthenticationOptions. cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [Twitteroptions. StateCookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None` |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [OpenIdConnectOptions.NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

O ASP.NET Core 3,1 e posterior fornece o seguinte suporte a SameSite:

* Redefine o comportamento de `SameSiteMode.None` para emitir `SameSite=None`
* Adiciona um novo valor `SameSiteMode.Unspecified` para omitir o atributo SameSite.
* Todas as APIs de cookies padrão para `Unspecified`. Alguns componentes que usam cookies definem valores mais específicos para seus cenários. Consulte a tabela acima para obter exemplos.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

No ASP.NET Core 3,0 e posteriores, os padrões de SameSite foram alterados para evitar conflitos com padrões de cliente inconsistentes. As APIs a seguir alteraram o padrão de `SameSiteMode.Lax ` para `-1` para evitar a emissão de um atributo SameSite para esses cookies:

* <xref:Microsoft.AspNetCore.Http.CookieOptions> usado com [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)
* <xref:Microsoft.AspNetCore.Http.CookieBuilder> usado como uma fábrica para `CookieOptions`
* [CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a>Histórico e alterações

O suporte a SameSite foi implementado pela primeira vez em ASP.NET Core em 2,0 usando o [padrão de rascunho 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1). O padrão 2016 foi aceitar. ASP.NET Core aceitou-in definindo vários cookies para `Lax` por padrão. Depois de encontrar vários [problemas](https://github.com/aspnet/Announcements/issues/318) com a autenticação, a maior parte do uso do SameSite foi [desabilitada](https://github.com/aspnet/Announcements/issues/348).

[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) foram emitidos em novembro de 2019 para atualizar do padrão 2016 para o padrão 2019. O [rascunho 2019 da especificação de SameSite](https://github.com/aspnet/Announcements/issues/390):

* **Não** é compatível com versões anteriores com o rascunho 2016. Para obter mais informações, consulte [dando suporte a navegadores mais antigos](#sob) neste documento.
* Especifica que os cookies são tratados como `SameSite=Lax` por padrão.
* Especifica cookies que declaram explicitamente `SameSite=None` para habilitar a entrega entre sites devem ser marcados como `Secure`. `None` é uma nova entrada a ser recusada.
* O é suportado por patches emitidos para o ASP.NET Core 2,1, 2,2 e 3,0. ASP.NET Core 3,1 tem suporte adicional a SameSite.
* Está agendado para ser habilitado pelo [Chrome](https://chromestatus.com/feature/5088147346030592) por padrão em [fevereiro de 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html). Os navegadores começaram a passar para esse padrão em 2019.

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a>APIs afetadas pela mudança do padrão de rascunho 2016 SameSite para o padrão de rascunho 2019

* [Http. SameSiteMode](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [Cookieoptions. SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [CookieBuilder.SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a>Suporte a navegadores mais antigos

O padrão de 2016 SameSite exigiu que valores desconhecidos devem ser tratados como valores `SameSite=Strict`. Os aplicativos acessados de navegadores mais antigos que dão suporte ao padrão de 2016 SameSite podem falhar quando obtêm uma propriedade SameSite com um valor de `None`. Os aplicativos Web devem implementar a detecção do navegador se pretenderem oferecer suporte a navegadores mais antigos. ASP.NET Core não implementa a detecção de navegador porque os valores dos agentes do usuário são altamente voláteis e mudam com frequência. Um ponto de extensão no <xref:Microsoft.AspNetCore.CookiePolicy> permite a conexão da lógica específica do agente do usuário.

Em `Startup.Configure`, adicione o código que chama <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> antes de chamar <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> ou *qualquer* método que grave cookies:

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

Em `Startup.ConfigureServices`, adicione um código semelhante ao seguinte:

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

No exemplo anterior, `MyUserAgentDetectionLib.DisallowsSameSiteNone` é uma biblioteca fornecida pelo usuário que detecta se o agente do usuário não dá suporte a SameSite `None`:

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

O código a seguir mostra um exemplo de método de `DisallowsSameSiteNone`:

> [!WARNING]
> O código a seguir é apenas para demonstração:
> * Ele não deve ser considerado concluído.
> * Ele não é mantido ou tem suporte.

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a>Testar aplicativos para problemas de SameSite

Aplicativos que interagem com sites remotos, como por meio de logon de terceiros, precisam:

* Teste a interação em vários navegadores.
* Aplique a [detecção e mitigação do navegador CookiePolicy](#sob) discutidas neste documento.

Teste os aplicativos Web usando uma versão do cliente que pode aceitar o novo comportamento de SameSite. O Chrome, o Firefox e o Chromium Edge têm novos sinalizadores de recurso de aceitação que podem ser usados para teste. Depois que seu aplicativo aplicar os patches do SameSite, teste-o com versões mais antigas do cliente, especialmente o Safari. Para obter mais informações, consulte [dando suporte a navegadores mais antigos](#sob) neste documento.

### <a name="test-with-chrome"></a>Teste com o Chrome

O Chrome 78 + fornece resultados enganosos porque tem uma mitigação temporária em vigor. A mitigação do Chrome 78 + temporária permite cookies com menos de dois minutos. O Chrome 76 ou 77 com os sinalizadores de teste apropriados habilitados fornece resultados mais precisos. Para testar o novo comportamento de SameSite, alterne `chrome://flags/#same-site-by-default-cookies` para **habilitado**. Versões mais antigas do Chrome (75 e inferior) são relatadas para falha com a nova configuração de `None`. Consulte [suporte a navegadores mais antigos](#sob) neste documento.

O Google não disponibiliza versões mais antigas do Chrome. Siga as instruções em [baixar o Chromium](https://www.chromium.org/getting-involved/download-chromium) para testar versões anteriores do Chrome. **Não** Baixe o Chrome de links fornecidos pela pesquisa de versões mais antigas do Chrome.

* [Chromium 76 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [Chromium 74 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

### <a name="test-with-safari"></a>Teste com o Safari

O Safari 12 implementou estritamente o rascunho anterior e falha quando o novo valor de `None` está em um cookie. `None` é evitada por meio do código de detecção de navegador que [dá suporte a navegadores mais antigos](#sob) neste documento. Teste OS logons de estilo do sistema operacional baseado no Safari 12, Safari 13 e WebKit usando MSAL, ADAL ou qualquer biblioteca que você esteja usando. O problema depende da versão subjacente do sistema operacional. OSX Mojave (10,14) e iOS 12 são conhecidos por ter problemas de compatibilidade com o novo comportamento de SameSite. Atualizar o sistema operacional para OSX Catalina (10,15) ou iOS 13 corrige o problema. No momento, o Safari não tem um sinalizador de aceitação para testar o novo comportamento de especificação.

### <a name="test-with-firefox"></a>Testar com o Firefox

O suporte do Firefox para o novo padrão pode ser testado na versão 68 +, optando na página de `about:config` com o sinalizador de recurso `network.cookie.sameSite.laxByDefault`. Não há relatórios de problemas de compatibilidade com versões mais antigas do Firefox.

### <a name="test-with-edge-browser"></a>Testar com o navegador Edge

O Edge dá suporte ao antigo SameSite padrão. A versão 44 do Edge não tem nenhum problema de compatibilidade conhecido com o novo padrão.

### <a name="test-with-edge-chromium"></a>Testar com borda (Chromium)

Os sinalizadores SameSite são definidos na página `edge://flags/#same-site-by-default-cookies`. Nenhum problema de compatibilidade foi descoberto com o Edge Chromium.

### <a name="test-with-electron"></a>Testar com o

As versões do at-SS são versões mais antigas do Chromium. Por exemplo, a versão do at-SS usada pelas equipes é Chromium 66, que exibe o comportamento mais antigo. Você deve executar seu próprio teste de compatibilidade com a versão do uso de digital que seu produto usa. Consulte [suporte a navegadores mais antigos](#sob) na seção a seguir.

## <a name="additional-resources"></a>Recursos adicionais

* [Blog do Chromium: desenvolvedores: Prepare-se para o New SameSite = None; Configurações de cookie seguro](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [SameSite cookies explicados](https://web.dev/samesite-cookies-explained/)
* [Patches de novembro de 2019](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)
