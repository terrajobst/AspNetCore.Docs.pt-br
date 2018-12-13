---
title: Módulo do ASP.NET Core
author: guardrex
description: Saiba como o Módulo do ASP.NET Core permite que o servidor Web Kestrel use o IIS ou o IIS Express.
ms.author: tdykstra
ms.custom: mvc
ms.date: 11/30/2018
uid: fundamentals/servers/aspnet-core-module
ms.openlocfilehash: d3f3a42dd7aebc425905b865376a584bcf0e5153
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861453"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="a4569-103">Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4569-103">ASP.NET Core Module</span></span>

<span data-ttu-id="a4569-104">Por [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl) e [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="a4569-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a4569-105">O módulo do ASP.NET Core permite que os aplicativos ASP.NET Core sejam executados em um processo de trabalho do IIS (*em processo*) por trás do IIS em uma configuração de proxy reverso (*fora do processo*).</span><span class="sxs-lookup"><span data-stu-id="a4569-105">The ASP.NET Core Module allows ASP.NET Core apps to run in an IIS worker process (*in-process*) or behind IIS in a reverse proxy configuration (*out-of-process*).</span></span> <span data-ttu-id="a4569-106">O IIS fornece recursos avançados de segurança e capacidade de gerenciamento de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="a4569-106">IIS provides advanced web app security and manageability features.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a4569-107">O Módulo do ASP.NET Core permite que os aplicativos ASP.NET Core sejam executados por trás do IIS em uma configuração de proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="a4569-107">The ASP.NET Core Module allows ASP.NET Core apps to run behind IIS in a reverse proxy configuration.</span></span> <span data-ttu-id="a4569-108">O IIS fornece recursos avançados de segurança e capacidade de gerenciamento de aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="a4569-108">IIS provides advanced web app security and manageability features.</span></span>

::: moniker-end

<span data-ttu-id="a4569-109">Versões do Windows compatíveis:</span><span class="sxs-lookup"><span data-stu-id="a4569-109">Supported Windows versions:</span></span>

* <span data-ttu-id="a4569-110">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="a4569-110">Windows 7 or later</span></span>
* <span data-ttu-id="a4569-111">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="a4569-111">Windows Server 2008 R2 or later</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a4569-112">Ao fazer uma hospedagem em processo, o módulo usa uma implementação de servidor em processo do IIS, o Servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="a4569-112">When hosting in-process, the module uses an IIS in-process server implementation, IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="a4569-113">Ao hospedar de fora do processo, o módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a4569-113">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="a4569-114">O módulo é incompatível com o [HTTP.sys](xref:fundamentals/servers/httpsys) (chamado anteriormente de [WebListener](xref:fundamentals/servers/weblistener)).</span><span class="sxs-lookup"><span data-stu-id="a4569-114">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys) (formerly called [WebListener](xref:fundamentals/servers/weblistener)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a4569-115">O módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a4569-115">The module only works with Kestrel.</span></span> <span data-ttu-id="a4569-116">O módulo é incompatível com o [HTTP.sys](xref:fundamentals/servers/httpsys) (chamado anteriormente de [WebListener](xref:fundamentals/servers/weblistener)).</span><span class="sxs-lookup"><span data-stu-id="a4569-116">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys) (formerly called [WebListener](xref:fundamentals/servers/weblistener)).</span></span>

::: moniker-end

## <a name="aspnet-core-module-description"></a><span data-ttu-id="a4569-117">Descrição de Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4569-117">ASP.NET Core Module description</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a4569-118">O módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para:</span><span class="sxs-lookup"><span data-stu-id="a4569-118">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="a4569-119">Hospedar um aplicativo ASP.NET Core dentro do processo de trabalho do IIS (`w3wp.exe`), chamado o [modelo de hospedagem no processo](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="a4569-119">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>

