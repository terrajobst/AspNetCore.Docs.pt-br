---
title: Implementação do servidor Web HTTP.sys no ASP.NET Core
author: guardrex
description: Conheça o HTTP.sys, um servidor Web para o ASP.NET Core executado no Windows. Desenvolvido com base no driver de modo kernel, o HTTP.sys é uma alternativa ao Kestrel, que pode ser usado na conexão direta com a Internet sem o IIS.
monikerRange: '>= aspnetcore-2.0'
ms.author: tdykstra
ms.custom: mvc
ms.date: 05/27/2019
uid: fundamentals/servers/httpsys
ms.openlocfilehash: d91a0f7b83fb073147978319edfbed0bda9a1c44
ms.sourcegitcommit: 06c4f2910dd54ded25e1b8750e09c66578748bc9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66395948"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="e9573-104">Implementação do servidor Web HTTP.sys no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e9573-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="e9573-105">Por [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e9573-105">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e9573-106">O [HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) é um [servidor Web para ASP.NET Core](xref:fundamentals/servers/index) executado apenas no Windows.</span><span class="sxs-lookup"><span data-stu-id="e9573-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="e9573-107">O HTTP.sys é uma alternativa ao servidor [Kestrel](xref:fundamentals/servers/kestrel) e oferece alguns recursos não disponibilizados pelo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e9573-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e9573-108">O HTTP.sys não é compatível com o [Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) e não pode ser usado com IIS ou IIS Express.</span><span class="sxs-lookup"><span data-stu-id="e9573-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="e9573-109">O HTTP.sys dá suporte aos seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="e9573-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="e9573-110">Autenticação do Windows</span><span class="sxs-lookup"><span data-stu-id="e9573-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="e9573-111">Compartilhamento de porta</span><span class="sxs-lookup"><span data-stu-id="e9573-111">Port sharing</span></span>
* <span data-ttu-id="e9573-112">HTTPS com SNI</span><span class="sxs-lookup"><span data-stu-id="e9573-112">HTTPS with SNI</span></span>
* <span data-ttu-id="e9573-113">HTTP/2 sobre TLS (Windows 10 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="e9573-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="e9573-114">Transmissão direta de arquivo</span><span class="sxs-lookup"><span data-stu-id="e9573-114">Direct file transmission</span></span>
* <span data-ttu-id="e9573-115">Cache de resposta</span><span class="sxs-lookup"><span data-stu-id="e9573-115">Response caching</span></span>
* <span data-ttu-id="e9573-116">WebSockets (Windows 8 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="e9573-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="e9573-117">Versões do Windows compatíveis:</span><span class="sxs-lookup"><span data-stu-id="e9573-117">Supported Windows versions:</span></span>

* <span data-ttu-id="e9573-118">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e9573-118">Windows 7 or later</span></span>
* <span data-ttu-id="e9573-119">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e9573-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="e9573-120">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e9573-120">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="e9573-121">Quando usar o HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e9573-121">When to use HTTP.sys</span></span>

<span data-ttu-id="e9573-122">O HTTP.sys é útil nas implantações em que:</span><span class="sxs-lookup"><span data-stu-id="e9573-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="e9573-123">É necessário expor o servidor diretamente à Internet sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="e9573-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![O HTTP.sys se comunica diretamente com a Internet](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="e9573-125">As implantações internas exigem um recurso que não está disponível no Kestrel, como a [Autenticação do Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="e9573-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![O HTTP.sys se comunica diretamente com a rede interna](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="e9573-127">O HTTP.sys é uma tecnologia madura que protege contra vários tipos de ataques e proporciona as propriedades de robustez, segurança e escalabilidade de um servidor Web completo.</span><span class="sxs-lookup"><span data-stu-id="e9573-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="e9573-128">O próprio IIS é executado como um ouvinte HTTP sobre o HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="e9573-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="e9573-129">Compatibilidade com HTTP/2</span><span class="sxs-lookup"><span data-stu-id="e9573-129">HTTP/2 support</span></span>

<span data-ttu-id="e9573-130">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) estará habilitado para aplicativos ASP.NET Core se os seguintes requisitos básicos forem atendidos:</span><span class="sxs-lookup"><span data-stu-id="e9573-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="e9573-131">Windows Server 2016/Windows 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e9573-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="e9573-132">Conexão [ALPN (Negociação de protocolo de camada de aplicativo)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="e9573-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="e9573-133">Conexão TLS 1.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e9573-133">TLS 1.2 or later connection</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e9573-134">Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="e9573-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e9573-135">Se uma conexão HTTP/2 for estabelecida, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) relatará `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="e9573-135">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

::: moniker-end

<span data-ttu-id="e9573-136">O HTTP/2 está habilitado por padrão.</span><span class="sxs-lookup"><span data-stu-id="e9573-136">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="e9573-137">Se uma conexão HTTP/2 não tiver sido estabelecida, a conexão retornará para HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="e9573-137">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="e9573-138">Em uma versão futura do Windows, os sinalizadores de configuração de HTTP/2 estarão disponíveis e contarão com a capacidade de desabilitar o HTTP/2 com HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="e9573-138">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="e9573-139">Autenticação de modo kernel com Kerberos</span><span class="sxs-lookup"><span data-stu-id="e9573-139">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="e9573-140">O HTTP.sys delega à autenticação de modo kernel com o protocolo de autenticação Kerberos.</span><span class="sxs-lookup"><span data-stu-id="e9573-140">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="e9573-141">Não há suporte para autenticação de modo de usuário com o Kerberos e o HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="e9573-141">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="e9573-142">A conta do computador precisa ser usada para descriptografar o token/tíquete do Kerberos que é obtido do Active Directory e encaminhado pelo cliente ao servidor para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="e9573-142">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="e9573-143">Registre o SPN (nome da entidade de serviço) do host, não do usuário do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e9573-143">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="e9573-144">Como usar o HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e9573-144">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="e9573-145">Configurar o aplicativo ASP.NET Core para usar o HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e9573-145">Configure the ASP.NET Core app to use HTTP.sys</span></span>

1. <span data-ttu-id="e9573-146">Não é necessário usar uma referência do pacote no arquivo de projeto ao usar o [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) (ASP.NET Core 2.1 ou posterior).</span><span class="sxs-lookup"><span data-stu-id="e9573-146">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) (ASP.NET Core 2.1 or later).</span></span> <span data-ttu-id="e9573-147">Se não estiver usando o metapacote `Microsoft.AspNetCore.App`, adicione uma referência do pacote a [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span><span class="sxs-lookup"><span data-stu-id="e9573-147">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

2. <span data-ttu-id="e9573-148">Chame o método de extensão <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> ao compilar o host da Web, especificando qualquer <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> necessário:</span><span class="sxs-lookup"><span data-stu-id="e9573-148">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building Web Host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>:</span></span>

   [!code-csharp[](httpsys/sample/Program.cs?name=snippet1&highlight=4-12)]

   <span data-ttu-id="e9573-149">A configuração adicional do HTTP.sys é tratada por meio das [configurações do registro](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span><span class="sxs-lookup"><span data-stu-id="e9573-149">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

   <span data-ttu-id="e9573-150">**Opções do HTTP.sys**</span><span class="sxs-lookup"><span data-stu-id="e9573-150">**HTTP.sys options**</span></span>

   | <span data-ttu-id="e9573-151">Propriedade</span><span class="sxs-lookup"><span data-stu-id="e9573-151">Property</span></span> | <span data-ttu-id="e9573-152">Descrição</span><span class="sxs-lookup"><span data-stu-id="e9573-152">Description</span></span> | <span data-ttu-id="e9573-153">Padrão</span><span class="sxs-lookup"><span data-stu-id="e9573-153">Default</span></span> |
   | -------- | ----------- | :-----: |
   | [<span data-ttu-id="e9573-154">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="e9573-154">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="e9573-155">Controlar quando a Entrada/Saída síncrona deve ser permitida para `HttpContext.Request.Body` e `HttpContext.Response.Body`.</span><span class="sxs-lookup"><span data-stu-id="e9573-155">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
   | [<span data-ttu-id="e9573-156">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="e9573-156">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="e9573-157">Permitir solicitações anônimas.</span><span class="sxs-lookup"><span data-stu-id="e9573-157">Allow anonymous requests.</span></span> | `true` |
   | [<span data-ttu-id="e9573-158">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="e9573-158">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="e9573-159">Especificar os esquemas de autenticação permitidos.</span><span class="sxs-lookup"><span data-stu-id="e9573-159">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="e9573-160">É possível modificar a qualquer momento antes de descartar o ouvinte.</span><span class="sxs-lookup"><span data-stu-id="e9573-160">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="e9573-161">Os valores são fornecidos pela [enumeração AuthenticationSchemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None` e `NTLM`.</span><span class="sxs-lookup"><span data-stu-id="e9573-161">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
   | [<span data-ttu-id="e9573-162">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="e9573-162">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="e9573-163">Tentativa de cache do [modo kernel](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) para obtenção de respostas com cabeçalhos qualificados.</span><span class="sxs-lookup"><span data-stu-id="e9573-163">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="e9573-164">A resposta pode não incluir `Set-Cookie`, `Vary` ou cabeçalhos `Pragma`.</span><span class="sxs-lookup"><span data-stu-id="e9573-164">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="e9573-165">Ela deve incluir um cabeçalho `Cache-Control` que seja `public` e um valor `shared-max-age` ou `max-age`, ou um cabeçalho `Expires`.</span><span class="sxs-lookup"><span data-stu-id="e9573-165">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="e9573-166">O número máximo de aceitações simultâneas.</span><span class="sxs-lookup"><span data-stu-id="e9573-166">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="e9573-167">5 &times; [Ambiente.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="e9573-167">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="e9573-168">O número máximo de conexões simultâneas a serem aceitas.</span><span class="sxs-lookup"><span data-stu-id="e9573-168">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="e9573-169">Usar `-1` como infinito.</span><span class="sxs-lookup"><span data-stu-id="e9573-169">Use `-1` for infinite.</span></span> <span data-ttu-id="e9573-170">Usar `null` a fim de usar a configuração que abranja toda máquina do registro.</span><span class="sxs-lookup"><span data-stu-id="e9573-170">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="e9573-171">(ilimitado)</span><span class="sxs-lookup"><span data-stu-id="e9573-171">(unlimited)</span></span> |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="e9573-172">Confira a seção <a href="#maxrequestbodysize">MaxRequestBodySize</a>.</span><span class="sxs-lookup"><span data-stu-id="e9573-172">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="e9573-173">30.000.000 de bytes</span><span class="sxs-lookup"><span data-stu-id="e9573-173">30000000 bytes</span></span><br><span data-ttu-id="e9573-174">(28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="e9573-174">(~28.6 MB)</span></span> |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="e9573-175">O número máximo de solicitações que podem ser colocadas na fila.</span><span class="sxs-lookup"><span data-stu-id="e9573-175">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="e9573-176">1000</span><span class="sxs-lookup"><span data-stu-id="e9573-176">1000</span></span> |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="e9573-177">Indica se as gravações do corpo da resposta que falham quando o cliente se desconecta devem gerar exceções ou serem concluídas normalmente.</span><span class="sxs-lookup"><span data-stu-id="e9573-177">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="e9573-178">(concluir normalmente)</span><span class="sxs-lookup"><span data-stu-id="e9573-178">(complete normally)</span></span> |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="e9573-179">Expor a configuração <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> do HTTP.sys, que também pode ser configurado no Registro.</span><span class="sxs-lookup"><span data-stu-id="e9573-179">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="e9573-180">Siga os links de API para saber mais sobre cada configuração, inclusive os valores padrão:</span><span class="sxs-lookup"><span data-stu-id="e9573-180">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="e9573-181">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; O tempo permitido para que a API do servidor HTTP esvazie o corpo da entidade em uma conexão Keep-Alive.</span><span class="sxs-lookup"><span data-stu-id="e9573-181">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="e9573-182">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; O tempo permitido para a chegada do corpo da entidade de solicitação.</span><span class="sxs-lookup"><span data-stu-id="e9573-182">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="e9573-183">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; O tempo permitido para que a API do servidor HTTP analise o cabeçalho da solicitação.</span><span class="sxs-lookup"><span data-stu-id="e9573-183">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="e9573-184">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; O tempo permitido para uma conexão ociosa.</span><span class="sxs-lookup"><span data-stu-id="e9573-184">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="e9573-185">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; A taxa de envio mínima para a resposta.</span><span class="sxs-lookup"><span data-stu-id="e9573-185">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; The minimum send rate for the response.</span></span></li><li><span data-ttu-id="e9573-186">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; O tempo permitido para que a solicitação permaneça na fila de solicitações até o aplicativo coletá-la.</span><span class="sxs-lookup"><span data-stu-id="e9573-186">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
   | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="e9573-187">Especifique o <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> para registrar com o HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="e9573-187">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="e9573-188">A mais útil é [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), que é usada para adicionar um prefixo à coleção.</span><span class="sxs-lookup"><span data-stu-id="e9573-188">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="e9573-189">É possível modificá-las a qualquer momento antes de descartar o ouvinte.</span><span class="sxs-lookup"><span data-stu-id="e9573-189">These may be modified at any time prior to disposing the listener.</span></span> |  |

   <a name="maxrequestbodysize"></a>

   <span data-ttu-id="e9573-190">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="e9573-190">**MaxRequestBodySize**</span></span>

   <span data-ttu-id="e9573-191">O tamanho máximo permitido em bytes para todos os corpos de solicitação.</span><span class="sxs-lookup"><span data-stu-id="e9573-191">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="e9573-192">Quando é definido como `null`, o tamanho máximo do corpo da solicitação é ilimitado.</span><span class="sxs-lookup"><span data-stu-id="e9573-192">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="e9573-193">Esse limite não afeta as conexões atualizadas que são sempre ilimitadas.</span><span class="sxs-lookup"><span data-stu-id="e9573-193">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

   <span data-ttu-id="e9573-194">O método recomendado para substituir o limite em um aplicativo ASP.NET Core MVC para um único `IActionResult` é usar o atributo <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="e9573-194">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

   ```csharp
   [RequestSizeLimit(100000000)]
   public IActionResult MyActionMethod()
   ```

   <span data-ttu-id="e9573-195">Uma exceção é gerada quando o aplicativo tenta configurar o limite de uma solicitação, depois que o aplicativo inicia a leitura da solicitação.</span><span class="sxs-lookup"><span data-stu-id="e9573-195">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="e9573-196">É possível usar uma propriedade `IsReadOnly` para indicar se a propriedade `MaxRequestBodySize` está no estado somente leitura, o que significa que é tarde demais para configurar o limite.</span><span class="sxs-lookup"><span data-stu-id="e9573-196">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

   <span data-ttu-id="e9573-197">Se o aplicativo precisar substituir <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> mediante solicitação, use o <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span><span class="sxs-lookup"><span data-stu-id="e9573-197">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

   [!code-csharp[](httpsys/sample/Startup.cs?name=snippet1&highlight=6-7)]

3. <span data-ttu-id="e9573-198">Quando usar o Visual Studio, verifique se que o aplicativo está configurado para executar o IIS ou IIS Express.</span><span class="sxs-lookup"><span data-stu-id="e9573-198">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

   <span data-ttu-id="e9573-199">No Visual Studio, o perfil de inicialização padrão destina-se ao IIS Express.</span><span class="sxs-lookup"><span data-stu-id="e9573-199">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="e9573-200">Para executar o projeto como um aplicativo de console, altere manualmente o perfil selecionado, conforme mostrado na captura de tela a seguir:</span><span class="sxs-lookup"><span data-stu-id="e9573-200">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

   ![Selecionar o perfil do aplicativo de console](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="e9573-202">Configurar o Windows Server</span><span class="sxs-lookup"><span data-stu-id="e9573-202">Configure Windows Server</span></span>

1. <span data-ttu-id="e9573-203">Determine as portas que serão abertas para o aplicativo e use o [Firewall do Windows](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) ou o cmdlet do PowerShell [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) para abrir as portas de firewall e permitir que o tráfego chegue até o HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="e9573-203">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="e9573-204">Nos seguintes comandos e configuração de aplicativo, a porta 443 é usada.</span><span class="sxs-lookup"><span data-stu-id="e9573-204">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="e9573-205">Ao implantar em uma VM do Azure, abra as portas no [Grupo de Segurança de Rede](/azure/virtual-machines/windows/nsg-quickstart-portal).</span><span class="sxs-lookup"><span data-stu-id="e9573-205">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="e9573-206">Nos seguintes comandos e configuração de aplicativo, a porta 443 é usada.</span><span class="sxs-lookup"><span data-stu-id="e9573-206">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="e9573-207">Obtenha e instale os certificados X.509, se precisar.</span><span class="sxs-lookup"><span data-stu-id="e9573-207">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="e9573-208">No Windows, crie certificados autoassinados, usando o [cmdlet do PowerShell New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate).</span><span class="sxs-lookup"><span data-stu-id="e9573-208">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="e9573-209">Para ver um exemplo sem suporte, confira [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="e9573-209">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="e9573-210">Instale certificados autoassinados ou assinados pela AC no repositório **Computador Local** > **Pessoal** do servidor.</span><span class="sxs-lookup"><span data-stu-id="e9573-210">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="e9573-211">Se o aplicativo for uma [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd), instale o .NET Core, o .NET Framework ou ambos (caso o aplicativo .NET Core seja direcionado ao .NET Framework).</span><span class="sxs-lookup"><span data-stu-id="e9573-211">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="e9573-212">**.NET Core** &ndash; Se o aplicativo exigir o .NET Core, obtenha e execute o instalador do **Tempo de Execução do .NET Core** em [Downloads do .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="e9573-212">**.NET Core** &ndash; If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="e9573-213">Não instale o SDK completo no servidor.</span><span class="sxs-lookup"><span data-stu-id="e9573-213">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="e9573-214">**.NET framework** &ndash; Se o aplicativo exigir o .NET Framework, confira o [Guia de instalação do .NET Framework](/dotnet/framework/install/).</span><span class="sxs-lookup"><span data-stu-id="e9573-214">**.NET Framework** &ndash; If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="e9573-215">Instale o .NET Framework necessário.</span><span class="sxs-lookup"><span data-stu-id="e9573-215">Install the required .NET Framework.</span></span> <span data-ttu-id="e9573-216">O instalador do .NET Framework mais recente está disponível na página [Downloads do .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="e9573-216">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="e9573-217">Se o aplicativo for uma [implantação autocontida](/dotnet/core/deploying/#framework-dependent-deployments-scd), ele incluirá o tempo de execução em sua implantação.</span><span class="sxs-lookup"><span data-stu-id="e9573-217">If the app is a [self-contained deployment](/dotnet/core/deploying/#framework-dependent-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="e9573-218">Nenhuma instalação do framework é necessária no servidor.</span><span class="sxs-lookup"><span data-stu-id="e9573-218">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="e9573-219">Configure URLs e portas no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e9573-219">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="e9573-220">Por padrão, o ASP.NET Core é associado a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="e9573-220">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="e9573-221">Para configurar portas e prefixos de URL, as opções incluem:</span><span class="sxs-lookup"><span data-stu-id="e9573-221">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="e9573-222">O argumento de linha de comando `urls`</span><span class="sxs-lookup"><span data-stu-id="e9573-222">`urls` command-line argument</span></span>
   * <span data-ttu-id="e9573-223">A variável de ambiente `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="e9573-223">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="e9573-224">O exemplo de código a seguir mostra como usar <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> com o endereço IP local do servidor `10.0.0.4` na porta 443:</span><span class="sxs-lookup"><span data-stu-id="e9573-224">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/sample_snapshot/Program.cs?name=snippet1&highlight=11)]

   <span data-ttu-id="e9573-225">Uma vantagem de usar `UrlPrefixes` é que uma mensagem de erro é gerada imediatamente no caso de prefixos formatados de forma incorreta.</span><span class="sxs-lookup"><span data-stu-id="e9573-225">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="e9573-226">As configurações de `UrlPrefixes` substituem as configurações `UseUrls`/`urls`/`ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="e9573-226">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="e9573-227">Portanto, uma vantagem de usar `UseUrls`, `urls` e a variável de ambiente `ASPNETCORE_URLS` é que fica mais fácil alternar entre o Kestrel e o HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="e9573-227">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span> <span data-ttu-id="e9573-228">Para obter mais informações, consulte <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="e9573-228">For more information, see <xref:fundamentals/host/web-host>.</span></span>

   <span data-ttu-id="e9573-229">O HTTP.sys usa os [formatos de cadeia de caracteres UrlPrefix da API do Servidor HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span><span class="sxs-lookup"><span data-stu-id="e9573-229">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).</span></span>

   > [!WARNING]
   > <span data-ttu-id="e9573-230">Associações de curinga de nível superior (`http://*:80/` e `http://+:80`) **não** devem ser usadas.</span><span class="sxs-lookup"><span data-stu-id="e9573-230">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="e9573-231">Associações de curinga de nível superior criam vulnerabilidades de segurança no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e9573-231">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="e9573-232">Isso se aplica a curingas fortes e fracos.</span><span class="sxs-lookup"><span data-stu-id="e9573-232">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="e9573-233">Use nomes de host explícitos ou endereços IP em vez de curingas.</span><span class="sxs-lookup"><span data-stu-id="e9573-233">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="e9573-234">Associações de curinga de subdomínio (por exemplo, `*.mysub.com`) não serão um risco à segurança se você controlar todo o domínio pai (ao contrário de `*.com`, o qual é vulnerável).</span><span class="sxs-lookup"><span data-stu-id="e9573-234">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="e9573-235">Para saber mais, confira [RFC 7230: Seção 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="e9573-235">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="e9573-236">Pré-registre os prefixos de URL no servidor.</span><span class="sxs-lookup"><span data-stu-id="e9573-236">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="e9573-237">O *netsh.exe* é a ferramenta interna destinada a configurar o HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="e9573-237">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="e9573-238">Com o *netsh.exe*, é possível reservar prefixos de URL e atribuir certificados X.509.</span><span class="sxs-lookup"><span data-stu-id="e9573-238">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="e9573-239">A ferramenta exige privilégios de administrador.</span><span class="sxs-lookup"><span data-stu-id="e9573-239">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="e9573-240">Use a ferramenta *netsh.exe* para registrar as URLs do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e9573-240">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="e9573-241">`<URL>` &ndash; A URL (Uniform Resource Locator) totalmente qualificada.</span><span class="sxs-lookup"><span data-stu-id="e9573-241">`<URL>` &ndash; The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="e9573-242">Não use uma associação de curinga.</span><span class="sxs-lookup"><span data-stu-id="e9573-242">Don't use a wildcard binding.</span></span> <span data-ttu-id="e9573-243">Use um nome de host válido ou o endereço IP local.</span><span class="sxs-lookup"><span data-stu-id="e9573-243">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="e9573-244">*A URL deve incluir uma barra à direita.*</span><span class="sxs-lookup"><span data-stu-id="e9573-244">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="e9573-245">`<USER>` &ndash; Especifica o nome de usuário ou do grupo de usuários.</span><span class="sxs-lookup"><span data-stu-id="e9573-245">`<USER>` &ndash; Specifies the user or user-group name.</span></span>

   <span data-ttu-id="e9573-246">No exemplo a seguir, o endereço IP local do servidor é `10.0.0.4`:</span><span class="sxs-lookup"><span data-stu-id="e9573-246">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="e9573-247">Quando uma URL é registrada, a ferramenta responde com `URL reservation successfully added`.</span><span class="sxs-lookup"><span data-stu-id="e9573-247">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="e9573-248">Para excluir uma URL registrada, use o comando `delete urlacl`:</span><span class="sxs-lookup"><span data-stu-id="e9573-248">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="e9573-249">Registre certificados X.509 no servidor.</span><span class="sxs-lookup"><span data-stu-id="e9573-249">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="e9573-250">Use a ferramenta *netsh.exe* para registrar certificados do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e9573-250">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="e9573-251">`<IP>` &ndash; Especifica o endereço IP local para a associação.</span><span class="sxs-lookup"><span data-stu-id="e9573-251">`<IP>` &ndash; Specifies the local IP address for the binding.</span></span> <span data-ttu-id="e9573-252">Não use uma associação de curinga.</span><span class="sxs-lookup"><span data-stu-id="e9573-252">Don't use a wildcard binding.</span></span> <span data-ttu-id="e9573-253">Use um endereço IP válido.</span><span class="sxs-lookup"><span data-stu-id="e9573-253">Use a valid IP address.</span></span>
   * <span data-ttu-id="e9573-254">`<PORT>` &ndash; Especifica a porta da associação.</span><span class="sxs-lookup"><span data-stu-id="e9573-254">`<PORT>` &ndash; Specifies the port for the binding.</span></span>
   * <span data-ttu-id="e9573-255">`<THUMBPRINT>` &ndash; A impressão digital do certificado X.509.</span><span class="sxs-lookup"><span data-stu-id="e9573-255">`<THUMBPRINT>` &ndash; The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="e9573-256">`<GUID>` &ndash; Um GUID gerado pelo desenvolvedor para representar o aplicativo para fins informativos.</span><span class="sxs-lookup"><span data-stu-id="e9573-256">`<GUID>` &ndash; A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="e9573-257">Para fins de referência, armazene o GUID no aplicativo como uma marca de pacote:</span><span class="sxs-lookup"><span data-stu-id="e9573-257">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="e9573-258">No Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="e9573-258">In Visual Studio:</span></span>
     * <span data-ttu-id="e9573-259">Abra as propriedades do projeto do aplicativo, clicando com o botão direito do mouse no aplicativo no **Gerenciador de Soluções** e selecionando **Propriedades**.</span><span class="sxs-lookup"><span data-stu-id="e9573-259">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="e9573-260">Selecione a guia **Pacote**.</span><span class="sxs-lookup"><span data-stu-id="e9573-260">Select the **Package** tab.</span></span>
     * <span data-ttu-id="e9573-261">Insira o GUID que você criou no campo **Marcas**.</span><span class="sxs-lookup"><span data-stu-id="e9573-261">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="e9573-262">Quando não estiver usando o Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="e9573-262">When not using Visual Studio:</span></span>
     * <span data-ttu-id="e9573-263">Abra o arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e9573-263">Open the app's project file.</span></span>
     * <span data-ttu-id="e9573-264">Adicione uma propriedade `<PackageTags>` a um `<PropertyGroup>` novo ou existente com o GUID que você criou:</span><span class="sxs-lookup"><span data-stu-id="e9573-264">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="e9573-265">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="e9573-265">In the following example:</span></span>

   * <span data-ttu-id="e9573-266">O endereço IP local do servidor é `10.0.0.4`.</span><span class="sxs-lookup"><span data-stu-id="e9573-266">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="e9573-267">Um gerador GUID aleatório online fornece o valor `appid`.</span><span class="sxs-lookup"><span data-stu-id="e9573-267">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="e9573-268">Quando um certificado é registrado, a ferramenta responde com `SSL Certificate successfully added`.</span><span class="sxs-lookup"><span data-stu-id="e9573-268">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="e9573-269">Para excluir um registro de certificado, use o comando `delete sslcert`:</span><span class="sxs-lookup"><span data-stu-id="e9573-269">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="e9573-270">Documentação de referência do *netsh.exe*:</span><span class="sxs-lookup"><span data-stu-id="e9573-270">Reference documentation for *netsh.exe*:</span></span>

   * [<span data-ttu-id="e9573-271">Comandos do Netsh para o protocolo HTTP</span><span class="sxs-lookup"><span data-stu-id="e9573-271">Netsh Commands for Hypertext Transfer Protocol (HTTP)</span></span>](https://technet.microsoft.com/library/cc725882.aspx)
   * [<span data-ttu-id="e9573-272">Cadeias de caracteres de UrlPrefix</span><span class="sxs-lookup"><span data-stu-id="e9573-272">UrlPrefix Strings</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. <span data-ttu-id="e9573-273">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e9573-273">Run the app.</span></span>

   <span data-ttu-id="e9573-274">Não é necessário ter privilégios de administrador para executar o aplicativo ao associar ao localhost usando HTTP (não HTTPS) com um número de porta maior do que 1024.</span><span class="sxs-lookup"><span data-stu-id="e9573-274">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="e9573-275">Para outras configurações (por exemplo, usar um endereço IP local ou associação à porta 443), execute o aplicativo com privilégios de administrador.</span><span class="sxs-lookup"><span data-stu-id="e9573-275">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="e9573-276">O aplicativo responde no endereço IP público do servidor.</span><span class="sxs-lookup"><span data-stu-id="e9573-276">The app responds at the server's public IP address.</span></span> <span data-ttu-id="e9573-277">Neste exemplo, o servidor é acessado pela Internet como seu endereço IP público de `104.214.79.47`.</span><span class="sxs-lookup"><span data-stu-id="e9573-277">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="e9573-278">Um certificado de desenvolvimento é usado neste exemplo.</span><span class="sxs-lookup"><span data-stu-id="e9573-278">A development certificate is used in this example.</span></span> <span data-ttu-id="e9573-279">A página é carregada com segurança após ignorar o aviso de certificado não confiável do navegador.</span><span class="sxs-lookup"><span data-stu-id="e9573-279">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Janela do navegador mostrando a página de Índice do aplicativo carregada](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e9573-281">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="e9573-281">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e9573-282">Para aplicativos hospedados pelo HTTP.sys que interagem com solicitações da Internet ou de uma rede corporativa, podem ser necessárias configurações adicionais ao hospedar atrás de balanceadores de carga e de servidores proxy.</span><span class="sxs-lookup"><span data-stu-id="e9573-282">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="e9573-283">Para obter mais informações, veja [Configurar o ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="e9573-283">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e9573-284">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e9573-284">Additional resources</span></span>

* [<span data-ttu-id="e9573-285">Habilitar a autenticação do Windows com HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e9573-285">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="e9573-286">API do servidor HTTP</span><span class="sxs-lookup"><span data-stu-id="e9573-286">HTTP Server API</span></span>](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [<span data-ttu-id="e9573-287">Repositório aspnet/HttpSysServer do GitHub (código-fonte)</span><span class="sxs-lookup"><span data-stu-id="e9573-287">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="e9573-288">O host</span><span class="sxs-lookup"><span data-stu-id="e9573-288">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>
