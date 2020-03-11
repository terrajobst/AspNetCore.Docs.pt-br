---
title: Implementações de servidor Web em ASP.NET Core
author: rick-anderson
description: Descubra os servidores Web Kestrel e HTTP.sys para ASP.NET Core. Saiba como escolher um servidor e quando usar um servidor proxy reverso.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/servers/index
ms.openlocfilehash: d46793ef54c99fe609b5983c5a658fb7b20032fa
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666337"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="e76a9-104">Implementações de servidor Web em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e76a9-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="e76a9-105">Por [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) e [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="e76a9-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="e76a9-106">Um aplicativo ASP.NET Core é executado com uma implementação do servidor HTTP em processo.</span><span class="sxs-lookup"><span data-stu-id="e76a9-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="e76a9-107">A implementação do servidor escuta solicitações HTTP e apresenta-as para o aplicativo como um conjunto de [recursos de solicitação](xref:fundamentals/request-features) compostos em um <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="e76a9-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

## <a name="kestrel"></a><span data-ttu-id="e76a9-108">Kestrel</span><span class="sxs-lookup"><span data-stu-id="e76a9-108">Kestrel</span></span>

<span data-ttu-id="e76a9-109">Kestrel é o servidor Web padrão especificado pelos modelos de projeto ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e76a9-109">Kestrel is the default web server specified by the ASP.NET Core project templates.</span></span>

<span data-ttu-id="e76a9-110">Use o Kestrel:</span><span class="sxs-lookup"><span data-stu-id="e76a9-110">Use Kestrel:</span></span>

