---
title: Impor HTTPS em ASP.NET Core
author: rick-anderson
description: Saiba como exigir HTTPS/TLS em um aplicativo Web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2019
uid: security/enforcing-ssl
ms.openlocfilehash: 1d1bba6a1f1da2af959bc69b31f79bac53bf48b9
ms.sourcegitcommit: fe3e556bf438fc4136fcf0bac61cf96e3e91caf5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71341470"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="29ae6-103">Impor HTTPS em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29ae6-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="29ae6-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="29ae6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="29ae6-105">Este documento demonstra como:</span><span class="sxs-lookup"><span data-stu-id="29ae6-105">This document shows how to:</span></span>

* <span data-ttu-id="29ae6-106">Exigir HTTPS para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="29ae6-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="29ae6-107">Redirecione todas as solicitações HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="29ae6-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="29ae6-108">Nenhuma API pode impedir que um cliente envie dados confidenciais na primeira solicitação.</span><span class="sxs-lookup"><span data-stu-id="29ae6-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="29ae6-109">Projetos de API</span><span class="sxs-lookup"><span data-stu-id="29ae6-109">API projects</span></span>
>
> <span data-ttu-id="29ae6-110">Não **use** [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) em APIs Web que recebam informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="29ae6-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="29ae6-111">`RequireHttpsAttribute`usa códigos de status HTTP para redirecionar os navegadores de HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="29ae6-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="29ae6-112">Os clientes de API podem não entender ou obedecer a redirecionamentos de HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="29ae6-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="29ae6-113">Esses clientes podem enviar informações por HTTP.</span><span class="sxs-lookup"><span data-stu-id="29ae6-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="29ae6-114">As APIs da Web devem ser:</span><span class="sxs-lookup"><span data-stu-id="29ae6-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="29ae6-115">Não escutar em HTTP.</span><span class="sxs-lookup"><span data-stu-id="29ae6-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="29ae6-116">Feche a conexão com o código de status 400 (solicitação inválida) e não atenda à solicitação.</span><span class="sxs-lookup"><span data-stu-id="29ae6-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="29ae6-117">Projetos de API e HSTS</span><span class="sxs-lookup"><span data-stu-id="29ae6-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="29ae6-118">Os projetos de API padrão não incluem [HSTS](#hsts) porque HSTS geralmente é uma instrução somente de navegador.</span><span class="sxs-lookup"><span data-stu-id="29ae6-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="29ae6-119">Outros chamadores, como telefone ou aplicativos da área de trabalho, **não** obedecem à instrução.</span><span class="sxs-lookup"><span data-stu-id="29ae6-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="29ae6-120">Mesmo em navegadores, uma única chamada autenticada para uma API sobre HTTP tem riscos em redes inseguras.</span><span class="sxs-lookup"><span data-stu-id="29ae6-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="29ae6-121">A abordagem segura é configurar projetos de API para escutar e responder apenas por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="29ae6-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="29ae6-122">Projetos de API</span><span class="sxs-lookup"><span data-stu-id="29ae6-122">API projects</span></span>
>
> <span data-ttu-id="29ae6-123">Não **use** [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) em APIs Web que recebam informações confidenciais.</span><span class="sxs-lookup"><span data-stu-id="29ae6-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="29ae6-124">`RequireHttpsAttribute`usa códigos de status HTTP para redirecionar os navegadores de HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="29ae6-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="29ae6-125">Os clientes de API podem não entender ou obedecer a redirecionamentos de HTTP para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="29ae6-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="29ae6-126">Esses clientes podem enviar informações por HTTP.</span><span class="sxs-lookup"><span data-stu-id="29ae6-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="29ae6-127">As APIs da Web devem ser:</span><span class="sxs-lookup"><span data-stu-id="29ae6-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="29ae6-128">Não escutar em HTTP.</span><span class="sxs-lookup"><span data-stu-id="29ae6-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="29ae6-129">Feche a conexão com o código de status 400 (solicitação inválida) e não atenda à solicitação.</span><span class="sxs-lookup"><span data-stu-id="29ae6-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="29ae6-130">Exigir HTTPS</span><span class="sxs-lookup"><span data-stu-id="29ae6-130">Require HTTPS</span></span>

<span data-ttu-id="29ae6-131">É recomendável que a produção ASP.NET Core aplicativos Web usem:</span><span class="sxs-lookup"><span data-stu-id="29ae6-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="29ae6-132">Middleware de redirecionamento de HTTPS<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>() para redirecionar solicitações HTTP para https.</span><span class="sxs-lookup"><span data-stu-id="29ae6-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="29ae6-133">Middleware HSTS ([UseHsts](#http-strict-transport-security-protocol-hsts)) para enviar cabeçalhos HSTS (protocolo de segurança de transporte estrito) http para clientes.</span><span class="sxs-lookup"><span data-stu-id="29ae6-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="29ae6-134">Os aplicativos implantados em uma configuração de proxy reverso permitem que o proxy manipule a segurança de conexão (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="29ae6-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="29ae6-135">Se o proxy também tratar o redirecionamento de HTTPS, não será necessário usar o middleware de redirecionamento de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="29ae6-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="29ae6-136">Se o servidor proxy também tratar da gravação de cabeçalhos HSTS (por exemplo, [suporte a HSTS nativo no IIS 10,0 (1709) ou posterior](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), o middleware HSTS não será exigido pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29ae6-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="29ae6-137">Para obter mais informações, consulte [recusar https/HSTS na criação do projeto](#opt-out-of-httpshsts-on-project-creation).</span><span class="sxs-lookup"><span data-stu-id="29ae6-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="29ae6-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="29ae6-138">UseHttpsRedirection</span></span>

<span data-ttu-id="29ae6-139">O código a seguir `UseHttpsRedirection` chama a `Startup` classe:</span><span class="sxs-lookup"><span data-stu-id="29ae6-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="29ae6-140">O código realçado anterior:</span><span class="sxs-lookup"><span data-stu-id="29ae6-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="29ae6-141">Usa o padrão [HttpsRedirectionOptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span><span class="sxs-lookup"><span data-stu-id="29ae6-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="29ae6-142">Usa o [HttpsRedirectionOptions. HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (nulo) padrão, a menos que `ASPNETCORE_HTTPS_PORT` seja substituído pela variável de ambiente ou [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span><span class="sxs-lookup"><span data-stu-id="29ae6-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="29ae6-143">É recomendável usar redirecionamentos temporários em vez de redirecionamentos permanentes.</span><span class="sxs-lookup"><span data-stu-id="29ae6-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="29ae6-144">O cache de link pode causar comportamento instável em ambientes de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="29ae6-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="29ae6-145">Se você preferir enviar um código de status de redirecionamento permanente quando o aplicativo estiver em um ambiente que não seja de desenvolvimento, consulte a seção [Configurar redirecionamentos permanentes na produção](#configure-permanent-redirects-in-production) .</span><span class="sxs-lookup"><span data-stu-id="29ae6-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="29ae6-146">É recomendável usar [HSTS](#http-strict-transport-security-protocol-hsts) para sinalizar para clientes que apenas as solicitações de recursos de segurança devem ser enviadas para o aplicativo (somente em produção).</span><span class="sxs-lookup"><span data-stu-id="29ae6-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="29ae6-147">Configuração de porta</span><span class="sxs-lookup"><span data-stu-id="29ae6-147">Port configuration</span></span>

<span data-ttu-id="29ae6-148">Uma porta deve estar disponível para o middleware redirecionar uma solicitação insegura para HTTPS.</span><span class="sxs-lookup"><span data-stu-id="29ae6-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="29ae6-149">Se nenhuma porta estiver disponível:</span><span class="sxs-lookup"><span data-stu-id="29ae6-149">If no port is available:</span></span>

* <span data-ttu-id="29ae6-150">O redirecionamento para HTTPS não ocorre.</span><span class="sxs-lookup"><span data-stu-id="29ae6-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="29ae6-151">O middleware registra o aviso "falha ao determinar a porta HTTPS para redirecionamento".</span><span class="sxs-lookup"><span data-stu-id="29ae6-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="29ae6-152">Especifique a porta HTTPS usando qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="29ae6-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="29ae6-153">Defina [HttpsRedirectionOptions. HttpsPort](#options).</span><span class="sxs-lookup"><span data-stu-id="29ae6-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="29ae6-154">Defina a `https_port` [configuração de host](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):</span><span class="sxs-lookup"><span data-stu-id="29ae6-154">Set the `https_port` [host setting](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="29ae6-155">Na configuração do host.</span><span class="sxs-lookup"><span data-stu-id="29ae6-155">In host configuration.</span></span>
  * <span data-ttu-id="29ae6-156">Definindo a variável `ASPNETCORE_HTTPS_PORT` de ambiente.</span><span class="sxs-lookup"><span data-stu-id="29ae6-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="29ae6-157">Adicionando uma entrada de nível superior em *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="29ae6-157">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* <span data-ttu-id="29ae6-158">Indique uma porta com o esquema seguro usando a [variável de ambiente ASPNETCORE_URLS](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls).</span><span class="sxs-lookup"><span data-stu-id="29ae6-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="29ae6-159">A variável de ambiente configura o servidor.</span><span class="sxs-lookup"><span data-stu-id="29ae6-159">The environment variable configures the server.</span></span> <span data-ttu-id="29ae6-160">O middleware descobre indiretamente a porta HTTPS via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="29ae6-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="29ae6-161">Essa abordagem não funciona em implantações de proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="29ae6-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="29ae6-162">Defina a `https_port` [configuração de host](xref:fundamentals/host/web-host#https-port):</span><span class="sxs-lookup"><span data-stu-id="29ae6-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="29ae6-163">Na configuração do host.</span><span class="sxs-lookup"><span data-stu-id="29ae6-163">In host configuration.</span></span>
  * <span data-ttu-id="29ae6-164">Definindo a variável `ASPNETCORE_HTTPS_PORT` de ambiente.</span><span class="sxs-lookup"><span data-stu-id="29ae6-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="29ae6-165">Adicionando uma entrada de nível superior em *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="29ae6-165">By adding a top-level entry in *appsettings.json*:</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* <span data-ttu-id="29ae6-166">Indique uma porta com o esquema seguro usando a [variável de ambiente ASPNETCORE_URLS](xref:fundamentals/host/web-host#server-urls).</span><span class="sxs-lookup"><span data-stu-id="29ae6-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="29ae6-167">A variável de ambiente configura o servidor.</span><span class="sxs-lookup"><span data-stu-id="29ae6-167">The environment variable configures the server.</span></span> <span data-ttu-id="29ae6-168">O middleware descobre indiretamente a porta HTTPS via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span><span class="sxs-lookup"><span data-stu-id="29ae6-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="29ae6-169">Essa abordagem não funciona em implantações de proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="29ae6-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="29ae6-170">Em desenvolvimento, defina uma URL HTTPS em *launchsettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="29ae6-170">In development, set an HTTPS URL in *launchsettings.json*.</span></span> <span data-ttu-id="29ae6-171">Habilite HTTPS quando IIS Express for usado.</span><span class="sxs-lookup"><span data-stu-id="29ae6-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="29ae6-172">Configure um ponto de extremidade de URL HTTPS para uma implantação de borda voltada para o público do servidor [Kestrel](xref:fundamentals/servers/kestrel) ou [http. sys](xref:fundamentals/servers/httpsys) .</span><span class="sxs-lookup"><span data-stu-id="29ae6-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="29ae6-173">Somente **uma porta https** é usada pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29ae6-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="29ae6-174">O middleware descobre a porta por meio <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>de.</span><span class="sxs-lookup"><span data-stu-id="29ae6-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="29ae6-175">Quando um aplicativo é executado em uma configuração de proxy reverso, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> o não está disponível.</span><span class="sxs-lookup"><span data-stu-id="29ae6-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="29ae6-176">Defina a porta usando uma das outras abordagens descritas nesta seção.</span><span class="sxs-lookup"><span data-stu-id="29ae6-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="29ae6-177">Implantações de borda</span><span class="sxs-lookup"><span data-stu-id="29ae6-177">Edge deployments</span></span> 

<span data-ttu-id="29ae6-178">Quando Kestrel ou HTTP. sys é usado como um servidor de borda voltado ao público, Kestrel ou HTTP. sys deve ser configurado para escutar em ambos:</span><span class="sxs-lookup"><span data-stu-id="29ae6-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="29ae6-179">A porta segura em que o cliente é redirecionado (normalmente, 443 em produção e 5001 em desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="29ae6-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="29ae6-180">A porta insegura (normalmente, 80 em produção e 5000 em desenvolvimento).</span><span class="sxs-lookup"><span data-stu-id="29ae6-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="29ae6-181">A porta insegura deve ser acessível pelo cliente para que o aplicativo receba uma solicitação insegura e redirecione o cliente para a porta segura.</span><span class="sxs-lookup"><span data-stu-id="29ae6-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="29ae6-182">Para obter mais informações, consulte configuração de ponto <xref:fundamentals/servers/httpsys>de [extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) ou.</span><span class="sxs-lookup"><span data-stu-id="29ae6-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="29ae6-183">Cenários de implantação</span><span class="sxs-lookup"><span data-stu-id="29ae6-183">Deployment scenarios</span></span>

<span data-ttu-id="29ae6-184">Qualquer firewall entre o cliente e o servidor também deve ter portas de comunicação abertas para tráfego.</span><span class="sxs-lookup"><span data-stu-id="29ae6-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="29ae6-185">Se as solicitações forem encaminhadas em uma configuração de proxy reverso, use o [middleware de cabeçalhos encaminhados](xref:host-and-deploy/proxy-load-balancer) antes de chamar o middleware de redirecionamento de HTTPS.</span><span class="sxs-lookup"><span data-stu-id="29ae6-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="29ae6-186">O middleware de cabeçalhos encaminhados atualiza `Request.Scheme`o, usando `X-Forwarded-Proto` o cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="29ae6-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="29ae6-187">O middleware permite que os URIs de redirecionamento e outras políticas de segurança funcionem corretamente.</span><span class="sxs-lookup"><span data-stu-id="29ae6-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="29ae6-188">Quando o middleware de cabeçalhos encaminhados não é usado, o aplicativo de back-end pode não receber o esquema correto e terminar em um loop de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="29ae6-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="29ae6-189">Uma mensagem de erro comum do usuário final é que ocorreram muitos redirecionamentos.</span><span class="sxs-lookup"><span data-stu-id="29ae6-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="29ae6-190">Ao implantar no serviço Azure app, siga as orientações em [tutorial: vincular um certificado SSL personalizado ao serviço Aplicativos Web do Azure](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="29ae6-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="29ae6-191">Opções</span><span class="sxs-lookup"><span data-stu-id="29ae6-191">Options</span></span>

<span data-ttu-id="29ae6-192">O código realçado a seguir chama [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) para configurar opções de middleware:</span><span class="sxs-lookup"><span data-stu-id="29ae6-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="29ae6-193">A `AddHttpsRedirection` chamada só é necessária para alterar os valores `HttpsPort` de `RedirectStatusCode`ou.</span><span class="sxs-lookup"><span data-stu-id="29ae6-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="29ae6-194">O código realçado anterior:</span><span class="sxs-lookup"><span data-stu-id="29ae6-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="29ae6-195">Define [HttpsRedirectionOptions. RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) como <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, que é o valor padrão.</span><span class="sxs-lookup"><span data-stu-id="29ae6-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="29ae6-196">Use os campos da classe <xref:Microsoft.AspNetCore.Http.StatusCodes> para as atribuições `RedirectStatusCode`para.</span><span class="sxs-lookup"><span data-stu-id="29ae6-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="29ae6-197">Define a porta HTTPS como 5001.</span><span class="sxs-lookup"><span data-stu-id="29ae6-197">Sets the HTTPS port to 5001.</span></span> <span data-ttu-id="29ae6-198">O valor padrão é 443.</span><span class="sxs-lookup"><span data-stu-id="29ae6-198">The default value is 443.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="29ae6-199">Configurar redirecionamentos permanentes na produção</span><span class="sxs-lookup"><span data-stu-id="29ae6-199">Configure permanent redirects in production</span></span>

<span data-ttu-id="29ae6-200">O padrão do middleware é enviar um [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) com todos os redirecionamentos.</span><span class="sxs-lookup"><span data-stu-id="29ae6-200">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="29ae6-201">Se você preferir enviar um código de status de redirecionamento permanente quando o aplicativo estiver em um ambiente que não seja de desenvolvimento, coloque a configuração de opções de middleware em uma verificação condicional para um ambiente que não seja de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="29ae6-201">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="29ae6-202">Ao configurar serviços no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="29ae6-202">When configuring services in *Startup.cs*:</span></span>

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

<span data-ttu-id="29ae6-203">Ao configurar serviços no *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="29ae6-203">When configuring services in *Startup.cs*:</span></span>

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


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="29ae6-204">Abordagem alternativa de middleware de redirecionamento de HTTPS</span><span class="sxs-lookup"><span data-stu-id="29ae6-204">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="29ae6-205">Uma alternativa ao uso do middleware de redirecionamento de`UseHttpsRedirection`HTTPS () é usar o middleware de reescrita`AddRedirectToHttps`de URL ().</span><span class="sxs-lookup"><span data-stu-id="29ae6-205">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="29ae6-206">`AddRedirectToHttps`também é possível definir o código de status e a porta quando o redirecionamento é executado.</span><span class="sxs-lookup"><span data-stu-id="29ae6-206">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="29ae6-207">Para obter mais informações, consulte [middleware de regravação de URL](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="29ae6-207">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="29ae6-208">Ao redirecionar para https sem o requisito de regras de redirecionamento adicionais, recomendamos o uso do middleware de`UseHttpsRedirection`redirecionamento de HTTPS () descrito neste tópico.</span><span class="sxs-lookup"><span data-stu-id="29ae6-208">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="29ae6-209">Protocolo de segurança de transporte estrito HTTP (HSTS)</span><span class="sxs-lookup"><span data-stu-id="29ae6-209">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="29ae6-210">Por [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), o [HSTS (segurança de transporte estrito) http](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) é um aprimoramento de segurança opcional que é especificado por um aplicativo Web por meio do uso de um cabeçalho de resposta.</span><span class="sxs-lookup"><span data-stu-id="29ae6-210">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="29ae6-211">Quando um [navegador que dá suporte a HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) recebe este cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="29ae6-211">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="29ae6-212">O navegador armazena a configuração para o domínio que impede o envio de qualquer comunicação via HTTP.</span><span class="sxs-lookup"><span data-stu-id="29ae6-212">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="29ae6-213">O navegador força toda a comunicação por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="29ae6-213">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="29ae6-214">O navegador impede que o usuário use certificados não confiáveis ou inválidos.</span><span class="sxs-lookup"><span data-stu-id="29ae6-214">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="29ae6-215">O navegador desabilita as solicitações que permitem que um usuário confie temporariamente em um certificado.</span><span class="sxs-lookup"><span data-stu-id="29ae6-215">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="29ae6-216">Como o HSTS é imposto pelo cliente, ele tem algumas limitações:</span><span class="sxs-lookup"><span data-stu-id="29ae6-216">Because HSTS is enforced by the client it has some limitations:</span></span>

* <span data-ttu-id="29ae6-217">O cliente deve dar suporte a HSTS.</span><span class="sxs-lookup"><span data-stu-id="29ae6-217">The client must support HSTS.</span></span>
* <span data-ttu-id="29ae6-218">HSTS requer pelo menos uma solicitação HTTPS bem-sucedida para estabelecer a política HSTS.</span><span class="sxs-lookup"><span data-stu-id="29ae6-218">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="29ae6-219">O aplicativo deve verificar cada solicitação HTTP e redirecionar ou rejeitar a solicitação HTTP.</span><span class="sxs-lookup"><span data-stu-id="29ae6-219">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="29ae6-220">ASP.NET Core 2,1 e posterior implementa HSTS com o `UseHsts` método de extensão.</span><span class="sxs-lookup"><span data-stu-id="29ae6-220">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="29ae6-221">O código a seguir `UseHsts` chama quando o aplicativo não está no [modo de desenvolvimento](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="29ae6-221">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="29ae6-222">`UseHsts`Não é recomendado no desenvolvimento porque as configurações de HSTS são altamente armazenáveis em cache pelos navegadores.</span><span class="sxs-lookup"><span data-stu-id="29ae6-222">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="29ae6-223">Por padrão, `UseHsts` o exclui o endereço de loopback local.</span><span class="sxs-lookup"><span data-stu-id="29ae6-223">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="29ae6-224">Para ambientes de produção que estão implementando https pela primeira vez, defina o [HstsOptions inicial. MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) como um valor pequeno usando um dos <xref:System.TimeSpan> métodos.</span><span class="sxs-lookup"><span data-stu-id="29ae6-224">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="29ae6-225">Defina o valor de horas para não mais do que um único dia, caso precise reverter a infraestrutura HTTPS para HTTP.</span><span class="sxs-lookup"><span data-stu-id="29ae6-225">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="29ae6-226">Depois que você estiver confiante na sustentabilidade da configuração de HTTPS, aumente o valor de idade máxima HSTS; um valor comumente usado é de um ano.</span><span class="sxs-lookup"><span data-stu-id="29ae6-226">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS max-age value; a commonly used value is one year.</span></span>

<span data-ttu-id="29ae6-227">O código a seguir:</span><span class="sxs-lookup"><span data-stu-id="29ae6-227">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="29ae6-228">Define o parâmetro PreLoad do cabeçalho Strict-Transport-Security.</span><span class="sxs-lookup"><span data-stu-id="29ae6-228">Sets the preload parameter of the Strict-Transport-Security header.</span></span> <span data-ttu-id="29ae6-229">Pré-carregar não faz parte da [especificação RFC HSTS](https://tools.ietf.org/html/rfc6797), mas tem suporte de navegadores da Web para pré-carregar HSTS sites na nova instalação.</span><span class="sxs-lookup"><span data-stu-id="29ae6-229">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="29ae6-230">Veja [https://hstspreload.org/](https://hstspreload.org/) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="29ae6-230">See [https://hstspreload.org/](https://hstspreload.org/) for more information.</span></span>
* <span data-ttu-id="29ae6-231">Habilita [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), que aplica a política HSTS aos subdomínios de host.</span><span class="sxs-lookup"><span data-stu-id="29ae6-231">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="29ae6-232">Define explicitamente o parâmetro Max-age do cabeçalho Strict-Transport-Security como 60 dias.</span><span class="sxs-lookup"><span data-stu-id="29ae6-232">Explicitly sets the max-age parameter of the Strict-Transport-Security header to 60 days.</span></span> <span data-ttu-id="29ae6-233">Se não estiver definido, o padrão será 30 dias.</span><span class="sxs-lookup"><span data-stu-id="29ae6-233">If not set, defaults to 30 days.</span></span> <span data-ttu-id="29ae6-234">Consulte a [diretiva Max-age](https://tools.ietf.org/html/rfc6797#section-6.1.1) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="29ae6-234">See the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1) for more information.</span></span>
* <span data-ttu-id="29ae6-235">Adiciona `example.com` à lista de hosts a serem excluídos.</span><span class="sxs-lookup"><span data-stu-id="29ae6-235">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="29ae6-236">`UseHsts`exclui os seguintes hosts de loopback:</span><span class="sxs-lookup"><span data-stu-id="29ae6-236">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="29ae6-237">`localhost` : O endereço de loopback IPv4.</span><span class="sxs-lookup"><span data-stu-id="29ae6-237">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="29ae6-238">`127.0.0.1` : O endereço de loopback IPv4.</span><span class="sxs-lookup"><span data-stu-id="29ae6-238">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="29ae6-239">`[::1]` : O endereço de loopback IPv6.</span><span class="sxs-lookup"><span data-stu-id="29ae6-239">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="29ae6-240">Recusa de HTTPS/HSTS na criação do projeto</span><span class="sxs-lookup"><span data-stu-id="29ae6-240">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="29ae6-241">Em alguns cenários de serviço de back-end em que a segurança de conexão é tratada na borda voltada para o público da rede, não é necessário configurar a segurança de conexão em cada nó.</span><span class="sxs-lookup"><span data-stu-id="29ae6-241">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="29ae6-242">Os aplicativos Web gerados nos modelos no Visual Studio ou no [novo comando dotnet](/dotnet/core/tools/dotnet-new) habilitam o [redirecionamento https](#require-https) e o [HSTS](#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="29ae6-242">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="29ae6-243">Para implantações que não exigem esses cenários, você pode recusar o HTTPS/HSTS quando o aplicativo é criado a partir do modelo.</span><span class="sxs-lookup"><span data-stu-id="29ae6-243">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="29ae6-244">Para recusar o HTTPS/HSTS:</span><span class="sxs-lookup"><span data-stu-id="29ae6-244">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="29ae6-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29ae6-245">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="29ae6-246">Desmarque a caixa de seleção **Configurar para https** .</span><span class="sxs-lookup"><span data-stu-id="29ae6-246">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

![Diálogo novo ASP.NET Core aplicativo Web mostrando a caixa de seleção configurar para HTTPS desmarcada.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Diálogo novo ASP.NET Core aplicativo Web mostrando a caixa de seleção configurar para HTTPS desmarcada.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="29ae6-249">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="29ae6-249">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="29ae6-250">Use a opção `--no-https`.</span><span class="sxs-lookup"><span data-stu-id="29ae6-250">Use the `--no-https` option.</span></span> <span data-ttu-id="29ae6-251">Por exemplo</span><span class="sxs-lookup"><span data-stu-id="29ae6-251">For example</span></span>

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="29ae6-252">Confiar no certificado de desenvolvimento ASP.NET Core HTTPS no Windows e no macOS</span><span class="sxs-lookup"><span data-stu-id="29ae6-252">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="29ae6-253">O SDK do .NET Core inclui um certificado de desenvolvimento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="29ae6-253">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="29ae6-254">O certificado é instalado como parte da experiência de primeira execução.</span><span class="sxs-lookup"><span data-stu-id="29ae6-254">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="29ae6-255">Por exemplo, `dotnet --info` produz uma saída semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="29ae6-255">For example, `dotnet --info` produces output similar to the following:</span></span>

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="29ae6-256">Instalar o SDK do .NET Core instala o certificado de desenvolvimento HTTPS do ASP.NET Core no repositório de certificados do usuário local.</span><span class="sxs-lookup"><span data-stu-id="29ae6-256">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="29ae6-257">O certificado foi instalado, mas não é confiável.</span><span class="sxs-lookup"><span data-stu-id="29ae6-257">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="29ae6-258">Para confiar no certificado, execute a etapa única para executar a ferramenta dotnet `dev-certs` :</span><span class="sxs-lookup"><span data-stu-id="29ae6-258">To trust the certificate perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="29ae6-259">O comando a seguir fornece ajuda para a ferramenta `dev-certs`:</span><span class="sxs-lookup"><span data-stu-id="29ae6-259">The following command provides help on the `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="29ae6-260">Como configurar um certificado de desenvolvedor para o Docker</span><span class="sxs-lookup"><span data-stu-id="29ae6-260">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="29ae6-261">Consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span><span class="sxs-lookup"><span data-stu-id="29ae6-261">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="29ae6-262">Confiar no certificado HTTPS do subsistema do Windows para Linux</span><span class="sxs-lookup"><span data-stu-id="29ae6-262">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="29ae6-263">O subsistema do Windows para Linux (WSL) gera um certificado HTTPS autoassinado. Para configurar o repositório de certificados do Windows para confiar no certificado WSL:</span><span class="sxs-lookup"><span data-stu-id="29ae6-263">The Windows Subsystem for Linux (WSL) generates a HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="29ae6-264">Execute o seguinte comando para exportar o certificado gerado WSL:`dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span><span class="sxs-lookup"><span data-stu-id="29ae6-264">Run the following command to export the WSL generated certificate: `dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>`</span></span>
* <span data-ttu-id="29ae6-265">Em uma janela do WSL, execute o seguinte comando:`ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span><span class="sxs-lookup"><span data-stu-id="29ae6-265">In a WSL window, run the following command: `ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx dotnet watch run`</span></span>

  <span data-ttu-id="29ae6-266">O comando anterior define as variáveis de ambiente para que o Linux use o certificado confiável do Windows.</span><span class="sxs-lookup"><span data-stu-id="29ae6-266">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="troubleshoot-certificate-problems"></a><span data-ttu-id="29ae6-267">Solucionar problemas de certificado</span><span class="sxs-lookup"><span data-stu-id="29ae6-267">Troubleshoot certificate problems</span></span>

<span data-ttu-id="29ae6-268">Esta seção fornece ajuda quando o ASP.NET Core certificado de desenvolvimento HTTPS foi [instalado e é confiável](#trust), mas você ainda tem avisos do navegador de que o certificado não é confiável.</span><span class="sxs-lookup"><span data-stu-id="29ae6-268">This section provides help when the ASP.NET Core HTTPS development certificate has been [installed and trusted](#trust), but you still have browser warnings that the certificate is not trusted.</span></span>

### <a name="all-platforms---certificate-not-trusted"></a><span data-ttu-id="29ae6-269">Todas as plataformas-certificado não confiável</span><span class="sxs-lookup"><span data-stu-id="29ae6-269">All platforms - certificate not trusted</span></span>

<span data-ttu-id="29ae6-270">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="29ae6-270">Run the following commands:</span></span>

```dotnetcli
dotnet devcerts https --clean
dotnet devcerts https --trust
```

<span data-ttu-id="29ae6-271">Feche todas as instâncias do navegador abertas.</span><span class="sxs-lookup"><span data-stu-id="29ae6-271">Close any browser instances open.</span></span> <span data-ttu-id="29ae6-272">Abra uma nova janela do navegador para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29ae6-272">Open a new browser window to app.</span></span> <span data-ttu-id="29ae6-273">A confiança de certificado é armazenada em cache por navegadores.</span><span class="sxs-lookup"><span data-stu-id="29ae6-273">Certificate trust is cached by browsers.</span></span>

<span data-ttu-id="29ae6-274">Os comandos anteriores resolvem a maioria dos problemas de confiança do navegador.</span><span class="sxs-lookup"><span data-stu-id="29ae6-274">The preceding commands solve most browser trust issues.</span></span> <span data-ttu-id="29ae6-275">Se o navegador ainda não estiver confiando no certificado, siga as sugestões específicas da plataforma a seguir.</span><span class="sxs-lookup"><span data-stu-id="29ae6-275">If the browser is still not trusting the certificate, follow the platform specific suggestions that follow.</span></span>

### <a name="docker---certificate-not-trusted"></a><span data-ttu-id="29ae6-276">Docker-certificado não confiável</span><span class="sxs-lookup"><span data-stu-id="29ae6-276">Docker - certificate not trusted</span></span>

* <span data-ttu-id="29ae6-277">Exclua a pasta *C:\Users\{User} \AppData\Roaming\ASP.NET\Https*</span><span class="sxs-lookup"><span data-stu-id="29ae6-277">Delete the *C:\Users\{USER}\AppData\Roaming\ASP.NET\Https* folder.</span></span>
* <span data-ttu-id="29ae6-278">Limpe a solução.</span><span class="sxs-lookup"><span data-stu-id="29ae6-278">Clean the solution.</span></span> <span data-ttu-id="29ae6-279">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="29ae6-279">Delete the *bin* and *obj* folders.</span></span>
* <span data-ttu-id="29ae6-280">Reinicie a ferramenta de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="29ae6-280">Restart the development tool.</span></span> <span data-ttu-id="29ae6-281">Por exemplo, Visual Studio, Visual Studio Code ou Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="29ae6-281">For example, Visual Studio, Visual Studio Code, or Visual Studio for Mac.</span></span>

### <a name="windows---certificate-not-trusted"></a><span data-ttu-id="29ae6-282">Windows-certificado não confiável</span><span class="sxs-lookup"><span data-stu-id="29ae6-282">Windows - certificate not trusted</span></span>

* <span data-ttu-id="29ae6-283">Verifique os certificados no repositório de certificados.</span><span class="sxs-lookup"><span data-stu-id="29ae6-283">Check the certificates in the certificate store.</span></span> <span data-ttu-id="29ae6-284">Deve haver um `localhost` certificado com o `ASP.NET Core HTTPS development certificate` nome amigável em `Current User > Personal > Certificates` e`Current User > Trusted root certification authorities > Certificates`</span><span class="sxs-lookup"><span data-stu-id="29ae6-284">There should be a `localhost` certificate with the `ASP.NET Core HTTPS development certificate` friendly name both under `Current User > Personal > Certificates` and `Current User > Trusted root certification authorities > Certificates`</span></span>
* <span data-ttu-id="29ae6-285">Remova todos os certificados encontrados das autoridades de certificação raiz pessoais e confiáveis.</span><span class="sxs-lookup"><span data-stu-id="29ae6-285">Remove all the found certificates from both Personal and Trusted root certification authorities.</span></span> <span data-ttu-id="29ae6-286">**Não** remova o IIS Express certificado localhost.</span><span class="sxs-lookup"><span data-stu-id="29ae6-286">Do **not** remove the IIS Express localhost certificate.</span></span>
* <span data-ttu-id="29ae6-287">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="29ae6-287">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="29ae6-288">Feche todas as instâncias do navegador abertas.</span><span class="sxs-lookup"><span data-stu-id="29ae6-288">Close any browser instances open.</span></span> <span data-ttu-id="29ae6-289">Abra uma nova janela do navegador para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29ae6-289">Open a new browser window to app.</span></span>

### <a name="os-x---certificate-not-trusted"></a><span data-ttu-id="29ae6-290">OS X-certificado não confiável</span><span class="sxs-lookup"><span data-stu-id="29ae6-290">OS X - certificate not trusted</span></span>

* <span data-ttu-id="29ae6-291">Abra o acesso ao conjunto de chaves.</span><span class="sxs-lookup"><span data-stu-id="29ae6-291">Open KeyChain Access.</span></span>
* <span data-ttu-id="29ae6-292">Selecione o conjunto de chaves do sistema.</span><span class="sxs-lookup"><span data-stu-id="29ae6-292">Select the System keychain.</span></span>
* <span data-ttu-id="29ae6-293">Verifique a presença de um certificado localhost.</span><span class="sxs-lookup"><span data-stu-id="29ae6-293">Check for the presence of a localhost certificate.</span></span>
* <span data-ttu-id="29ae6-294">Verifique se ele contém um `+` símbolo no ícone para indicar seu confiável para todos os usuários.</span><span class="sxs-lookup"><span data-stu-id="29ae6-294">Check that it contains a `+` symbol on the icon to indicate its trusted for all users.</span></span>
* <span data-ttu-id="29ae6-295">Remova o certificado do conjunto de chaves do sistema.</span><span class="sxs-lookup"><span data-stu-id="29ae6-295">Remove the certificate from the system keychain.</span></span>
* <span data-ttu-id="29ae6-296">Execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="29ae6-296">Run the following commands:</span></span>

```dotnetcli
dotnet devcerts https --clean
dotnet devcerts https --trust
```

<span data-ttu-id="29ae6-297">Feche todas as instâncias do navegador abertas.</span><span class="sxs-lookup"><span data-stu-id="29ae6-297">Close any browser instances open.</span></span> <span data-ttu-id="29ae6-298">Abra uma nova janela do navegador para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="29ae6-298">Open a new browser window to app.</span></span>

## <a name="additional-information"></a><span data-ttu-id="29ae6-299">Informações adicionais</span><span class="sxs-lookup"><span data-stu-id="29ae6-299">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="29ae6-300">ASP.NET Core do host no Linux com Apache: Configuração de HTTPS</span><span class="sxs-lookup"><span data-stu-id="29ae6-300">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="29ae6-301">ASP.NET Core do host no Linux com Nginx: Configuração de HTTPS</span><span class="sxs-lookup"><span data-stu-id="29ae6-301">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="29ae6-302">Como configurar o SSL no IIS</span><span class="sxs-lookup"><span data-stu-id="29ae6-302">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="29ae6-303">Suporte ao navegador OWASP HSTS</span><span class="sxs-lookup"><span data-stu-id="29ae6-303">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
