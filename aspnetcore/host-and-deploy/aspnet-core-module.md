---
title: Módulo do ASP.NET Core
author: guardrex
description: Saiba como configurar o módulo do ASP.NET Core para hospedar aplicativos do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: dee4fe7a498d211cb8ef6a3c49017c3cc8a56847
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637853"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="0ebb1-103">Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0ebb1-103">ASP.NET Core Module</span></span>

<span data-ttu-id="0ebb1-104">Por [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0ebb1-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0ebb1-105">O módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para:</span><span class="sxs-lookup"><span data-stu-id="0ebb1-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="0ebb1-106">Hospedar um aplicativo ASP.NET Core dentro do processo de trabalho do IIS (`w3wp.exe`), chamado o [modelo de hospedagem no processo](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="0ebb1-107">Encaminhar solicitações da Web a um aplicativo ASP.NET Core de back-end que executa o [servidor Kestrel](xref:fundamentals/servers/kestrel), chamado o [modelo de hospedagem de fora do processo](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="0ebb1-108">Versões do Windows compatíveis:</span><span class="sxs-lookup"><span data-stu-id="0ebb1-108">Supported Windows versions:</span></span>

* <span data-ttu-id="0ebb1-109">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="0ebb1-109">Windows 7 or later</span></span>
* <span data-ttu-id="0ebb1-110">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="0ebb1-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="0ebb1-111">Ao fazer uma hospedagem em processo, o módulo usa uma implementação de servidor em processo do IIS, chamado Servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="0ebb1-112">Ao hospedar de fora do processo, o módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="0ebb1-113">O módulo é incompatível com [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-113">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="0ebb1-114">Modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="0ebb1-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="0ebb1-115">Modelo de hospedagem em processo</span><span class="sxs-lookup"><span data-stu-id="0ebb1-115">In-process hosting model</span></span>

<span data-ttu-id="0ebb1-116">Para configurar um aplicativo para hospedagem em processo, adicione a propriedade `<AspNetCoreHostingModel>` ao arquivo de projeto do aplicativo com um valor de `InProcess` (a hospedagem de fora do processo é definida com `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="0ebb1-116">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="0ebb1-117">Se a propriedade `<AspNetCoreHostingModel>` não estiver presente no arquivo, o valor padrão será `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-117">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="0ebb1-118">As seguintes características se aplicam ao hospedar em processo:</span><span class="sxs-lookup"><span data-stu-id="0ebb1-118">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="0ebb1-119">O Servidor HTTP do IIS (`IISHttpServer`) é usado, em vez do servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-119">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

* <span data-ttu-id="0ebb1-120">O [requestTimeout atributo](#attributes-of-the-aspnetcore-element) não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-120">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="0ebb1-121">Não há suporte para o compartilhamento do pool de aplicativos entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-121">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="0ebb1-122">Use um pool de aplicativos por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-122">Use one app pool per app.</span></span>

* <span data-ttu-id="0ebb1-123">Ao usar a [Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) ou inserir manualmente um [arquivo app_offline.htm na implantação](xref:host-and-deploy/iis/index#locked-deployment-files), o aplicativo talvez não seja desligado imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-123">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="0ebb1-124">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-124">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="0ebb1-125">A arquitetura (número de bit) do aplicativo e o tempo de execução instalado (x64 ou x86) devem corresponder à arquitetura do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-125">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="0ebb1-126">Se a configuração manual do host do aplicativo com `WebHostBuilder` (não usando [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) e o aplicativo não forem executados diretamente no servidor Kestrel (auto-hospedado), chame `UseKestrel` antes de chamar `UseIISIntegration`.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-126">If setting up the app's host manually with `WebHostBuilder` (not using [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) and the app is ever run directly on the Kestrel server (self-hosted), call `UseKestrel` before calling `UseIISIntegration`.</span></span> <span data-ttu-id="0ebb1-127">Se a ordem for invertida, a inicialização do host falhará.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-127">If the order is reversed, the host fails to start.</span></span>

* <span data-ttu-id="0ebb1-128">As desconexões do cliente são detectadas.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-128">Client disconnects are detected.</span></span> <span data-ttu-id="0ebb1-129">O token de cancelamento [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) é cancelado quando o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-129">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="0ebb1-130"><xref:System.IO.Directory.GetCurrentDirectory*> retorna o diretório de trabalho do processo iniciado pelo IIS em vez de o diretório do aplicativo (por exemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-130"><xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="0ebb1-131">Para obter o código de exemplo que define o diretório atual do aplicativo, confira a [classe CurrentDirectoryHelpers](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-131">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="0ebb1-132">Chame o método `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-132">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="0ebb1-133">As chamadas seguintes a <xref:System.IO.Directory.GetCurrentDirectory*> fornecem o diretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-133">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="0ebb1-134">Modelo de hospedagem de fora do processo</span><span class="sxs-lookup"><span data-stu-id="0ebb1-134">Out-of-process hosting model</span></span>

<span data-ttu-id="0ebb1-135">Para configurar um aplicativo para hospedagem fora do processo, use qualquer uma das abordagens a seguir no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="0ebb1-135">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="0ebb1-136">não especifique a propriedade `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-136">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="0ebb1-137">Se a propriedade `<AspNetCoreHostingModel>` não estiver presente no arquivo, o valor padrão será `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-137">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="0ebb1-138">Defina o valor da propriedade `<AspNetCoreHostingModel>` como `OutOfProcess` (a hospedagem no processo é definida com `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="0ebb1-138">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="0ebb1-139">O servidor [Kestrel](xref:fundamentals/servers/kestrel) é usado, em vez do servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-139">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="0ebb1-140">Alterações no modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="0ebb1-140">Hosting model changes</span></span>

<span data-ttu-id="0ebb1-141">Se a configuração `hostingModel` for alterada no arquivo *web.config* (explicado na seção [Configuração a Web.config](#configuration-with-webconfig)), o módulo reciclará o processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-141">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="0ebb1-142">Para o IIS Express, o módulo não recicla o processo de trabalho, mas em vez disso, dispara um desligamento normal do processo atual do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-142">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="0ebb1-143">A próxima solicitação para o aplicativo gera um novo processo do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-143">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="0ebb1-144">Nome do processo</span><span class="sxs-lookup"><span data-stu-id="0ebb1-144">Process name</span></span>

<span data-ttu-id="0ebb1-145">`Process.GetCurrentProcess().ProcessName` relata `w3wp`/`iisexpress` (em processo) ou `dotnet` (fora do processo).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-145">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="0ebb1-146">O Módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para encaminhar solicitações da Web para aplicativos ASP.NET Core de back-end.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-146">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="0ebb1-147">Versões do Windows compatíveis:</span><span class="sxs-lookup"><span data-stu-id="0ebb1-147">Supported Windows versions:</span></span>

* <span data-ttu-id="0ebb1-148">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="0ebb1-148">Windows 7 or later</span></span>
* <span data-ttu-id="0ebb1-149">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="0ebb1-149">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="0ebb1-150">O módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-150">The module only works with Kestrel.</span></span> <span data-ttu-id="0ebb1-151">O módulo é incompatível com [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-151">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="0ebb1-152">Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo também realiza o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-152">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="0ebb1-153">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo quando ele falha.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-153">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="0ebb1-154">Isso é basicamente o mesmo comportamento que o dos aplicativos ASP.NET 4.x que são executados dentro do processo do IIS e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-154">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="0ebb1-155">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0ebb1-155">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Módulo do ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="0ebb1-157">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-157">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="0ebb1-158">O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-158">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="0ebb1-159">O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-159">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="0ebb1-160">O módulo especifica a porta por meio de uma variável de ambiente na inicialização e o middleware de integração do IIS configura o servidor para escutar em `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-160">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="0ebb1-161">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-161">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="0ebb1-162">O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-162">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="0ebb1-163">Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-163">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="0ebb1-164">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-164">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="0ebb1-165">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-165">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="0ebb1-166">A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-166">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

<span data-ttu-id="0ebb1-167">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-167">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="0ebb1-168">Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-168">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="0ebb1-169">O Módulo do ASP.NET Core também pode:</span><span class="sxs-lookup"><span data-stu-id="0ebb1-169">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="0ebb1-170">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-170">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="0ebb1-171">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-171">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="0ebb1-172">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-172">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="0ebb1-173">Como instalar e usar o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0ebb1-173">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="0ebb1-174">Para obter instruções sobre como instalar e usar o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-174">For instructions on how to install and use the ASP.NET Core Module, see <xref:host-and-deploy/iis/index>.</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="0ebb1-175">Configuração com web.config</span><span class="sxs-lookup"><span data-stu-id="0ebb1-175">Configuration with web.config</span></span>

<span data-ttu-id="0ebb1-176">O Módulo do ASP.NET Core está configurado com a seção `aspNetCore` do nó `system.webServer` no arquivo *web.config* do site.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-176">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="0ebb1-177">O seguinte arquivo *web.config* é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o Módulo do ASP.NET Core para manipular solicitações de site:</span><span class="sxs-lookup"><span data-stu-id="0ebb1-177">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet" 
                  arguments=".\MyApp.dll" 
                  stdoutLogEnabled="false" 
                  stdoutLogFile=".\logs\stdout" 
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet" 
                arguments=".\MyApp.dll" 
                stdoutLogEnabled="false" 
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

<span data-ttu-id="0ebb1-178">O seguinte *web.config* é publicado para uma [implantação autossuficiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="0ebb1-178">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe" 
                  stdoutLogEnabled="false" 
                  stdoutLogFile=".\logs\stdout" 
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="0ebb1-179">A propriedade <xref:System.Configuration.SectionInformation.InheritInChildApplications*> é definida como `false` para indicar que as configurações especificadas no elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) não são herdadas por aplicativos que residem em um subdiretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-179">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe" 
                stdoutLogEnabled="false" 
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

<span data-ttu-id="0ebb1-180">Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-180">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="0ebb1-181">O caminho salva logs de stdout para a pasta *LogFiles*, que é um local criado automaticamente pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-181">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="0ebb1-182">Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-182">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="0ebb1-183">Atributos do elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="0ebb1-183">Attributes of the aspNetCore element</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="0ebb1-184">Atributo</span><span class="sxs-lookup"><span data-stu-id="0ebb1-184">Attribute</span></span> | <span data-ttu-id="0ebb1-185">Descrição</span><span class="sxs-lookup"><span data-stu-id="0ebb1-185">Description</span></span> | <span data-ttu-id="0ebb1-186">Padrão</span><span class="sxs-lookup"><span data-stu-id="0ebb1-186">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="0ebb1-187">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-187">Optional string attribute.</span></span></p><p><span data-ttu-id="0ebb1-188">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-188">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="0ebb1-189">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-189">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ebb1-190">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-190">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="0ebb1-191">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-191">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ebb1-192">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-192">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="0ebb1-193">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-193">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="0ebb1-194">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-194">Optional string attribute.</span></span></p><p><span data-ttu-id="0ebb1-195">Especifica o modelo de hospedagem como em processo (`InProcess`) ou de fora do processo (`OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-195">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="0ebb1-196">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-196">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ebb1-197">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-197">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="0ebb1-198">&dagger;Para hospedagem em processo, o valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-198">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p> | <span data-ttu-id="0ebb1-199">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-199">Default: `1`</span></span><br><span data-ttu-id="0ebb1-200">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-200">Min: `1`</span></span><br><span data-ttu-id="0ebb1-201">Máx.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="0ebb1-201">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="0ebb1-202">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-202">Required string attribute.</span></span></p><p><span data-ttu-id="0ebb1-203">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-203">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="0ebb1-204">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-204">Relative paths are supported.</span></span> <span data-ttu-id="0ebb1-205">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-205">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="0ebb1-206">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-206">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ebb1-207">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-207">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="0ebb1-208">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-208">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="0ebb1-209">Sem suporte com hospedagem padrão.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-209">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="0ebb1-210">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-210">Default: `10`</span></span><br><span data-ttu-id="0ebb1-211">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-211">Min: `0`</span></span><br><span data-ttu-id="0ebb1-212">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-212">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="0ebb1-213">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-213">Optional timespan attribute.</span></span></p><p><span data-ttu-id="0ebb1-214">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-214">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="0ebb1-215">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-215">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="0ebb1-216">Não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-216">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="0ebb1-217">Para a hospedagem em processo, o módulo aguarda o aplicativo processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-217">For in-process hosting, the module waits for the app to process the request.</span></span></p> | <span data-ttu-id="0ebb1-218">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-218">Default: `00:02:00`</span></span><br><span data-ttu-id="0ebb1-219">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-219">Min: `00:00:00`</span></span><br><span data-ttu-id="0ebb1-220">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-220">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="0ebb1-221">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-221">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ebb1-222">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-222">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="0ebb1-223">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-223">Default: `10`</span></span><br><span data-ttu-id="0ebb1-224">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-224">Min: `0`</span></span><br><span data-ttu-id="0ebb1-225">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-225">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="0ebb1-226">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-226">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ebb1-227">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-227">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="0ebb1-228">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-228">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="0ebb1-229">O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-229">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="0ebb1-230">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-230">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="0ebb1-231">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-231">Default: `120`</span></span><br><span data-ttu-id="0ebb1-232">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-232">Min: `0`</span></span><br><span data-ttu-id="0ebb1-233">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-233">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="0ebb1-234">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-234">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ebb1-235">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-235">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="0ebb1-236">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-236">Optional string attribute.</span></span></p><p><span data-ttu-id="0ebb1-237">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-237">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="0ebb1-238">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-238">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="0ebb1-239">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-239">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="0ebb1-240">As pastas fornecidas no caminho devem existir para que o módulo crie o arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-240">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="0ebb1-241">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo (*.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-241">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="0ebb1-242">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-242">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="0ebb1-243">Atributo</span><span class="sxs-lookup"><span data-stu-id="0ebb1-243">Attribute</span></span> | <span data-ttu-id="0ebb1-244">Descrição</span><span class="sxs-lookup"><span data-stu-id="0ebb1-244">Description</span></span> | <span data-ttu-id="0ebb1-245">Padrão</span><span class="sxs-lookup"><span data-stu-id="0ebb1-245">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="0ebb1-246">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-246">Optional string attribute.</span></span></p><p><span data-ttu-id="0ebb1-247">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-247">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="0ebb1-248">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-248">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ebb1-249">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-249">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="0ebb1-250">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-250">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ebb1-251">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-251">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="0ebb1-252">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-252">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="0ebb1-253">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-253">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ebb1-254">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-254">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p> | <span data-ttu-id="0ebb1-255">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-255">Default: `1`</span></span><br><span data-ttu-id="0ebb1-256">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-256">Min: `1`</span></span><br><span data-ttu-id="0ebb1-257">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-257">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="0ebb1-258">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-258">Required string attribute.</span></span></p><p><span data-ttu-id="0ebb1-259">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-259">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="0ebb1-260">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-260">Relative paths are supported.</span></span> <span data-ttu-id="0ebb1-261">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-261">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="0ebb1-262">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-262">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ebb1-263">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-263">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="0ebb1-264">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-264">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="0ebb1-265">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-265">Default: `10`</span></span><br><span data-ttu-id="0ebb1-266">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-266">Min: `0`</span></span><br><span data-ttu-id="0ebb1-267">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-267">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="0ebb1-268">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-268">Optional timespan attribute.</span></span></p><p><span data-ttu-id="0ebb1-269">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-269">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="0ebb1-270">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-270">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="0ebb1-271">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-271">Default: `00:02:00`</span></span><br><span data-ttu-id="0ebb1-272">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-272">Min: `00:00:00`</span></span><br><span data-ttu-id="0ebb1-273">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-273">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="0ebb1-274">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-274">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ebb1-275">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-275">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="0ebb1-276">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-276">Default: `10`</span></span><br><span data-ttu-id="0ebb1-277">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-277">Min: `0`</span></span><br><span data-ttu-id="0ebb1-278">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-278">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="0ebb1-279">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-279">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ebb1-280">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-280">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="0ebb1-281">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-281">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="0ebb1-282">O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-282">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="0ebb1-283">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-283">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="0ebb1-284">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-284">Default: `120`</span></span><br><span data-ttu-id="0ebb1-285">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-285">Min: `0`</span></span><br><span data-ttu-id="0ebb1-286">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-286">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="0ebb1-287">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-287">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ebb1-288">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-288">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="0ebb1-289">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-289">Optional string attribute.</span></span></p><p><span data-ttu-id="0ebb1-290">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-290">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="0ebb1-291">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-291">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="0ebb1-292">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-292">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="0ebb1-293">As pastas fornecidas no caminho devem existir para que o módulo crie o arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-293">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="0ebb1-294">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo (*.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-294">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="0ebb1-295">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-295">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

| <span data-ttu-id="0ebb1-296">Atributo</span><span class="sxs-lookup"><span data-stu-id="0ebb1-296">Attribute</span></span> | <span data-ttu-id="0ebb1-297">Descrição</span><span class="sxs-lookup"><span data-stu-id="0ebb1-297">Description</span></span> | <span data-ttu-id="0ebb1-298">Padrão</span><span class="sxs-lookup"><span data-stu-id="0ebb1-298">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="0ebb1-299">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-299">Optional string attribute.</span></span></p><p><span data-ttu-id="0ebb1-300">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-300">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="0ebb1-301">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-301">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ebb1-302">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-302">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="0ebb1-303">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-303">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ebb1-304">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-304">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="0ebb1-305">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-305">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="0ebb1-306">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-306">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ebb1-307">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-307">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p> | <span data-ttu-id="0ebb1-308">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-308">Default: `1`</span></span><br><span data-ttu-id="0ebb1-309">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-309">Min: `1`</span></span><br><span data-ttu-id="0ebb1-310">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-310">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="0ebb1-311">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-311">Required string attribute.</span></span></p><p><span data-ttu-id="0ebb1-312">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-312">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="0ebb1-313">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-313">Relative paths are supported.</span></span> <span data-ttu-id="0ebb1-314">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-314">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="0ebb1-315">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-315">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ebb1-316">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-316">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="0ebb1-317">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-317">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="0ebb1-318">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-318">Default: `10`</span></span><br><span data-ttu-id="0ebb1-319">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-319">Min: `0`</span></span><br><span data-ttu-id="0ebb1-320">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-320">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="0ebb1-321">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-321">Optional timespan attribute.</span></span></p><p><span data-ttu-id="0ebb1-322">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-322">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="0ebb1-323">Em versões do Módulo ASP.NET Core que acompanham a versão do ASP.NET Core 2.0 ou anterior, o `requestTimeout` deve ser especificado somente em minutos inteiros, caso contrário, ele assume o valor padrão de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-323">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.0 or earlier, the `requestTimeout` must be specified in whole minutes only, otherwise it defaults to 2 minutes.</span></span></p> | <span data-ttu-id="0ebb1-324">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-324">Default: `00:02:00`</span></span><br><span data-ttu-id="0ebb1-325">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-325">Min: `00:00:00`</span></span><br><span data-ttu-id="0ebb1-326">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-326">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="0ebb1-327">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-327">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ebb1-328">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-328">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="0ebb1-329">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-329">Default: `10`</span></span><br><span data-ttu-id="0ebb1-330">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-330">Min: `0`</span></span><br><span data-ttu-id="0ebb1-331">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-331">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="0ebb1-332">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-332">Optional integer attribute.</span></span></p><p><span data-ttu-id="0ebb1-333">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-333">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="0ebb1-334">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-334">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="0ebb1-335">O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-335">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p> | <span data-ttu-id="0ebb1-336">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-336">Default: `120`</span></span><br><span data-ttu-id="0ebb1-337">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-337">Min: `0`</span></span><br><span data-ttu-id="0ebb1-338">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="0ebb1-338">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="0ebb1-339">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-339">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0ebb1-340">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-340">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="0ebb1-341">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-341">Optional string attribute.</span></span></p><p><span data-ttu-id="0ebb1-342">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-342">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="0ebb1-343">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-343">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="0ebb1-344">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-344">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="0ebb1-345">As pastas fornecidas no caminho devem existir para que o módulo crie o arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-345">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="0ebb1-346">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo (*.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-346">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="0ebb1-347">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-347">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a><span data-ttu-id="0ebb1-348">Definindo variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="0ebb1-348">Setting environment variables</span></span>

<span data-ttu-id="0ebb1-349">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-349">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="0ebb1-350">Especificar uma variável de ambiente com o elemento filho `environmentVariable` de um elemento de coleção `environmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-350">Specify an environment variable with the `environmentVariable` child element of an `environmentVariables` collection element.</span></span> <span data-ttu-id="0ebb1-351">Variáveis de ambiente definidas nesta seção têm precedência sobre variáveis de ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-351">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="0ebb1-352">O exemplo a seguir define duas variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-352">The following example sets two environment variables.</span></span> <span data-ttu-id="0ebb1-353">`ASPNETCORE_ENVIRONMENT` configura o ambiente do aplicativo para `Development`.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-353">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="0ebb1-354">Um desenvolvedor pode definir esse valor temporariamente no arquivo *web.config* para forçar o carregamento da [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) ao depurar uma exceção de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-354">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="0ebb1-355">`CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-355">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

> [!WARNING]
> <span data-ttu-id="0ebb1-356">Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-356">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="appofflinehtm"></a><span data-ttu-id="0ebb1-357">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="0ebb1-357">app_offline.htm</span></span>

<span data-ttu-id="0ebb1-358">Se um arquivo com o nome *app_offline.htm* é detectado no diretório raiz de um aplicativo, o Módulo do ASP.NET Core tenta desligar normalmente o aplicativo e parar o processamento de solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-358">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="0ebb1-359">Se o aplicativo ainda está em execução após o número de segundos definido em `shutdownTimeLimit`, o Módulo do ASP.NET Core encerra o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-359">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="0ebb1-360">Enquanto o arquivo *app_offline.htm* estiver presente, o Módulo do ASP.NET Core responderá às solicitações enviando o conteúdo do arquivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-360">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="0ebb1-361">Quando o arquivo *app_offline.htm* é removido, a próxima solicitação inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-361">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0ebb1-362">Ao usar o modelo de hospedagem de fora do processo, talvez o aplicativo não desligue imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-362">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="0ebb1-363">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-363">For example, a websocket connection may delay app shut down.</span></span>

::: moniker-end

## <a name="start-up-error-page"></a><span data-ttu-id="0ebb1-364">Página de erro de inicialização</span><span class="sxs-lookup"><span data-stu-id="0ebb1-364">Start-up error page</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0ebb1-365">A hospedagem em processo e fora do processo produzem páginas de erro personalizadas quando falham ao iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-365">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="0ebb1-366">Se o Módulo do ASP.NET Core falhar ao encontrar o manipulador de solicitação em processo ou fora do processo, uma página de código de status *500.0 – falha ao carregar manipulador no processo/fora do processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-366">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="0ebb1-367">No caso da hospedagem em processo, se o módulo do ASP.NET Core falhar ao iniciar o aplicativo, uma página de código de status *500.30 – falha ao iniciar* será exibida.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-367">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="0ebb1-368">Para hospedagem fora do processo, se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-368">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="0ebb1-369">Para suprimir essa página e reverter para a página de código de status 5xx padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-369">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="0ebb1-370">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-370">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="0ebb1-371">Se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-371">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="0ebb1-372">Para omitir esta página e reverter para a página de código de status 502 padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-372">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="0ebb1-373">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-373">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Página de código de status 502.5 – Falha do Processo](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a><span data-ttu-id="0ebb1-375">Criação de log e redirecionamento</span><span class="sxs-lookup"><span data-stu-id="0ebb1-375">Log creation and redirection</span></span>

<span data-ttu-id="0ebb1-376">O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-376">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="0ebb1-377">As pastas no caminho `stdoutLogFile` devem existir para que o módulo crie o arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-377">Any folders in the `stdoutLogFile` path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="0ebb1-378">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-378">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="0ebb1-379">Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-379">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="0ebb1-380">É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-380">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="0ebb1-381">Usar o log de stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-381">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="0ebb1-382">Não use o log de stdout para fins gerais de registro em log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-382">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="0ebb1-383">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-383">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="0ebb1-384">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="0ebb1-384">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="0ebb1-385">Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-385">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="0ebb1-386">O nome do arquivo de log é composto por meio do acréscimo do carimbo de data/hora, da ID do processo e da extensão de arquivo (*.log*) para o último segmento do caminho `stdoutLogFile` (normalmente *stdout*), delimitados por sublinhados.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-386">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="0ebb1-387">Se o caminho `stdoutLogFile` termina com *stdout*, um log para um aplicativo com um PID de 1934, criado em 5/2/2018 às 19:42:32, tem o nome de arquivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-387">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0ebb1-388">Se `stdoutLogEnabled` for falso, os erros que ocorrerem na inicialização do aplicativo serão capturados e emitidos no log de eventos até 30 KB.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-388">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="0ebb1-389">Após a inicialização, todos os logs adicionais são descartados.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-389">After startup, all additional logs are discarded.</span></span>

::: moniker-end

<span data-ttu-id="0ebb1-390">O elemento `aspNetCore` de exemplo a seguir configura o registro em log de stdout para um aplicativo hospedado no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-390">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="0ebb1-391">Um caminho local ou um caminho de compartilhamento de rede é aceitável para o registro em log local.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-391">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="0ebb1-392">Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-392">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="0ebb1-393">Logs de diagnóstico avançados</span><span class="sxs-lookup"><span data-stu-id="0ebb1-393">Enhanced diagnostic logs</span></span>

<span data-ttu-id="0ebb1-394">O Módulo do ASP.NET Core pode ser configurado para fornecer logs de diagnóstico avançados.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-394">The ASP.NET Core Module provides is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="0ebb1-395">Adicione o elemento `<handlerSettings>` ao elemento `<aspNetCore>` no *web.config*. A definição de `debugLevel` como `TRACE` expõe uma fidelidade maior de informações de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="0ebb1-395">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value="aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="0ebb1-396">Os valores do nível de depuração (`debugLevel`) podem incluir o nível e a localização.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-396">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="0ebb1-397">Níveis (na ordem do menos para o mais detalhado):</span><span class="sxs-lookup"><span data-stu-id="0ebb1-397">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="0ebb1-398">ERROR</span><span class="sxs-lookup"><span data-stu-id="0ebb1-398">ERROR</span></span>
* <span data-ttu-id="0ebb1-399">WARNING</span><span class="sxs-lookup"><span data-stu-id="0ebb1-399">WARNING</span></span>
* <span data-ttu-id="0ebb1-400">INFO</span><span class="sxs-lookup"><span data-stu-id="0ebb1-400">INFO</span></span>
* <span data-ttu-id="0ebb1-401">TRACE</span><span class="sxs-lookup"><span data-stu-id="0ebb1-401">TRACE</span></span>

<span data-ttu-id="0ebb1-402">Locais (vários locais são permitidos):</span><span class="sxs-lookup"><span data-stu-id="0ebb1-402">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="0ebb1-403">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="0ebb1-403">CONSOLE</span></span>
* <span data-ttu-id="0ebb1-404">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="0ebb1-404">EVENTLOG</span></span>
* <span data-ttu-id="0ebb1-405">FILE</span><span class="sxs-lookup"><span data-stu-id="0ebb1-405">FILE</span></span>

<span data-ttu-id="0ebb1-406">As configurações do manipulador também podem ser fornecidas por meio de variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="0ebb1-406">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="0ebb1-407">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; caminho para o arquivo de log de depuração.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-407">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="0ebb1-408">(Padrão: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="0ebb1-408">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="0ebb1-409">`ASPNETCORE_MODULE_DEBUG` &ndash; configuração do nível de depuração.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-409">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="0ebb1-410">**Não** deixe o log de depuração habilitado na implantação por mais tempo que o necessário para solucionar um problema.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-410">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="0ebb1-411">O tamanho do log não é limitado.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-411">The size of the log isn't limited.</span></span> <span data-ttu-id="0ebb1-412">Deixar o log de depuração habilitado pode esgotar o espaço em disco disponível e causar falha no servidor ou no serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-412">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

::: moniker-end

<span data-ttu-id="0ebb1-413">Veja [Configuração com web.config](#configuration-with-webconfig) para obter um exemplo do elemento `aspNetCore` no arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-413">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="0ebb1-414">A configuração de proxy usa o protocolo HTTP e um token de emparelhamento</span><span class="sxs-lookup"><span data-stu-id="0ebb1-414">Proxy configuration uses HTTP protocol and a pairing token</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0ebb1-415">*Só se aplica à hospedagem de fora do processo.*</span><span class="sxs-lookup"><span data-stu-id="0ebb1-415">*Only applies to out-of-process hosting.*</span></span>

::: moniker-end

<span data-ttu-id="0ebb1-416">O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-416">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="0ebb1-417">O uso de HTTP é uma otimização de desempenho na qual o tráfego entre o módulo e o Kestrel ocorre em um endereço de loopback fora do adaptador de rede.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-417">Using HTTP is a performance optimization, where the traffic between the module and Kestrel takes place on a loopback address off of the network interface.</span></span> <span data-ttu-id="0ebb1-418">Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-418">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="0ebb1-419">Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-419">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="0ebb1-420">O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-420">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="0ebb1-421">O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-421">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="0ebb1-422">O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-422">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="0ebb1-423">Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-423">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="0ebb1-424">A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-424">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="0ebb1-425">Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-425">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="0ebb1-426">Módulo do ASP.NET Core com uma configuração do IIS compartilhada</span><span class="sxs-lookup"><span data-stu-id="0ebb1-426">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="0ebb1-427">O instalador do módulo do ASP.NET Core é executado com os privilégios da conta de **SISTEMA**.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-427">The ASP.NET Core Module installer runs with the privileges of the **SYSTEM** account.</span></span> <span data-ttu-id="0ebb1-428">Já que a conta de sistema local não tem permissão para modificar o caminho do compartilhamento usado pela configuração compartilhada de IIS, o instalador experimenta um erro de acesso negado ao tentar definir as configurações de módulo em *applicationHost.config* no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-428">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer hits an access denied error when attempting to configure the module settings in *applicationHost.config* on the share.</span></span> <span data-ttu-id="0ebb1-429">Ao usar uma configuração compartilhada de IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="0ebb1-429">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="0ebb1-430">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-430">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="0ebb1-431">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-431">Run the installer.</span></span>
1. <span data-ttu-id="0ebb1-432">Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-432">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="0ebb1-433">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-433">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="0ebb1-434">Versão do módulo e logs do instalador do pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="0ebb1-434">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="0ebb1-435">Para determinar a versão do Módulo do ASP.NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="0ebb1-435">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="0ebb1-436">No sistema de hospedagem, navegue até *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-436">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="0ebb1-437">Localize o arquivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-437">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="0ebb1-438">Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-438">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="0ebb1-439">Selecione a guia **Detalhes**. A **Versão do arquivo** e a **Versão do produto** representam a versão instalada do módulo.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-439">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="0ebb1-440">Os logs de instalador do pacote de hospedagem para o módulo são encontrados em *C:\\Usuários\\%UserName%\\AppData\\Local\\Temp*. O arquivo é nomeado *dd_DotNetCoreWinSvrHosting__\<carimbo de data/hora>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-440">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="0ebb1-441">Locais dos arquivos de módulo, de esquema e de configuração</span><span class="sxs-lookup"><span data-stu-id="0ebb1-441">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="0ebb1-442">Módulo</span><span class="sxs-lookup"><span data-stu-id="0ebb1-442">Module</span></span>

<span data-ttu-id="0ebb1-443">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="0ebb1-443">**IIS (x86/amd64):**</span></span>

   * <span data-ttu-id="0ebb1-444">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="0ebb1-444">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

   * <span data-ttu-id="0ebb1-445">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="0ebb1-445">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="0ebb1-446">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="0ebb1-446">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="0ebb1-447">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="0ebb1-447">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

<span data-ttu-id="0ebb1-448">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="0ebb1-448">**IIS Express (x86/amd64):**</span></span>

   * <span data-ttu-id="0ebb1-449">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="0ebb1-449">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

   * <span data-ttu-id="0ebb1-450">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="0ebb1-450">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="0ebb1-451">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="0ebb1-451">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="0ebb1-452">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="0ebb1-452">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

### <a name="schema"></a><span data-ttu-id="0ebb1-453">Esquema</span><span class="sxs-lookup"><span data-stu-id="0ebb1-453">Schema</span></span>

<span data-ttu-id="0ebb1-454">**IIS**</span><span class="sxs-lookup"><span data-stu-id="0ebb1-454">**IIS**</span></span>

   * <span data-ttu-id="0ebb1-455">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="0ebb1-455">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="0ebb1-456">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="0ebb1-456">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end
<span data-ttu-id="0ebb1-457">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="0ebb1-457">**IIS Express**</span></span>

   * <span data-ttu-id="0ebb1-458">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="0ebb1-458">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="0ebb1-459">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="0ebb1-459">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="0ebb1-460">Configuração</span><span class="sxs-lookup"><span data-stu-id="0ebb1-460">Configuration</span></span>

<span data-ttu-id="0ebb1-461">**IIS**</span><span class="sxs-lookup"><span data-stu-id="0ebb1-461">**IIS**</span></span>

   * <span data-ttu-id="0ebb1-462">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="0ebb1-462">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="0ebb1-463">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="0ebb1-463">**IIS Express**</span></span>

   * <span data-ttu-id="0ebb1-464">%ProgramFiles%\IIS Express\config\templates\PersonalWebServer\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="0ebb1-464">%ProgramFiles%\IIS Express\config\templates\PersonalWebServer\applicationHost.config</span></span>

<span data-ttu-id="0ebb1-465">Os arquivos podem ser encontrados pesquisando por *aspnetcore* no arquivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="0ebb1-465">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0ebb1-466">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0ebb1-466">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="0ebb1-467">Repositório do GitHub do Módulo do ASP.NET Core (origem de referência)</span><span class="sxs-lookup"><span data-stu-id="0ebb1-467">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>