---
title: Impor uma política de segurança de conteúdo para ASP.NET Core Blazor
author: guardrex
description: Saiba como usar uma política de segurança de conteúdo (CSP) com ASP.NET Core aplicativos Blazor para ajudar a proteger contra ataques XSS (scripts entre sites).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/content-security-policy
ms.openlocfilehash: 1cfebf7b3d3bbb98a671b6f2db7c6518cda74b65
ms.sourcegitcommit: 51c86c003ab5436598dbc42f26ea4a83a795fd6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78964546"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-opno-locblazor"></a><span data-ttu-id="62732-103">Impor uma política de segurança de conteúdo para ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="62732-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="62732-104">Por [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="62732-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="62732-105">O [XSS (script entre sites)](xref:security/cross-site-scripting) é uma vulnerabilidade de segurança em que um invasor coloca um ou mais scripts mal-intencionados do lado do cliente no conteúdo renderizado de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62732-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="62732-106">Uma política de segurança de conteúdo (CSP) ajuda a proteger contra ataques de XSS, informando o navegador de válido:</span><span class="sxs-lookup"><span data-stu-id="62732-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="62732-107">Fontes de conteúdo carregado, incluindo scripts, folhas de estilos e imagens.</span><span class="sxs-lookup"><span data-stu-id="62732-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="62732-108">Ações tomadas por uma página, especificando destinos de URL permitidos de formulários.</span><span class="sxs-lookup"><span data-stu-id="62732-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="62732-109">Plug-ins que podem ser carregados.</span><span class="sxs-lookup"><span data-stu-id="62732-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="62732-110">Para aplicar um CSP a um aplicativo, o desenvolvedor especifica várias *diretivas* de segurança de conteúdo do CSP em um ou mais cabeçalhos de `Content-Security-Policy` ou `<meta>` marcas.</span><span class="sxs-lookup"><span data-stu-id="62732-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="62732-111">As políticas são avaliadas pelo navegador enquanto uma página está sendo carregada.</span><span class="sxs-lookup"><span data-stu-id="62732-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="62732-112">O navegador inspeciona as fontes da página e determina se elas atendem aos requisitos das diretivas de segurança do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="62732-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="62732-113">Quando as diretivas de política não são atendidas para um recurso, o navegador não carrega o recurso.</span><span class="sxs-lookup"><span data-stu-id="62732-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="62732-114">Por exemplo, considere uma política que não permita scripts de terceiros.</span><span class="sxs-lookup"><span data-stu-id="62732-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="62732-115">Quando uma página contém uma marca de `<script>` com uma origem de terceiros no atributo `src`, o navegador impede que o script seja carregado.</span><span class="sxs-lookup"><span data-stu-id="62732-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="62732-116">O CSP tem suporte na maioria dos navegadores de desktop e móvel modernos, incluindo Chrome, Edge, Firefox, Opera e Safari.</span><span class="sxs-lookup"><span data-stu-id="62732-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="62732-117">O CSP é recomendado para aplicativos Blazor.</span><span class="sxs-lookup"><span data-stu-id="62732-117">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="62732-118">Diretivas de política</span><span class="sxs-lookup"><span data-stu-id="62732-118">Policy directives</span></span>

