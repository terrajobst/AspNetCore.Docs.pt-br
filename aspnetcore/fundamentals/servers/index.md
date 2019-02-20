---
title: Implementações de servidor Web em ASP.NET Core
author: guardrex
description: Descubra os servidores Web Kestrel e HTTP.sys para ASP.NET Core. Saiba como escolher um servidor e quando usar um servidor proxy reverso.
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/14/2019
uid: fundamentals/servers/index
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="ca4d0-104">Implementações de servidor Web em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ca4d0-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="ca4d0-105">Por [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) e [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="ca4d0-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="ca4d0-106">Um aplicativo ASP.NET Core é executado com uma implementação do servidor HTTP em processo.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="ca4d0-107">A implementação do servidor escuta solicitações HTTP e apresenta-as para o aplicativo como um conjunto de [recursos de solicitação](xref:fundamentals/request-features) compostos em um <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="ca4d0-108">Windows</span><span class="sxs-lookup"><span data-stu-id="ca4d0-108">Windows</span></span>](#tab/windows)

<span data-ttu-id="ca4d0-109">O ASP.NET Core vem com os seguintes itens:</span><span class="sxs-lookup"><span data-stu-id="ca4d0-109">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="ca4d0-110">O [servidor Kestrel](xref:fundamentals/servers/kestrel) é a implementação padrão do servidor HTTP multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-110">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="ca4d0-111">O servidor HTTP do IIS é um [servidor em processo](#in-process-hosting-model) do IIS.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-111">IIS HTTP Server is an [in-process server](#in-process-hosting-model) for IIS.</span></span>
* <span data-ttu-id="ca4d0-112">O [servidor HTTP.sys](xref:fundamentals/servers/httpsys) é um servidor HTTP somente do Windows com base no [driver do kernel HTTP.sys e na API do servidor HTTP](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="ca4d0-113">Ao usar o [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) ou o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), o aplicativo é executado:</span><span class="sxs-lookup"><span data-stu-id="ca4d0-113">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="ca4d0-114">No mesmo processo que o processo de trabalho do IIS (o [modelo de hospedagem em processo](#in-process-hosting-model)) com o [servidor HTTP do IIS](#iis-http-server).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-114">In the same process as the IIS worker process (the [in-process hosting model](#in-process-hosting-model)) with the [IIS HTTP Server](#iis-http-server).</span></span> <span data-ttu-id="ca4d0-115">*Em processo* é a configuração recomendada.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-115">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="ca4d0-116">Em um processo separado do processo de trabalho do IIS (o [modelo de hospedagem fora do processo](#out-of-process-hosting-model)) com o [servidor Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-116">In a process separate from the IIS worker process (the [out-of-process hosting model](#out-of-process-hosting-model)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="ca4d0-117">O [módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) é um módulo nativo do IIS que manipula as solicitações nativas do IIS entre o IIS e o servidor HTTP do IIS em processo ou o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-117">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="ca4d0-118">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-118">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="ca4d0-119">Modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="ca4d0-119">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="ca4d0-120">Modelo de hospedagem em processo</span><span class="sxs-lookup"><span data-stu-id="ca4d0-120">In-process hosting model</span></span>

<span data-ttu-id="ca4d0-121">Usando uma hospedagem em processo, um aplicativo ASP.NET Core é executado no mesmo processo que seu processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-121">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="ca4d0-122">A hospedagem em processo oferece desempenho melhor em hospedagem fora do processo porque as solicitações não são transmitidas por proxy pelo adaptador de loopback, um adaptador de rede que retorna o tráfego de rede de saída para o mesmo computador.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-122">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="ca4d0-123">O IIS manipula o gerenciamento de processos com o [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-123">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="ca4d0-124">O Módulo do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ca4d0-124">The ASP.NET Core Module:</span></span>

* <span data-ttu-id="ca4d0-125">Executa a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-125">Performs app initialization.</span></span>
  * <span data-ttu-id="ca4d0-126">Carrega o [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-126">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="ca4d0-127">Chama `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-127">Calls `Program.Main`.</span></span>
* <span data-ttu-id="ca4d0-128">Manipula o tempo de vida da solicitação nativa do IIS.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-128">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="ca4d0-129">Não há suporte para o modelo de hospedagem em processo para aplicativos ASP.NET Core direcionados ao .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-129">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="ca4d0-130">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado em processo:</span><span class="sxs-lookup"><span data-stu-id="ca4d0-130">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Módulo do ASP.NET Core](_static/ancm-inprocess.png)

<span data-ttu-id="ca4d0-132">A solicitação chega da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-132">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="ca4d0-133">O driver roteia as solicitações nativas ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-133">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="ca4d0-134">O módulo recebe a solicitação nativa e a passa para o Servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-134">The module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="ca4d0-135">O servidor HTTP do IIS é uma implementação de servidor em processo do IIS que converte a solicitação de nativa para gerenciada.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-135">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="ca4d0-136">Depois que o Servidor HTTP do IIS processa a solicitação, a solicitação é enviada por push para o pipeline de middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-136">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="ca4d0-137">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-137">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="ca4d0-138">A resposta do aplicativo é retornada ao IIS por meio do Servidor HTTP do IIS.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-138">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="ca4d0-139">O IIS enviará a resposta ao cliente que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-139">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="ca4d0-140">A hospedagem em processo é uma opção de aceitação para os aplicativos existentes, mas o padrão dos modelos [dotnet new](/dotnet/core/tools/dotnet-new) é o modelo de hospedagem em processo para todos os cenários do IIS e do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-140">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="ca4d0-141">Modelo de hospedagem de fora do processo</span><span class="sxs-lookup"><span data-stu-id="ca4d0-141">Out-of-process hosting model</span></span>

<span data-ttu-id="ca4d0-142">Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo realiza o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-142">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="ca4d0-143">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-143">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="ca4d0-144">Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-144">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="ca4d0-145">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado de fora d processo:</span><span class="sxs-lookup"><span data-stu-id="ca4d0-145">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Módulo do ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="ca4d0-147">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-147">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="ca4d0-148">O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-148">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="ca4d0-149">O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-149">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="ca4d0-150">O módulo especifica a porta por meio de uma variável de ambiente na inicialização e o middleware de integração do IIS configura o servidor para escutar em `http://localhost:{PORT}`.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-150">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="ca4d0-151">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-151">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="ca4d0-152">O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-152">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="ca4d0-153">Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-153">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="ca4d0-154">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-154">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="ca4d0-155">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-155">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="ca4d0-156">A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-156">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="ca4d0-157">Para obter as diretrizes de configuração do IIS e do módulo do ASP.NET Core, confira os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="ca4d0-157">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[<span data-ttu-id="ca4d0-158">macOS</span><span class="sxs-lookup"><span data-stu-id="ca4d0-158">macOS</span></span>](#tab/macos)

<span data-ttu-id="ca4d0-159">O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), que é o servidor HTTP padrão multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-159">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="ca4d0-160">Linux</span><span class="sxs-lookup"><span data-stu-id="ca4d0-160">Linux</span></span>](#tab/linux)

<span data-ttu-id="ca4d0-161">O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), que é o servidor HTTP padrão multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-161">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="ca4d0-162">Windows</span><span class="sxs-lookup"><span data-stu-id="ca4d0-162">Windows</span></span>](#tab/windows)

<span data-ttu-id="ca4d0-163">O ASP.NET Core vem com os seguintes itens:</span><span class="sxs-lookup"><span data-stu-id="ca4d0-163">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="ca4d0-164">O [servidor Kestrel](xref:fundamentals/servers/kestrel) é o servidor HTTP padrão multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-164">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="ca4d0-165">O [servidor HTTP.sys](xref:fundamentals/servers/httpsys) é um servidor HTTP somente do Windows com base no [driver do kernel HTTP.sys e na API do servidor HTTP](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-165">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="ca4d0-166">Ao usar o [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) ou o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), o aplicativo é executado em um processo separado do processo de trabalho do IIS (*fora do processo*) com o [servidor Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-166">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="ca4d0-167">Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo realiza o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-167">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="ca4d0-168">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-168">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="ca4d0-169">Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-169">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="ca4d0-170">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado de fora d processo:</span><span class="sxs-lookup"><span data-stu-id="ca4d0-170">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Módulo do ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="ca4d0-172">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-172">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="ca4d0-173">O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-173">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="ca4d0-174">O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-174">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="ca4d0-175">O módulo especifica a porta por meio de uma variável de ambiente na inicialização e o middleware de integração do IIS configura o servidor para escutar em `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-175">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="ca4d0-176">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-176">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="ca4d0-177">O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-177">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="ca4d0-178">Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-178">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="ca4d0-179">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-179">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="ca4d0-180">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-180">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="ca4d0-181">A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-181">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="ca4d0-182">Para obter as diretrizes de configuração do IIS e do módulo do ASP.NET Core, confira os tópicos a seguir:</span><span class="sxs-lookup"><span data-stu-id="ca4d0-182">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[<span data-ttu-id="ca4d0-183">macOS</span><span class="sxs-lookup"><span data-stu-id="ca4d0-183">macOS</span></span>](#tab/macos)

<span data-ttu-id="ca4d0-184">O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), que é o servidor HTTP padrão multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-184">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="ca4d0-185">Linux</span><span class="sxs-lookup"><span data-stu-id="ca4d0-185">Linux</span></span>](#tab/linux)

<span data-ttu-id="ca4d0-186">O ASP.NET Core vem com o [servidor Kestrel](xref:fundamentals/servers/kestrel), que é o servidor HTTP padrão multiplataforma.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-186">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

## <a name="kestrel"></a><span data-ttu-id="ca4d0-187">Kestrel</span><span class="sxs-lookup"><span data-stu-id="ca4d0-187">Kestrel</span></span>

<span data-ttu-id="ca4d0-188">O Kestrel é o servidor Web padrão incluído nos modelos de projeto do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-188">Kestrel is the default web server included in ASP.NET Core project templates.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="ca4d0-189">O Kestrel pode ser usado:</span><span class="sxs-lookup"><span data-stu-id="ca4d0-189">Kestrel can be used:</span></span>

* <span data-ttu-id="ca4d0-190">Sozinho, como um servidor de borda que processa solicitações diretamente de uma rede, incluindo a Internet.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-190">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![O Kestrel se comunica diretamente com a Internet, sem um servidor proxy reverso](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="ca4d0-192">Com um *servidor proxy reverso* como [IIS (Serviços de Informações da Internet)](https://www.iis.net/), [Nginx](http://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-192">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="ca4d0-193">Um servidor proxy reverso recebe solicitações HTTP da Internet e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-193">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="ca4d0-195">Qualquer configuração de hospedagem (com ou sem um servidor proxy reverso) é compatível com os aplicativos ASP.NET Core 2.1 ou posteriores.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-195">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported for ASP.NET Core 2.1 or later apps.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="ca4d0-196">Se o aplicativo aceita somente solicitações de uma rede interna, o Kestrel pode ser usado sozinho.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-196">If the app only accepts requests from an internal network, Kestrel can be used by itself.</span></span>

![O Kestrel se comunica diretamente com a rede interna](kestrel/_static/kestrel-to-internal.png)

<span data-ttu-id="ca4d0-198">Se o aplicativo for exposto à Internet, o Kestrel deverá usar um *servidor proxy reverso*, como o [IIS (Serviços de Informações da Internet)](https://www.iis.net/), [Nginx](http://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-198">If the app is exposed to the Internet, Kestrel must use a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="ca4d0-199">Um servidor proxy reverso recebe solicitações HTTP da Internet e encaminha-as para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-199">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

![O Kestrel se comunica indiretamente com a Internet através de um servidor proxy reverso, tal como o IIS, o Nginx ou o Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="ca4d0-201">O motivo mais importante para usar um proxy reverso para implantações de servidores de borda voltados para o público que são expostas diretamente na Internet é a segurança.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-201">The most important reason for using a reverse proxy for public-facing edge server deployments that are exposed directly the Internet is security.</span></span> <span data-ttu-id="ca4d0-202">As versões 1.x do Kestrel não incluem recursos de segurança importantes para proteção contra ataques da Internet.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-202">The 1.x versions of Kestrel don't include important security features to defend against attacks from the Internet.</span></span> <span data-ttu-id="ca4d0-203">Isso inclui, mas não se limita aos tempos limite, limites de tamanho da solicitação e limites de conexões simultâneas apropriados.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-203">This includes, but isn't limited to, appropriate timeouts, request size limits, and concurrent connection limits.</span></span>

::: moniker-end

<span data-ttu-id="ca4d0-204">Para obter diretrizes de configuração do Kestrel e informações sobre quando usar o Kestrel em uma configuração de proxy reverso, confira <xref:fundamentals/servers/kestrel>.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-204">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

### <a name="nginx-with-kestrel"></a><span data-ttu-id="ca4d0-205">Nginx com Kestrel</span><span class="sxs-lookup"><span data-stu-id="ca4d0-205">Nginx with Kestrel</span></span>

<span data-ttu-id="ca4d0-206">Para obter informações sobre como usar Nginx no Linux como um servidor proxy reverso para Kestrel, confira <xref:host-and-deploy/linux-nginx>.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-206">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="ca4d0-207">Apache com Kestrel</span><span class="sxs-lookup"><span data-stu-id="ca4d0-207">Apache with Kestrel</span></span>

<span data-ttu-id="ca4d0-208">Para obter informações sobre como usar Apache no Linux como um servidor proxy reverso para Kestrel, confira <xref:host-and-deploy/linux-apache>.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-208">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="iis-http-server"></a><span data-ttu-id="ca4d0-209">Servidor HTTP do IIS</span><span class="sxs-lookup"><span data-stu-id="ca4d0-209">IIS HTTP Server</span></span>

<span data-ttu-id="ca4d0-210">O servidor HTTP do IIS é um [servidor em processo](#in-process-hosting-model) do IIS e é necessário para implantações em processo.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-210">IIS HTTP Server is an [in-process server](#in-process-hosting-model) for IIS and required for in-process deployments.</span></span> <span data-ttu-id="ca4d0-211">O [módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) manipula solicitações nativas do IIS entre o IIS e o servidor HTTP do IIS.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-211">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) handles native IIS requests between IIS and IIS HTTP Server.</span></span> <span data-ttu-id="ca4d0-212">Para obter mais informações, consulte <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-212">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="ca4d0-213">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ca4d0-213">HTTP.sys</span></span>

<span data-ttu-id="ca4d0-214">Se os aplicativos ASP.NET Core forem executados no Windows, o HTTP.sys será uma alternativa ao Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-214">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="ca4d0-215">O Kestrel geralmente é recomendado para melhor desempenho.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-215">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="ca4d0-216">O HTTP.sys pode ser usado em cenários em que o aplicativo é exposto à Internet e as funcionalidades necessárias são compatíveis com HTTP.sys, mas não com Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-216">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="ca4d0-217">Para obter mais informações, consulte <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-217">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![O HTTP.sys se comunica diretamente com a Internet](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="ca4d0-219">O HTTP.sys também pode ser usado para aplicativos que são expostos somente a uma rede interna.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-219">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![O HTTP.sys se comunica diretamente com a rede interna](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="ca4d0-221">Para obter as diretrizes de configuração do HTTP.sys, confira <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-221">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="ca4d0-222">Infraestrutura de servidor do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ca4d0-222">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="ca4d0-223">O <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> disponível no método `Startup.Configure` expõe a propriedade <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> do tipo <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-223">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="ca4d0-224">O Kestrel e o HTTP.sys expõem apenas um único recurso cada, o <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, mas diferentes implementações de servidor podem expor funcionalidades adicionais.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-224">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="ca4d0-225">`IServerAddressesFeature` pode ser usado para descobrir a qual porta a implementação do servidor se acoplou durante o tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-225">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="ca4d0-226">Servidores personalizados</span><span class="sxs-lookup"><span data-stu-id="ca4d0-226">Custom servers</span></span>

<span data-ttu-id="ca4d0-227">Se os servidores internos não atenderem aos requisitos do aplicativo, um servidor personalizado poderá ser criado.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-227">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="ca4d0-228">O [Guia de OWIN (Open Web Interface para .NET)](xref:fundamentals/owin) demonstra como gravar uma implementação [com base em ](https://github.com/Bobris/Nowin)Nowin<xref:Microsoft.AspNetCore.Hosting.Server.IServer>.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-228">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="ca4d0-229">Somente as interfaces de recurso que o aplicativo usa exigem implementação, embora no mínimo <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> e <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> devam ser compatíveis.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-229">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="ca4d0-230">Inicialização do servidor</span><span class="sxs-lookup"><span data-stu-id="ca4d0-230">Server startup</span></span>

<span data-ttu-id="ca4d0-231">O servidor é iniciado quando o IDE (Ambiente de Desenvolvimento Integrado) ou o editor inicia o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="ca4d0-231">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="ca4d0-232">[Visual Studio](https://www.visualstudio.com/vs/) &ndash; os perfis de inicialização podem ser usados para iniciar o aplicativo e o servidor com o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module) ou o console.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-232">[Visual Studio](https://www.visualstudio.com/vs/) &ndash; Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="ca4d0-233">[Visual Studio Code](https://code.visualstudio.com/) &ndash; o aplicativo e o servidor são iniciados pelo [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), que ativa o depurador CoreCLR.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-233">[Visual Studio Code](https://code.visualstudio.com/) &ndash; The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="ca4d0-234">[Visual Studio para Mac](https://www.visualstudio.com/vs/mac/) &ndash; o aplicativo e o servidor são iniciados pelo [Depurador de modo suave Mono](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-234">[Visual Studio for Mac](https://www.visualstudio.com/vs/mac/) &ndash; The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="ca4d0-235">Ao iniciar o aplicativo usando um prompt de comando na pasta do projeto, o [dotnet run](/dotnet/core/tools/dotnet-run) inicia o aplicativo e o servidor (apenas Kestrel e HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-235">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="ca4d0-236">A configuração é especificada pela opção `-c|--configuration`, que é definida como `Debug` (padrão) ou `Release`.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-236">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span> <span data-ttu-id="ca4d0-237">Se os perfis de inicialização estiverem presentes em um arquivo *launchSettings.json*, use a opção `--launch-profile <NAME>` para definir o perfil de inicialização (por exemplo, `Development` ou `Production`).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-237">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile <NAME>` option to set the launch profile (for example, `Development` or `Production`).</span></span> <span data-ttu-id="ca4d0-238">Para obter mais informações, confira [dotnet run](/dotnet/core/tools/dotnet-run) e [pacote de distribuição do .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="ca4d0-238">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="ca4d0-239">Compatibilidade com HTTP/2</span><span class="sxs-lookup"><span data-stu-id="ca4d0-239">HTTP/2 support</span></span>

<span data-ttu-id="ca4d0-240">O [HTTP/2](https://httpwg.org/specs/rfc7540.html) é compatível com ASP.NET Core nos seguintes cenários de implantação:</span><span class="sxs-lookup"><span data-stu-id="ca4d0-240">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="ca4d0-241">Kestrel</span><span class="sxs-lookup"><span data-stu-id="ca4d0-241">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="ca4d0-242">Sistema operacional</span><span class="sxs-lookup"><span data-stu-id="ca4d0-242">Operating system</span></span>
    * <span data-ttu-id="ca4d0-243">Windows Server 2016/Windows 10 ou posterior&dagger;</span><span class="sxs-lookup"><span data-stu-id="ca4d0-243">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="ca4d0-244">Linux com OpenSSL 1.0.2 ou posterior (por exemplo, Ubuntu 16.04 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="ca4d0-244">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="ca4d0-245">O HTTP/2 será compatível com macOS em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-245">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="ca4d0-246">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ca4d0-246">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="ca4d0-247">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ca4d0-247">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="ca4d0-248">Windows Server 2016/Windows 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ca4d0-248">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="ca4d0-249">Estrutura de destino: Não aplicável a implantações do HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-249">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="ca4d0-250">IIS (em processo)</span><span class="sxs-lookup"><span data-stu-id="ca4d0-250">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="ca4d0-251">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ca4d0-251">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="ca4d0-252">Estrutura de destino: .NET Core 2.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ca4d0-252">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="ca4d0-253">IIS (fora do processo)</span><span class="sxs-lookup"><span data-stu-id="ca4d0-253">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="ca4d0-254">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ca4d0-254">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="ca4d0-255">Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o Kestrel usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-255">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="ca4d0-256">Estrutura de destino: Não aplicável a implantações fora do processo do IIS.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-256">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="ca4d0-257">&dagger;O Kestrel tem suporte limitado para HTTP/2 no Windows Server 2012 R2 e Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-257">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="ca4d0-258">O suporte é limitado porque a lista de conjuntos de codificação TLS disponível nesses sistemas operacionais é limitada.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-258">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="ca4d0-259">Um certificado gerado usando um ECDSA (Algoritmo de Assinatura Digital Curva Elíptica) pode ser necessário para proteger conexões TLS.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-259">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="ca4d0-260">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ca4d0-260">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="ca4d0-261">Windows Server 2016/Windows 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ca4d0-261">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="ca4d0-262">Estrutura de destino: Não aplicável a implantações do HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-262">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="ca4d0-263">IIS (fora do processo)</span><span class="sxs-lookup"><span data-stu-id="ca4d0-263">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="ca4d0-264">Windows Server 2016/Windows 10 ou posterior; IIS 10 ou posterior</span><span class="sxs-lookup"><span data-stu-id="ca4d0-264">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="ca4d0-265">Conexões de servidor de borda voltadas para o público usam HTTP/2, mas a conexão de proxy reverso para o Kestrel usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-265">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="ca4d0-266">Estrutura de destino: Não aplicável a implantações fora do processo do IIS.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-266">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="ca4d0-267">Uma conexão HTTP/2 precisa usar [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) e TLS 1.2 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-267">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="ca4d0-268">Para obter mais informações, consulte os tópicos referentes aos seus cenários de implantação do servidor.</span><span class="sxs-lookup"><span data-stu-id="ca4d0-268">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ca4d0-269">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="ca4d0-269">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