* <span data-ttu-id="e76a9-111">Sozinho, como um servidor de borda que processa solicitações diretamente de uma rede, incluindo a Internet.</span><span class="sxs-lookup"><span data-stu-id="e76a9-111">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="e76a9-113">Com um *servidor proxy reverso* como [IIS (Serviços de Informações da Internet)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="e76a9-113">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="e76a9-114">Um servidor proxy reverso recebe solicitações HTTP da Internet e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e76a9-114">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="e76a9-116">Há suporte para hospedar a configuração&mdash;com ou sem um servidor proxy reverso&mdash;.</span><span class="sxs-lookup"><span data-stu-id="e76a9-116">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="e76a9-117">Para obter diretrizes de configuração do Kestrel e informações sobre quando usar o Kestrel em uma configuração de proxy reverso, confira <xref:fundamentals/servers/kestrel>.</span><span class="sxs-lookup"><span data-stu-id="e76a9-117">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="e76a9-118">Windows</span><span class="sxs-lookup"><span data-stu-id="e76a9-118">Windows</span></span>](#tab/windows)

<span data-ttu-id="e76a9-119">O ASP.NET Core vem com os seguintes itens:</span><span class="sxs-lookup"><span data-stu-id="e76a9-119">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="e76a9-120">O [servidor Kestrel](xref:fundamentals/servers/kestrel) é a implementação padrão do servidor HTTP multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="e76a9-120">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="e76a9-121">O servidor HTTP do IIS é um [servidor em processo](#hosting-models) do IIS.</span><span class="sxs-lookup"><span data-stu-id="e76a9-121">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="e76a9-122">O [servidor HTTP.sys](xref:fundamentals/servers/httpsys) é um servidor HTTP somente do Windows com base no [driver do kernel HTTP.sys e na API do servidor HTTP](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="e76a9-122">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="e76a9-123">Ao usar o [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) ou o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), o aplicativo é executado:</span><span class="sxs-lookup"><span data-stu-id="e76a9-123">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="e76a9-124">No mesmo processo que o processo de trabalho do IIS (o [modelo de hospedagem em processo](#hosting-models)) com o servidor HTTP do IIS.</span><span class="sxs-lookup"><span data-stu-id="e76a9-124">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="e76a9-125">*Em processo* é a configuração recomendada.</span><span class="sxs-lookup"><span data-stu-id="e76a9-125">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="e76a9-126">Em um processo separado do processo de trabalho do IIS (o [modelo de hospedagem fora do processo](#hosting-models)) com o [servidor Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="e76a9-126">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="e76a9-127">O [módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) é um módulo nativo do IIS que manipula as solicitações nativas do IIS entre o IIS e o servidor HTTP do IIS em processo ou o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e76a9-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="e76a9-128">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="e76a9-128">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="e76a9-129">Modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="e76a9-129">Hosting models</span></span>

<span data-ttu-id="e76a9-130">Usando uma hospedagem em processo, um aplicativo ASP.NET Core é executado no mesmo processo que seu processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="e76a9-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="e76a9-131">A hospedagem em processo oferece desempenho melhor em hospedagem fora do processo porque as solicitações não são transmitidas por proxy pelo adaptador de loopback, um adaptador de rede que retorna o tráfego de rede de saída para o mesmo computador.</span><span class="sxs-lookup"><span data-stu-id="e76a9-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="e76a9-132">O IIS manipula o gerenciamento de processos com o [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="e76a9-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e76a9-133">Usando a hospedagem fora do processo, os aplicativos do ASP.NET Core são executados em um processo separado do processo de trabalho do IIS e o módulo cuida do gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="e76a9-133">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="e76a9-134">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha.</span><span class="sxs-lookup"><span data-stu-id="e76a9-134">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="e76a9-135">Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="e76a9-135">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e76a9-136">Para obter mais informações e orientação sobre a configuração, consulte os seguintes tópicos:</span><span class="sxs-lookup"><span data-stu-id="e76a9-136">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="e76a9-137">macOS</span><span class="sxs-lookup"><span data-stu-id="e76a9-137">macOS</span></span>](#tab/macos)

<span data-ttu-id="e76a9-138">O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), que é o servidor HTTP padrão multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="e76a9-138">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="e76a9-139">Linux</span><span class="sxs-lookup"><span data-stu-id="e76a9-139">Linux</span></span>](#tab/linux)

<span data-ttu-id="e76a9-140">O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), que é o servidor HTTP padrão multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="e76a9-140">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="e76a9-141">Windows</span><span class="sxs-lookup"><span data-stu-id="e76a9-141">Windows</span></span>](#tab/windows)

<span data-ttu-id="e76a9-142">O ASP.NET Core vem com os seguintes itens:</span><span class="sxs-lookup"><span data-stu-id="e76a9-142">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="e76a9-143">O [servidor Kestrel](xref:fundamentals/servers/kestrel) é o servidor HTTP padrão multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="e76a9-143">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="e76a9-144">O [servidor HTTP.sys](xref:fundamentals/servers/httpsys) é um servidor HTTP somente do Windows com base no [driver do kernel HTTP.sys e na API do servidor HTTP](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="e76a9-144">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="e76a9-145">Ao usar o [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) ou o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), o aplicativo é executado em um processo separado do processo de trabalho do IIS (*fora do processo*) com o [servidor Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="e76a9-145">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="e76a9-146">Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo realiza o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="e76a9-146">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="e76a9-147">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha.</span><span class="sxs-lookup"><span data-stu-id="e76a9-147">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="e76a9-148">Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="e76a9-148">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="e76a9-149">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado de fora d processo:</span><span class="sxs-lookup"><span data-stu-id="e76a9-149">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Módulo do ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="e76a9-151">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="e76a9-151">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="e76a9-152">O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="e76a9-152">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="e76a9-153">O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.</span><span class="sxs-lookup"><span data-stu-id="e76a9-153">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="e76a9-154">O módulo especifica a porta por meio de uma variável de ambiente na inicialização e o [middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura o servidor para escutar em `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="e76a9-154">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="e76a9-155">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="e76a9-155">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="e76a9-156">O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e76a9-156">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="e76a9-157">Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e76a9-157">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="e76a9-158">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e76a9-158">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="e76a9-159">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e76a9-159">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="e76a9-160">A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="e76a9-160">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="e76a9-161">Para obter as diretrizes de configuração do IIS e do módulo do ASP.NET Core, confira os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="e76a9-161">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="e76a9-162">macOS</span><span class="sxs-lookup"><span data-stu-id="e76a9-162">macOS</span></span>](#tab/macos)

<span data-ttu-id="e76a9-163">O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), que é o servidor HTTP padrão multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="e76a9-163">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="e76a9-164">Linux</span><span class="sxs-lookup"><span data-stu-id="e76a9-164">Linux</span></span>](#tab/linux)

<span data-ttu-id="e76a9-165">O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), que é o servidor HTTP padrão multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="e76a9-165">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="e76a9-166">Nginx com Kestrel</span><span class="sxs-lookup"><span data-stu-id="e76a9-166">Nginx with Kestrel</span></span>

<span data-ttu-id="e76a9-167">Para obter informações sobre como usar Nginx no Linux como um servidor proxy reverso para Kestrel, confira <xref:host-and-deploy/linux-nginx>.</span><span class="sxs-lookup"><span data-stu-id="e76a9-167">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="e76a9-168">Apache com Kestrel</span><span class="sxs-lookup"><span data-stu-id="e76a9-168">Apache with Kestrel</span></span>

<span data-ttu-id="e76a9-169">Para obter informações sobre como usar Apache no Linux como um servidor proxy reverso para Kestrel, confira <xref:host-and-deploy/linux-apache>.</span><span class="sxs-lookup"><span data-stu-id="e76a9-169">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="e76a9-170">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e76a9-170">HTTP.sys</span></span>

<span data-ttu-id="e76a9-171">Se os aplicativos ASP.NET Core forem executados no Windows, o HTTP.sys será uma alternativa ao Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e76a9-171">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="e76a9-172">O Kestrel geralmente é recomendado para melhor desempenho.</span><span class="sxs-lookup"><span data-stu-id="e76a9-172">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="e76a9-173">O HTTP.sys pode ser usado em cenários em que o aplicativo é exposto à Internet e as funcionalidades necessárias são compatíveis com HTTP.sys, mas não com Kestrel.</span><span class="sxs-lookup"><span data-stu-id="e76a9-173">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="e76a9-174">Para obter mais informações, consulte <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="e76a9-174">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![O HTTP.sys se comunica diretamente com a Internet](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="e76a9-176">O HTTP.sys também pode ser usado para aplicativos que são expostos somente a uma rede interna.</span><span class="sxs-lookup"><span data-stu-id="e76a9-176">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![O HTTP.sys se comunica diretamente com a rede interna](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="e76a9-178">Para obter as diretrizes de configuração do HTTP.sys, confira <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="e76a9-178">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="e76a9-179">Infraestrutura de servidor do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e76a9-179">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="e76a9-180">O <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> disponível no método `Startup.Configure` expõe a propriedade <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> do tipo <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span><span class="sxs-lookup"><span data-stu-id="e76a9-180">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="e76a9-181">O Kestrel e o HTTP.sys expõem apenas um único recurso cada, o <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, mas diferentes implementações de servidor podem expor funcionalidades adicionais.</span><span class="sxs-lookup"><span data-stu-id="e76a9-181">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="e76a9-182">`IServerAddressesFeature` pode ser usado para descobrir a qual porta a implementação do servidor se acoplou durante o runtime.</span><span class="sxs-lookup"><span data-stu-id="e76a9-182">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="e76a9-183">Servidores personalizados</span><span class="sxs-lookup"><span data-stu-id="e76a9-183">Custom servers</span></span>

<span data-ttu-id="e76a9-184">Se os servidores internos não atenderem aos requisitos do aplicativo, um servidor personalizado poderá ser criado.</span><span class="sxs-lookup"><span data-stu-id="e76a9-184">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="e76a9-185">O [Guia de OWIN (Open Web Interface para .NET)](xref:fundamentals/owin) demonstra como gravar uma implementação [com base em ](https://github.com/Bobris/Nowin)Nowin<xref:Microsoft.AspNetCore.Hosting.Server.IServer>.</span><span class="sxs-lookup"><span data-stu-id="e76a9-185">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="e76a9-186">Somente as interfaces de recurso que o aplicativo usa exigem implementação, embora no mínimo <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> e <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> devam ser compatíveis.</span><span class="sxs-lookup"><span data-stu-id="e76a9-186">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="e76a9-187">Inicialização do servidor</span><span class="sxs-lookup"><span data-stu-id="e76a9-187">Server startup</span></span>

<span data-ttu-id="e76a9-188">O servidor é iniciado quando o IDE (Ambiente de Desenvolvimento Integrado) ou o editor inicia o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e76a9-188">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="e76a9-189">Os perfis de inicialização do [Visual Studio](https://visualstudio.microsoft.com) &ndash; podem ser usados para iniciar o aplicativo e o servidor com [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[módulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) ou o console do.</span><span class="sxs-lookup"><span data-stu-id="e76a9-189">[Visual Studio](https://visualstudio.microsoft.com) &ndash; Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="e76a9-190">[Visual Studio Code](https://code.visualstudio.com/) &ndash; o aplicativo e o servidor são iniciados pelo [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), o que ativa o depurador CoreCLR.</span><span class="sxs-lookup"><span data-stu-id="e76a9-190">[Visual Studio Code](https://code.visualstudio.com/) &ndash; The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="e76a9-191">[Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; o aplicativo e o servidor são iniciados pelo [depurador do modo Soft-mono](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span><span class="sxs-lookup"><span data-stu-id="e76a9-191">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="e76a9-192">Ao iniciar o aplicativo usando um prompt de comando na pasta do projeto, o [dotnet run](/dotnet/core/tools/dotnet-run) inicia o aplicativo e o servidor (apenas Kestrel e HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="e76a9-192">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="e76a9-193">A configuração é especificada pela opção `-c|--configuration`, que é definida como `Debug` (padrão) ou `Release`.</span><span class="sxs-lookup"><span data-stu-id="e76a9-193">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="e76a9-194">Um arquivo *launchSettings. JSON* fornece configuração ao iniciar um aplicativo com `dotnet run` ou com um depurador interno de ferramentas, como o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e76a9-194">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="e76a9-195">Se os perfis de inicialização estiverem presentes em um arquivo *launchSettings. JSON* , use a opção `--launch-profile {PROFILE NAME}` com o comando `dotnet run` ou selecione o perfil no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e76a9-195">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="e76a9-196">Para obter mais informações, confira [dotnet run](/dotnet/core/tools/dotnet-run) e [pacote de distribuição do .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="e76a9-196">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="e76a9-197">Suporte do HTTP/2</span><span class="sxs-lookup"><span data-stu-id="e76a9-197">HTTP/2 support</span></span>

<span data-ttu-id="e76a9-198">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) é compatível com ASP.NET Core nos seguintes cenários de implantação:</span><span class="sxs-lookup"><span data-stu-id="e76a9-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="e76a9-199">Kestrel</span><span class="sxs-lookup"><span data-stu-id="e76a9-199">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="e76a9-200">Sistema operacional</span><span class="sxs-lookup"><span data-stu-id="e76a9-200">Operating system</span></span>
    * <span data-ttu-id="e76a9-201">Windows Server 2016/Windows 10 ou posterior&dagger;</span><span class="sxs-lookup"><span data-stu-id="e76a9-201">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="e76a9-202">Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="e76a9-202">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="e76a9-203">O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="e76a9-203">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="e76a9-204">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e76a9-204">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="e76a9-205">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e76a9-205">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="e76a9-206">Windows Server 2016/Windows 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e76a9-206">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="e76a9-207">Estrutura de destino: não aplicável a implantações de HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="e76a9-207">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="e76a9-208">IIS (em processo)</span><span class="sxs-lookup"><span data-stu-id="e76a9-208">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="e76a9-209">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e76a9-209">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="e76a9-210">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e76a9-210">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="e76a9-211">IIS (fora do processo)</span><span class="sxs-lookup"><span data-stu-id="e76a9-211">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="e76a9-212">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e76a9-212">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="e76a9-213">Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o Kestrel usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="e76a9-213">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="e76a9-214">Estrutura de destino: não aplicável a implantações IIS fora de processo.</span><span class="sxs-lookup"><span data-stu-id="e76a9-214">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="e76a9-215">&dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="e76a9-215">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="e76a9-216">O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada.</span><span class="sxs-lookup"><span data-stu-id="e76a9-216">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="e76a9-217">Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.</span><span class="sxs-lookup"><span data-stu-id="e76a9-217">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="e76a9-218">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e76a9-218">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="e76a9-219">Windows Server 2016/Windows 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e76a9-219">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="e76a9-220">Estrutura de destino: não aplicável a implantações de HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="e76a9-220">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="e76a9-221">IIS (fora do processo)</span><span class="sxs-lookup"><span data-stu-id="e76a9-221">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="e76a9-222">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="e76a9-222">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="e76a9-223">Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o Kestrel usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="e76a9-223">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="e76a9-224">Estrutura de destino: não aplicável a implantações IIS fora de processo.</span><span class="sxs-lookup"><span data-stu-id="e76a9-224">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="e76a9-225">Uma conexão HTTP/2 precisa usar [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) e TLS 1.2 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="e76a9-225">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="e76a9-226">Para obter mais informações, consulte os tópicos referentes aos seus cenários de implantação do servidor.</span><span class="sxs-lookup"><span data-stu-id="e76a9-226">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e76a9-227">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e76a9-227">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