* <span data-ttu-id="a4569-120">Encaminhar solicitações da Web a um aplicativo ASP.NET Core de back-end que executa o [servidor Kestrel](xref:fundamentals/servers/kestrel), chamado o [modelo de hospedagem de fora do processo](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="a4569-120">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="a4569-121">Modelo de hospedagem em processo</span><span class="sxs-lookup"><span data-stu-id="a4569-121">In-process hosting model</span></span>

<span data-ttu-id="a4569-122">Usando uma hospedagem em processo, um aplicativo ASP.NET Core é executado no mesmo processo que seu processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="a4569-122">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="a4569-123">Isso remove a penalidade de desempenho de solicitações de criação de proxies pelo adaptador de loopback ao usar o modelo de hospedagem de fora do processo.</span><span class="sxs-lookup"><span data-stu-id="a4569-123">This removes the performance penalty of proxying requests over the loopback adapter when using the out-of-process hosting model.</span></span> <span data-ttu-id="a4569-124">O IIS manipula o gerenciamento de processos com o [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="a4569-124">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="a4569-125">O Módulo do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a4569-125">The ASP.NET Core Module:</span></span>

* <span data-ttu-id="a4569-126">Executa a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a4569-126">Performs app initialization.</span></span>
  * <span data-ttu-id="a4569-127">Carrega o [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="a4569-127">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="a4569-128">Chama `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="a4569-128">Calls `Program.Main`.</span></span>
* <span data-ttu-id="a4569-129">Manipula o tempo de vida da solicitação nativa do IIS.</span><span class="sxs-lookup"><span data-stu-id="a4569-129">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="a4569-130">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado em processo:</span><span class="sxs-lookup"><span data-stu-id="a4569-130">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Módulo do ASP.NET Core](aspnet-core-module/_static/ancm-inprocess.png)

<span data-ttu-id="a4569-132">A solicitação chega da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="a4569-132">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="a4569-133">O driver roteia as solicitações nativas ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="a4569-133">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="a4569-134">O módulo recebe a solicitação nativa e a passa para o Servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="a4569-134">The module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="a4569-135">O Servidor HTTP do IIS é uma implementação de servidor em processo do IIS que converte a solicitação de nativa para gerenciada.</span><span class="sxs-lookup"><span data-stu-id="a4569-135">IIS HTTP Server is an IIS in-process server implementation that converts the request from native to managed.</span></span>

<span data-ttu-id="a4569-136">Depois que o Servidor HTTP do IIS processa a solicitação, a solicitação é enviada por push para o pipeline de middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a4569-136">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="a4569-137">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a4569-137">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="a4569-138">A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="a4569-138">The app's response is passed back to IIS, which pushes it back out to the client that initiated the request.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="a4569-139">Modelo de hospedagem de fora do processo</span><span class="sxs-lookup"><span data-stu-id="a4569-139">Out-of-process hosting model</span></span>

<span data-ttu-id="a4569-140">Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo realiza o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="a4569-140">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="a4569-141">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo se ele é desligado ou falha.</span><span class="sxs-lookup"><span data-stu-id="a4569-141">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="a4569-142">Isso é basicamente o mesmo comportamento que o dos aplicativos que são executados dentro do processo e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="a4569-142">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="a4569-143">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo hospedado de fora d processo:</span><span class="sxs-lookup"><span data-stu-id="a4569-143">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Módulo do ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="a4569-145">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="a4569-145">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="a4569-146">O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="a4569-146">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="a4569-147">O módulo encaminha as solicitações ao Kestrel em uma porta aleatória para o aplicativo, que não seja a porta 80/443.</span><span class="sxs-lookup"><span data-stu-id="a4569-147">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80/443.</span></span>

<span data-ttu-id="a4569-148">O módulo especifica a porta por meio de uma variável de ambiente na inicialização e o middleware de integração do IIS configura o servidor para escutar em `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="a4569-148">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="a4569-149">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="a4569-149">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="a4569-150">O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a4569-150">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="a4569-151">Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a4569-151">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="a4569-152">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a4569-152">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="a4569-153">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a4569-153">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="a4569-154">A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="a4569-154">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a4569-155">O Módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para encaminhar solicitações da Web para aplicativos ASP.NET Core de back-end.</span><span class="sxs-lookup"><span data-stu-id="a4569-155">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="a4569-156">Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo também realiza o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="a4569-156">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="a4569-157">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo quando ele falha.</span><span class="sxs-lookup"><span data-stu-id="a4569-157">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="a4569-158">Isso é basicamente o mesmo comportamento que o dos aplicativos ASP.NET 4.x que são executados dentro do processo do IIS e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="a4569-158">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="a4569-159">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="a4569-159">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Módulo do ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="a4569-161">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="a4569-161">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="a4569-162">O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="a4569-162">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="a4569-163">O módulo encaminha as solicitações ao Kestrel em uma porta aleatória para o aplicativo, que não seja a porta 80/443.</span><span class="sxs-lookup"><span data-stu-id="a4569-163">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80/443.</span></span>

<span data-ttu-id="a4569-164">O módulo especifica a porta por meio de uma variável de ambiente na inicialização e o middleware de integração do IIS configura o servidor para escutar em `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="a4569-164">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="a4569-165">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="a4569-165">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="a4569-166">O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a4569-166">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="a4569-167">Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a4569-167">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="a4569-168">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a4569-168">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="a4569-169">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="a4569-169">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="a4569-170">A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="a4569-170">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

<span data-ttu-id="a4569-171">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="a4569-171">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="a4569-172">Para saber mais sobre módulos do IIS ativos com o módulo, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="a4569-172">To learn more about IIS modules active with the module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="a4569-173">O Módulo do ASP.NET Core tem algumas outras funções.</span><span class="sxs-lookup"><span data-stu-id="a4569-173">The ASP.NET Core Module has a few other functions.</span></span> <span data-ttu-id="a4569-174">O módulo pode:</span><span class="sxs-lookup"><span data-stu-id="a4569-174">The module can:</span></span>

* <span data-ttu-id="a4569-175">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="a4569-175">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="a4569-176">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="a4569-176">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="a4569-177">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="a4569-177">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="a4569-178">Como instalar e usar o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4569-178">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="a4569-179">Para obter instruções detalhadas sobre como instalar e usar o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="a4569-179">For detailed instructions on how to install and use the ASP.NET Core Module, see <xref:host-and-deploy/iis/index>.</span></span> <span data-ttu-id="a4569-180">Para obter informações sobre como configurar o módulo, confira o <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="a4569-180">For information on configuring the module, see the <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a4569-181">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a4569-181">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* [<span data-ttu-id="a4569-182">Repositório do GitHub do Módulo do ASP.NET Core (código-fonte)</span><span class="sxs-lookup"><span data-stu-id="a4569-182">ASP.NET Core Module GitHub repository (source code)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
