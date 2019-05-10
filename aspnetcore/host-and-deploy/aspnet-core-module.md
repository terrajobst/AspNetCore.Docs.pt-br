---
title: Módulo do ASP.NET Core
author: guardrex
description: Saiba como configurar o módulo do ASP.NET Core para hospedar aplicativos do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: a33606bce6c78a19e3d380f7440e5892778806c3
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64889391"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="d3163-103">Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d3163-103">ASP.NET Core Module</span></span>

<span data-ttu-id="d3163-104">Por [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d3163-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="d3163-105">O módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para:</span><span class="sxs-lookup"><span data-stu-id="d3163-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="d3163-106">Hospedar um aplicativo ASP.NET Core dentro do processo de trabalho do IIS (`w3wp.exe`), chamado o [modelo de hospedagem no processo](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="d3163-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="d3163-107">Encaminhar solicitações da Web a um aplicativo ASP.NET Core de back-end que executa o [servidor Kestrel](xref:fundamentals/servers/kestrel), chamado o [modelo de hospedagem de fora do processo](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="d3163-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="d3163-108">Versões do Windows compatíveis:</span><span class="sxs-lookup"><span data-stu-id="d3163-108">Supported Windows versions:</span></span>

* <span data-ttu-id="d3163-109">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="d3163-109">Windows 7 or later</span></span>
* <span data-ttu-id="d3163-110">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="d3163-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="d3163-111">Ao fazer uma hospedagem em processo, o módulo usa uma implementação de servidor em processo do IIS, chamado Servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="d3163-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="d3163-112">Ao hospedar de fora do processo, o módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d3163-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="d3163-113">O módulo é incompatível com [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="d3163-113">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="d3163-114">Modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="d3163-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="d3163-115">Modelo de hospedagem em processo</span><span class="sxs-lookup"><span data-stu-id="d3163-115">In-process hosting model</span></span>

<span data-ttu-id="d3163-116">Para configurar um aplicativo para hospedagem em processo, adicione a propriedade `<AspNetCoreHostingModel>` ao arquivo de projeto do aplicativo com um valor de `InProcess` (a hospedagem de fora do processo é definida com `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="d3163-116">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="d3163-117">Não há suporte para o modelo de hospedagem em processo para aplicativos ASP.NET Core direcionados ao .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="d3163-117">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="d3163-118">Se a propriedade `<AspNetCoreHostingModel>` não estiver presente no arquivo, o valor padrão será `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="d3163-118">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="d3163-119">As seguintes características se aplicam ao hospedar em processo:</span><span class="sxs-lookup"><span data-stu-id="d3163-119">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="d3163-120">O Servidor HTTP do IIS (`IISHttpServer`) é usado, em vez do servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="d3163-120">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="d3163-121">Em processo, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:</span><span class="sxs-lookup"><span data-stu-id="d3163-121">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="d3163-122">Registrar o `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="d3163-122">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="d3163-123">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d3163-123">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="d3163-124">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="d3163-124">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="d3163-125">O [requestTimeout atributo](#attributes-of-the-aspnetcore-element) não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="d3163-125">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="d3163-126">Não há suporte para o compartilhamento do pool de aplicativos entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="d3163-126">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="d3163-127">Use um pool de aplicativos por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-127">Use one app pool per app.</span></span>

* <span data-ttu-id="d3163-128">Ao usar a [Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) ou inserir manualmente um [arquivo app_offline.htm na implantação](xref:host-and-deploy/iis/index#locked-deployment-files), o aplicativo talvez não seja desligado imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d3163-128">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="d3163-129">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-129">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="d3163-130">A arquitetura (número de bit) do aplicativo e o tempo de execução instalado (x64 ou x86) devem corresponder à arquitetura do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="d3163-130">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="d3163-131">Se a configuração manual do host do aplicativo com `WebHostBuilder` (não usando [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) e o aplicativo não forem executados diretamente no servidor Kestrel (auto-hospedado), chame `UseKestrel` antes de chamar `UseIISIntegration`.</span><span class="sxs-lookup"><span data-stu-id="d3163-131">If setting up the app's host manually with `WebHostBuilder` (not using [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) and the app is ever run directly on the Kestrel server (self-hosted), call `UseKestrel` before calling `UseIISIntegration`.</span></span> <span data-ttu-id="d3163-132">Se a ordem for invertida, a inicialização do host falhará.</span><span class="sxs-lookup"><span data-stu-id="d3163-132">If the order is reversed, the host fails to start.</span></span>

* <span data-ttu-id="d3163-133">As desconexões do cliente são detectadas.</span><span class="sxs-lookup"><span data-stu-id="d3163-133">Client disconnects are detected.</span></span> <span data-ttu-id="d3163-134">O token de cancelamento [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) é cancelado quando o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="d3163-134">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="d3163-135">No ASP.NET Core 2.2.1 ou anterior, <xref:System.IO.Directory.GetCurrentDirectory*> retorna o diretório de trabalho do processo iniciado pelo IIS em vez de o diretório do aplicativo (por exemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="d3163-135">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="d3163-136">Para obter o código de exemplo que define o diretório atual do aplicativo, confira a [classe CurrentDirectoryHelpers](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="d3163-136">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="d3163-137">Chame o método `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="d3163-137">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="d3163-138">As chamadas seguintes a <xref:System.IO.Directory.GetCurrentDirectory*> fornecem o diretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-138">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="d3163-139">Ao hospedar em processo, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="d3163-139">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="d3163-140">Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="d3163-140">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="d3163-141">Quando a transformação de declarações com uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, chame <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para adicionar serviços de autenticação:</span><span class="sxs-lookup"><span data-stu-id="d3163-141">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="d3163-142">Modelo de hospedagem de fora do processo</span><span class="sxs-lookup"><span data-stu-id="d3163-142">Out-of-process hosting model</span></span>

<span data-ttu-id="d3163-143">Para configurar um aplicativo para hospedagem fora do processo, use qualquer uma das abordagens a seguir no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="d3163-143">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="d3163-144">não especifique a propriedade `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="d3163-144">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="d3163-145">Se a propriedade `<AspNetCoreHostingModel>` não estiver presente no arquivo, o valor padrão será `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="d3163-145">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="d3163-146">Defina o valor da propriedade `<AspNetCoreHostingModel>` como `OutOfProcess` (a hospedagem no processo é definida com `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="d3163-146">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="d3163-147">O servidor [Kestrel](xref:fundamentals/servers/kestrel) é usado, em vez do servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="d3163-147">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="d3163-148">Fora do processo, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:</span><span class="sxs-lookup"><span data-stu-id="d3163-148">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="d3163-149">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d3163-149">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="d3163-150">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="d3163-150">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="d3163-151">Alterações no modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="d3163-151">Hosting model changes</span></span>

<span data-ttu-id="d3163-152">Se a configuração `hostingModel` for alterada no arquivo *web.config* (explicado na seção [Configuração a Web.config](#configuration-with-webconfig)), o módulo reciclará o processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="d3163-152">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="d3163-153">Para o IIS Express, o módulo não recicla o processo de trabalho, mas em vez disso, dispara um desligamento normal do processo atual do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="d3163-153">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="d3163-154">A próxima solicitação para o aplicativo gera um novo processo do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="d3163-154">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="d3163-155">Nome do processo</span><span class="sxs-lookup"><span data-stu-id="d3163-155">Process name</span></span>

<span data-ttu-id="d3163-156">`Process.GetCurrentProcess().ProcessName` relata `w3wp`/`iisexpress` (em processo) ou `dotnet` (fora do processo).</span><span class="sxs-lookup"><span data-stu-id="d3163-156">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="d3163-157">O Módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para encaminhar solicitações da Web para aplicativos ASP.NET Core de back-end.</span><span class="sxs-lookup"><span data-stu-id="d3163-157">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="d3163-158">Versões do Windows compatíveis:</span><span class="sxs-lookup"><span data-stu-id="d3163-158">Supported Windows versions:</span></span>

* <span data-ttu-id="d3163-159">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="d3163-159">Windows 7 or later</span></span>
* <span data-ttu-id="d3163-160">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="d3163-160">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="d3163-161">O módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d3163-161">The module only works with Kestrel.</span></span> <span data-ttu-id="d3163-162">O módulo é incompatível com [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="d3163-162">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="d3163-163">Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo também realiza o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="d3163-163">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="d3163-164">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo quando ele falha.</span><span class="sxs-lookup"><span data-stu-id="d3163-164">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="d3163-165">Isso é basicamente o mesmo comportamento que o dos aplicativos ASP.NET 4.x que são executados dentro do processo do IIS e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="d3163-165">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="d3163-166">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d3163-166">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Módulo do ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="d3163-168">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="d3163-168">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="d3163-169">O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d3163-169">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="d3163-170">O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.</span><span class="sxs-lookup"><span data-stu-id="d3163-170">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="d3163-171">O módulo especifica a porta por meio de uma variável de ambiente na inicialização e o middleware de integração do IIS configura o servidor para escutar em `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="d3163-171">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="d3163-172">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="d3163-172">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="d3163-173">O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d3163-173">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="d3163-174">Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d3163-174">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="d3163-175">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-175">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="d3163-176">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d3163-176">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="d3163-177">A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="d3163-177">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

<span data-ttu-id="d3163-178">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="d3163-178">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="d3163-179">Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="d3163-179">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="d3163-180">O Módulo do ASP.NET Core também pode:</span><span class="sxs-lookup"><span data-stu-id="d3163-180">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="d3163-181">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="d3163-181">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="d3163-182">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="d3163-182">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="d3163-183">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="d3163-183">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="d3163-184">Como instalar e usar o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d3163-184">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="d3163-185">Para obter instruções sobre como instalar e usar o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="d3163-185">For instructions on how to install and use the ASP.NET Core Module, see <xref:host-and-deploy/iis/index>.</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="d3163-186">Configuração com web.config</span><span class="sxs-lookup"><span data-stu-id="d3163-186">Configuration with web.config</span></span>

<span data-ttu-id="d3163-187">O Módulo do ASP.NET Core está configurado com a seção `aspNetCore` do nó `system.webServer` no arquivo *web.config* do site.</span><span class="sxs-lookup"><span data-stu-id="d3163-187">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="d3163-188">O seguinte arquivo *web.config* é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o Módulo do ASP.NET Core para manipular solicitações de site:</span><span class="sxs-lookup"><span data-stu-id="d3163-188">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="d3163-189">O seguinte *web.config* é publicado para uma [implantação autossuficiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="d3163-189">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="d3163-190">A propriedade <xref:System.Configuration.SectionInformation.InheritInChildApplications*> é definida como `false` para indicar que as configurações especificadas no elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) não são herdadas por aplicativos que residem em um subdiretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-190">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

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

<span data-ttu-id="d3163-191">Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="d3163-191">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="d3163-192">O caminho salva logs de stdout para a pasta *LogFiles*, que é um local criado automaticamente pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="d3163-192">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="d3163-193">Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="d3163-193">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="d3163-194">Atributos do elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="d3163-194">Attributes of the aspNetCore element</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="d3163-195">Atributo</span><span class="sxs-lookup"><span data-stu-id="d3163-195">Attribute</span></span> | <span data-ttu-id="d3163-196">Descrição</span><span class="sxs-lookup"><span data-stu-id="d3163-196">Description</span></span> | <span data-ttu-id="d3163-197">Padrão</span><span class="sxs-lookup"><span data-stu-id="d3163-197">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="d3163-198">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-198">Optional string attribute.</span></span></p><p><span data-ttu-id="d3163-199">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="d3163-199">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="d3163-200">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-200">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="d3163-201">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="d3163-201">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="d3163-202">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-202">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="d3163-203">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="d3163-203">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="d3163-204">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="d3163-204">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="d3163-205">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-205">Optional string attribute.</span></span></p><p><span data-ttu-id="d3163-206">Especifica o modelo de hospedagem como em processo (`InProcess`) ou de fora do processo (`OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="d3163-206">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="d3163-207">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-207">Optional integer attribute.</span></span></p><p><span data-ttu-id="d3163-208">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-208">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="d3163-209">&dagger;Para hospedagem em processo, o valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="d3163-209">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="d3163-210">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="d3163-210">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="d3163-211">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="d3163-211">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="d3163-212">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="d3163-212">Default: `1`</span></span><br><span data-ttu-id="d3163-213">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="d3163-213">Min: `1`</span></span><br><span data-ttu-id="d3163-214">Máx.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="d3163-214">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="d3163-215">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="d3163-215">Required string attribute.</span></span></p><p><span data-ttu-id="d3163-216">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d3163-216">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="d3163-217">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="d3163-217">Relative paths are supported.</span></span> <span data-ttu-id="d3163-218">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="d3163-218">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="d3163-219">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-219">Optional integer attribute.</span></span></p><p><span data-ttu-id="d3163-220">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="d3163-220">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="d3163-221">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="d3163-221">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="d3163-222">Sem suporte com hospedagem padrão.</span><span class="sxs-lookup"><span data-stu-id="d3163-222">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="d3163-223">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="d3163-223">Default: `10`</span></span><br><span data-ttu-id="d3163-224">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="d3163-224">Min: `0`</span></span><br><span data-ttu-id="d3163-225">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="d3163-225">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="d3163-226">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-226">Optional timespan attribute.</span></span></p><p><span data-ttu-id="d3163-227">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="d3163-227">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="d3163-228">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="d3163-228">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="d3163-229">Não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="d3163-229">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="d3163-230">Para a hospedagem em processo, o módulo aguarda o aplicativo processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="d3163-230">For in-process hosting, the module waits for the app to process the request.</span></span></p> | <span data-ttu-id="d3163-231">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="d3163-231">Default: `00:02:00`</span></span><br><span data-ttu-id="d3163-232">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="d3163-232">Min: `00:00:00`</span></span><br><span data-ttu-id="d3163-233">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="d3163-233">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="d3163-234">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-234">Optional integer attribute.</span></span></p><p><span data-ttu-id="d3163-235">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="d3163-235">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="d3163-236">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="d3163-236">Default: `10`</span></span><br><span data-ttu-id="d3163-237">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="d3163-237">Min: `0`</span></span><br><span data-ttu-id="d3163-238">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="d3163-238">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="d3163-239">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-239">Optional integer attribute.</span></span></p><p><span data-ttu-id="d3163-240">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="d3163-240">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="d3163-241">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="d3163-241">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="d3163-242">O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</span><span class="sxs-lookup"><span data-stu-id="d3163-242">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="d3163-243">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="d3163-243">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="d3163-244">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="d3163-244">Default: `120`</span></span><br><span data-ttu-id="d3163-245">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="d3163-245">Min: `0`</span></span><br><span data-ttu-id="d3163-246">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="d3163-246">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="d3163-247">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-247">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="d3163-248">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="d3163-248">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="d3163-249">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-249">Optional string attribute.</span></span></p><p><span data-ttu-id="d3163-250">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="d3163-250">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="d3163-251">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="d3163-251">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="d3163-252">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="d3163-252">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="d3163-253">Todas as pastas fornecidas no caminho são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="d3163-253">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="d3163-254">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo (*.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="d3163-254">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="d3163-255">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="d3163-255">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="d3163-256">Atributo</span><span class="sxs-lookup"><span data-stu-id="d3163-256">Attribute</span></span> | <span data-ttu-id="d3163-257">Descrição</span><span class="sxs-lookup"><span data-stu-id="d3163-257">Description</span></span> | <span data-ttu-id="d3163-258">Padrão</span><span class="sxs-lookup"><span data-stu-id="d3163-258">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="d3163-259">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-259">Optional string attribute.</span></span></p><p><span data-ttu-id="d3163-260">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="d3163-260">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="d3163-261">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-261">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="d3163-262">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="d3163-262">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="d3163-263">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-263">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="d3163-264">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="d3163-264">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="d3163-265">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="d3163-265">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="d3163-266">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-266">Optional integer attribute.</span></span></p><p><span data-ttu-id="d3163-267">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-267">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="d3163-268">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="d3163-268">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="d3163-269">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="d3163-269">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="d3163-270">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="d3163-270">Default: `1`</span></span><br><span data-ttu-id="d3163-271">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="d3163-271">Min: `1`</span></span><br><span data-ttu-id="d3163-272">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="d3163-272">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="d3163-273">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="d3163-273">Required string attribute.</span></span></p><p><span data-ttu-id="d3163-274">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d3163-274">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="d3163-275">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="d3163-275">Relative paths are supported.</span></span> <span data-ttu-id="d3163-276">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="d3163-276">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="d3163-277">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-277">Optional integer attribute.</span></span></p><p><span data-ttu-id="d3163-278">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="d3163-278">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="d3163-279">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="d3163-279">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="d3163-280">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="d3163-280">Default: `10`</span></span><br><span data-ttu-id="d3163-281">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="d3163-281">Min: `0`</span></span><br><span data-ttu-id="d3163-282">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="d3163-282">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="d3163-283">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-283">Optional timespan attribute.</span></span></p><p><span data-ttu-id="d3163-284">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="d3163-284">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="d3163-285">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="d3163-285">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="d3163-286">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="d3163-286">Default: `00:02:00`</span></span><br><span data-ttu-id="d3163-287">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="d3163-287">Min: `00:00:00`</span></span><br><span data-ttu-id="d3163-288">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="d3163-288">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="d3163-289">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-289">Optional integer attribute.</span></span></p><p><span data-ttu-id="d3163-290">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="d3163-290">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="d3163-291">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="d3163-291">Default: `10`</span></span><br><span data-ttu-id="d3163-292">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="d3163-292">Min: `0`</span></span><br><span data-ttu-id="d3163-293">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="d3163-293">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="d3163-294">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-294">Optional integer attribute.</span></span></p><p><span data-ttu-id="d3163-295">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="d3163-295">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="d3163-296">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="d3163-296">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="d3163-297">O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</span><span class="sxs-lookup"><span data-stu-id="d3163-297">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="d3163-298">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="d3163-298">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="d3163-299">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="d3163-299">Default: `120`</span></span><br><span data-ttu-id="d3163-300">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="d3163-300">Min: `0`</span></span><br><span data-ttu-id="d3163-301">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="d3163-301">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="d3163-302">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-302">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="d3163-303">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="d3163-303">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="d3163-304">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-304">Optional string attribute.</span></span></p><p><span data-ttu-id="d3163-305">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="d3163-305">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="d3163-306">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="d3163-306">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="d3163-307">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="d3163-307">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="d3163-308">As pastas fornecidas no caminho devem existir para que o módulo crie o arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="d3163-308">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="d3163-309">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo (*.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="d3163-309">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="d3163-310">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="d3163-310">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

| <span data-ttu-id="d3163-311">Atributo</span><span class="sxs-lookup"><span data-stu-id="d3163-311">Attribute</span></span> | <span data-ttu-id="d3163-312">Descrição</span><span class="sxs-lookup"><span data-stu-id="d3163-312">Description</span></span> | <span data-ttu-id="d3163-313">Padrão</span><span class="sxs-lookup"><span data-stu-id="d3163-313">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="d3163-314">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-314">Optional string attribute.</span></span></p><p><span data-ttu-id="d3163-315">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="d3163-315">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="d3163-316">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-316">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="d3163-317">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="d3163-317">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="d3163-318">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-318">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="d3163-319">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="d3163-319">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="d3163-320">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="d3163-320">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="d3163-321">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-321">Optional integer attribute.</span></span></p><p><span data-ttu-id="d3163-322">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-322">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="d3163-323">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="d3163-323">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="d3163-324">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="d3163-324">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="d3163-325">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="d3163-325">Default: `1`</span></span><br><span data-ttu-id="d3163-326">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="d3163-326">Min: `1`</span></span><br><span data-ttu-id="d3163-327">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="d3163-327">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="d3163-328">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="d3163-328">Required string attribute.</span></span></p><p><span data-ttu-id="d3163-329">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="d3163-329">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="d3163-330">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="d3163-330">Relative paths are supported.</span></span> <span data-ttu-id="d3163-331">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="d3163-331">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="d3163-332">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-332">Optional integer attribute.</span></span></p><p><span data-ttu-id="d3163-333">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="d3163-333">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="d3163-334">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="d3163-334">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="d3163-335">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="d3163-335">Default: `10`</span></span><br><span data-ttu-id="d3163-336">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="d3163-336">Min: `0`</span></span><br><span data-ttu-id="d3163-337">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="d3163-337">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="d3163-338">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-338">Optional timespan attribute.</span></span></p><p><span data-ttu-id="d3163-339">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="d3163-339">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="d3163-340">Em versões do Módulo ASP.NET Core que acompanham a versão do ASP.NET Core 2.0 ou anterior, o `requestTimeout` deve ser especificado somente em minutos inteiros, caso contrário, ele assume o valor padrão de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="d3163-340">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.0 or earlier, the `requestTimeout` must be specified in whole minutes only, otherwise it defaults to 2 minutes.</span></span></p> | <span data-ttu-id="d3163-341">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="d3163-341">Default: `00:02:00`</span></span><br><span data-ttu-id="d3163-342">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="d3163-342">Min: `00:00:00`</span></span><br><span data-ttu-id="d3163-343">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="d3163-343">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="d3163-344">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-344">Optional integer attribute.</span></span></p><p><span data-ttu-id="d3163-345">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="d3163-345">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="d3163-346">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="d3163-346">Default: `10`</span></span><br><span data-ttu-id="d3163-347">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="d3163-347">Min: `0`</span></span><br><span data-ttu-id="d3163-348">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="d3163-348">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="d3163-349">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-349">Optional integer attribute.</span></span></p><p><span data-ttu-id="d3163-350">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="d3163-350">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="d3163-351">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="d3163-351">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="d3163-352">O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</span><span class="sxs-lookup"><span data-stu-id="d3163-352">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p> | <span data-ttu-id="d3163-353">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="d3163-353">Default: `120`</span></span><br><span data-ttu-id="d3163-354">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="d3163-354">Min: `0`</span></span><br><span data-ttu-id="d3163-355">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="d3163-355">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="d3163-356">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-356">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="d3163-357">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="d3163-357">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="d3163-358">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="d3163-358">Optional string attribute.</span></span></p><p><span data-ttu-id="d3163-359">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="d3163-359">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="d3163-360">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="d3163-360">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="d3163-361">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="d3163-361">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="d3163-362">As pastas fornecidas no caminho devem existir para que o módulo crie o arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="d3163-362">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="d3163-363">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo (*.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="d3163-363">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="d3163-364">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="d3163-364">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a><span data-ttu-id="d3163-365">Definindo variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="d3163-365">Setting environment variables</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d3163-366">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="d3163-366">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="d3163-367">Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="d3163-367">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="d3163-368">Variáveis de ambiente definidas nesta seção têm precedência sobre variáveis de ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="d3163-368">Environment variables set in this section take precedence over system environment variables.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d3163-369">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="d3163-369">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="d3163-370">Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="d3163-370">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="d3163-371">As variáveis de ambiente definidas nesta seção são conflitantes com as variáveis de ambiente do sistema definidas com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="d3163-371">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="d3163-372">Quando a variável de ambiente é definida no arquivo *web.config* e no nível do sistema do Windows, o valor do arquivo *web.config* fica anexado ao valor da variável de ambiente do sistema (por exemplo, `ASPNETCORE_ENVIRONMENT: Development;Development`), o que impede a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-372">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

::: moniker-end

<span data-ttu-id="d3163-373">O exemplo a seguir define duas variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="d3163-373">The following example sets two environment variables.</span></span> <span data-ttu-id="d3163-374">`ASPNETCORE_ENVIRONMENT` configura o ambiente do aplicativo para `Development`.</span><span class="sxs-lookup"><span data-stu-id="d3163-374">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="d3163-375">Um desenvolvedor pode definir esse valor temporariamente no arquivo *web.config* para forçar o carregamento da [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) ao depurar uma exceção de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-375">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="d3163-376">`CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-376">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="d3163-377">Em vez de configurar o ambiente diretamente no *web.config*, você pode incluir a propriedade `<EnvironmentName>` no perfil de publicação (*.pubxml*) ou no perfil de projeto.</span><span class="sxs-lookup"><span data-stu-id="d3163-377">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="d3163-378">Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="d3163-378">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

::: moniker-end

> [!WARNING]
> <span data-ttu-id="d3163-379">Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.</span><span class="sxs-lookup"><span data-stu-id="d3163-379">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="appofflinehtm"></a><span data-ttu-id="d3163-380">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="d3163-380">app_offline.htm</span></span>

<span data-ttu-id="d3163-381">Se um arquivo com o nome *app_offline.htm* é detectado no diretório raiz de um aplicativo, o Módulo do ASP.NET Core tenta desligar normalmente o aplicativo e parar o processamento de solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="d3163-381">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="d3163-382">Se o aplicativo ainda está em execução após o número de segundos definido em `shutdownTimeLimit`, o Módulo do ASP.NET Core encerra o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="d3163-382">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="d3163-383">Enquanto o arquivo *app_offline.htm* estiver presente, o Módulo do ASP.NET Core responderá às solicitações enviando o conteúdo do arquivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="d3163-383">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="d3163-384">Quando o arquivo *app_offline.htm* é removido, a próxima solicitação inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-384">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="d3163-385">Ao usar o modelo de hospedagem de fora do processo, talvez o aplicativo não desligue imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="d3163-385">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="d3163-386">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-386">For example, a websocket connection may delay app shut down.</span></span>

::: moniker-end

## <a name="start-up-error-page"></a><span data-ttu-id="d3163-387">Página de erro de inicialização</span><span class="sxs-lookup"><span data-stu-id="d3163-387">Start-up error page</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="d3163-388">A hospedagem em processo e fora do processo produzem páginas de erro personalizadas quando falham ao iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-388">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="d3163-389">Se o Módulo do ASP.NET Core falhar ao encontrar o manipulador de solicitação em processo ou fora do processo, uma página de código de status *500.0 – falha ao carregar manipulador no processo/fora do processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="d3163-389">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="d3163-390">No caso da hospedagem em processo, se o módulo do ASP.NET Core falhar ao iniciar o aplicativo, uma página de código de status *500.30 – falha ao iniciar* será exibida.</span><span class="sxs-lookup"><span data-stu-id="d3163-390">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="d3163-391">Para hospedagem fora do processo, se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="d3163-391">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="d3163-392">Para suprimir essa página e reverter para a página de código de status 5xx padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="d3163-392">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="d3163-393">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="d3163-393">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="d3163-394">Se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="d3163-394">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="d3163-395">Para omitir esta página e reverter para a página de código de status 502 padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="d3163-395">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="d3163-396">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="d3163-396">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Página de código de status 502.5 – Falha do Processo](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a><span data-ttu-id="d3163-398">Criação de log e redirecionamento</span><span class="sxs-lookup"><span data-stu-id="d3163-398">Log creation and redirection</span></span>

<span data-ttu-id="d3163-399">O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos.</span><span class="sxs-lookup"><span data-stu-id="d3163-399">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="d3163-400">Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="d3163-400">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="d3163-401">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="d3163-401">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="d3163-402">Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo.</span><span class="sxs-lookup"><span data-stu-id="d3163-402">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="d3163-403">É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.</span><span class="sxs-lookup"><span data-stu-id="d3163-403">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="d3163-404">Usar o log de stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-404">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="d3163-405">Não use o log de stdout para fins gerais de registro em log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-405">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="d3163-406">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="d3163-406">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="d3163-407">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="d3163-407">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="d3163-408">Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="d3163-408">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="d3163-409">O nome do arquivo de log é composto por meio do acréscimo do carimbo de data/hora, da ID do processo e da extensão de arquivo (*.log*) para o último segmento do caminho `stdoutLogFile` (normalmente *stdout*), delimitados por sublinhados.</span><span class="sxs-lookup"><span data-stu-id="d3163-409">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="d3163-410">Se o caminho `stdoutLogFile` termina com *stdout*, um log para um aplicativo com um PID de 1934, criado em 5/2/2018 às 19:42:32, tem o nome de arquivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="d3163-410">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="d3163-411">Se `stdoutLogEnabled` for falso, os erros que ocorrerem na inicialização do aplicativo serão capturados e emitidos no log de eventos até 30 KB.</span><span class="sxs-lookup"><span data-stu-id="d3163-411">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="d3163-412">Após a inicialização, todos os logs adicionais são descartados.</span><span class="sxs-lookup"><span data-stu-id="d3163-412">After startup, all additional logs are discarded.</span></span>

::: moniker-end

<span data-ttu-id="d3163-413">O elemento `aspNetCore` de exemplo a seguir configura o registro em log de stdout para um aplicativo hospedado no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="d3163-413">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="d3163-414">Um caminho local ou um caminho de compartilhamento de rede é aceitável para o registro em log local.</span><span class="sxs-lookup"><span data-stu-id="d3163-414">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="d3163-415">Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="d3163-415">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

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

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="d3163-416">Logs de diagnóstico avançados</span><span class="sxs-lookup"><span data-stu-id="d3163-416">Enhanced diagnostic logs</span></span>

<span data-ttu-id="d3163-417">O Módulo do ASP.NET Core é configurável para fornecer logs de diagnóstico avançados.</span><span class="sxs-lookup"><span data-stu-id="d3163-417">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="d3163-418">Adicione o elemento `<handlerSettings>` ao elemento `<aspNetCore>` no *web.config*. A definição de `debugLevel` como `TRACE` expõe uma fidelidade maior de informações de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="d3163-418">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="d3163-419">Os valores do nível de depuração (`debugLevel`) podem incluir o nível e a localização.</span><span class="sxs-lookup"><span data-stu-id="d3163-419">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="d3163-420">Níveis (na ordem do menos para o mais detalhado):</span><span class="sxs-lookup"><span data-stu-id="d3163-420">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="d3163-421">ERROR</span><span class="sxs-lookup"><span data-stu-id="d3163-421">ERROR</span></span>
* <span data-ttu-id="d3163-422">WARNING</span><span class="sxs-lookup"><span data-stu-id="d3163-422">WARNING</span></span>
* <span data-ttu-id="d3163-423">INFO</span><span class="sxs-lookup"><span data-stu-id="d3163-423">INFO</span></span>
* <span data-ttu-id="d3163-424">TRACE</span><span class="sxs-lookup"><span data-stu-id="d3163-424">TRACE</span></span>

<span data-ttu-id="d3163-425">Locais (vários locais são permitidos):</span><span class="sxs-lookup"><span data-stu-id="d3163-425">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="d3163-426">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="d3163-426">CONSOLE</span></span>
* <span data-ttu-id="d3163-427">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="d3163-427">EVENTLOG</span></span>
* <span data-ttu-id="d3163-428">FILE</span><span class="sxs-lookup"><span data-stu-id="d3163-428">FILE</span></span>

<span data-ttu-id="d3163-429">As configurações do manipulador também podem ser fornecidas por meio de variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="d3163-429">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="d3163-430">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; caminho para o arquivo de log de depuração.</span><span class="sxs-lookup"><span data-stu-id="d3163-430">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="d3163-431">(Padrão: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="d3163-431">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="d3163-432">`ASPNETCORE_MODULE_DEBUG` &ndash; configuração do nível de depuração.</span><span class="sxs-lookup"><span data-stu-id="d3163-432">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="d3163-433">**Não** deixe o log de depuração habilitado na implantação por mais tempo que o necessário para solucionar um problema.</span><span class="sxs-lookup"><span data-stu-id="d3163-433">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="d3163-434">O tamanho do log não é limitado.</span><span class="sxs-lookup"><span data-stu-id="d3163-434">The size of the log isn't limited.</span></span> <span data-ttu-id="d3163-435">Deixar o log de depuração habilitado pode esgotar o espaço em disco disponível e causar falha no servidor ou no serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-435">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

::: moniker-end

<span data-ttu-id="d3163-436">Veja [Configuração com web.config](#configuration-with-webconfig) para obter um exemplo do elemento `aspNetCore` no arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="d3163-436">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="d3163-437">A configuração de proxy usa o protocolo HTTP e um token de emparelhamento</span><span class="sxs-lookup"><span data-stu-id="d3163-437">Proxy configuration uses HTTP protocol and a pairing token</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="d3163-438">*Só se aplica à hospedagem de fora do processo.*</span><span class="sxs-lookup"><span data-stu-id="d3163-438">*Only applies to out-of-process hosting.*</span></span>

::: moniker-end

<span data-ttu-id="d3163-439">O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="d3163-439">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="d3163-440">O uso de HTTP é uma otimização de desempenho na qual o tráfego entre o módulo e o Kestrel ocorre em um endereço de loopback fora do adaptador de rede.</span><span class="sxs-lookup"><span data-stu-id="d3163-440">Using HTTP is a performance optimization, where the traffic between the module and Kestrel takes place on a loopback address off of the network interface.</span></span> <span data-ttu-id="d3163-441">Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="d3163-441">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="d3163-442">Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem.</span><span class="sxs-lookup"><span data-stu-id="d3163-442">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="d3163-443">O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="d3163-443">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="d3163-444">O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy.</span><span class="sxs-lookup"><span data-stu-id="d3163-444">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="d3163-445">O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="d3163-445">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="d3163-446">Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada.</span><span class="sxs-lookup"><span data-stu-id="d3163-446">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="d3163-447">A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="d3163-447">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="d3163-448">Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.</span><span class="sxs-lookup"><span data-stu-id="d3163-448">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="d3163-449">Módulo do ASP.NET Core com uma configuração do IIS compartilhada</span><span class="sxs-lookup"><span data-stu-id="d3163-449">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="d3163-450">O instalador do módulo do ASP.NET Core é executado com os privilégios da conta de **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="d3163-450">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="d3163-451">Já que a conta de sistema local não tem permissão para modificar o caminho do compartilhamento usado pela configuração compartilhada de IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações de módulo no arquivo *applicationHost.config* no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="d3163-451">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="d3163-452">Ao usar uma configuração compartilhada de IIS no mesmo computador que a instalação do IIS, execute o instalador do pacote de hospedagem do ASP.NET Core com o parâmetro `OPT_NO_SHARED_CONFIG_CHECK` definido como `1`:</span><span class="sxs-lookup"><span data-stu-id="d3163-452">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="d3163-453">Quando o caminho para a configuração compartilhada não está no mesmo computador que a instalação do IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="d3163-453">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="d3163-454">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="d3163-454">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="d3163-455">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="d3163-455">Run the installer.</span></span>
1. <span data-ttu-id="d3163-456">Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="d3163-456">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="d3163-457">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="d3163-457">Re-enable the IIS Shared Configuration.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="d3163-458">Ao usar uma configuração compartilhada de IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="d3163-458">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="d3163-459">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="d3163-459">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="d3163-460">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="d3163-460">Run the installer.</span></span>
1. <span data-ttu-id="d3163-461">Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="d3163-461">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="d3163-462">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="d3163-462">Re-enable the IIS Shared Configuration.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="application-initialization"></a><span data-ttu-id="d3163-463">Inicialização de Aplicativo</span><span class="sxs-lookup"><span data-stu-id="d3163-463">Application Initialization</span></span>

<span data-ttu-id="d3163-464">[Inicialização de Aplicativo IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) é um recurso do IIS que envia uma solicitação HTTP para o aplicativo quando o pool de aplicativos é iniciado ou reciclado.</span><span class="sxs-lookup"><span data-stu-id="d3163-464">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="d3163-465">A solicitação dispara a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-465">The request triggers the app to start.</span></span> <span data-ttu-id="d3163-466">A Inicialização de Aplicativo pode ser usada pelo [modelo de hospedagem em processo](xref:fundamentals/servers/index#in-process-hosting-model) e pelo [modelo de hospedagem fora do processo](xref:fundamentals/servers/index#out-of-process-hosting-model) com a versão 2 do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d3163-466">Application Initialization can be used by both the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model) and [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model) with the ASP.NET Core Module version 2.</span></span>

<span data-ttu-id="d3163-467">Para habilitar a Inicialização de Aplicativo:</span><span class="sxs-lookup"><span data-stu-id="d3163-467">To enable Application Initialization:</span></span>

1. <span data-ttu-id="d3163-468">Confirme se o recurso da função Inicialização de Aplicativo do IIS está habilitado:</span><span class="sxs-lookup"><span data-stu-id="d3163-468">Confirm that the IIS Application Initialization role feature in enabled:</span></span>
   * <span data-ttu-id="d3163-469">No Windows 7 ou posterior: Navegue para **Painel de Controle** > **Programas** > **Programas e Recursos** > **Ativar ou desativar recursos do Windows** (lado esquerdo da tela).</span><span class="sxs-lookup"><span data-stu-id="d3163-469">On Windows 7 or later: Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span> <span data-ttu-id="d3163-470">Abra **Serviços de Informações da Internet** > **Serviços da World Wide Web** > **Recursos de Desenvolvimento de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="d3163-470">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="d3163-471">Marque a caixa de seleção **Inicialização de Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="d3163-471">Select the check box for **Application Initialization**.</span></span>
   * <span data-ttu-id="d3163-472">No Windows Server 2008 R2 ou posterior, abra o **Assistente para Adicionar Funções e Recursos**.</span><span class="sxs-lookup"><span data-stu-id="d3163-472">On Windows Server 2008 R2 or later, open the **Add Roles and Features Wizard**.</span></span> <span data-ttu-id="d3163-473">Ao acessar o painel **Selecionar serviços de função**, abra o nó **Desenvolvimento de Aplicativos** e marque a caixa de seleção **Inicialização de Aplicativo**.</span><span class="sxs-lookup"><span data-stu-id="d3163-473">When you reach the **Select role services** panel, open the **Application Development** node and select the **Application Initialization** check box.</span></span>
1. <span data-ttu-id="d3163-474">No Gerenciador do IIS, selecione **Pools de Aplicativos** no painel **Conexões**.</span><span class="sxs-lookup"><span data-stu-id="d3163-474">In IIS Manager, select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="d3163-475">Selecione o pool de aplicativos do aplicativo na lista.</span><span class="sxs-lookup"><span data-stu-id="d3163-475">Select the app's app pool in the list.</span></span>
1. <span data-ttu-id="d3163-476">Selecione **Configurações Avançadas** em **Editar Pool de Aplicativos** no painel **Ações**.</span><span class="sxs-lookup"><span data-stu-id="d3163-476">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="d3163-477">Defina **Modo de Inicialização** como **AlwaysRunning**.</span><span class="sxs-lookup"><span data-stu-id="d3163-477">Set **Start Mode** to **AlwaysRunning**.</span></span>
1. <span data-ttu-id="d3163-478">Abra o nó **Sites** no painel **Conexões**.</span><span class="sxs-lookup"><span data-stu-id="d3163-478">Open the **Sites** node in the **Connections** panel.</span></span>
1. <span data-ttu-id="d3163-479">Selecione o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="d3163-479">Select the app.</span></span>
1. <span data-ttu-id="d3163-480">Selecione **Configurações Avançadas** em **Gerenciar Site** no painel **Ações**.</span><span class="sxs-lookup"><span data-stu-id="d3163-480">Select **Advanced Settings** under **Manage Website** in the **Actions** panel.</span></span>
1. <span data-ttu-id="d3163-481">Defina **Pré-carregamento Habilitado** como **Verdadeiro**.</span><span class="sxs-lookup"><span data-stu-id="d3163-481">Set **Preload Enabled** to **True**.</span></span>

<span data-ttu-id="d3163-482">Para saber mais, confira [Inicialização de Aplicativo do IIS 8.0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization).</span><span class="sxs-lookup"><span data-stu-id="d3163-482">For more information, see [IIS 8.0 Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization).</span></span>

<span data-ttu-id="d3163-483">Aplicativos que usam o [modelo de hospedagem fora do processo](xref:fundamentals/servers/index#out-of-process-hosting-model) devem usar um serviço externo para executar periodicamente o ping no aplicativo a fim de mantê-lo em execução.</span><span class="sxs-lookup"><span data-stu-id="d3163-483">Apps that use the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model) must use an external service to periodically ping the app in order to keep it running.</span></span>

::: moniker-end

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="d3163-484">Versão do módulo e logs do instalador do pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="d3163-484">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="d3163-485">Para determinar a versão do Módulo do ASP.NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="d3163-485">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="d3163-486">No sistema de hospedagem, navegue até *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="d3163-486">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="d3163-487">Localize o arquivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="d3163-487">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="d3163-488">Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="d3163-488">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="d3163-489">Selecione a guia **Detalhes**. A **Versão do arquivo** e a **Versão do produto** representam a versão instalada do módulo.</span><span class="sxs-lookup"><span data-stu-id="d3163-489">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="d3163-490">Os logs de instalador do pacote de hospedagem para o módulo são encontrados em *C:\\Usuários\\%UserName%\\AppData\\Local\\Temp*. O arquivo é nomeado *dd_DotNetCoreWinSvrHosting__\<carimbo de data/hora>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="d3163-490">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="d3163-491">Locais dos arquivos de módulo, de esquema e de configuração</span><span class="sxs-lookup"><span data-stu-id="d3163-491">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="d3163-492">Módulo</span><span class="sxs-lookup"><span data-stu-id="d3163-492">Module</span></span>

<span data-ttu-id="d3163-493">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="d3163-493">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="d3163-494">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="d3163-494">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="d3163-495">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="d3163-495">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d3163-496">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="d3163-496">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="d3163-497">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="d3163-497">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

<span data-ttu-id="d3163-498">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="d3163-498">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="d3163-499">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="d3163-499">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="d3163-500">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="d3163-500">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d3163-501">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="d3163-501">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="d3163-502">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="d3163-502">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

### <a name="schema"></a><span data-ttu-id="d3163-503">Esquema</span><span class="sxs-lookup"><span data-stu-id="d3163-503">Schema</span></span>

<span data-ttu-id="d3163-504">**IIS**</span><span class="sxs-lookup"><span data-stu-id="d3163-504">**IIS**</span></span>

* <span data-ttu-id="d3163-505">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="d3163-505">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d3163-506">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="d3163-506">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

<span data-ttu-id="d3163-507">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="d3163-507">**IIS Express**</span></span>

* <span data-ttu-id="d3163-508">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="d3163-508">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="d3163-509">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="d3163-509">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="d3163-510">Configuração</span><span class="sxs-lookup"><span data-stu-id="d3163-510">Configuration</span></span>

<span data-ttu-id="d3163-511">**IIS**</span><span class="sxs-lookup"><span data-stu-id="d3163-511">**IIS**</span></span>

* <span data-ttu-id="d3163-512">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="d3163-512">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="d3163-513">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="d3163-513">**IIS Express**</span></span>

* <span data-ttu-id="d3163-514">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="d3163-514">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="d3163-515">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="d3163-515">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="d3163-516">Os arquivos podem ser encontrados pesquisando por *aspnetcore* no arquivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="d3163-516">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d3163-517">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d3163-517">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="d3163-518">Repositório do GitHub do Módulo do ASP.NET Core (origem de referência)</span><span class="sxs-lookup"><span data-stu-id="d3163-518">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