<span data-ttu-id="62732-119">No mínimo, especifique as seguintes diretivas e fontes para Blazor aplicativos.</span><span class="sxs-lookup"><span data-stu-id="62732-119">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="62732-120">Adicione outras diretivas e fontes conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="62732-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="62732-121">As seguintes diretivas são usadas na seção [aplicar a política](#apply-the-policy) deste artigo, onde as políticas de segurança de exemplo para Blazor Webassembly e Blazor Server são fornecidas:</span><span class="sxs-lookup"><span data-stu-id="62732-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="62732-122">o [URI de base](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; restringe as URLs para a marca `<base>` de uma página.</span><span class="sxs-lookup"><span data-stu-id="62732-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="62732-123">Especifique `self` para indicar que a origem do aplicativo, incluindo o número de porta e o esquema, é uma origem válida.</span><span class="sxs-lookup"><span data-stu-id="62732-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="62732-124">[bloco-tudo-misto-conteúdo](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; impede o carregamento de conteúdo http e HTTPS mistos.</span><span class="sxs-lookup"><span data-stu-id="62732-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="62732-125">[Default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; indica um fallback para diretivas de origem que não são explicitamente especificadas pela política.</span><span class="sxs-lookup"><span data-stu-id="62732-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="62732-126">Especifique `self` para indicar que a origem do aplicativo, incluindo o número de porta e o esquema, é uma origem válida.</span><span class="sxs-lookup"><span data-stu-id="62732-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="62732-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; indica fontes válidas para imagens.</span><span class="sxs-lookup"><span data-stu-id="62732-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; Indicates valid sources for images.</span></span>
  * <span data-ttu-id="62732-128">Especifique `data:` para permitir o carregamento de imagens de `data:` URLs.</span><span class="sxs-lookup"><span data-stu-id="62732-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="62732-129">Especifique `https:` para permitir o carregamento de imagens de pontos de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="62732-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="62732-130">[objeto-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; indica fontes válidas para as marcas `<object>`, `<embed>`e `<applet>`.</span><span class="sxs-lookup"><span data-stu-id="62732-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="62732-131">Especifique `none` para impedir todas as fontes de URL.</span><span class="sxs-lookup"><span data-stu-id="62732-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="62732-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; indica fontes válidas para scripts.</span><span class="sxs-lookup"><span data-stu-id="62732-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="62732-133">Especifique o `https://stackpath.bootstrapcdn.com/` origem do host para scripts de bootstrap.</span><span class="sxs-lookup"><span data-stu-id="62732-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="62732-134">Especifique `self` para indicar que a origem do aplicativo, incluindo o número de porta e o esquema, é uma origem válida.</span><span class="sxs-lookup"><span data-stu-id="62732-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="62732-135">Em um aplicativo Webassembly Blazor:</span><span class="sxs-lookup"><span data-stu-id="62732-135">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="62732-136">Especifique os seguintes hashes para permitir que os scripts embutidos Blazor Webassembly necessários sejam carregados:</span><span class="sxs-lookup"><span data-stu-id="62732-136">Specify the following hashes to permit the required Blazor WebAssembly inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="62732-137">Especifique `unsafe-eval` para usar `eval()` e métodos para criar código a partir de cadeias de caracteres.</span><span class="sxs-lookup"><span data-stu-id="62732-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="62732-138">Em um aplicativo do Blazor Server, especifique o hash de `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` para o script embutido que executa a detecção de fallback para folhas de estilo.</span><span class="sxs-lookup"><span data-stu-id="62732-138">In a Blazor Server app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="62732-139">[Style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; indica fontes válidas para folhas de estilo.</span><span class="sxs-lookup"><span data-stu-id="62732-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="62732-140">Especifique a origem do host `https://stackpath.bootstrapcdn.com/` para as folhas de estilo de inicialização.</span><span class="sxs-lookup"><span data-stu-id="62732-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="62732-141">Especifique `self` para indicar que a origem do aplicativo, incluindo o número de porta e o esquema, é uma origem válida.</span><span class="sxs-lookup"><span data-stu-id="62732-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="62732-142">Especifique `unsafe-inline` para permitir o uso de estilos embutidos.</span><span class="sxs-lookup"><span data-stu-id="62732-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="62732-143">A declaração embutida é necessária para a interface do usuário em aplicativos Blazor Server para reconectar o cliente e o servidor após a solicitação inicial.</span><span class="sxs-lookup"><span data-stu-id="62732-143">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="62732-144">Em uma versão futura, o estilo embutido pode ser removido para que `unsafe-inline` não seja mais necessária.</span><span class="sxs-lookup"><span data-stu-id="62732-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="62732-145">[upgrade-Unsecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; indica que as URLs de conteúdo de fontes inseguras (http) devem ser adquiridas com segurança via HTTPS.</span><span class="sxs-lookup"><span data-stu-id="62732-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="62732-146">As diretivas anteriores têm suporte de todos os navegadores, exceto o Microsoft Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="62732-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="62732-147">Para obter hashes de SHA para scripts embutidos adicionais:</span><span class="sxs-lookup"><span data-stu-id="62732-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="62732-148">Aplique o CSP mostrado na seção [aplicar a política](#apply-the-policy) .</span><span class="sxs-lookup"><span data-stu-id="62732-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="62732-149">Acesse o console de ferramentas de desenvolvedor do navegador ao executar o aplicativo localmente.</span><span class="sxs-lookup"><span data-stu-id="62732-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="62732-150">O navegador calcula e exibe hashes de scripts bloqueados quando um cabeçalho CSP ou marca de `meta` está presente.</span><span class="sxs-lookup"><span data-stu-id="62732-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="62732-151">Copie os hashes fornecidos pelo navegador para as fontes de `script-src`.</span><span class="sxs-lookup"><span data-stu-id="62732-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="62732-152">Use aspas simples em volta de cada hash.</span><span class="sxs-lookup"><span data-stu-id="62732-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="62732-153">Para uma matriz de suporte de navegador de nível 2 de política de segurança de conteúdo, consulte posso [usar: nível de política de segurança de conteúdo 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span><span class="sxs-lookup"><span data-stu-id="62732-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="62732-154">Aplicar a política</span><span class="sxs-lookup"><span data-stu-id="62732-154">Apply the policy</span></span>

<span data-ttu-id="62732-155">Use uma marca de `<meta>` para aplicar a política:</span><span class="sxs-lookup"><span data-stu-id="62732-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="62732-156">Defina o valor do atributo `http-equiv` como `Content-Security-Policy`.</span><span class="sxs-lookup"><span data-stu-id="62732-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="62732-157">Coloque as diretivas no valor do atributo `content`.</span><span class="sxs-lookup"><span data-stu-id="62732-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="62732-158">Diretivas separadas com um ponto-e-vírgula (`;`).</span><span class="sxs-lookup"><span data-stu-id="62732-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="62732-159">Sempre coloque a marca de `meta` no conteúdo `<head>`.</span><span class="sxs-lookup"><span data-stu-id="62732-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="62732-160">As seções a seguir mostram as políticas de exemplo para Blazor Webassembly e Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="62732-160">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="62732-161">Esses exemplos têm a versão deste artigo para cada versão do Blazor.</span><span class="sxs-lookup"><span data-stu-id="62732-161">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="62732-162">Para usar uma versão apropriada para sua versão, selecione a versão do documento com o seletor de **versão** na página da Web.</span><span class="sxs-lookup"><span data-stu-id="62732-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="62732-163"> Webassembly</span><span class="sxs-lookup"><span data-stu-id="62732-163"> WebAssembly</span></span>

<span data-ttu-id="62732-164">No conteúdo `<head>` da página host *wwwroot/index.html* , aplique as diretivas descritas na seção [diretivas de política](#policy-directives) :</span><span class="sxs-lookup"><span data-stu-id="62732-164">In the `<head>` content of the *wwwroot/index.html* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### <a name="opno-locblazor-server"></a><span data-ttu-id="62732-165">Servidor de Blazor</span><span class="sxs-lookup"><span data-stu-id="62732-165">Blazor Server</span></span>

<span data-ttu-id="62732-166">No conteúdo `<head>` da página de host *pages/_Host. cshtml* , aplique as diretivas descritas na seção [diretivas de política](#policy-directives) :</span><span class="sxs-lookup"><span data-stu-id="62732-166">In the `<head>` content of the *Pages/_Host.cshtml* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a><span data-ttu-id="62732-167">Limitações da marca meta</span><span class="sxs-lookup"><span data-stu-id="62732-167">Meta tag limitations</span></span>

<span data-ttu-id="62732-168">Uma política de marcação de `<meta>` não dá suporte às seguintes diretivas:</span><span class="sxs-lookup"><span data-stu-id="62732-168">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="62732-169">quadro-ancestrais</span><span class="sxs-lookup"><span data-stu-id="62732-169">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="62732-170">relatório-para</span><span class="sxs-lookup"><span data-stu-id="62732-170">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="62732-171">URI do relatório</span><span class="sxs-lookup"><span data-stu-id="62732-171">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="62732-172">AppDomain</span><span class="sxs-lookup"><span data-stu-id="62732-172">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="62732-173">Para dar suporte às diretivas anteriores, use um cabeçalho chamado `Content-Security-Policy`.</span><span class="sxs-lookup"><span data-stu-id="62732-173">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="62732-174">A cadeia de caracteres de diretiva é o valor do cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="62732-174">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="62732-175">Testar uma política e receber relatórios de violação</span><span class="sxs-lookup"><span data-stu-id="62732-175">Test a policy and receive violation reports</span></span>

<span data-ttu-id="62732-176">O teste ajuda a confirmar se os scripts de terceiros não são bloqueados inadvertidamente durante a criação de uma política inicial.</span><span class="sxs-lookup"><span data-stu-id="62732-176">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="62732-177">Para testar uma política durante um período de tempo sem impor as diretivas de política, defina o atributo de `http-equiv` da marca de `<meta>` ou o nome do cabeçalho de uma política baseada em cabeçalho como `Content-Security-Policy-Report-Only`.</span><span class="sxs-lookup"><span data-stu-id="62732-177">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="62732-178">Os relatórios de falha são enviados como documentos JSON para uma URL especificada.</span><span class="sxs-lookup"><span data-stu-id="62732-178">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="62732-179">Para obter mais informações, consulte [MDN Web docs: content-Security-Policy-Report-only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span><span class="sxs-lookup"><span data-stu-id="62732-179">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="62732-180">Para relatórios sobre violações enquanto uma política estiver ativa, consulte os seguintes artigos:</span><span class="sxs-lookup"><span data-stu-id="62732-180">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="62732-181">relatório-para</span><span class="sxs-lookup"><span data-stu-id="62732-181">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="62732-182">URI do relatório</span><span class="sxs-lookup"><span data-stu-id="62732-182">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="62732-183">Embora `report-uri` não seja mais recomendado para uso, ambas as diretivas devem ser usadas até que `report-to` tenha suporte em todos os principais navegadores.</span><span class="sxs-lookup"><span data-stu-id="62732-183">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="62732-184">Não use exclusivamente `report-uri` porque o suporte para `report-uri` está sujeito a ser descartado a *qualquer momento* dos navegadores.</span><span class="sxs-lookup"><span data-stu-id="62732-184">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="62732-185">Remova o suporte para `report-uri` em suas políticas quando `report-to` tiver suporte total.</span><span class="sxs-lookup"><span data-stu-id="62732-185">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="62732-186">Para acompanhar a adoção de `report-to`, consulte posso [usar: relatar para](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span><span class="sxs-lookup"><span data-stu-id="62732-186">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="62732-187">Testar e atualizar a política de um aplicativo a cada versão.</span><span class="sxs-lookup"><span data-stu-id="62732-187">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="62732-188">Solucionar problemas</span><span class="sxs-lookup"><span data-stu-id="62732-188">Troubleshoot</span></span>

* <span data-ttu-id="62732-189">Os erros aparecem no console de ferramentas para desenvolvedores do navegador.</span><span class="sxs-lookup"><span data-stu-id="62732-189">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="62732-190">Os navegadores fornecem informações sobre:</span><span class="sxs-lookup"><span data-stu-id="62732-190">Browsers provide information about:</span></span>
  * <span data-ttu-id="62732-191">Elementos que não estão em conformidade com a política.</span><span class="sxs-lookup"><span data-stu-id="62732-191">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="62732-192">Como modificar a política para permitir um item bloqueado.</span><span class="sxs-lookup"><span data-stu-id="62732-192">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="62732-193">Uma política só será completamente efetiva quando o navegador do cliente oferecer suporte a todas as diretivas incluídas.</span><span class="sxs-lookup"><span data-stu-id="62732-193">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="62732-194">Para obter uma matriz de suporte do navegador atual, consulte [posso usar: content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span><span class="sxs-lookup"><span data-stu-id="62732-194">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="62732-195">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="62732-195">Additional resources</span></span>

* [<span data-ttu-id="62732-196">MDN Web docs: content-Security-Policy</span><span class="sxs-lookup"><span data-stu-id="62732-196">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="62732-197">Nível de política de segurança de conteúdo 2</span><span class="sxs-lookup"><span data-stu-id="62732-197">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="62732-198">Avaliador do Google CSP</span><span class="sxs-lookup"><span data-stu-id="62732-198">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
