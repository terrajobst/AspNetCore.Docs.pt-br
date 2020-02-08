---
title: Impor HTTPS em ASP.NET Core
author: rick-anderson
description: Saiba como exigir HTTPS/TLS em um aplicativo Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
uid: security/enforcing-ssl
ms.openlocfilehash: 59883a8165040fa58edb2f6cf22d4d6b3abf6f3e
ms.sourcegitcommit: 80286715afb93c4d13c931b008016d6086c0312b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77074543"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="f9562-103">Impor HTTPS em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f9562-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="f9562-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f9562-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f9562-105">Este documento demonstra como:</span><span class="sxs-lookup"><span data-stu-id="f9562-105">This document shows how to:</span></span>

* <span data-ttu-id="f9562-106">Exigir HTTPS para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="f9562-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="f9562-107">Redirecione todas as solicitações HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9562-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="f9562-108">Nenhuma API pode impedir que um cliente envie dados confidenciais na primeira solicitação.</span><span class="sxs-lookup"><span data-stu-id="f9562-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="f9562-109">Projetos de API</span><span class="sxs-lookup"><span data-stu-id="f9562-109">API projects</span></span>
>
> <span data-ttu-id="f9562-110">Não **use** [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) em APIs Web que recebam informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="f9562-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="f9562-111">`RequireHttpsAttribute` usa códigos de status HTTP para redirecionar os navegadores de HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9562-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="f9562-112">Os clientes de API podem não entender ou obedecer a redirecionamentos de HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9562-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="f9562-113">Esses clientes podem enviar informações por HTTP.</span><span class="sxs-lookup"><span data-stu-id="f9562-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="f9562-114">As APIs da Web devem ser:</span><span class="sxs-lookup"><span data-stu-id="f9562-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="f9562-115">Não escutar em HTTP.</span><span class="sxs-lookup"><span data-stu-id="f9562-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="f9562-116">Feche a conexão com o código de status 400 (solicitação inválida) e não atenda à solicitação.</span><span class="sxs-lookup"><span data-stu-id="f9562-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="f9562-117">Projetos de API e HSTS</span><span class="sxs-lookup"><span data-stu-id="f9562-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="f9562-118">Os projetos de API padrão não incluem [HSTS](#hsts) porque HSTS geralmente é uma instrução somente de navegador.</span><span class="sxs-lookup"><span data-stu-id="f9562-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="f9562-119">Outros chamadores, como telefone ou aplicativos da área de trabalho, **não** obedecem à instrução.</span><span class="sxs-lookup"><span data-stu-id="f9562-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="f9562-120">Mesmo em navegadores, uma única chamada autenticada para uma API sobre HTTP tem riscos em redes inseguras.</span><span class="sxs-lookup"><span data-stu-id="f9562-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="f9562-121">A abordagem segura é configurar projetos de API para escutar e responder apenas por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9562-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="f9562-122">Projetos de API</span><span class="sxs-lookup"><span data-stu-id="f9562-122">API projects</span></span>
>
> <span data-ttu-id="f9562-123">Não **use** [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) em APIs Web que recebam informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="f9562-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="f9562-124">`RequireHttpsAttribute` usa códigos de status HTTP para redirecionar os navegadores de HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9562-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="f9562-125">Os clientes de API podem não entender ou obedecer a redirecionamentos de HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9562-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="f9562-126">Esses clientes podem enviar informações por HTTP.</span><span class="sxs-lookup"><span data-stu-id="f9562-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="f9562-127">As APIs da Web devem ser:</span><span class="sxs-lookup"><span data-stu-id="f9562-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="f9562-128">Não escutar em HTTP.</span><span class="sxs-lookup"><span data-stu-id="f9562-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="f9562-129">Feche a conexão com o código de status 400 (solicitação inválida) e não atenda à solicitação.</span><span class="sxs-lookup"><span data-stu-id="f9562-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="f9562-130">Solicitar HTTPS</span><span class="sxs-lookup"><span data-stu-id="f9562-130">Require HTTPS</span></span>

<span data-ttu-id="f9562-131">É recomendável que a produção ASP.NET Core aplicativos Web usem:</span><span class="sxs-lookup"><span data-stu-id="f9562-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="f9562-132">Middleware de redirecionamento de HTTPS (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) para redirecionar solicitações HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9562-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="f9562-133">Middleware HSTS ([UseHsts](#http-strict-transport-security-protocol-hsts)) para enviar cabeçalhos HSTS (protocolo de segurança de transporte estrito) http para clientes.</span><span class="sxs-lookup"><span data-stu-id="f9562-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="f9562-134">Os aplicativos implantados em uma configuração de proxy reverso permitem que o proxy manipule a segurança de conexão (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f9562-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="f9562-135">Se o proxy também tratar o redirecionamento de HTTPS, não será necessário usar o middleware de redirecionamento de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9562-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="f9562-136">Se o servidor proxy também tratar da gravação de cabeçalhos HSTS (por exemplo, [suporte a HSTS nativo no IIS 10,0 (1709) ou posterior](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), o middleware HSTS não será exigido pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9562-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="f9562-137">Para obter mais informações, consulte [recusar https/HSTS na criação do projeto](#opt-out-of-httpshsts-on-project-creation).</span><span class="sxs-lookup"><span data-stu-id="f9562-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="f9562-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="f9562-138">UseHttpsRedirection</span></span>

<span data-ttu-id="f9562-139">O código a seguir chama `UseHttpsRedirection` na classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="f9562-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="f9562-140">O código realçado anterior:</span><span class="sxs-lookup"><span data-stu-id="f9562-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="f9562-141">Usa o padrão [HttpsRedirectionOptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span><span class="sxs-lookup"><span data-stu-id="f9562-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="f9562-142">Usa o [HttpsRedirectionOptions. HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (nulo) padrão, a menos que seja substituído pelo `ASPNETCORE_HTTPS_PORT` variável de ambiente ou [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span><span class="sxs-lookup"><span data-stu-id="f9562-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="f9562-143">É recomendável usar redirecionamentos temporários em vez de redirecionamentos permanentes.</span><span class="sxs-lookup"><span data-stu-id="f9562-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="f9562-144">O cache de link pode causar comportamento instável em ambientes de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="f9562-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="f9562-145">Se você preferir enviar um código de status de redirecionamento permanente quando o aplicativo estiver em um ambiente que não seja de desenvolvimento, consulte a seção [Configurar redirecionamentos permanentes na produção](#configure-permanent-redirects-in-production) .</span><span class="sxs-lookup"><span data-stu-id="f9562-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="f9562-146">É recomendável usar [HSTS](#http-strict-transport-security-protocol-hsts) para sinalizar para clientes que apenas as solicitações de recursos de segurança devem ser enviadas para o aplicativo (somente em produção).</span><span class="sxs-lookup"><span data-stu-id="f9562-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="f9562-147">Configuração de portas</span><span class="sxs-lookup"><span data-stu-id="f9562-147">Port configuration</span></span>

<span data-ttu-id="f9562-148">Uma porta deve estar disponível para o middleware redirecionar uma solicitação insegura para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9562-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="f9562-149">Se nenhuma porta estiver disponível:</span><span class="sxs-lookup"><span data-stu-id="f9562-149">If no port is available:</span></span>

* <span data-ttu-id="f9562-150">O redirecionamento para HTTPS não ocorre.</span><span class="sxs-lookup"><span data-stu-id="f9562-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="f9562-151">O middleware registra o aviso "falha ao determinar a porta HTTPS para redirecionamento".</span><span class="sxs-lookup"><span data-stu-id="f9562-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="f9562-152">Especifique a porta HTTPS usando qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="f9562-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="f9562-153">Defina [HttpsRedirectionOptions. HttpsPort](#options).</span><span class="sxs-lookup"><span data-stu-id="f9562-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="f9562-154">Defina a [configuração de host](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port)`https_port`:</span><span class="sxs-lookup"><span data-stu-id="f9562-154">Set the `https_port` [host setting](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="f9562-155">Na configuração do host.</span><span class="sxs-lookup"><span data-stu-id="f9562-155">In host configuration.</span></span>
  * <span data-ttu-id="f9562-156">Definindo a variável de ambiente `ASPNETCORE_HTTPS_PORT`.</span><span class="sxs-lookup"><span data-stu-id="f9562-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="f9562-157">Adicionando uma entrada de nível superior em *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="f9562-157">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* <span data-ttu-id="f9562-158">Indique uma porta com o esquema seguro usando a [variável de ambiente ASPNETCORE_URLS](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls).</span><span class="sxs-lookup"><span data-stu-id="f9562-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="f9562-159">A variável de ambiente configura o servidor.</span><span class="sxs-lookup"><span data-stu-id="f9562-159">The environment variable configures the server.</span></span> <span data-ttu-id="f9562-160">O middleware descobre indiretamente a porta HTTPS por meio de <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="f9562-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="f9562-161">Essa abordagem não funciona em implantações de proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="f9562-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="f9562-162">Defina a [configuração de host](xref:fundamentals/host/web-host#https-port)`https_port`:</span><span class="sxs-lookup"><span data-stu-id="f9562-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="f9562-163">Na configuração do host.</span><span class="sxs-lookup"><span data-stu-id="f9562-163">In host configuration.</span></span>
  * <span data-ttu-id="f9562-164">Definindo a variável de ambiente `ASPNETCORE_HTTPS_PORT`.</span><span class="sxs-lookup"><span data-stu-id="f9562-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="f9562-165">Adicionando uma entrada de nível superior em *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="f9562-165">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* <span data-ttu-id="f9562-166">Indique uma porta com o esquema seguro usando a [variável de ambiente ASPNETCORE_URLS](xref:fundamentals/host/web-host#server-urls).</span><span class="sxs-lookup"><span data-stu-id="f9562-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="f9562-167">A variável de ambiente configura o servidor.</span><span class="sxs-lookup"><span data-stu-id="f9562-167">The environment variable configures the server.</span></span> <span data-ttu-id="f9562-168">O middleware descobre indiretamente a porta HTTPS por meio de <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="f9562-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="f9562-169">Essa abordagem não funciona em implantações de proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="f9562-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="f9562-170">Em desenvolvimento, defina uma URL HTTPS em *launchsettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="f9562-170">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="f9562-171">Habilite HTTPS quando IIS Express for usado.</span><span class="sxs-lookup"><span data-stu-id="f9562-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="f9562-172">Configure um ponto de extremidade de URL HTTPS para uma implantação de borda voltada para o público do servidor [Kestrel](xref:fundamentals/servers/kestrel) ou [http. sys](xref:fundamentals/servers/httpsys) .</span><span class="sxs-lookup"><span data-stu-id="f9562-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="f9562-173">Somente **uma porta https** é usada pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9562-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="f9562-174">O middleware descobre a porta por meio de <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="f9562-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="f9562-175">Quando um aplicativo é executado em uma configuração de proxy reverso, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> não está disponível.</span><span class="sxs-lookup"><span data-stu-id="f9562-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="f9562-176">Defina a porta usando uma das outras abordagens descritas nesta seção.</span><span class="sxs-lookup"><span data-stu-id="f9562-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="f9562-177">Implantações do Edge</span><span class="sxs-lookup"><span data-stu-id="f9562-177">Edge deployments</span></span> 

<span data-ttu-id="f9562-178">Quando Kestrel ou HTTP. sys é usado como um servidor de borda voltado ao público, Kestrel ou HTTP. sys deve ser configurado para escutar em ambos:</span><span class="sxs-lookup"><span data-stu-id="f9562-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="f9562-179">A porta segura em que o cliente é redirecionado (normalmente, 443 em produção e 5001 em desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="f9562-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="f9562-180">A porta insegura (normalmente, 80 em produção e 5000 em desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="f9562-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="f9562-181">A porta insegura deve ser acessível pelo cliente para que o aplicativo receba uma solicitação insegura e redirecione o cliente para a porta segura.</span><span class="sxs-lookup"><span data-stu-id="f9562-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="f9562-182">Para obter mais informações, consulte [configuração de ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) ou <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="f9562-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="f9562-183">Cenários de implantação</span><span class="sxs-lookup"><span data-stu-id="f9562-183">Deployment scenarios</span></span>

<span data-ttu-id="f9562-184">Qualquer firewall entre o cliente e o servidor também deve ter portas de comunicação abertas para tráfego.</span><span class="sxs-lookup"><span data-stu-id="f9562-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="f9562-185">Se as solicitações forem encaminhadas em uma configuração de proxy reverso, use o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer) antes de chamar o middleware de redirecionamento de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9562-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="f9562-186">O middleware de cabeçalhos encaminhados atualiza o `Request.Scheme`, usando o cabeçalho `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="f9562-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="f9562-187">O middleware permite que os URIs de redirecionamento e outras políticas de segurança funcionem corretamente.</span><span class="sxs-lookup"><span data-stu-id="f9562-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="f9562-188">Quando o middleware de cabeçalhos encaminhados não é usado, o aplicativo de back-end pode não receber o esquema correto e terminar em um loop de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="f9562-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="f9562-189">Uma mensagem de erro comum do usuário final é que ocorreram muitos redirecionamentos.</span><span class="sxs-lookup"><span data-stu-id="f9562-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="f9562-190">Ao implantar no serviço Azure App, siga as diretrizes em [tutorial: associar um certificado SSL personalizado existente aos aplicativos Web do Azure](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="f9562-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="f9562-191">Opções</span><span class="sxs-lookup"><span data-stu-id="f9562-191">Options</span></span>

<span data-ttu-id="f9562-192">O código realçado a seguir chama [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) para configurar opções de middleware:</span><span class="sxs-lookup"><span data-stu-id="f9562-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="f9562-193">A chamada de `AddHttpsRedirection` só é necessária para alterar os valores de `HttpsPort` ou `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="f9562-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="f9562-194">O código realçado anterior:</span><span class="sxs-lookup"><span data-stu-id="f9562-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="f9562-195">Define [HttpsRedirectionOptions. RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) como <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, que é o valor padrão.</span><span class="sxs-lookup"><span data-stu-id="f9562-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="f9562-196">Use os campos da classe <xref:Microsoft.AspNetCore.Http.StatusCodes> para as atribuições a serem `RedirectStatusCode`.</span><span class="sxs-lookup"><span data-stu-id="f9562-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="f9562-197">Define a porta HTTPS como 5001.</span><span class="sxs-lookup"><span data-stu-id="f9562-197">Sets the HTTPS port to 5001.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="f9562-198">Configurar redirecionamentos permanentes na produção</span><span class="sxs-lookup"><span data-stu-id="f9562-198">Configure permanent redirects in production</span></span>

<span data-ttu-id="f9562-199">O padrão do middleware é enviar um [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) com todos os redirecionamentos.</span><span class="sxs-lookup"><span data-stu-id="f9562-199">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="f9562-200">Se você preferir enviar um código de status de redirecionamento permanente quando o aplicativo estiver em um ambiente que não seja de desenvolvimento, coloque a configuração de opções de middleware em uma verificação condicional para um ambiente que não seja de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="f9562-200">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f9562-201">Ao configurar serviços no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f9562-201">When configuring services in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
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

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="f9562-202">Ao configurar serviços no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f9562-202">When configuring services in *Startup.cs*:</span></span>

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

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="f9562-203">Abordagem alternativa de middleware de redirecionamento de HTTPS</span><span class="sxs-lookup"><span data-stu-id="f9562-203">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="f9562-204">Uma alternativa ao uso do middleware de redirecionamento de HTTPS (`UseHttpsRedirection`) é usar o middleware de reescrita de URL (`AddRedirectToHttps`).</span><span class="sxs-lookup"><span data-stu-id="f9562-204">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="f9562-205">`AddRedirectToHttps` também pode definir o código de status e a porta quando o redirecionamento é executado.</span><span class="sxs-lookup"><span data-stu-id="f9562-205">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="f9562-206">Para obter mais informações, consulte [middleware de regravação de URL](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="f9562-206">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="f9562-207">Ao redirecionar para HTTPS sem a necessidade de regras de redirecionamento adicionais, recomendamos o uso do middleware de redirecionamento de HTTPS (`UseHttpsRedirection`) descrito neste tópico.</span><span class="sxs-lookup"><span data-stu-id="f9562-207">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="f9562-208">Protocolo de segurança de transporte estrito HTTP (HSTS)</span><span class="sxs-lookup"><span data-stu-id="f9562-208">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="f9562-209">Por [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), o [HSTS (segurança de transporte estrito) http](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) é um aprimoramento de segurança opcional que é especificado por um aplicativo Web por meio do uso de um cabeçalho de resposta.</span><span class="sxs-lookup"><span data-stu-id="f9562-209">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="f9562-210">Quando um [navegador que dá suporte a HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) recebe este cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="f9562-210">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="f9562-211">O navegador armazena a configuração para o domínio que impede o envio de qualquer comunicação via HTTP.</span><span class="sxs-lookup"><span data-stu-id="f9562-211">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="f9562-212">O navegador força toda a comunicação por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9562-212">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="f9562-213">O navegador impede que o usuário use certificados não confiáveis ou inválidos.</span><span class="sxs-lookup"><span data-stu-id="f9562-213">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="f9562-214">O navegador desabilita as solicitações que permitem que um usuário confie temporariamente em um certificado.</span><span class="sxs-lookup"><span data-stu-id="f9562-214">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="f9562-215">Como o HSTS é imposto pelo cliente, ele tem algumas limitações:</span><span class="sxs-lookup"><span data-stu-id="f9562-215">Because HSTS is enforced by the client it has some limitations:</span></span>

* <span data-ttu-id="f9562-216">O cliente deve dar suporte a HSTS.</span><span class="sxs-lookup"><span data-stu-id="f9562-216">The client must support HSTS.</span></span>
* <span data-ttu-id="f9562-217">HSTS requer pelo menos uma solicitação HTTPS bem-sucedida para estabelecer a política HSTS.</span><span class="sxs-lookup"><span data-stu-id="f9562-217">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="f9562-218">O aplicativo deve verificar cada solicitação HTTP e redirecionar ou rejeitar a solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="f9562-218">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="f9562-219">ASP.NET Core 2,1 e posterior implementa HSTS com o método de extensão `UseHsts`.</span><span class="sxs-lookup"><span data-stu-id="f9562-219">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="f9562-220">O código a seguir chama `UseHsts` quando o aplicativo não está no [modo de desenvolvimento](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="f9562-220">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="f9562-221">o `UseHsts` não é recomendado no desenvolvimento porque as configurações de HSTS são altamente armazenáveis em cache pelos navegadores.</span><span class="sxs-lookup"><span data-stu-id="f9562-221">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="f9562-222">Por padrão, `UseHsts` exclui o endereço de loopback local.</span><span class="sxs-lookup"><span data-stu-id="f9562-222">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="f9562-223">Para ambientes de produção que estão implementando HTTPS pela primeira vez, defina o [HstsOptions inicial. MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) como um valor pequeno usando um dos métodos <xref:System.TimeSpan>.</span><span class="sxs-lookup"><span data-stu-id="f9562-223">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="f9562-224">Defina o valor de horas para não mais do que um único dia, caso precise reverter a infraestrutura HTTPS para HTTP.</span><span class="sxs-lookup"><span data-stu-id="f9562-224">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="f9562-225">Depois que você estiver confiante na sustentabilidade da configuração de HTTPS, aumente o valor de idade máxima HSTS; um valor comumente usado é de um ano.</span><span class="sxs-lookup"><span data-stu-id="f9562-225">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS max-age value; a commonly used value is one year.</span></span>

<span data-ttu-id="f9562-226">O seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f9562-226">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="f9562-227">Define o parâmetro PreLoad do cabeçalho Strict-Transport-Security.</span><span class="sxs-lookup"><span data-stu-id="f9562-227">Sets the preload parameter of the Strict-Transport-Security header.</span></span> <span data-ttu-id="f9562-228">Pré-carregar não faz parte da [especificação RFC HSTS](https://tools.ietf.org/html/rfc6797), mas tem suporte de navegadores da Web para pré-carregar HSTS sites na nova instalação.</span><span class="sxs-lookup"><span data-stu-id="f9562-228">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="f9562-229">Veja [https://hstspreload.org/](https://hstspreload.org/) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="f9562-229">See [https://hstspreload.org/](https://hstspreload.org/) for more information.</span></span>
* <span data-ttu-id="f9562-230">Habilita [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), que aplica a política HSTS aos subdomínios de host.</span><span class="sxs-lookup"><span data-stu-id="f9562-230">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="f9562-231">Define explicitamente o parâmetro Max-age do cabeçalho Strict-Transport-Security como 60 dias.</span><span class="sxs-lookup"><span data-stu-id="f9562-231">Explicitly sets the max-age parameter of the Strict-Transport-Security header to 60 days.</span></span> <span data-ttu-id="f9562-232">Se não estiver definido, o padrão será 30 dias.</span><span class="sxs-lookup"><span data-stu-id="f9562-232">If not set, defaults to 30 days.</span></span> <span data-ttu-id="f9562-233">Consulte a [diretiva Max-age](https://tools.ietf.org/html/rfc6797#section-6.1.1) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="f9562-233">See the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1) for more information.</span></span>
* <span data-ttu-id="f9562-234">Adiciona `example.com` à lista de hosts a serem excluídos.</span><span class="sxs-lookup"><span data-stu-id="f9562-234">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="f9562-235">`UseHsts` exclui os seguintes hosts de auto-retorno:</span><span class="sxs-lookup"><span data-stu-id="f9562-235">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="f9562-236">`localhost`: o endereço de loopback IPv4.</span><span class="sxs-lookup"><span data-stu-id="f9562-236">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="f9562-237">`127.0.0.1`: o endereço de loopback IPv4.</span><span class="sxs-lookup"><span data-stu-id="f9562-237">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="f9562-238">`[::1]`: o endereço de loopback IPv6.</span><span class="sxs-lookup"><span data-stu-id="f9562-238">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="f9562-239">Recusa de HTTPS/HSTS na criação do projeto</span><span class="sxs-lookup"><span data-stu-id="f9562-239">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="f9562-240">Em alguns cenários de serviço de back-end em que a segurança de conexão é tratada na borda voltada para o público da rede, não é necessário configurar a segurança de conexão em cada nó.</span><span class="sxs-lookup"><span data-stu-id="f9562-240">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="f9562-241">Os aplicativos Web gerados nos modelos no Visual Studio ou no [novo comando dotnet](/dotnet/core/tools/dotnet-new) habilitam o [redirecionamento https](#require-https) e o [HSTS](#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="f9562-241">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="f9562-242">Para implantações que não exigem esses cenários, você pode recusar o HTTPS/HSTS quando o aplicativo é criado a partir do modelo.</span><span class="sxs-lookup"><span data-stu-id="f9562-242">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="f9562-243">Para recusar o HTTPS/HSTS:</span><span class="sxs-lookup"><span data-stu-id="f9562-243">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f9562-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9562-244">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="f9562-245">Desmarque a caixa de seleção **Configurar para https** .</span><span class="sxs-lookup"><span data-stu-id="f9562-245">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

![Diálogo novo ASP.NET Core aplicativo Web mostrando a caixa de seleção configurar para HTTPS desmarcada.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Diálogo novo ASP.NET Core aplicativo Web mostrando a caixa de seleção configurar para HTTPS desmarcada.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f9562-248">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="f9562-248">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="f9562-249">Use a opção `--no-https`.</span><span class="sxs-lookup"><span data-stu-id="f9562-249">Use the `--no-https` option.</span></span> <span data-ttu-id="f9562-250">Por exemplo</span><span class="sxs-lookup"><span data-stu-id="f9562-250">For example</span></span>

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="f9562-251">Confiar no certificado de desenvolvimento ASP.NET Core HTTPS no Windows e no macOS</span><span class="sxs-lookup"><span data-stu-id="f9562-251">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="f9562-252">O SDK do .NET Core inclui um certificado de desenvolvimento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f9562-252">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="f9562-253">O certificado é instalado como parte da experiência de primeira execução.</span><span class="sxs-lookup"><span data-stu-id="f9562-253">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="f9562-254">Por exemplo, `dotnet --info` produz uma saída semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="f9562-254">For example, `dotnet --info` produces output similar to the following:</span></span>

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="f9562-255">Instalar o SDK do .NET Core instala o certificado de desenvolvimento HTTPS do ASP.NET Core no repositório de certificados do usuário local.</span><span class="sxs-lookup"><span data-stu-id="f9562-255">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="f9562-256">O certificado foi instalado, mas não é confiável.</span><span class="sxs-lookup"><span data-stu-id="f9562-256">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="f9562-257">Para confiar no certificado, execute a etapa única para executar a ferramenta dotnet `dev-certs`:</span><span class="sxs-lookup"><span data-stu-id="f9562-257">To trust the certificate perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="f9562-258">O comando a seguir fornece ajuda para a ferramenta `dev-certs`:</span><span class="sxs-lookup"><span data-stu-id="f9562-258">The following command provides help on the `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="f9562-259">Como configurar um certificado de desenvolvedor para o Docker</span><span class="sxs-lookup"><span data-stu-id="f9562-259">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="f9562-260">Consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span><span class="sxs-lookup"><span data-stu-id="f9562-260">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="f9562-261">Confiar no certificado HTTPS do subsistema do Windows para Linux</span><span class="sxs-lookup"><span data-stu-id="f9562-261">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="f9562-262">O subsistema do Windows para Linux (WSL) gera um certificado HTTPS autoassinado. Para configurar o repositório de certificados do Windows para confiar no certificado WSL:</span><span class="sxs-lookup"><span data-stu-id="f9562-262">The Windows Subsystem for Linux (WSL) generates a HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="f9562-263">Execute o seguinte comando para exportar o certificado gerado WSL: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span><span class="sxs-lookup"><span data-stu-id="f9562-263">Run the following command to export the WSL generated certificate: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span></span>
* <span data-ttu-id="f9562-264">Em uma janela do WSL, execute o seguinte comando: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span><span class="sxs-lookup"><span data-stu-id="f9562-264">In a WSL window, run the following command: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span></span>

  <span data-ttu-id="f9562-265">O comando anterior define as variáveis de ambiente para que o Linux use o certificado confiável do Windows.</span><span class="sxs-lookup"><span data-stu-id="f9562-265">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="troubleshoot-certificate-problems"></a><span data-ttu-id="f9562-266">Solucionar problemas de certificado</span><span class="sxs-lookup"><span data-stu-id="f9562-266">Troubleshoot certificate problems</span></span>

<span data-ttu-id="f9562-267">Esta seção fornece ajuda quando o ASP.NET Core certificado de desenvolvimento HTTPS foi [instalado e é confiável](#trust), mas você ainda tem avisos do navegador de que o certificado não é confiável.</span><span class="sxs-lookup"><span data-stu-id="f9562-267">This section provides help when the ASP.NET Core HTTPS development certificate has been [installed and trusted](#trust), but you still have browser warnings that the certificate is not trusted.</span></span> <span data-ttu-id="f9562-268">O certificado de desenvolvimento de ASP.NET Core HTTPS é usado pelo [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="f9562-268">The ASP.NET Core HTTPS development certificate is used by [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

### <a name="all-platforms---certificate-not-trusted"></a><span data-ttu-id="f9562-269">Todas as plataformas-certificado não confiável</span><span class="sxs-lookup"><span data-stu-id="f9562-269">All platforms - certificate not trusted</span></span>

<span data-ttu-id="f9562-270">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="f9562-270">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="f9562-271">Feche todas as instâncias do navegador abertas.</span><span class="sxs-lookup"><span data-stu-id="f9562-271">Close any browser instances open.</span></span> <span data-ttu-id="f9562-272">Abra uma nova janela do navegador para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9562-272">Open a new browser window to app.</span></span> <span data-ttu-id="f9562-273">A confiança de certificado é armazenada em cache por navegadores.</span><span class="sxs-lookup"><span data-stu-id="f9562-273">Certificate trust is cached by browsers.</span></span>

<span data-ttu-id="f9562-274">Os comandos anteriores resolvem a maioria dos problemas de confiança do navegador.</span><span class="sxs-lookup"><span data-stu-id="f9562-274">The preceding commands solve most browser trust issues.</span></span> <span data-ttu-id="f9562-275">Se o navegador ainda não estiver confiando no certificado, siga as sugestões específicas da plataforma a seguir.</span><span class="sxs-lookup"><span data-stu-id="f9562-275">If the browser is still not trusting the certificate, follow the platform specific suggestions that follow.</span></span>

### <a name="docker---certificate-not-trusted"></a><span data-ttu-id="f9562-276">Docker-certificado não confiável</span><span class="sxs-lookup"><span data-stu-id="f9562-276">Docker - certificate not trusted</span></span>

* <span data-ttu-id="f9562-277">Exclua a pasta *C:\Users\{usuário} \AppData\Roaming\ASP.NET\Https*</span><span class="sxs-lookup"><span data-stu-id="f9562-277">Delete the *C:\Users\{USER}\AppData\Roaming\ASP.NET\Https* folder.</span></span>
* <span data-ttu-id="f9562-278">Limpe a solução.</span><span class="sxs-lookup"><span data-stu-id="f9562-278">Clean the solution.</span></span> <span data-ttu-id="f9562-279">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="f9562-279">Delete the *bin* and *obj* folders.</span></span>
* <span data-ttu-id="f9562-280">Reinicie a ferramenta de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="f9562-280">Restart the development tool.</span></span> <span data-ttu-id="f9562-281">Por exemplo, Visual Studio, Visual Studio Code ou Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="f9562-281">For example, Visual Studio, Visual Studio Code, or Visual Studio for Mac.</span></span>

### <a name="windows---certificate-not-trusted"></a><span data-ttu-id="f9562-282">Windows-certificado não confiável</span><span class="sxs-lookup"><span data-stu-id="f9562-282">Windows - certificate not trusted</span></span>

* <span data-ttu-id="f9562-283">Verifique os certificados no repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="f9562-283">Check the certificates in the certificate store.</span></span> <span data-ttu-id="f9562-284">Deve haver um certificado de `localhost` com o nome amigável `ASP.NET Core HTTPS development certificate` em `Current User > Personal > Certificates` e `Current User > Trusted root certification authorities > Certificates`</span><span class="sxs-lookup"><span data-stu-id="f9562-284">There should be a `localhost` certificate with the `ASP.NET Core HTTPS development certificate` friendly name both under `Current User > Personal > Certificates` and `Current User > Trusted root certification authorities > Certificates`</span></span>
* <span data-ttu-id="f9562-285">Remova todos os certificados encontrados das autoridades de certificação raiz pessoais e confiáveis.</span><span class="sxs-lookup"><span data-stu-id="f9562-285">Remove all the found certificates from both Personal and Trusted root certification authorities.</span></span> <span data-ttu-id="f9562-286">**Não** remova o IIS Express certificado localhost.</span><span class="sxs-lookup"><span data-stu-id="f9562-286">Do **not** remove the IIS Express localhost certificate.</span></span>
* <span data-ttu-id="f9562-287">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="f9562-287">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="f9562-288">Feche todas as instâncias do navegador abertas.</span><span class="sxs-lookup"><span data-stu-id="f9562-288">Close any browser instances open.</span></span> <span data-ttu-id="f9562-289">Abra uma nova janela do navegador para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9562-289">Open a new browser window to app.</span></span>

### <a name="os-x---certificate-not-trusted"></a><span data-ttu-id="f9562-290">OS X-certificado não confiável</span><span class="sxs-lookup"><span data-stu-id="f9562-290">OS X - certificate not trusted</span></span>

* <span data-ttu-id="f9562-291">Abra o acesso ao conjunto de chaves.</span><span class="sxs-lookup"><span data-stu-id="f9562-291">Open KeyChain Access.</span></span>
* <span data-ttu-id="f9562-292">Selecione o conjunto de chaves do sistema.</span><span class="sxs-lookup"><span data-stu-id="f9562-292">Select the System keychain.</span></span>
* <span data-ttu-id="f9562-293">Verifique a presença de um certificado localhost.</span><span class="sxs-lookup"><span data-stu-id="f9562-293">Check for the presence of a localhost certificate.</span></span>
* <span data-ttu-id="f9562-294">Verifique se ele contém um símbolo de `+` no ícone para indicar seu confiável para todos os usuários.</span><span class="sxs-lookup"><span data-stu-id="f9562-294">Check that it contains a `+` symbol on the icon to indicate its trusted for all users.</span></span>
* <span data-ttu-id="f9562-295">Remova o certificado do conjunto de chaves do sistema.</span><span class="sxs-lookup"><span data-stu-id="f9562-295">Remove the certificate from the system keychain.</span></span>
* <span data-ttu-id="f9562-296">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="f9562-296">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="f9562-297">Feche todas as instâncias do navegador abertas.</span><span class="sxs-lookup"><span data-stu-id="f9562-297">Close any browser instances open.</span></span> <span data-ttu-id="f9562-298">Abra uma nova janela do navegador para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f9562-298">Open a new browser window to app.</span></span>

<span data-ttu-id="f9562-299">Consulte [erro de HTTPS usando IIS Express (dotNet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) para solucionar problemas de certificado com o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f9562-299">See [HTTPS Error using IIS Express (dotnet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) for troubleshooting certificate issues with Visual Studio.</span></span>

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a><span data-ttu-id="f9562-300">IIS Express certificado SSL usado com o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9562-300">IIS Express SSL certificate used with Visual Studio</span></span>

<span data-ttu-id="f9562-301">Para corrigir problemas com o certificado de IIS Express, selecione **reparar** no instalador do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f9562-301">To fix problems with the IIS Express certificate, select **Repair** from the Visual Studio installer.</span></span> <span data-ttu-id="f9562-302">Saiba mais neste [tópico do GitHub](https://github.com/dotnet/aspnetcore/issues/16892).</span><span class="sxs-lookup"><span data-stu-id="f9562-302">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/16892).</span></span>

## <a name="additional-information"></a><span data-ttu-id="f9562-303">Informações adicionais</span><span class="sxs-lookup"><span data-stu-id="f9562-303">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="f9562-304">ASP.NET Core de host no Linux com Apache: Configuração HTTPS</span><span class="sxs-lookup"><span data-stu-id="f9562-304">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="f9562-305">Host ASP.NET Core no Linux com Nginx: configuração de HTTPS</span><span class="sxs-lookup"><span data-stu-id="f9562-305">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="f9562-306">Como configurar o SSL no IIS</span><span class="sxs-lookup"><span data-stu-id="f9562-306">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="f9562-307">Suporte ao navegador OWASP HSTS</span><span class="sxs-lookup"><span data-stu-id="f9562-307">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
