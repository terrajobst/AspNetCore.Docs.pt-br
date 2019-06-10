---
title: Módulo do ASP.NET Core
author: guardrex
description: Saiba como configurar o módulo do ASP.NET Core para hospedar aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/05/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: f287a9bad623c5ff5c41868c7c4408b572b39000
ms.sourcegitcommit: c716ea9155a6b404c1f3d3d34e2388454cd276d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66716360"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="c1556-103">Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1556-103">ASP.NET Core Module</span></span>

<span data-ttu-id="c1556-104">Por [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c1556-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c1556-105">O módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para:</span><span class="sxs-lookup"><span data-stu-id="c1556-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="c1556-106">Hospedar um aplicativo ASP.NET Core dentro do processo de trabalho do IIS (`w3wp.exe`), chamado o [modelo de hospedagem no processo](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="c1556-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="c1556-107">Encaminhar solicitações da Web a um aplicativo ASP.NET Core de back-end que executa o [servidor Kestrel](xref:fundamentals/servers/kestrel), chamado o [modelo de hospedagem de fora do processo](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="c1556-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="c1556-108">Versões do Windows compatíveis:</span><span class="sxs-lookup"><span data-stu-id="c1556-108">Supported Windows versions:</span></span>

* <span data-ttu-id="c1556-109">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="c1556-109">Windows 7 or later</span></span>
* <span data-ttu-id="c1556-110">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="c1556-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="c1556-111">Ao fazer uma hospedagem em processo, o módulo usa uma implementação de servidor em processo do IIS, chamado Servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="c1556-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="c1556-112">Ao hospedar de fora do processo, o módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c1556-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="c1556-113">O módulo é incompatível com [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="c1556-113">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="c1556-114">Modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="c1556-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="c1556-115">Modelo de hospedagem em processo</span><span class="sxs-lookup"><span data-stu-id="c1556-115">In-process hosting model</span></span>

<span data-ttu-id="c1556-116">Para configurar um aplicativo para hospedagem em processo, adicione a propriedade `<AspNetCoreHostingModel>` ao arquivo de projeto do aplicativo com um valor de `InProcess` (a hospedagem de fora do processo é definida com `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="c1556-116">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="c1556-117">Não há suporte para o modelo de hospedagem em processo para aplicativos ASP.NET Core direcionados ao .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="c1556-117">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="c1556-118">Se a propriedade `<AspNetCoreHostingModel>` não estiver presente no arquivo, o valor padrão será `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="c1556-118">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="c1556-119">As seguintes características se aplicam ao hospedar em processo:</span><span class="sxs-lookup"><span data-stu-id="c1556-119">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="c1556-120">O Servidor HTTP do IIS (`IISHttpServer`) é usado, em vez do servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="c1556-120">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="c1556-121">Em processo, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:</span><span class="sxs-lookup"><span data-stu-id="c1556-121">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="c1556-122">Registrar o `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="c1556-122">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="c1556-123">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1556-123">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="c1556-124">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="c1556-124">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="c1556-125">O [requestTimeout atributo](#attributes-of-the-aspnetcore-element) não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="c1556-125">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="c1556-126">Não há suporte para o compartilhamento do pool de aplicativos entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="c1556-126">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="c1556-127">Use um pool de aplicativos por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1556-127">Use one app pool per app.</span></span>

* <span data-ttu-id="c1556-128">Ao usar a [Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) ou inserir manualmente um [arquivo app_offline.htm na implantação](xref:host-and-deploy/iis/index#locked-deployment-files), o aplicativo talvez não seja desligado imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="c1556-128">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="c1556-129">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1556-129">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="c1556-130">A arquitetura (número de bit) do aplicativo e o tempo de execução instalado (x64 ou x86) devem corresponder à arquitetura do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="c1556-130">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="c1556-131">Se a configuração manual do host do aplicativo com `WebHostBuilder` (não usando [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) e o aplicativo não forem executados diretamente no servidor Kestrel (auto-hospedado), chame `UseKestrel` antes de chamar `UseIISIntegration`.</span><span class="sxs-lookup"><span data-stu-id="c1556-131">If setting up the app's host manually with `WebHostBuilder` (not using [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) and the app is ever run directly on the Kestrel server (self-hosted), call `UseKestrel` before calling `UseIISIntegration`.</span></span> <span data-ttu-id="c1556-132">Se a ordem for invertida, a inicialização do host falhará.</span><span class="sxs-lookup"><span data-stu-id="c1556-132">If the order is reversed, the host fails to start.</span></span>

* <span data-ttu-id="c1556-133">As desconexões do cliente são detectadas.</span><span class="sxs-lookup"><span data-stu-id="c1556-133">Client disconnects are detected.</span></span> <span data-ttu-id="c1556-134">O token de cancelamento [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) é cancelado quando o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="c1556-134">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="c1556-135">No ASP.NET Core 2.2.1 ou anterior, <xref:System.IO.Directory.GetCurrentDirectory*> retorna o diretório de trabalho do processo iniciado pelo IIS em vez de o diretório do aplicativo (por exemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="c1556-135">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="c1556-136">Para obter o código de exemplo que define o diretório atual do aplicativo, confira a [classe CurrentDirectoryHelpers](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="c1556-136">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="c1556-137">Chame o método `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="c1556-137">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="c1556-138">As chamadas seguintes a <xref:System.IO.Directory.GetCurrentDirectory*> fornecem o diretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1556-138">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="c1556-139">Ao hospedar em processo, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="c1556-139">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="c1556-140">Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="c1556-140">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="c1556-141">Quando a transformação de declarações com uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, chame <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para adicionar serviços de autenticação:</span><span class="sxs-lookup"><span data-stu-id="c1556-141">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="c1556-142">Modelo de hospedagem de fora do processo</span><span class="sxs-lookup"><span data-stu-id="c1556-142">Out-of-process hosting model</span></span>

<span data-ttu-id="c1556-143">Para configurar um aplicativo para hospedagem fora do processo, use qualquer uma das abordagens a seguir no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="c1556-143">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="c1556-144">não especifique a propriedade `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="c1556-144">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="c1556-145">Se a propriedade `<AspNetCoreHostingModel>` não estiver presente no arquivo, o valor padrão será `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="c1556-145">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="c1556-146">Defina o valor da propriedade `<AspNetCoreHostingModel>` como `OutOfProcess` (a hospedagem no processo é definida com `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="c1556-146">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="c1556-147">O servidor [Kestrel](xref:fundamentals/servers/kestrel) é usado, em vez do servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="c1556-147">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="c1556-148">Fora do processo, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:</span><span class="sxs-lookup"><span data-stu-id="c1556-148">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="c1556-149">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1556-149">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="c1556-150">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="c1556-150">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="c1556-151">Alterações no modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="c1556-151">Hosting model changes</span></span>

<span data-ttu-id="c1556-152">Se a configuração `hostingModel` for alterada no arquivo *web.config* (explicado na seção [Configuração a Web.config](#configuration-with-webconfig)), o módulo reciclará o processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="c1556-152">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="c1556-153">Para o IIS Express, o módulo não recicla o processo de trabalho, mas em vez disso, dispara um desligamento normal do processo atual do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="c1556-153">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="c1556-154">A próxima solicitação para o aplicativo gera um novo processo do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="c1556-154">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="c1556-155">Nome do processo</span><span class="sxs-lookup"><span data-stu-id="c1556-155">Process name</span></span>

<span data-ttu-id="c1556-156">`Process.GetCurrentProcess().ProcessName` relata `w3wp`/`iisexpress` (em processo) ou `dotnet` (fora do processo).</span><span class="sxs-lookup"><span data-stu-id="c1556-156">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c1556-157">O Módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para encaminhar solicitações da Web para aplicativos ASP.NET Core de back-end.</span><span class="sxs-lookup"><span data-stu-id="c1556-157">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="c1556-158">Versões do Windows compatíveis:</span><span class="sxs-lookup"><span data-stu-id="c1556-158">Supported Windows versions:</span></span>

* <span data-ttu-id="c1556-159">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="c1556-159">Windows 7 or later</span></span>
* <span data-ttu-id="c1556-160">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="c1556-160">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="c1556-161">O módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c1556-161">The module only works with Kestrel.</span></span> <span data-ttu-id="c1556-162">O módulo é incompatível com [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="c1556-162">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="c1556-163">Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo também realiza o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="c1556-163">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="c1556-164">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo quando ele falha.</span><span class="sxs-lookup"><span data-stu-id="c1556-164">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="c1556-165">Isso é basicamente o mesmo comportamento que o dos aplicativos ASP.NET 4.x que são executados dentro do processo do IIS e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="c1556-165">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="c1556-166">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c1556-166">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Módulo do ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="c1556-168">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="c1556-168">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="c1556-169">O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="c1556-169">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="c1556-170">O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.</span><span class="sxs-lookup"><span data-stu-id="c1556-170">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="c1556-171">O módulo especifica a porta por meio de uma variável de ambiente na inicialização e o [middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura o servidor para escutar em `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="c1556-171">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="c1556-172">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="c1556-172">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="c1556-173">O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c1556-173">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="c1556-174">Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c1556-174">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="c1556-175">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1556-175">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="c1556-176">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c1556-176">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="c1556-177">A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="c1556-177">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

<span data-ttu-id="c1556-178">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="c1556-178">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="c1556-179">Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="c1556-179">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="c1556-180">O Módulo do ASP.NET Core também pode:</span><span class="sxs-lookup"><span data-stu-id="c1556-180">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="c1556-181">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="c1556-181">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="c1556-182">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="c1556-182">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="c1556-183">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="c1556-183">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="c1556-184">Como instalar e usar o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1556-184">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="c1556-185">Para obter instruções de como instalar o Módulo do ASP.NET Core, confira [Instalar o Pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="c1556-185">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="c1556-186">Configuração com web.config</span><span class="sxs-lookup"><span data-stu-id="c1556-186">Configuration with web.config</span></span>

<span data-ttu-id="c1556-187">O Módulo do ASP.NET Core está configurado com a seção `aspNetCore` do nó `system.webServer` no arquivo *web.config* do site.</span><span class="sxs-lookup"><span data-stu-id="c1556-187">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="c1556-188">O seguinte arquivo *web.config* é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o Módulo do ASP.NET Core para manipular solicitações de site:</span><span class="sxs-lookup"><span data-stu-id="c1556-188">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="c1556-189">O seguinte *web.config* é publicado para uma [implantação autossuficiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="c1556-189">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="c1556-190">A propriedade <xref:System.Configuration.SectionInformation.InheritInChildApplications*> é definida como `false` para indicar que as configurações especificadas no elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) não são herdadas por aplicativos que residem em um subdiretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1556-190">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

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

<span data-ttu-id="c1556-191">Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="c1556-191">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="c1556-192">O caminho salva logs de stdout para a pasta *LogFiles*, que é um local criado automaticamente pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="c1556-192">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="c1556-193">Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="c1556-193">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="c1556-194">Atributos do elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="c1556-194">Attributes of the aspNetCore element</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="c1556-195">Atributo</span><span class="sxs-lookup"><span data-stu-id="c1556-195">Attribute</span></span> | <span data-ttu-id="c1556-196">Descrição</span><span class="sxs-lookup"><span data-stu-id="c1556-196">Description</span></span> | <span data-ttu-id="c1556-197">Padrão</span><span class="sxs-lookup"><span data-stu-id="c1556-197">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="c1556-198">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-198">Optional string attribute.</span></span></p><p><span data-ttu-id="c1556-199">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="c1556-199">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="c1556-200">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-200">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="c1556-201">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="c1556-201">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="c1556-202">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-202">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="c1556-203">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="c1556-203">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="c1556-204">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="c1556-204">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="c1556-205">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-205">Optional string attribute.</span></span></p><p><span data-ttu-id="c1556-206">Especifica o modelo de hospedagem como em processo (`InProcess`) ou de fora do processo (`OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="c1556-206">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="c1556-207">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-207">Optional integer attribute.</span></span></p><p><span data-ttu-id="c1556-208">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1556-208">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="c1556-209">&dagger;Para hospedagem em processo, o valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="c1556-209">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="c1556-210">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="c1556-210">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="c1556-211">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="c1556-211">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="c1556-212">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="c1556-212">Default: `1`</span></span><br><span data-ttu-id="c1556-213">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="c1556-213">Min: `1`</span></span><br><span data-ttu-id="c1556-214">Máx.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="c1556-214">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="c1556-215">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="c1556-215">Required string attribute.</span></span></p><p><span data-ttu-id="c1556-216">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="c1556-216">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="c1556-217">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="c1556-217">Relative paths are supported.</span></span> <span data-ttu-id="c1556-218">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="c1556-218">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="c1556-219">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-219">Optional integer attribute.</span></span></p><p><span data-ttu-id="c1556-220">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="c1556-220">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="c1556-221">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="c1556-221">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="c1556-222">Sem suporte com hospedagem padrão.</span><span class="sxs-lookup"><span data-stu-id="c1556-222">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="c1556-223">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="c1556-223">Default: `10`</span></span><br><span data-ttu-id="c1556-224">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="c1556-224">Min: `0`</span></span><br><span data-ttu-id="c1556-225">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="c1556-225">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="c1556-226">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-226">Optional timespan attribute.</span></span></p><p><span data-ttu-id="c1556-227">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="c1556-227">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="c1556-228">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="c1556-228">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="c1556-229">Não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="c1556-229">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="c1556-230">Para a hospedagem em processo, o módulo aguarda o aplicativo processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="c1556-230">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="c1556-231">Os valores válidos para segmentos de minutos e segundos da cadeia de caracteres estão no intervalo 0 a 59.</span><span class="sxs-lookup"><span data-stu-id="c1556-231">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="c1556-232">O uso de **60** no valor para minutos ou segundos resulta em um *500 - Erro Interno do Servidor*.</span><span class="sxs-lookup"><span data-stu-id="c1556-232">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="c1556-233">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="c1556-233">Default: `00:02:00`</span></span><br><span data-ttu-id="c1556-234">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="c1556-234">Min: `00:00:00`</span></span><br><span data-ttu-id="c1556-235">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="c1556-235">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="c1556-236">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-236">Optional integer attribute.</span></span></p><p><span data-ttu-id="c1556-237">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="c1556-237">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="c1556-238">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="c1556-238">Default: `10`</span></span><br><span data-ttu-id="c1556-239">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="c1556-239">Min: `0`</span></span><br><span data-ttu-id="c1556-240">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="c1556-240">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="c1556-241">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-241">Optional integer attribute.</span></span></p><p><span data-ttu-id="c1556-242">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="c1556-242">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="c1556-243">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="c1556-243">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="c1556-244">O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</span><span class="sxs-lookup"><span data-stu-id="c1556-244">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="c1556-245">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="c1556-245">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="c1556-246">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="c1556-246">Default: `120`</span></span><br><span data-ttu-id="c1556-247">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="c1556-247">Min: `0`</span></span><br><span data-ttu-id="c1556-248">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="c1556-248">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="c1556-249">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-249">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="c1556-250">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="c1556-250">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="c1556-251">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-251">Optional string attribute.</span></span></p><p><span data-ttu-id="c1556-252">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="c1556-252">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="c1556-253">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="c1556-253">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="c1556-254">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="c1556-254">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="c1556-255">Todas as pastas fornecidas no caminho são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="c1556-255">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="c1556-256">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo ( *.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="c1556-256">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="c1556-257">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="c1556-257">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| <span data-ttu-id="c1556-258">Atributo</span><span class="sxs-lookup"><span data-stu-id="c1556-258">Attribute</span></span> | <span data-ttu-id="c1556-259">Descrição</span><span class="sxs-lookup"><span data-stu-id="c1556-259">Description</span></span> | <span data-ttu-id="c1556-260">Padrão</span><span class="sxs-lookup"><span data-stu-id="c1556-260">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="c1556-261">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-261">Optional string attribute.</span></span></p><p><span data-ttu-id="c1556-262">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="c1556-262">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="c1556-263">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-263">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="c1556-264">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="c1556-264">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="c1556-265">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-265">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="c1556-266">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="c1556-266">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="c1556-267">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="c1556-267">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="c1556-268">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-268">Optional integer attribute.</span></span></p><p><span data-ttu-id="c1556-269">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1556-269">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="c1556-270">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="c1556-270">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="c1556-271">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="c1556-271">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="c1556-272">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="c1556-272">Default: `1`</span></span><br><span data-ttu-id="c1556-273">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="c1556-273">Min: `1`</span></span><br><span data-ttu-id="c1556-274">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="c1556-274">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="c1556-275">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="c1556-275">Required string attribute.</span></span></p><p><span data-ttu-id="c1556-276">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="c1556-276">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="c1556-277">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="c1556-277">Relative paths are supported.</span></span> <span data-ttu-id="c1556-278">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="c1556-278">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="c1556-279">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-279">Optional integer attribute.</span></span></p><p><span data-ttu-id="c1556-280">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="c1556-280">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="c1556-281">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="c1556-281">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="c1556-282">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="c1556-282">Default: `10`</span></span><br><span data-ttu-id="c1556-283">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="c1556-283">Min: `0`</span></span><br><span data-ttu-id="c1556-284">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="c1556-284">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="c1556-285">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-285">Optional timespan attribute.</span></span></p><p><span data-ttu-id="c1556-286">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="c1556-286">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="c1556-287">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="c1556-287">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="c1556-288">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="c1556-288">Default: `00:02:00`</span></span><br><span data-ttu-id="c1556-289">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="c1556-289">Min: `00:00:00`</span></span><br><span data-ttu-id="c1556-290">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="c1556-290">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="c1556-291">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-291">Optional integer attribute.</span></span></p><p><span data-ttu-id="c1556-292">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="c1556-292">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="c1556-293">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="c1556-293">Default: `10`</span></span><br><span data-ttu-id="c1556-294">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="c1556-294">Min: `0`</span></span><br><span data-ttu-id="c1556-295">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="c1556-295">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="c1556-296">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-296">Optional integer attribute.</span></span></p><p><span data-ttu-id="c1556-297">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="c1556-297">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="c1556-298">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="c1556-298">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="c1556-299">O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</span><span class="sxs-lookup"><span data-stu-id="c1556-299">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="c1556-300">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="c1556-300">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="c1556-301">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="c1556-301">Default: `120`</span></span><br><span data-ttu-id="c1556-302">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="c1556-302">Min: `0`</span></span><br><span data-ttu-id="c1556-303">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="c1556-303">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="c1556-304">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-304">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="c1556-305">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="c1556-305">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="c1556-306">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="c1556-306">Optional string attribute.</span></span></p><p><span data-ttu-id="c1556-307">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="c1556-307">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="c1556-308">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="c1556-308">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="c1556-309">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="c1556-309">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="c1556-310">As pastas fornecidas no caminho devem existir para que o módulo crie o arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="c1556-310">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="c1556-311">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo ( *.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="c1556-311">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="c1556-312">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="c1556-312">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a><span data-ttu-id="c1556-313">Definindo variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="c1556-313">Setting environment variables</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c1556-314">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="c1556-314">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="c1556-315">Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="c1556-315">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="c1556-316">Variáveis de ambiente definidas nesta seção têm precedência sobre variáveis de ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="c1556-316">Environment variables set in this section take precedence over system environment variables.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c1556-317">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="c1556-317">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="c1556-318">Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="c1556-318">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="c1556-319">As variáveis de ambiente definidas nesta seção são conflitantes com as variáveis de ambiente do sistema definidas com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="c1556-319">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="c1556-320">Quando a variável de ambiente é definida no arquivo *web.config* e no nível do sistema do Windows, o valor do arquivo *web.config* fica anexado ao valor da variável de ambiente do sistema (por exemplo, `ASPNETCORE_ENVIRONMENT: Development;Development`), o que impede a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1556-320">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

::: moniker-end

<span data-ttu-id="c1556-321">O exemplo a seguir define duas variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="c1556-321">The following example sets two environment variables.</span></span> <span data-ttu-id="c1556-322">`ASPNETCORE_ENVIRONMENT` configura o ambiente do aplicativo para `Development`.</span><span class="sxs-lookup"><span data-stu-id="c1556-322">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="c1556-323">Um desenvolvedor pode definir esse valor temporariamente no arquivo *web.config* para forçar o carregamento da [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) ao depurar uma exceção de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1556-323">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="c1556-324">`CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1556-324">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="c1556-325">Em vez de configurar o ambiente diretamente no *web.config*, você pode incluir a propriedade `<EnvironmentName>` no perfil de publicação ( *.pubxml*) ou no perfil de projeto.</span><span class="sxs-lookup"><span data-stu-id="c1556-325">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="c1556-326">Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="c1556-326">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

::: moniker-end

> [!WARNING]
> <span data-ttu-id="c1556-327">Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.</span><span class="sxs-lookup"><span data-stu-id="c1556-327">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="appofflinehtm"></a><span data-ttu-id="c1556-328">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="c1556-328">app_offline.htm</span></span>

<span data-ttu-id="c1556-329">Se um arquivo com o nome *app_offline.htm* é detectado no diretório raiz de um aplicativo, o Módulo do ASP.NET Core tenta desligar normalmente o aplicativo e parar o processamento de solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="c1556-329">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="c1556-330">Se o aplicativo ainda está em execução após o número de segundos definido em `shutdownTimeLimit`, o Módulo do ASP.NET Core encerra o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="c1556-330">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="c1556-331">Enquanto o arquivo *app_offline.htm* estiver presente, o Módulo do ASP.NET Core responderá às solicitações enviando o conteúdo do arquivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="c1556-331">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="c1556-332">Quando o arquivo *app_offline.htm* é removido, a próxima solicitação inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1556-332">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c1556-333">Ao usar o modelo de hospedagem de fora do processo, talvez o aplicativo não desligue imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="c1556-333">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="c1556-334">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1556-334">For example, a websocket connection may delay app shut down.</span></span>

::: moniker-end

## <a name="start-up-error-page"></a><span data-ttu-id="c1556-335">Página de erro de inicialização</span><span class="sxs-lookup"><span data-stu-id="c1556-335">Start-up error page</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c1556-336">A hospedagem em processo e fora do processo produzem páginas de erro personalizadas quando falham ao iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1556-336">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="c1556-337">Se o Módulo do ASP.NET Core falhar ao encontrar o manipulador de solicitação em processo ou fora do processo, uma página de código de status *500.0 – falha ao carregar manipulador no processo/fora do processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="c1556-337">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="c1556-338">No caso da hospedagem em processo, se o módulo do ASP.NET Core falhar ao iniciar o aplicativo, uma página de código de status *500.30 – falha ao iniciar* será exibida.</span><span class="sxs-lookup"><span data-stu-id="c1556-338">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="c1556-339">Para hospedagem fora do processo, se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="c1556-339">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="c1556-340">Para suprimir essa página e reverter para a página de código de status 5xx padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="c1556-340">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="c1556-341">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="c1556-341">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c1556-342">Se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="c1556-342">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="c1556-343">Para omitir esta página e reverter para a página de código de status 502 padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="c1556-343">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="c1556-344">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="c1556-344">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Página de código de status 502.5 – Falha do Processo](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a><span data-ttu-id="c1556-346">Criação de log e redirecionamento</span><span class="sxs-lookup"><span data-stu-id="c1556-346">Log creation and redirection</span></span>

<span data-ttu-id="c1556-347">O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos.</span><span class="sxs-lookup"><span data-stu-id="c1556-347">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="c1556-348">Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="c1556-348">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="c1556-349">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="c1556-349">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="c1556-350">Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo.</span><span class="sxs-lookup"><span data-stu-id="c1556-350">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="c1556-351">É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.</span><span class="sxs-lookup"><span data-stu-id="c1556-351">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="c1556-352">Usar o log de stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1556-352">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="c1556-353">Não use o log de stdout para fins gerais de registro em log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1556-353">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="c1556-354">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="c1556-354">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c1556-355">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="c1556-355">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="c1556-356">Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="c1556-356">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="c1556-357">O nome do arquivo de log é composto por meio do acréscimo do carimbo de data/hora, da ID do processo e da extensão de arquivo ( *.log*) para o último segmento do caminho `stdoutLogFile` (normalmente *stdout*), delimitados por sublinhados.</span><span class="sxs-lookup"><span data-stu-id="c1556-357">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="c1556-358">Se o caminho `stdoutLogFile` termina com *stdout*, um log para um aplicativo com um PID de 1934, criado em 5/2/2018 às 19:42:32, tem o nome de arquivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="c1556-358">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c1556-359">Se `stdoutLogEnabled` for falso, os erros que ocorrerem na inicialização do aplicativo serão capturados e emitidos no log de eventos até 30 KB.</span><span class="sxs-lookup"><span data-stu-id="c1556-359">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="c1556-360">Após a inicialização, todos os logs adicionais são descartados.</span><span class="sxs-lookup"><span data-stu-id="c1556-360">After startup, all additional logs are discarded.</span></span>

::: moniker-end

<span data-ttu-id="c1556-361">O elemento `aspNetCore` de exemplo a seguir configura o registro em log de stdout para um aplicativo hospedado no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="c1556-361">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="c1556-362">Um caminho local ou um caminho de compartilhamento de rede é aceitável para o registro em log local.</span><span class="sxs-lookup"><span data-stu-id="c1556-362">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="c1556-363">Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="c1556-363">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

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

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="c1556-364">Logs de diagnóstico avançados</span><span class="sxs-lookup"><span data-stu-id="c1556-364">Enhanced diagnostic logs</span></span>

<span data-ttu-id="c1556-365">O Módulo do ASP.NET Core é configurável para fornecer logs de diagnóstico avançados.</span><span class="sxs-lookup"><span data-stu-id="c1556-365">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="c1556-366">Adicione o elemento `<handlerSettings>` ao elemento `<aspNetCore>` no *web.config*. A definição de `debugLevel` como `TRACE` expõe uma fidelidade maior de informações de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="c1556-366">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="c1556-367">Os valores do nível de depuração (`debugLevel`) podem incluir o nível e a localização.</span><span class="sxs-lookup"><span data-stu-id="c1556-367">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="c1556-368">Níveis (na ordem do menos para o mais detalhado):</span><span class="sxs-lookup"><span data-stu-id="c1556-368">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="c1556-369">ERROR</span><span class="sxs-lookup"><span data-stu-id="c1556-369">ERROR</span></span>
* <span data-ttu-id="c1556-370">WARNING</span><span class="sxs-lookup"><span data-stu-id="c1556-370">WARNING</span></span>
* <span data-ttu-id="c1556-371">INFO</span><span class="sxs-lookup"><span data-stu-id="c1556-371">INFO</span></span>
* <span data-ttu-id="c1556-372">TRACE</span><span class="sxs-lookup"><span data-stu-id="c1556-372">TRACE</span></span>

<span data-ttu-id="c1556-373">Locais (vários locais são permitidos):</span><span class="sxs-lookup"><span data-stu-id="c1556-373">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="c1556-374">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="c1556-374">CONSOLE</span></span>
* <span data-ttu-id="c1556-375">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="c1556-375">EVENTLOG</span></span>
* <span data-ttu-id="c1556-376">FILE</span><span class="sxs-lookup"><span data-stu-id="c1556-376">FILE</span></span>

<span data-ttu-id="c1556-377">As configurações do manipulador também podem ser fornecidas por meio de variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="c1556-377">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="c1556-378">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; caminho para o arquivo de log de depuração.</span><span class="sxs-lookup"><span data-stu-id="c1556-378">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="c1556-379">(Padrão: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="c1556-379">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="c1556-380">`ASPNETCORE_MODULE_DEBUG` &ndash; configuração do nível de depuração.</span><span class="sxs-lookup"><span data-stu-id="c1556-380">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="c1556-381">**Não** deixe o log de depuração habilitado na implantação por mais tempo que o necessário para solucionar um problema.</span><span class="sxs-lookup"><span data-stu-id="c1556-381">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="c1556-382">O tamanho do log não é limitado.</span><span class="sxs-lookup"><span data-stu-id="c1556-382">The size of the log isn't limited.</span></span> <span data-ttu-id="c1556-383">Deixar o log de depuração habilitado pode esgotar o espaço em disco disponível e causar falha no servidor ou no serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c1556-383">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

::: moniker-end

<span data-ttu-id="c1556-384">Veja [Configuração com web.config](#configuration-with-webconfig) para obter um exemplo do elemento `aspNetCore` no arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="c1556-384">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="modify-the-stack-size"></a><span data-ttu-id="c1556-385">Modificar o tamanho da pilha</span><span class="sxs-lookup"><span data-stu-id="c1556-385">Modify the stack size</span></span>

<span data-ttu-id="c1556-386">Configurar o tamanho da pilha gerenciada usando a configuração `stackSize` em bytes.</span><span class="sxs-lookup"><span data-stu-id="c1556-386">Configure the managed stack size using the `stackSize` setting in bytes.</span></span> <span data-ttu-id="c1556-387">O tamanho padrão é de `1048576` bytes (1 MB).</span><span class="sxs-lookup"><span data-stu-id="c1556-387">The default size is `1048576` bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

::: moniker-end

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="c1556-388">A configuração de proxy usa o protocolo HTTP e um token de emparelhamento</span><span class="sxs-lookup"><span data-stu-id="c1556-388">Proxy configuration uses HTTP protocol and a pairing token</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c1556-389">*Só se aplica à hospedagem de fora do processo.*</span><span class="sxs-lookup"><span data-stu-id="c1556-389">*Only applies to out-of-process hosting.*</span></span>

::: moniker-end

<span data-ttu-id="c1556-390">O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="c1556-390">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="c1556-391">O uso de HTTP é uma otimização de desempenho na qual o tráfego entre o módulo e o Kestrel ocorre em um endereço de loopback fora do adaptador de rede.</span><span class="sxs-lookup"><span data-stu-id="c1556-391">Using HTTP is a performance optimization, where the traffic between the module and Kestrel takes place on a loopback address off of the network interface.</span></span> <span data-ttu-id="c1556-392">Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="c1556-392">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="c1556-393">Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem.</span><span class="sxs-lookup"><span data-stu-id="c1556-393">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="c1556-394">O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="c1556-394">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="c1556-395">O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy.</span><span class="sxs-lookup"><span data-stu-id="c1556-395">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="c1556-396">O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="c1556-396">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="c1556-397">Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada.</span><span class="sxs-lookup"><span data-stu-id="c1556-397">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="c1556-398">A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="c1556-398">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="c1556-399">Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.</span><span class="sxs-lookup"><span data-stu-id="c1556-399">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="c1556-400">Módulo do ASP.NET Core com uma configuração do IIS compartilhada</span><span class="sxs-lookup"><span data-stu-id="c1556-400">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="c1556-401">O instalador do módulo do ASP.NET Core é executado com os privilégios da conta de **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="c1556-401">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="c1556-402">Já que a conta de sistema local não tem permissão para modificar o caminho do compartilhamento usado pela configuração compartilhada de IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações de módulo no arquivo *applicationHost.config* no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="c1556-402">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c1556-403">Ao usar uma configuração compartilhada de IIS no mesmo computador que a instalação do IIS, execute o instalador do pacote de hospedagem do ASP.NET Core com o parâmetro `OPT_NO_SHARED_CONFIG_CHECK` definido como `1`:</span><span class="sxs-lookup"><span data-stu-id="c1556-403">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="c1556-404">Quando o caminho para a configuração compartilhada não está no mesmo computador que a instalação do IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="c1556-404">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="c1556-405">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="c1556-405">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="c1556-406">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="c1556-406">Run the installer.</span></span>
1. <span data-ttu-id="c1556-407">Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="c1556-407">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="c1556-408">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="c1556-408">Re-enable the IIS Shared Configuration.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c1556-409">Ao usar uma configuração compartilhada de IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="c1556-409">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="c1556-410">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="c1556-410">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="c1556-411">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="c1556-411">Run the installer.</span></span>
1. <span data-ttu-id="c1556-412">Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="c1556-412">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="c1556-413">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="c1556-413">Re-enable the IIS Shared Configuration.</span></span>

::: moniker-end

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="c1556-414">Versão do módulo e logs do instalador do pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="c1556-414">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="c1556-415">Para determinar a versão do Módulo do ASP.NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="c1556-415">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="c1556-416">No sistema de hospedagem, navegue até *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="c1556-416">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="c1556-417">Localize o arquivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="c1556-417">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="c1556-418">Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="c1556-418">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="c1556-419">Selecione a guia **Detalhes**. A **Versão do arquivo** e a **Versão do produto** representam a versão instalada do módulo.</span><span class="sxs-lookup"><span data-stu-id="c1556-419">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="c1556-420">Os logs de instalador do pacote de hospedagem para o módulo são encontrados em *C:\\Usuários\\%UserName%\\AppData\\Local\\Temp*. O arquivo é nomeado *dd_DotNetCoreWinSvrHosting__\<carimbo de data/hora>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="c1556-420">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="c1556-421">Locais dos arquivos de módulo, de esquema e de configuração</span><span class="sxs-lookup"><span data-stu-id="c1556-421">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="c1556-422">Módulo</span><span class="sxs-lookup"><span data-stu-id="c1556-422">Module</span></span>

<span data-ttu-id="c1556-423">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="c1556-423">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="c1556-424">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="c1556-424">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="c1556-425">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="c1556-425">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="c1556-426">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="c1556-426">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="c1556-427">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="c1556-427">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

<span data-ttu-id="c1556-428">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="c1556-428">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="c1556-429">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="c1556-429">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="c1556-430">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="c1556-430">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="c1556-431">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="c1556-431">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="c1556-432">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="c1556-432">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

### <a name="schema"></a><span data-ttu-id="c1556-433">Esquema</span><span class="sxs-lookup"><span data-stu-id="c1556-433">Schema</span></span>

<span data-ttu-id="c1556-434">**IIS**</span><span class="sxs-lookup"><span data-stu-id="c1556-434">**IIS**</span></span>

* <span data-ttu-id="c1556-435">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="c1556-435">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="c1556-436">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="c1556-436">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

<span data-ttu-id="c1556-437">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="c1556-437">**IIS Express**</span></span>

* <span data-ttu-id="c1556-438">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="c1556-438">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="c1556-439">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="c1556-439">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="c1556-440">Configuração</span><span class="sxs-lookup"><span data-stu-id="c1556-440">Configuration</span></span>

<span data-ttu-id="c1556-441">**IIS**</span><span class="sxs-lookup"><span data-stu-id="c1556-441">**IIS**</span></span>

* <span data-ttu-id="c1556-442">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="c1556-442">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="c1556-443">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="c1556-443">**IIS Express**</span></span>

* <span data-ttu-id="c1556-444">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="c1556-444">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="c1556-445">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="c1556-445">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="c1556-446">Os arquivos podem ser encontrados pesquisando por *aspnetcore* no arquivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="c1556-446">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c1556-447">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c1556-447">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="c1556-448">Repositório do GitHub do Módulo do ASP.NET Core (origem de referência)</span><span class="sxs-lookup"><span data-stu-id="c1556-448">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
