---
title: Impor HTTPS no ASP.NET Core
author: rick-anderson
description: Aprenda a exigir HTTPS/TLS em um aplicativo web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/01/2018
uid: security/enforcing-ssl
ms.openlocfilehash: 08ce50775d1b5348cb0528a1724cec2e5c72dae2
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152908"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="a6046-103">Impor HTTPS no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a6046-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="a6046-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a6046-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a6046-105">Este documento demonstra como:</span><span class="sxs-lookup"><span data-stu-id="a6046-105">This document shows how to:</span></span>

* <span data-ttu-id="a6046-106">Exigir HTTPS para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="a6046-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="a6046-107">Redirecione todas as solicitações HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a6046-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="a6046-108">Nenhuma API pode impedir que um cliente envie dados confidenciais na primeira solicitação.</span><span class="sxs-lookup"><span data-stu-id="a6046-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="a6046-109">Projetos de API</span><span class="sxs-lookup"><span data-stu-id="a6046-109">API projects</span></span>
>
> <span data-ttu-id="a6046-110">Fazer **não** usar [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) em APIs da Web que recebe informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="a6046-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="a6046-111">`RequireHttpsAttribute` usa códigos de status HTTP para redirecionar navegadores de HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a6046-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="a6046-112">Os clientes da API não podem compreender ou obedecem redirecionamentos de HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a6046-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="a6046-113">Esses clientes podem enviar informações por meio de HTTP.</span><span class="sxs-lookup"><span data-stu-id="a6046-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="a6046-114">As APIs da Web deverá:</span><span class="sxs-lookup"><span data-stu-id="a6046-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="a6046-115">Não realizar a escuta em HTTP.</span><span class="sxs-lookup"><span data-stu-id="a6046-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="a6046-116">Feche a conexão com o código de status 400 (solicitação incorreta) e não atender à solicitação.</span><span class="sxs-lookup"><span data-stu-id="a6046-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="a6046-117">Projetos de API</span><span class="sxs-lookup"><span data-stu-id="a6046-117">API projects</span></span>
>
> <span data-ttu-id="a6046-118">Fazer **não** usar [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) em APIs da Web que recebe informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="a6046-118">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="a6046-119">`RequireHttpsAttribute` usa códigos de status HTTP para redirecionar navegadores de HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a6046-119">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="a6046-120">Os clientes da API não podem compreender ou obedecem redirecionamentos de HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a6046-120">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="a6046-121">Esses clientes podem enviar informações por meio de HTTP.</span><span class="sxs-lookup"><span data-stu-id="a6046-121">Such clients may send information over HTTP.</span></span> <span data-ttu-id="a6046-122">As APIs da Web deverá:</span><span class="sxs-lookup"><span data-stu-id="a6046-122">Web APIs should either:</span></span>
>
> * <span data-ttu-id="a6046-123">Não realizar a escuta em HTTP.</span><span class="sxs-lookup"><span data-stu-id="a6046-123">Not listen on HTTP.</span></span>
> * <span data-ttu-id="a6046-124">Feche a conexão com o código de status 400 (solicitação incorreta) e não atender à solicitação.</span><span class="sxs-lookup"><span data-stu-id="a6046-124">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="a6046-125">Projetos HSTS e API</span><span class="sxs-lookup"><span data-stu-id="a6046-125">HSTS and API projects</span></span>
>
> <span data-ttu-id="a6046-126">Os projetos de API padrão não incluem [HSTS](#hsts) porque HSTS geralmente é uma instrução única de navegador.</span><span class="sxs-lookup"><span data-stu-id="a6046-126">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="a6046-127">Outros chamadores, como telefone ou aplicativos da área de trabalho, faça **não** obedecem a instrução.</span><span class="sxs-lookup"><span data-stu-id="a6046-127">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="a6046-128">Mesmo dentro de navegadores, uma única chamada autenticada para uma API via HTTP tem riscos em redes não seguras.</span><span class="sxs-lookup"><span data-stu-id="a6046-128">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="a6046-129">A abordagem segura é configurar projetos de API para apenas ouvir e responder por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a6046-129">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="a6046-130">Exigir HTTPS</span><span class="sxs-lookup"><span data-stu-id="a6046-130">Require HTTPS</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="a6046-131">É recomendável que produção do ASP.NET Core chamada de aplicativos web:</span><span class="sxs-lookup"><span data-stu-id="a6046-131">We recommend that production ASP.NET Core web apps call:</span></span>

* <span data-ttu-id="a6046-132">Middleware de redirecionamento de HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) para redirecionar solicitações HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a6046-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="a6046-133">Middleware HSTS ([UseHsts](#http-strict-transport-security-protocol-hsts)) para enviar cabeçalhos de protocolo de segurança de transporte estrito (HSTS) HTTP aos clientes.</span><span class="sxs-lookup"><span data-stu-id="a6046-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="a6046-134">Aplicativos implantados em uma configuração de proxy reverso permitem que o proxy lidar com a segurança de conexão (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="a6046-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="a6046-135">Se o proxy também manipula o redirecionamento de HTTPS, não é necessário usar o Middleware de redirecionamento de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a6046-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="a6046-136">Se o servidor proxy também manipula a HSTS cabeçalhos de gravação (por exemplo, [dar suporte a HSTS nativos no IIS 10.0 (1709) ou posterior](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), Middleware HSTS não é necessário para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a6046-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="a6046-137">Para obter mais informações, consulte [Opt-out de HTTPS/HSTS na criação do projeto](#opt-out-of-httpshsts-on-project-creation).</span><span class="sxs-lookup"><span data-stu-id="a6046-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="a6046-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="a6046-138">UseHttpsRedirection</span></span>

<span data-ttu-id="a6046-139">O código a seguir chama `UseHttpsRedirection` no `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="a6046-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=13)]

<span data-ttu-id="a6046-140">O código realçado anterior:</span><span class="sxs-lookup"><span data-stu-id="a6046-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="a6046-141">Usa o padrão [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span><span class="sxs-lookup"><span data-stu-id="a6046-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="a6046-142">Usa o padrão [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), a menos que substituído pela `ASPNETCORE_HTTPS_PORT` variável de ambiente ou [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span><span class="sxs-lookup"><span data-stu-id="a6046-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="a6046-143">É recomendável usar redirecionamentos temporários em vez de redirecionamentos permanentes.</span><span class="sxs-lookup"><span data-stu-id="a6046-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="a6046-144">Cache de link pode causar um comportamento instável em ambientes de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="a6046-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="a6046-145">Se você preferir enviar um código de status de redirecionamento permanente quando o aplicativo estiver em um ambiente de não desenvolvimento, consulte o [configurar redirecionamentos permanentes em produção](#configure-permanent-redirects-in-production) seção.</span><span class="sxs-lookup"><span data-stu-id="a6046-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="a6046-146">É recomendável usar [HSTS](#http-strict-transport-security-protocol-hsts) para sinalizar para os clientes que proteger somente o recurso deve ser redirecionada para o aplicativo (somente em produção).</span><span class="sxs-lookup"><span data-stu-id="a6046-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="a6046-147">Configuração de porta</span><span class="sxs-lookup"><span data-stu-id="a6046-147">Port configuration</span></span>

<span data-ttu-id="a6046-148">Uma porta deve estar disponível para o middleware redirecionar uma solicitação não segura para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a6046-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="a6046-149">Se nenhuma porta estiver disponível:</span><span class="sxs-lookup"><span data-stu-id="a6046-149">If no port is available:</span></span>

* <span data-ttu-id="a6046-150">Redirecionamento de HTTPS não ocorre.</span><span class="sxs-lookup"><span data-stu-id="a6046-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="a6046-151">O middleware registra o aviso "Falha ao determinar a porta https para redirecionamento."</span><span class="sxs-lookup"><span data-stu-id="a6046-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="a6046-152">Especifique a porta HTTPS usando qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="a6046-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="a6046-153">Definir [HttpsRedirectionOptions.HttpsPort](#options).</span><span class="sxs-lookup"><span data-stu-id="a6046-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>
* <span data-ttu-id="a6046-154">Defina as `ASPNETCORE_HTTPS_PORT` variável de ambiente ou [definição de configuração do Host da Web https_port](xref:fundamentals/host/web-host#https-port):</span><span class="sxs-lookup"><span data-stu-id="a6046-154">Set the `ASPNETCORE_HTTPS_PORT` environment variable or [https_port Web Host configuration setting](xref:fundamentals/host/web-host#https-port):</span></span>

  <span data-ttu-id="a6046-155">**Chave**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="a6046-155">**Key**: `https_port`</span></span>  
  <span data-ttu-id="a6046-156">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="a6046-156">**Type**: *string*</span></span>  
  <span data-ttu-id="a6046-157">**Padrão**: um valor padrão não está definido.</span><span class="sxs-lookup"><span data-stu-id="a6046-157">**Default**: A default value isn't set.</span></span>  
  <span data-ttu-id="a6046-158">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="a6046-158">**Set using**: `UseSetting`</span></span>  
  <span data-ttu-id="a6046-159">**Variável de ambiente**: `<PREFIX_>HTTPS_PORT` (É o prefixo `ASPNETCORE_` ao usar o [Host Web](xref:fundamentals/host/web-host).)</span><span class="sxs-lookup"><span data-stu-id="a6046-159">**Environment variable**: `<PREFIX_>HTTPS_PORT` (The prefix is `ASPNETCORE_` when using the [Web Host](xref:fundamentals/host/web-host).)</span></span>

  <span data-ttu-id="a6046-160">Ao configurar uma <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> em `Program`:</span><span class="sxs-lookup"><span data-stu-id="a6046-160">When configuring an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> in `Program`:</span></span>

  [!code-csharp[](enforcing-ssl/sample-snapshot/Program.cs?name=snippet_Program&highlight=10)]
* <span data-ttu-id="a6046-161">Indicar uma porta com o esquema segura usando o `ASPNETCORE_URLS` variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="a6046-161">Indicate a port with the secure scheme using the `ASPNETCORE_URLS` environment variable.</span></span> <span data-ttu-id="a6046-162">A variável de ambiente configura o servidor.</span><span class="sxs-lookup"><span data-stu-id="a6046-162">The environment variable configures the server.</span></span> <span data-ttu-id="a6046-163">O middleware indiretamente descobre a porta HTTPS por meio de <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="a6046-163">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="a6046-164">Essa abordagem não funciona em implantações de proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="a6046-164">This approach doesn't work in reverse proxy deployments.</span></span>
* <span data-ttu-id="a6046-165">No desenvolvimento, defina uma URL HTTPS *launchsettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="a6046-165">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="a6046-166">Habilite HTTPS quando o IIS Express é usado.</span><span class="sxs-lookup"><span data-stu-id="a6046-166">Enable HTTPS when IIS Express is used.</span></span>
* <span data-ttu-id="a6046-167">Configurar um ponto de extremidade de URL HTTPS para uma implantação de borda para o público do [Kestrel](xref:fundamentals/servers/kestrel) server ou [HTTP. sys](xref:fundamentals/servers/httpsys) server.</span><span class="sxs-lookup"><span data-stu-id="a6046-167">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="a6046-168">Somente **uma porta HTTPS** é usado pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a6046-168">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="a6046-169">O middleware descobre a porta por meio de <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="a6046-169">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="a6046-170">Quando um aplicativo é executado em uma configuração de proxy reverso, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> não está disponível.</span><span class="sxs-lookup"><span data-stu-id="a6046-170">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="a6046-171">Defina a porta usando uma das outras abordagens descritas nesta seção.</span><span class="sxs-lookup"><span data-stu-id="a6046-171">Set the port using one of the other approaches described in this section.</span></span>

<span data-ttu-id="a6046-172">Quando o Kestrel ou HTTP. sys é usado como um servidor de borda para o público, o Kestrel ou HTTP. sys deve ser configurado para escutar em ambos:</span><span class="sxs-lookup"><span data-stu-id="a6046-172">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="a6046-173">A porta segura em que o cliente é redirecionado (normalmente, 443 em 5001 no desenvolvimento e produção).</span><span class="sxs-lookup"><span data-stu-id="a6046-173">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="a6046-174">A porta não segura (normalmente, 80 na produção) e 5000 no desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="a6046-174">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="a6046-175">A porta não segura deve estar acessível pelo cliente para que o aplicativo para receber uma solicitação não segura e redirecionar o cliente para a porta segura.</span><span class="sxs-lookup"><span data-stu-id="a6046-175">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="a6046-176">Para obter mais informações, consulte [configuração de ponto de extremidade do Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) ou <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="a6046-176">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="a6046-177">Cenários de implantação</span><span class="sxs-lookup"><span data-stu-id="a6046-177">Deployment scenarios</span></span>

<span data-ttu-id="a6046-178">Qualquer firewall entre o cliente e o servidor também deve ter as portas de comunicação aberto para o tráfego.</span><span class="sxs-lookup"><span data-stu-id="a6046-178">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="a6046-179">Se as solicitações são encaminhadas em uma configuração de proxy reverso, use [Middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer) antes de chamar Middleware de redirecionamento de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a6046-179">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="a6046-180">Encaminhado atualizações de Middleware de cabeçalhos a `Request.Scheme`, usando o `X-Forwarded-Proto` cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="a6046-180">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="a6046-181">O middleware permite redirecionar URIs e outras políticas de segurança para funcionar corretamente.</span><span class="sxs-lookup"><span data-stu-id="a6046-181">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="a6046-182">Quando o Middleware de cabeçalhos encaminhados não for usado, o aplicativo de back-end não pode receber o esquema correto e acabar em um loop de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="a6046-182">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="a6046-183">Uma mensagem de erro comuns do usuário final é que muitos redirecionamentos ocorreram.</span><span class="sxs-lookup"><span data-stu-id="a6046-183">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="a6046-184">Ao implantar o serviço de aplicativo do Azure, siga as orientações em [Tutorial: vincular um certificado SSL personalizado ao serviço Aplicativos Web do Azure](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="a6046-184">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="a6046-185">Opções</span><span class="sxs-lookup"><span data-stu-id="a6046-185">Options</span></span>

<span data-ttu-id="a6046-186">O seguinte realçado código chama [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) para configurar as opções de middleware:</span><span class="sxs-lookup"><span data-stu-id="a6046-186">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=14-99)]

<span data-ttu-id="a6046-187">Chamando `AddHttpsRedirection` só é necessário alterar os valores de `HttpsPort` ou `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="a6046-187">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="a6046-188">O código realçado anterior:</span><span class="sxs-lookup"><span data-stu-id="a6046-188">The preceding highlighted code:</span></span>

* <span data-ttu-id="a6046-189">Conjuntos [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) para <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, que é o valor padrão.</span><span class="sxs-lookup"><span data-stu-id="a6046-189">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="a6046-190">Use os campos do <xref:Microsoft.AspNetCore.Http.StatusCodes> classe atribuições para `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="a6046-190">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="a6046-191">Define a porta HTTPS para 5001.</span><span class="sxs-lookup"><span data-stu-id="a6046-191">Sets the HTTPS port to 5001.</span></span> <span data-ttu-id="a6046-192">O valor padrão é 443.</span><span class="sxs-lookup"><span data-stu-id="a6046-192">The default value is 443.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="a6046-193">Configurar redirecionamentos permanentes em produção</span><span class="sxs-lookup"><span data-stu-id="a6046-193">Configure permanent redirects in production</span></span>

<span data-ttu-id="a6046-194">O middleware usará como padrão para envio de uma [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) com todos os redirecionamentos.</span><span class="sxs-lookup"><span data-stu-id="a6046-194">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="a6046-195">Se você preferir enviar um código de status de redirecionamento permanente quando o aplicativo estiver em um ambiente de não desenvolvimento, encapsule a configuração de opções de middleware em uma verificação condicional para um ambiente de desenvolvimento não.</span><span class="sxs-lookup"><span data-stu-id="a6046-195">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

<span data-ttu-id="a6046-196">Ao configurar uma `IWebHostBuilder` na *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a6046-196">When configuring an `IWebHostBuilder` in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="a6046-197">Abordagem alternativa de Middleware de redirecionamento de HTTPS</span><span class="sxs-lookup"><span data-stu-id="a6046-197">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="a6046-198">Uma alternativa ao uso de Middleware de redirecionamento de HTTPS (`UseHttpsRedirection`) é usar o Middleware de reconfiguração de URL (`AddRedirectToHttps`).</span><span class="sxs-lookup"><span data-stu-id="a6046-198">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="a6046-199">`AddRedirectToHttps` também pode definir o código de status e a porta quando o redirecionamento é executado.</span><span class="sxs-lookup"><span data-stu-id="a6046-199">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="a6046-200">Para obter mais informações, consulte [Middleware de reconfiguração de URL](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="a6046-200">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="a6046-201">Ao redirecionar para HTTPS sem a necessidade de regras de redirecionamento adicional, é recomendável usar o Middleware de redirecionamento de HTTPS (`UseHttpsRedirection`) descrito neste tópico.</span><span class="sxs-lookup"><span data-stu-id="a6046-201">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="a6046-202">O [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) é usada para exigir HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a6046-202">The [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) is used to require HTTPS.</span></span> <span data-ttu-id="a6046-203">`[RequireHttpsAttribute]` pode decorar controladores ou métodos, ou podem ser aplicadas globalmente.</span><span class="sxs-lookup"><span data-stu-id="a6046-203">`[RequireHttpsAttribute]` can decorate controllers or methods, or can be applied globally.</span></span> <span data-ttu-id="a6046-204">Para aplicar o atributo globalmente, adicione o seguinte código ao `ConfigureServices` em `Startup`:</span><span class="sxs-lookup"><span data-stu-id="a6046-204">To apply the attribute globally, add the following code to `ConfigureServices` in `Startup`:</span></span>

[!code-csharp[](~/security/authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-999)]

<span data-ttu-id="a6046-205">O código realçado anterior requer que todas as solicitações usem `HTTPS`; portanto, as solicitações HTTP são ignoradas.</span><span class="sxs-lookup"><span data-stu-id="a6046-205">The preceding highlighted code requires all requests use `HTTPS`; therefore, HTTP requests are ignored.</span></span> <span data-ttu-id="a6046-206">O seguinte código realçado redireciona todas as solicitações HTTP para HTTPS:</span><span class="sxs-lookup"><span data-stu-id="a6046-206">The following highlighted code redirects all HTTP requests to HTTPS:</span></span>

[!code-csharp[](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-999)]

<span data-ttu-id="a6046-207">Para obter mais informações, consulte [Middleware de reconfiguração de URL](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="a6046-207">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span> <span data-ttu-id="a6046-208">O middleware também permite que o aplicativo para definir o código de status ou o código de status e a porta quando o redirecionamento é executado.</span><span class="sxs-lookup"><span data-stu-id="a6046-208">The middleware also permits the app to set the status code or the status code and the port when the redirect is executed.</span></span>

<span data-ttu-id="a6046-209">Exigir HTTPS globalmente (`options.Filters.Add(new RequireHttpsAttribute());`) é uma prática recomendada de segurança.</span><span class="sxs-lookup"><span data-stu-id="a6046-209">Requiring HTTPS globally (`options.Filters.Add(new RequireHttpsAttribute());`) is a security best practice.</span></span> <span data-ttu-id="a6046-210">Aplicando o `[RequireHttps]` atributo a todas as páginas do Razor/controladores não é considerado tão seguro quanto exigir HTTPS globalmente.</span><span class="sxs-lookup"><span data-stu-id="a6046-210">Applying the `[RequireHttps]` attribute to all controllers/Razor Pages isn't considered as secure as requiring HTTPS globally.</span></span> <span data-ttu-id="a6046-211">Você não pode garantir o `[RequireHttps]` atributo é aplicado quando novos controladores e páginas Razor são adicionadas.</span><span class="sxs-lookup"><span data-stu-id="a6046-211">You can't guarantee the `[RequireHttps]` attribute is applied when new controllers and Razor Pages are added.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="a6046-212">Protocolo de segurança de transporte estrito HTTP (HSTS)</span><span class="sxs-lookup"><span data-stu-id="a6046-212">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="a6046-213">Por [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [segurança de transporte estrito HTTP (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) é um aprimoramento de segurança opcional é especificado por um aplicativo web com o uso de um cabeçalho de resposta.</span><span class="sxs-lookup"><span data-stu-id="a6046-213">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="a6046-214">Quando um [navegador que ofereça suporte a HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support) recebe esse cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="a6046-214">When a [browser that supports HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support) receives this header:</span></span>

* <span data-ttu-id="a6046-215">O navegador armazena a configuração para o domínio que evita o envio de qualquer comunicação por HTTP.</span><span class="sxs-lookup"><span data-stu-id="a6046-215">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="a6046-216">O navegador força todas as comunicações via HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a6046-216">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="a6046-217">O navegador impede que o usuário usando os certificados não confiáveis ou é inválidos.</span><span class="sxs-lookup"><span data-stu-id="a6046-217">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="a6046-218">O navegador desativa prompts que permitem que um usuário para confiar temporariamente esses certificados.</span><span class="sxs-lookup"><span data-stu-id="a6046-218">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="a6046-219">Como HSTS é imposta pelo cliente, ele tem algumas limitações:</span><span class="sxs-lookup"><span data-stu-id="a6046-219">Because HSTS is enforced by the client it has some limitations:</span></span>

* <span data-ttu-id="a6046-220">O cliente deve oferecer suporte a HSTS.</span><span class="sxs-lookup"><span data-stu-id="a6046-220">The client must support HSTS.</span></span>
* <span data-ttu-id="a6046-221">HSTS requer pelo menos uma solicitação HTTPS bem-sucedida para estabelecer a política HSTS.</span><span class="sxs-lookup"><span data-stu-id="a6046-221">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="a6046-222">O aplicativo deve verificar todas as solicitações HTTP e redirecionar ou rejeitar a solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="a6046-222">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="a6046-223">ASP.NET Core 2.1 ou posterior implementa HSTS com o `UseHsts` método de extensão.</span><span class="sxs-lookup"><span data-stu-id="a6046-223">ASP.NET Core 2.1 or later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="a6046-224">O código a seguir chama `UseHsts` quando o aplicativo não está no [modo de desenvolvimento](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="a6046-224">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=10)]

<span data-ttu-id="a6046-225">`UseHsts` não é recomendado em desenvolvimento porque as configurações de HSTS são altamente armazenáveis em cache por navegadores.</span><span class="sxs-lookup"><span data-stu-id="a6046-225">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="a6046-226">Por padrão, `UseHsts` exclui o endereço de loopback local.</span><span class="sxs-lookup"><span data-stu-id="a6046-226">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="a6046-227">Para ambientes de produção implementando HTTPS pela primeira vez, definir inicial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) com um valor pequeno usando um do <xref:System.TimeSpan> métodos.</span><span class="sxs-lookup"><span data-stu-id="a6046-227">For production environments implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="a6046-228">Defina o valor de horas como não mais do que um único dia caso você precise reverter a infraestrutura HTTPS para HTTP.</span><span class="sxs-lookup"><span data-stu-id="a6046-228">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="a6046-229">Depois que você estiver confiante em sustentabilidade da configuração do HTTPS, aumente o valor de idade máxima HSTS; um valor comumente usado é um ano.</span><span class="sxs-lookup"><span data-stu-id="a6046-229">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS max-age value; a commonly used value is one year.</span></span>

<span data-ttu-id="a6046-230">O código a seguir:</span><span class="sxs-lookup"><span data-stu-id="a6046-230">The following code:</span></span>

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=5-12)]

* <span data-ttu-id="a6046-231">Define o parâmetro de pré-carregamento do cabeçalho de segurança de transporte estrito.</span><span class="sxs-lookup"><span data-stu-id="a6046-231">Sets the preload parameter of the Strict-Transport-Security header.</span></span> <span data-ttu-id="a6046-232">Pré-carregamento não faz parte dos [especificação RFC HSTS](https://tools.ietf.org/html/rfc6797), mas é compatível com navegadores da web para pré-carregar sites HSTS na nova instalação.</span><span class="sxs-lookup"><span data-stu-id="a6046-232">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="a6046-233">Veja [https://hstspreload.org/](https://hstspreload.org/) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a6046-233">See [https://hstspreload.org/](https://hstspreload.org/) for more information.</span></span>
* <span data-ttu-id="a6046-234">Habilita [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), que se aplica a política HSTS para hospedar subdomínios.</span><span class="sxs-lookup"><span data-stu-id="a6046-234">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="a6046-235">Define explicitamente o parâmetro de idade máxima do cabeçalho de segurança de transporte estrito para 60 dias.</span><span class="sxs-lookup"><span data-stu-id="a6046-235">Explicitly sets the max-age parameter of the Strict-Transport-Security header to 60 days.</span></span> <span data-ttu-id="a6046-236">Se não for definido, o padrão é 30 dias.</span><span class="sxs-lookup"><span data-stu-id="a6046-236">If not set, defaults to 30 days.</span></span> <span data-ttu-id="a6046-237">Consulte a [max-age diretiva](https://tools.ietf.org/html/rfc6797#section-6.1.1) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a6046-237">See the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1) for more information.</span></span>
* <span data-ttu-id="a6046-238">Adiciona `example.com` à lista de hosts a serem excluídos.</span><span class="sxs-lookup"><span data-stu-id="a6046-238">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="a6046-239">`UseHsts` Exclui os seguintes hosts de loopback:</span><span class="sxs-lookup"><span data-stu-id="a6046-239">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="a6046-240">`localhost`: O endereço de loopback do IPv4.</span><span class="sxs-lookup"><span data-stu-id="a6046-240">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="a6046-241">`127.0.0.1`: O endereço de loopback do IPv4.</span><span class="sxs-lookup"><span data-stu-id="a6046-241">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="a6046-242">`[::1]`: O endereço de loopback do IPv6.</span><span class="sxs-lookup"><span data-stu-id="a6046-242">`[::1]` : The IPv6 loopback address.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="a6046-243">Recusa de HTTPS/HSTS na criação do projeto</span><span class="sxs-lookup"><span data-stu-id="a6046-243">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="a6046-244">Em alguns cenários de serviço de back-end em que a segurança de conexão é manipulada na borda da rede para o público, configurando a segurança de conexão em cada nó não é necessária.</span><span class="sxs-lookup"><span data-stu-id="a6046-244">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="a6046-245">Aplicativos gerados a partir de modelos no Visual Studio ou na Web a [dotnet new](/dotnet/core/tools/dotnet-new) comando enable [redirecionamento a HTTPS](#require-https) e [HSTS](#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="a6046-245">Web apps generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="a6046-246">Para implantações que não exigem esses cenários, você pode recusar HTTPS/HSTS quando o aplicativo é criado a partir do modelo.</span><span class="sxs-lookup"><span data-stu-id="a6046-246">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="a6046-247">A recusa de HTTPS/HSTS:</span><span class="sxs-lookup"><span data-stu-id="a6046-247">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a6046-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6046-248">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="a6046-249">Desmarque a **configurar para HTTPS** caixa de seleção.</span><span class="sxs-lookup"><span data-stu-id="a6046-249">Uncheck the **Configure for HTTPS** check box.</span></span>

![Novo aplicativo Web ASP.NET Core caixa de diálogo mostrando a configurar para HTTPS caixa de seleção desmarcada.](enforcing-ssl/_static/out.png)

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a6046-251">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="a6046-251">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="a6046-252">Use a opção `--no-https`.</span><span class="sxs-lookup"><span data-stu-id="a6046-252">Use the `--no-https` option.</span></span> <span data-ttu-id="a6046-253">Por exemplo</span><span class="sxs-lookup"><span data-stu-id="a6046-253">For example</span></span>

```console
dotnet new webapp --no-https
```

---

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="a6046-254">Confiar no certificado de desenvolvimento do ASP.NET Core HTTPS no Windows e macOS</span><span class="sxs-lookup"><span data-stu-id="a6046-254">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="a6046-255">SDK do .NET core inclui um certificado de desenvolvimento de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a6046-255">.NET Core SDK includes a HTTPS development certificate.</span></span> <span data-ttu-id="a6046-256">O certificado é instalado como parte da experiência de primeira execução.</span><span class="sxs-lookup"><span data-stu-id="a6046-256">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="a6046-257">Por exemplo, `dotnet --info` produz uma saída semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="a6046-257">For example, `dotnet --info` produces output similar to the following:</span></span>

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="a6046-258">Instalar o SDK do .NET Core instala o certificado de desenvolvimento HTTPS do ASP.NET Core no repositório de certificados do usuário local.</span><span class="sxs-lookup"><span data-stu-id="a6046-258">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="a6046-259">O certificado foi instalado, mas não for confiável.</span><span class="sxs-lookup"><span data-stu-id="a6046-259">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="a6046-260">Confiar em certificado execute a etapa única para executar o dotnet `dev-certs` ferramenta:</span><span class="sxs-lookup"><span data-stu-id="a6046-260">To trust the certificate perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```console
dotnet dev-certs https --trust
```

<span data-ttu-id="a6046-261">O comando a seguir fornece ajuda para a ferramenta `dev-certs`:</span><span class="sxs-lookup"><span data-stu-id="a6046-261">The following command provides help on the `dev-certs` tool:</span></span>

```console
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="a6046-262">Como configurar um certificado de desenvolvedor para o Docker</span><span class="sxs-lookup"><span data-stu-id="a6046-262">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="a6046-263">Ver [esse problema de GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span><span class="sxs-lookup"><span data-stu-id="a6046-263">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span></span>

::: moniker-end

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="a6046-264">Confiar em certificado HTTPS do subsistema do Windows para Linux</span><span class="sxs-lookup"><span data-stu-id="a6046-264">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="a6046-265">O subsistema Windows para Linux (WSL) gera um certificado autoassinado de HTTPS. Para configurar o repositório de certificados do Windows para confiar no certificado WSL:</span><span class="sxs-lookup"><span data-stu-id="a6046-265">The Windows Subsystem for Linux (WSL) generates a HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="a6046-266">Execute o seguinte comando para exportar o certificado WSL gerado: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span><span class="sxs-lookup"><span data-stu-id="a6046-266">Run the following command to export the WSL generated certificate: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span></span>
* <span data-ttu-id="a6046-267">Em uma janela WSL, execute o seguinte comando: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span><span class="sxs-lookup"><span data-stu-id="a6046-267">In a WSL window, run the following command: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span></span>

  <span data-ttu-id="a6046-268">O comando anterior define as variáveis de ambiente para que o Linux usa o certificado confiável do Windows.</span><span class="sxs-lookup"><span data-stu-id="a6046-268">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="additional-information"></a><span data-ttu-id="a6046-269">Informações adicionais</span><span class="sxs-lookup"><span data-stu-id="a6046-269">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="a6046-270">Hospede o ASP.NET Core no Linux com o Apache: Configuração HTTPS</span><span class="sxs-lookup"><span data-stu-id="a6046-270">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="a6046-271">Hospede o ASP.NET Core no Linux com Nginx: Configuração HTTPS</span><span class="sxs-lookup"><span data-stu-id="a6046-271">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="a6046-272">Como configurar o SSL no IIS</span><span class="sxs-lookup"><span data-stu-id="a6046-272">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="a6046-273">Suporte a navegador HSTS OWASP</span><span class="sxs-lookup"><span data-stu-id="a6046-273">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
