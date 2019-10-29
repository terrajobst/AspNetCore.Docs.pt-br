---
title: Módulo do ASP.NET Core
author: guardrex
description: Saiba como configurar o módulo do ASP.NET Core para hospedar aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 42ff4438738931fde70e123031412bcfc8a83efb
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034206"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="cc9fe-103">Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc9fe-103">ASP.NET Core Module</span></span>

<span data-ttu-id="cc9fe-104">Por [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cc9fe-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cc9fe-105">O módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="cc9fe-106">Hospedar um aplicativo ASP.NET Core dentro do processo de trabalho do IIS (`w3wp.exe`), chamado o [modelo de hospedagem no processo](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="cc9fe-107">Encaminhar solicitações da Web a um aplicativo ASP.NET Core de back-end que executa o [servidor Kestrel](xref:fundamentals/servers/kestrel), chamado o [modelo de hospedagem de fora do processo](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="cc9fe-108">Versões do Windows compatíveis:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-108">Supported Windows versions:</span></span>

* <span data-ttu-id="cc9fe-109">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="cc9fe-109">Windows 7 or later</span></span>
* <span data-ttu-id="cc9fe-110">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="cc9fe-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="cc9fe-111">Ao fazer uma hospedagem em processo, o módulo usa uma implementação de servidor em processo do IIS, chamado Servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="cc9fe-112">Ao hospedar de fora do processo, o módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="cc9fe-113">O módulo não funciona com [http. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="cc9fe-114">Modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="cc9fe-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="cc9fe-115">Modelo de hospedagem em processo</span><span class="sxs-lookup"><span data-stu-id="cc9fe-115">In-process hosting model</span></span>

<span data-ttu-id="cc9fe-116">ASP.NET Core aplicativos assumem como padrão o modelo de hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="cc9fe-117">As seguintes características se aplicam ao hospedar em processo:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="cc9fe-118">O Servidor HTTP do IIS (`IISHttpServer`) é usado, em vez do servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="cc9fe-119">Em processo, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="cc9fe-120">Registrar o `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="cc9fe-121">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="cc9fe-122">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="cc9fe-123">O [requestTimeout atributo](#attributes-of-the-aspnetcore-element) não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="cc9fe-124">Não há suporte para o compartilhamento do pool de aplicativos entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="cc9fe-125">Use um pool de aplicativos por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-125">Use one app pool per app.</span></span>

* <span data-ttu-id="cc9fe-126">Ao usar a [Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) ou inserir manualmente um [arquivo app_offline.htm na implantação](xref:host-and-deploy/iis/index#locked-deployment-files), o aplicativo talvez não seja desligado imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="cc9fe-127">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="cc9fe-128">A arquitetura (número de bit) do aplicativo e o tempo de execução instalado (x64 ou x86) devem corresponder à arquitetura do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="cc9fe-129">As desconexões do cliente são detectadas.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-129">Client disconnects are detected.</span></span> <span data-ttu-id="cc9fe-130">O token de cancelamento [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) é cancelado quando o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="cc9fe-131">No ASP.NET Core 2.2.1 ou anterior, <xref:System.IO.Directory.GetCurrentDirectory*> retorna o diretório de trabalho do processo iniciado pelo IIS em vez de o diretório do aplicativo (por exemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="cc9fe-132">Para obter o código de exemplo que define o diretório atual do aplicativo, confira a [classe CurrentDirectoryHelpers](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="cc9fe-133">Chame o método `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="cc9fe-134">As chamadas seguintes a <xref:System.IO.Directory.GetCurrentDirectory*> fornecem o diretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="cc9fe-135">Ao hospedar em processo, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="cc9fe-136">Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="cc9fe-137">Quando a transformação de declarações com uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, chame <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para adicionar serviços de autenticação:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="cc9fe-138">Não há suporte para [implantações de pacote da Web (arquivo único)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) .</span><span class="sxs-lookup"><span data-stu-id="cc9fe-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="cc9fe-139">Modelo de hospedagem de fora do processo</span><span class="sxs-lookup"><span data-stu-id="cc9fe-139">Out-of-process hosting model</span></span>

<span data-ttu-id="cc9fe-140">Para configurar um aplicativo para hospedagem fora do processo, defina o valor da propriedade `<AspNetCoreHostingModel>` como `OutOfProcess` no arquivo de projeto ( *. csproj*):</span><span class="sxs-lookup"><span data-stu-id="cc9fe-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="cc9fe-141">A hospedagem em processo é definida com `InProcess`, que é o valor padrão.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="cc9fe-142">O valor de `<AspNetCoreHostingModel>` não diferencia maiúsculas de minúsculas e, portanto, `inprocess` e `outofprocess` são valores válidos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-142">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="cc9fe-143">O servidor [Kestrel](xref:fundamentals/servers/kestrel) é usado, em vez do servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-143">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="cc9fe-144">Fora do processo, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-144">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="cc9fe-145">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-145">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="cc9fe-146">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-146">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="cc9fe-147">Alterações no modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="cc9fe-147">Hosting model changes</span></span>

<span data-ttu-id="cc9fe-148">Se a configuração `hostingModel` for alterada no arquivo *web.config* (explicado na seção [Configuração a Web.config](#configuration-with-webconfig)), o módulo reciclará o processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-148">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="cc9fe-149">Para o IIS Express, o módulo não recicla o processo de trabalho, mas em vez disso, dispara um desligamento normal do processo atual do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-149">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="cc9fe-150">A próxima solicitação para o aplicativo gera um novo processo do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-150">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="cc9fe-151">Nome do processo</span><span class="sxs-lookup"><span data-stu-id="cc9fe-151">Process name</span></span>

<span data-ttu-id="cc9fe-152">`Process.GetCurrentProcess().ProcessName` relata `w3wp`/`iisexpress` (em processo) ou `dotnet` (fora do processo).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-152">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="cc9fe-153">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-153">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="cc9fe-154">Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-154">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="cc9fe-155">O Módulo do ASP.NET Core também pode:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-155">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="cc9fe-156">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-156">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="cc9fe-157">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-157">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="cc9fe-158">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-158">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="cc9fe-159">Como instalar e usar o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc9fe-159">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="cc9fe-160">Para obter instruções de como instalar o Módulo do ASP.NET Core, confira [Instalar o Pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-160">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="cc9fe-161">Configuração com web.config</span><span class="sxs-lookup"><span data-stu-id="cc9fe-161">Configuration with web.config</span></span>

<span data-ttu-id="cc9fe-162">O Módulo do ASP.NET Core está configurado com a seção `aspNetCore` do nó `system.webServer` no arquivo *web.config* do site.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-162">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="cc9fe-163">O seguinte arquivo *web.config* é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o Módulo do ASP.NET Core para manipular solicitações de site:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-163">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="cc9fe-164">O seguinte *web.config* é publicado para uma [implantação autossuficiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="cc9fe-164">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="cc9fe-165">A propriedade <xref:System.Configuration.SectionInformation.InheritInChildApplications*> é definida como `false` para indicar que as configurações especificadas no elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) não são herdadas por aplicativos que residem em um subdiretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-165">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="cc9fe-166">Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-166">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="cc9fe-167">O caminho salva logs de stdout para a pasta *LogFiles*, que é um local criado automaticamente pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-167">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="cc9fe-168">Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-168">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="cc9fe-169">Atributos do elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="cc9fe-169">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="cc9fe-170">Atributo</span><span class="sxs-lookup"><span data-stu-id="cc9fe-170">Attribute</span></span> | <span data-ttu-id="cc9fe-171">Descrição</span><span class="sxs-lookup"><span data-stu-id="cc9fe-171">Description</span></span> | <span data-ttu-id="cc9fe-172">Padrão</span><span class="sxs-lookup"><span data-stu-id="cc9fe-172">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="cc9fe-173">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-173">Optional string attribute.</span></span></p><p><span data-ttu-id="cc9fe-174">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-174">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="cc9fe-175">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-175">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cc9fe-176">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-176">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="cc9fe-177">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-177">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cc9fe-178">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-178">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="cc9fe-179">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-179">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="cc9fe-180">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-180">Optional string attribute.</span></span></p><p><span data-ttu-id="cc9fe-181">Especifica o modelo de hospedagem como em processo (`InProcess`/`inprocess`) ou fora do processo (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-181">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="cc9fe-182">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-182">Optional integer attribute.</span></span></p><p><span data-ttu-id="cc9fe-183">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-183">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="cc9fe-184">&dagger;Para hospedagem em processo, o valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-184">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="cc9fe-185">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-185">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="cc9fe-186">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-186">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="cc9fe-187">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-187">Default: `1`</span></span><br><span data-ttu-id="cc9fe-188">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-188">Min: `1`</span></span><br><span data-ttu-id="cc9fe-189">Máx.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="cc9fe-189">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="cc9fe-190">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-190">Required string attribute.</span></span></p><p><span data-ttu-id="cc9fe-191">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-191">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="cc9fe-192">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-192">Relative paths are supported.</span></span> <span data-ttu-id="cc9fe-193">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-193">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="cc9fe-194">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-194">Optional integer attribute.</span></span></p><p><span data-ttu-id="cc9fe-195">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-195">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="cc9fe-196">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-196">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="cc9fe-197">Sem suporte com hospedagem padrão.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-197">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="cc9fe-198">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-198">Default: `10`</span></span><br><span data-ttu-id="cc9fe-199">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-199">Min: `0`</span></span><br><span data-ttu-id="cc9fe-200">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-200">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="cc9fe-201">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-201">Optional timespan attribute.</span></span></p><p><span data-ttu-id="cc9fe-202">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-202">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="cc9fe-203">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-203">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="cc9fe-204">Não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-204">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="cc9fe-205">Para a hospedagem em processo, o módulo aguarda o aplicativo processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-205">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="cc9fe-206">Os valores válidos para segmentos de minutos e segundos da cadeia de caracteres estão no intervalo 0 a 59.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-206">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="cc9fe-207">O uso de **60** no valor para minutos ou segundos resulta em um *500 - Erro Interno do Servidor*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-207">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="cc9fe-208">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-208">Default: `00:02:00`</span></span><br><span data-ttu-id="cc9fe-209">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-209">Min: `00:00:00`</span></span><br><span data-ttu-id="cc9fe-210">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-210">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="cc9fe-211">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-211">Optional integer attribute.</span></span></p><p><span data-ttu-id="cc9fe-212">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-212">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="cc9fe-213">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-213">Default: `10`</span></span><br><span data-ttu-id="cc9fe-214">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-214">Min: `0`</span></span><br><span data-ttu-id="cc9fe-215">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-215">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="cc9fe-216">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-216">Optional integer attribute.</span></span></p><p><span data-ttu-id="cc9fe-217">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-217">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="cc9fe-218">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-218">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="cc9fe-219">O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-219">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="cc9fe-220">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-220">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="cc9fe-221">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-221">Default: `120`</span></span><br><span data-ttu-id="cc9fe-222">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-222">Min: `0`</span></span><br><span data-ttu-id="cc9fe-223">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-223">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="cc9fe-224">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-224">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cc9fe-225">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-225">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="cc9fe-226">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-226">Optional string attribute.</span></span></p><p><span data-ttu-id="cc9fe-227">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-227">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="cc9fe-228">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-228">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="cc9fe-229">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-229">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="cc9fe-230">Todas as pastas fornecidas no caminho são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-230">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="cc9fe-231">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo ( *.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-231">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="cc9fe-232">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-232">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="cc9fe-233">Definir variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="cc9fe-233">Set environment variables</span></span>

<span data-ttu-id="cc9fe-234">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-234">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="cc9fe-235">Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-235">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="cc9fe-236">Variáveis de ambiente definidas nesta seção têm precedência sobre variáveis de ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-236">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="cc9fe-237">O exemplo a seguir define duas variáveis de ambiente em *Web. config*. `ASPNETCORE_ENVIRONMENT` configura o ambiente do aplicativo para `Development`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-237">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="cc9fe-238">Um desenvolvedor pode definir esse valor temporariamente no arquivo *web.config* para forçar o carregamento da [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) ao depurar uma exceção de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-238">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="cc9fe-239">`CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-239">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="cc9fe-240">Em vez de configurar o ambiente diretamente no *web.config*, você pode incluir a propriedade `<EnvironmentName>` no perfil de publicação ( *.pubxml*) ou no perfil de projeto.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-240">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="cc9fe-241">Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-241">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="cc9fe-242">Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-242">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="cc9fe-243">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="cc9fe-243">app_offline.htm</span></span>

<span data-ttu-id="cc9fe-244">Se um arquivo com o nome *app_offline.htm* é detectado no diretório raiz de um aplicativo, o Módulo do ASP.NET Core tenta desligar normalmente o aplicativo e parar o processamento de solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-244">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="cc9fe-245">Se o aplicativo ainda está em execução após o número de segundos definido em `shutdownTimeLimit`, o Módulo do ASP.NET Core encerra o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-245">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="cc9fe-246">Enquanto o arquivo *app_offline.htm* estiver presente, o Módulo do ASP.NET Core responderá às solicitações enviando o conteúdo do arquivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-246">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="cc9fe-247">Quando o arquivo *app_offline.htm* é removido, a próxima solicitação inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-247">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="cc9fe-248">Ao usar o modelo de hospedagem de fora do processo, talvez o aplicativo não desligue imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-248">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="cc9fe-249">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-249">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="cc9fe-250">Página de erro de inicialização</span><span class="sxs-lookup"><span data-stu-id="cc9fe-250">Start-up error page</span></span>

<span data-ttu-id="cc9fe-251">A hospedagem em processo e fora do processo produzem páginas de erro personalizadas quando falham ao iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-251">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="cc9fe-252">Se o Módulo do ASP.NET Core falhar ao encontrar o manipulador de solicitação em processo ou fora do processo, uma página de código de status *500.0 – falha ao carregar manipulador no processo/fora do processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-252">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="cc9fe-253">No caso da hospedagem em processo, se o módulo do ASP.NET Core falhar ao iniciar o aplicativo, uma página de código de status *500.30 – falha ao iniciar* será exibida.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-253">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="cc9fe-254">Para hospedagem fora do processo, se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-254">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="cc9fe-255">Para suprimir essa página e reverter para a página de código de status 5xx padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-255">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="cc9fe-256">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-256">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="cc9fe-257">Criação de log e redirecionamento</span><span class="sxs-lookup"><span data-stu-id="cc9fe-257">Log creation and redirection</span></span>

<span data-ttu-id="cc9fe-258">O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-258">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="cc9fe-259">Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-259">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="cc9fe-260">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-260">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="cc9fe-261">Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-261">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="cc9fe-262">É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-262">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="cc9fe-263">O uso do log stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo ao hospedar no IIS ou ao usar o [suporte a tempo de desenvolvimento para o IIS com o Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), não durante a depuração local e a execução do aplicativo com IIS Express.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-263">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="cc9fe-264">Não use o log de stdout para fins gerais de registro em log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-264">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="cc9fe-265">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-265">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="cc9fe-266">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-266">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="cc9fe-267">Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-267">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="cc9fe-268">O nome do arquivo de log é composto por meio do acréscimo do carimbo de data/hora, da ID do processo e da extensão de arquivo ( *.log*) para o último segmento do caminho `stdoutLogFile` (normalmente *stdout*), delimitados por sublinhados.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-268">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="cc9fe-269">Se o caminho `stdoutLogFile` termina com *stdout*, um log para um aplicativo com um PID de 1934, criado em 5/2/2018 às 19:42:32, tem o nome de arquivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-269">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="cc9fe-270">Se `stdoutLogEnabled` for falso, os erros que ocorrerem na inicialização do aplicativo serão capturados e emitidos no log de eventos até 30 KB.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-270">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="cc9fe-271">Após a inicialização, todos os logs adicionais são descartados.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-271">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="cc9fe-272">O elemento `aspNetCore` de exemplo a seguir configura o log de stdout no caminho relativo `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-272">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="cc9fe-273">Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="cc9fe-274">Ao publicar um aplicativo para implantação Azure App serviço, o SDK da Web define o valor de `stdoutLogFile` como `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-274">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="cc9fe-275">A variável de ambiente `%home` é predefinida para aplicativos hospedados pelo serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-275">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="cc9fe-276">Para criar regras de filtro de log, consulte as seções de [configuração](xref:fundamentals/logging/index#log-filtering) e [filtragem de log](xref:fundamentals/logging/index#log-filtering) da documentação de log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-276">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="cc9fe-277">Para obter mais informações sobre formatos de caminho, consulte [formatos de caminho de arquivo em sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-277">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="cc9fe-278">Logs de diagnóstico avançados</span><span class="sxs-lookup"><span data-stu-id="cc9fe-278">Enhanced diagnostic logs</span></span>

<span data-ttu-id="cc9fe-279">O Módulo do ASP.NET Core é configurável para fornecer logs de diagnóstico avançados.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-279">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="cc9fe-280">Adicione o elemento `<handlerSettings>` ao elemento `<aspNetCore>` em *Web. config*. Definir a `debugLevel` como `TRACE` expõe uma fidelidade mais alta das informações de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-280">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="cc9fe-281">Todas as pastas no caminho (*logs* no exemplo anterior) são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-281">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="cc9fe-282">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-282">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="cc9fe-283">Os valores do nível de depuração (`debugLevel`) podem incluir o nível e a localização.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-283">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="cc9fe-284">Níveis (na ordem do menos para o mais detalhado):</span><span class="sxs-lookup"><span data-stu-id="cc9fe-284">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="cc9fe-285">ERROR</span><span class="sxs-lookup"><span data-stu-id="cc9fe-285">ERROR</span></span>
* <span data-ttu-id="cc9fe-286">WARNING</span><span class="sxs-lookup"><span data-stu-id="cc9fe-286">WARNING</span></span>
* <span data-ttu-id="cc9fe-287">INFO</span><span class="sxs-lookup"><span data-stu-id="cc9fe-287">INFO</span></span>
* <span data-ttu-id="cc9fe-288">TRACE</span><span class="sxs-lookup"><span data-stu-id="cc9fe-288">TRACE</span></span>

<span data-ttu-id="cc9fe-289">Locais (vários locais são permitidos):</span><span class="sxs-lookup"><span data-stu-id="cc9fe-289">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="cc9fe-290">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="cc9fe-290">CONSOLE</span></span>
* <span data-ttu-id="cc9fe-291">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="cc9fe-291">EVENTLOG</span></span>
* <span data-ttu-id="cc9fe-292">FILE</span><span class="sxs-lookup"><span data-stu-id="cc9fe-292">FILE</span></span>

<span data-ttu-id="cc9fe-293">As configurações do manipulador também podem ser fornecidas por meio de variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-293">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="cc9fe-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; caminho para o arquivo de log de depuração.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-294">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="cc9fe-295">(Padrão: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="cc9fe-295">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="cc9fe-296">`ASPNETCORE_MODULE_DEBUG` &ndash; configuração do nível de depuração.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-296">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="cc9fe-297">**Não** deixe o log de depuração habilitado na implantação por mais tempo que o necessário para solucionar um problema.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-297">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="cc9fe-298">O tamanho do log não é limitado.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-298">The size of the log isn't limited.</span></span> <span data-ttu-id="cc9fe-299">Deixar o log de depuração habilitado pode esgotar o espaço em disco disponível e causar falha no servidor ou no serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-299">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="cc9fe-300">Veja [Configuração com web.config](#configuration-with-webconfig) para obter um exemplo do elemento `aspNetCore` no arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-300">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="cc9fe-301">Modificar o tamanho da pilha</span><span class="sxs-lookup"><span data-stu-id="cc9fe-301">Modify the stack size</span></span>

<span data-ttu-id="cc9fe-302">*Aplica-se somente ao usar o modelo de hospedagem em processo.*</span><span class="sxs-lookup"><span data-stu-id="cc9fe-302">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="cc9fe-303">Configure o tamanho da pilha gerenciada usando a configuração de `stackSize` em bytes em *Web. config*. O tamanho padrão é `1048576` bytes (1 MB).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-303">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="cc9fe-304">A configuração de proxy usa o protocolo HTTP e um token de emparelhamento</span><span class="sxs-lookup"><span data-stu-id="cc9fe-304">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="cc9fe-305">*Só se aplica à hospedagem de fora do processo.*</span><span class="sxs-lookup"><span data-stu-id="cc9fe-305">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="cc9fe-306">O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-306">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="cc9fe-307">Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-307">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="cc9fe-308">Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-308">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="cc9fe-309">O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-309">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="cc9fe-310">O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-310">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="cc9fe-311">O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-311">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="cc9fe-312">Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-312">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="cc9fe-313">A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-313">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="cc9fe-314">Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-314">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="cc9fe-315">Módulo do ASP.NET Core com uma configuração do IIS compartilhada</span><span class="sxs-lookup"><span data-stu-id="cc9fe-315">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="cc9fe-316">O instalador do módulo do ASP.NET Core é executado com os privilégios da conta de **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-316">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="cc9fe-317">Já que a conta de sistema local não tem permissão para modificar o caminho do compartilhamento usado pela configuração compartilhada de IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações de módulo no arquivo *applicationHost.config* no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-317">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="cc9fe-318">Ao usar uma configuração compartilhada de IIS no mesmo computador que a instalação do IIS, execute o instalador do pacote de hospedagem do ASP.NET Core com o parâmetro `OPT_NO_SHARED_CONFIG_CHECK` definido como `1`:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-318">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="cc9fe-319">Quando o caminho para a configuração compartilhada não está no mesmo computador que a instalação do IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-319">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="cc9fe-320">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-320">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="cc9fe-321">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-321">Run the installer.</span></span>
1. <span data-ttu-id="cc9fe-322">Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-322">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="cc9fe-323">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-323">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="cc9fe-324">Versão do módulo e logs do instalador do pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="cc9fe-324">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="cc9fe-325">Para determinar a versão do Módulo do ASP.NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-325">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="cc9fe-326">No sistema de hospedagem, navegue até *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-326">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="cc9fe-327">Localize o arquivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-327">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="cc9fe-328">Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-328">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="cc9fe-329">Selecione a guia **detalhes** . A versão do **arquivo** e a **versão do produto** representam a versão instalada do módulo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-329">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="cc9fe-330">Os logs do instalador do pacote de hospedagem para o módulo são encontrados em *C:\\usuários\\% username%\\AppData\\Local\\Temp*. O arquivo é denominado *dd_DotNetCoreWinSvrHosting__\<carimbo de data/hora > _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-330">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="cc9fe-331">Locais dos arquivos de módulo, de esquema e de configuração</span><span class="sxs-lookup"><span data-stu-id="cc9fe-331">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="cc9fe-332">Módulo</span><span class="sxs-lookup"><span data-stu-id="cc9fe-332">Module</span></span>

<span data-ttu-id="cc9fe-333">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-333">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="cc9fe-334">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-334">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="cc9fe-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-335">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="cc9fe-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-336">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="cc9fe-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-337">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="cc9fe-338">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-338">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="cc9fe-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-339">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="cc9fe-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-340">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="cc9fe-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-341">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="cc9fe-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-342">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="cc9fe-343">Esquema</span><span class="sxs-lookup"><span data-stu-id="cc9fe-343">Schema</span></span>

<span data-ttu-id="cc9fe-344">**IIS**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-344">**IIS**</span></span>

* <span data-ttu-id="cc9fe-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="cc9fe-345">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="cc9fe-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="cc9fe-346">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="cc9fe-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-347">**IIS Express**</span></span>

* <span data-ttu-id="cc9fe-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="cc9fe-348">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="cc9fe-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="cc9fe-349">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="cc9fe-350">Configuração</span><span class="sxs-lookup"><span data-stu-id="cc9fe-350">Configuration</span></span>

<span data-ttu-id="cc9fe-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-351">**IIS**</span></span>

* <span data-ttu-id="cc9fe-352">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="cc9fe-352">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="cc9fe-353">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-353">**IIS Express**</span></span>

* <span data-ttu-id="cc9fe-354">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="cc9fe-354">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="cc9fe-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="cc9fe-355">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="cc9fe-356">Os arquivos podem ser encontrados pesquisando por *aspnetcore* no arquivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-356">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="cc9fe-357">O módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-357">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="cc9fe-358">Hospedar um aplicativo ASP.NET Core dentro do processo de trabalho do IIS (`w3wp.exe`), chamado o [modelo de hospedagem no processo](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-358">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="cc9fe-359">Encaminhar solicitações da Web a um aplicativo ASP.NET Core de back-end que executa o [servidor Kestrel](xref:fundamentals/servers/kestrel), chamado o [modelo de hospedagem de fora do processo](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-359">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="cc9fe-360">Versões do Windows compatíveis:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-360">Supported Windows versions:</span></span>

* <span data-ttu-id="cc9fe-361">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="cc9fe-361">Windows 7 or later</span></span>
* <span data-ttu-id="cc9fe-362">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="cc9fe-362">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="cc9fe-363">Ao fazer uma hospedagem em processo, o módulo usa uma implementação de servidor em processo do IIS, chamado Servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-363">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="cc9fe-364">Ao hospedar de fora do processo, o módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-364">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="cc9fe-365">O módulo não funciona com [http. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-365">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="cc9fe-366">Modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="cc9fe-366">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="cc9fe-367">Modelo de hospedagem em processo</span><span class="sxs-lookup"><span data-stu-id="cc9fe-367">In-process hosting model</span></span>

<span data-ttu-id="cc9fe-368">Para configurar um aplicativo para hospedagem em processo, adicione a propriedade `<AspNetCoreHostingModel>` ao arquivo de projeto do aplicativo com um valor de `InProcess` (a hospedagem de fora do processo é definida com `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="cc9fe-368">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="cc9fe-369">Não há suporte para o modelo de hospedagem em processo para aplicativos ASP.NET Core direcionados ao .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-369">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="cc9fe-370">O valor de `<AspNetCoreHostingModel>` não diferencia maiúsculas de minúsculas e, portanto, `inprocess` e `outofprocess` são valores válidos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-370">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="cc9fe-371">Se a propriedade `<AspNetCoreHostingModel>` não estiver presente no arquivo, o valor padrão será `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-371">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="cc9fe-372">As seguintes características se aplicam ao hospedar em processo:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-372">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="cc9fe-373">O Servidor HTTP do IIS (`IISHttpServer`) é usado, em vez do servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-373">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="cc9fe-374">Em processo, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-374">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="cc9fe-375">Registrar o `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-375">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="cc9fe-376">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-376">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="cc9fe-377">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-377">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="cc9fe-378">O [requestTimeout atributo](#attributes-of-the-aspnetcore-element) não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-378">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="cc9fe-379">Não há suporte para o compartilhamento do pool de aplicativos entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-379">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="cc9fe-380">Use um pool de aplicativos por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-380">Use one app pool per app.</span></span>

* <span data-ttu-id="cc9fe-381">Ao usar a [Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) ou inserir manualmente um [arquivo app_offline.htm na implantação](xref:host-and-deploy/iis/index#locked-deployment-files), o aplicativo talvez não seja desligado imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-381">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="cc9fe-382">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-382">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="cc9fe-383">A arquitetura (número de bit) do aplicativo e o tempo de execução instalado (x64 ou x86) devem corresponder à arquitetura do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-383">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="cc9fe-384">As desconexões do cliente são detectadas.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-384">Client disconnects are detected.</span></span> <span data-ttu-id="cc9fe-385">O token de cancelamento [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) é cancelado quando o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-385">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="cc9fe-386">No ASP.NET Core 2.2.1 ou anterior, <xref:System.IO.Directory.GetCurrentDirectory*> retorna o diretório de trabalho do processo iniciado pelo IIS em vez de o diretório do aplicativo (por exemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-386">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="cc9fe-387">Para obter o código de exemplo que define o diretório atual do aplicativo, confira a [classe CurrentDirectoryHelpers](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-387">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="cc9fe-388">Chame o método `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-388">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="cc9fe-389">As chamadas seguintes a <xref:System.IO.Directory.GetCurrentDirectory*> fornecem o diretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-389">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="cc9fe-390">Ao hospedar em processo, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-390">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="cc9fe-391">Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-391">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="cc9fe-392">Quando a transformação de declarações com uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, chame <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para adicionar serviços de autenticação:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-392">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="cc9fe-393">Modelo de hospedagem de fora do processo</span><span class="sxs-lookup"><span data-stu-id="cc9fe-393">Out-of-process hosting model</span></span>

<span data-ttu-id="cc9fe-394">Para configurar um aplicativo para hospedagem fora do processo, use qualquer uma das abordagens a seguir no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-394">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="cc9fe-395">não especifique a propriedade `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-395">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="cc9fe-396">Se a propriedade `<AspNetCoreHostingModel>` não estiver presente no arquivo, o valor padrão será `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-396">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="cc9fe-397">Defina o valor da propriedade `<AspNetCoreHostingModel>` como `OutOfProcess` (a hospedagem no processo é definida com `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="cc9fe-397">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="cc9fe-398">O valor não diferencia maiúsculas de minúsculas, portanto `inprocess` e `outofprocess` são valores válidos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-398">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="cc9fe-399">O servidor [Kestrel](xref:fundamentals/servers/kestrel) é usado, em vez do servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-399">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="cc9fe-400">Fora do processo, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-400">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="cc9fe-401">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-401">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="cc9fe-402">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-402">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="cc9fe-403">Alterações no modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="cc9fe-403">Hosting model changes</span></span>

<span data-ttu-id="cc9fe-404">Se a configuração `hostingModel` for alterada no arquivo *web.config* (explicado na seção [Configuração a Web.config](#configuration-with-webconfig)), o módulo reciclará o processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-404">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="cc9fe-405">Para o IIS Express, o módulo não recicla o processo de trabalho, mas em vez disso, dispara um desligamento normal do processo atual do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-405">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="cc9fe-406">A próxima solicitação para o aplicativo gera um novo processo do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-406">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="cc9fe-407">Nome do processo</span><span class="sxs-lookup"><span data-stu-id="cc9fe-407">Process name</span></span>

<span data-ttu-id="cc9fe-408">`Process.GetCurrentProcess().ProcessName` relata `w3wp`/`iisexpress` (em processo) ou `dotnet` (fora do processo).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-408">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="cc9fe-409">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-409">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="cc9fe-410">Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-410">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="cc9fe-411">O Módulo do ASP.NET Core também pode:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-411">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="cc9fe-412">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-412">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="cc9fe-413">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-413">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="cc9fe-414">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-414">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="cc9fe-415">Como instalar e usar o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc9fe-415">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="cc9fe-416">Para obter instruções de como instalar o Módulo do ASP.NET Core, confira [Instalar o Pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-416">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="cc9fe-417">Configuração com web.config</span><span class="sxs-lookup"><span data-stu-id="cc9fe-417">Configuration with web.config</span></span>

<span data-ttu-id="cc9fe-418">O Módulo do ASP.NET Core está configurado com a seção `aspNetCore` do nó `system.webServer` no arquivo *web.config* do site.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-418">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="cc9fe-419">O seguinte arquivo *web.config* é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o Módulo do ASP.NET Core para manipular solicitações de site:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-419">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="cc9fe-420">O seguinte *web.config* é publicado para uma [implantação autossuficiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="cc9fe-420">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="cc9fe-421">A propriedade <xref:System.Configuration.SectionInformation.InheritInChildApplications*> é definida como `false` para indicar que as configurações especificadas no elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) não são herdadas por aplicativos que residem em um subdiretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-421">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="cc9fe-422">Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-422">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="cc9fe-423">O caminho salva logs de stdout para a pasta *LogFiles*, que é um local criado automaticamente pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-423">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="cc9fe-424">Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-424">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="cc9fe-425">Atributos do elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="cc9fe-425">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="cc9fe-426">Atributo</span><span class="sxs-lookup"><span data-stu-id="cc9fe-426">Attribute</span></span> | <span data-ttu-id="cc9fe-427">Descrição</span><span class="sxs-lookup"><span data-stu-id="cc9fe-427">Description</span></span> | <span data-ttu-id="cc9fe-428">Padrão</span><span class="sxs-lookup"><span data-stu-id="cc9fe-428">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="cc9fe-429">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-429">Optional string attribute.</span></span></p><p><span data-ttu-id="cc9fe-430">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-430">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="cc9fe-431">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-431">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cc9fe-432">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-432">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="cc9fe-433">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cc9fe-434">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-434">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="cc9fe-435">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-435">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="cc9fe-436">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-436">Optional string attribute.</span></span></p><p><span data-ttu-id="cc9fe-437">Especifica o modelo de hospedagem como em processo (`InProcess`/`inprocess`) ou fora do processo (`OutOfProcess`/`outofprocess`).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-437">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="cc9fe-438">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-438">Optional integer attribute.</span></span></p><p><span data-ttu-id="cc9fe-439">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-439">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="cc9fe-440">&dagger;Para hospedagem em processo, o valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-440">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="cc9fe-441">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-441">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="cc9fe-442">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-442">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="cc9fe-443">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-443">Default: `1`</span></span><br><span data-ttu-id="cc9fe-444">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-444">Min: `1`</span></span><br><span data-ttu-id="cc9fe-445">Máx.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="cc9fe-445">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="cc9fe-446">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-446">Required string attribute.</span></span></p><p><span data-ttu-id="cc9fe-447">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-447">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="cc9fe-448">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-448">Relative paths are supported.</span></span> <span data-ttu-id="cc9fe-449">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-449">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="cc9fe-450">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-450">Optional integer attribute.</span></span></p><p><span data-ttu-id="cc9fe-451">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-451">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="cc9fe-452">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-452">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="cc9fe-453">Sem suporte com hospedagem padrão.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-453">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="cc9fe-454">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-454">Default: `10`</span></span><br><span data-ttu-id="cc9fe-455">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-455">Min: `0`</span></span><br><span data-ttu-id="cc9fe-456">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-456">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="cc9fe-457">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-457">Optional timespan attribute.</span></span></p><p><span data-ttu-id="cc9fe-458">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-458">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="cc9fe-459">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-459">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="cc9fe-460">Não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-460">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="cc9fe-461">Para a hospedagem em processo, o módulo aguarda o aplicativo processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-461">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="cc9fe-462">Os valores válidos para segmentos de minutos e segundos da cadeia de caracteres estão no intervalo 0 a 59.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-462">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="cc9fe-463">O uso de **60** no valor para minutos ou segundos resulta em um *500 - Erro Interno do Servidor*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-463">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="cc9fe-464">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-464">Default: `00:02:00`</span></span><br><span data-ttu-id="cc9fe-465">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-465">Min: `00:00:00`</span></span><br><span data-ttu-id="cc9fe-466">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-466">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="cc9fe-467">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-467">Optional integer attribute.</span></span></p><p><span data-ttu-id="cc9fe-468">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-468">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="cc9fe-469">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-469">Default: `10`</span></span><br><span data-ttu-id="cc9fe-470">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-470">Min: `0`</span></span><br><span data-ttu-id="cc9fe-471">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-471">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="cc9fe-472">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-472">Optional integer attribute.</span></span></p><p><span data-ttu-id="cc9fe-473">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-473">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="cc9fe-474">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-474">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="cc9fe-475">O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-475">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="cc9fe-476">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-476">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="cc9fe-477">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-477">Default: `120`</span></span><br><span data-ttu-id="cc9fe-478">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-478">Min: `0`</span></span><br><span data-ttu-id="cc9fe-479">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-479">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="cc9fe-480">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-480">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cc9fe-481">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-481">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="cc9fe-482">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-482">Optional string attribute.</span></span></p><p><span data-ttu-id="cc9fe-483">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-483">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="cc9fe-484">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-484">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="cc9fe-485">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-485">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="cc9fe-486">Todas as pastas fornecidas no caminho são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-486">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="cc9fe-487">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo ( *.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-487">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="cc9fe-488">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-488">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="cc9fe-489">Definindo variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="cc9fe-489">Setting environment variables</span></span>

<span data-ttu-id="cc9fe-490">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-490">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="cc9fe-491">Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-491">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="cc9fe-492">Variáveis de ambiente definidas nesta seção têm precedência sobre variáveis de ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-492">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="cc9fe-493">O exemplo a seguir define duas variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-493">The following example sets two environment variables.</span></span> <span data-ttu-id="cc9fe-494">`ASPNETCORE_ENVIRONMENT` configura o ambiente do aplicativo para `Development`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-494">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="cc9fe-495">Um desenvolvedor pode definir esse valor temporariamente no arquivo *web.config* para forçar o carregamento da [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) ao depurar uma exceção de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-495">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="cc9fe-496">`CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-496">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="cc9fe-497">Em vez de configurar o ambiente diretamente no *web.config*, você pode incluir a propriedade `<EnvironmentName>` no perfil de publicação ( *.pubxml*) ou no perfil de projeto.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-497">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="cc9fe-498">Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-498">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="cc9fe-499">Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-499">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="cc9fe-500">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="cc9fe-500">app_offline.htm</span></span>

<span data-ttu-id="cc9fe-501">Se um arquivo com o nome *app_offline.htm* é detectado no diretório raiz de um aplicativo, o Módulo do ASP.NET Core tenta desligar normalmente o aplicativo e parar o processamento de solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-501">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="cc9fe-502">Se o aplicativo ainda está em execução após o número de segundos definido em `shutdownTimeLimit`, o Módulo do ASP.NET Core encerra o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-502">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="cc9fe-503">Enquanto o arquivo *app_offline.htm* estiver presente, o Módulo do ASP.NET Core responderá às solicitações enviando o conteúdo do arquivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-503">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="cc9fe-504">Quando o arquivo *app_offline.htm* é removido, a próxima solicitação inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-504">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="cc9fe-505">Ao usar o modelo de hospedagem de fora do processo, talvez o aplicativo não desligue imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-505">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="cc9fe-506">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-506">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="cc9fe-507">Página de erro de inicialização</span><span class="sxs-lookup"><span data-stu-id="cc9fe-507">Start-up error page</span></span>

<span data-ttu-id="cc9fe-508">A hospedagem em processo e fora do processo produzem páginas de erro personalizadas quando falham ao iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-508">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="cc9fe-509">Se o Módulo do ASP.NET Core falhar ao encontrar o manipulador de solicitação em processo ou fora do processo, uma página de código de status *500.0 – falha ao carregar manipulador no processo/fora do processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-509">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="cc9fe-510">No caso da hospedagem em processo, se o módulo do ASP.NET Core falhar ao iniciar o aplicativo, uma página de código de status *500.30 – falha ao iniciar* será exibida.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-510">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="cc9fe-511">Para hospedagem fora do processo, se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-511">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="cc9fe-512">Para suprimir essa página e reverter para a página de código de status 5xx padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-512">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="cc9fe-513">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-513">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="cc9fe-514">Criação de log e redirecionamento</span><span class="sxs-lookup"><span data-stu-id="cc9fe-514">Log creation and redirection</span></span>

<span data-ttu-id="cc9fe-515">O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-515">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="cc9fe-516">Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-516">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="cc9fe-517">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-517">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="cc9fe-518">Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-518">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="cc9fe-519">É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-519">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="cc9fe-520">O uso do log stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo ao hospedar no IIS ou ao usar o [suporte a tempo de desenvolvimento para o IIS com o Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), não durante a depuração local e a execução do aplicativo com IIS Express.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-520">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="cc9fe-521">Não use o log de stdout para fins gerais de registro em log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-521">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="cc9fe-522">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-522">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="cc9fe-523">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-523">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="cc9fe-524">Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-524">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="cc9fe-525">O nome do arquivo de log é composto por meio do acréscimo do carimbo de data/hora, da ID do processo e da extensão de arquivo ( *.log*) para o último segmento do caminho `stdoutLogFile` (normalmente *stdout*), delimitados por sublinhados.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-525">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="cc9fe-526">Se o caminho `stdoutLogFile` termina com *stdout*, um log para um aplicativo com um PID de 1934, criado em 5/2/2018 às 19:42:32, tem o nome de arquivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-526">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="cc9fe-527">Se `stdoutLogEnabled` for falso, os erros que ocorrerem na inicialização do aplicativo serão capturados e emitidos no log de eventos até 30 KB.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-527">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="cc9fe-528">Após a inicialização, todos os logs adicionais são descartados.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-528">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="cc9fe-529">O elemento `aspNetCore` de exemplo a seguir configura o log de stdout no caminho relativo `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-529">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="cc9fe-530">Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-530">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="cc9fe-531">Ao publicar um aplicativo para implantação Azure App serviço, o SDK da Web define o valor de `stdoutLogFile` como `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-531">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="cc9fe-532">A variável de ambiente `%home` é predefinida para aplicativos hospedados pelo serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-532">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="cc9fe-533">Para obter mais informações sobre formatos de caminho, consulte [formatos de caminho de arquivo em sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-533">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="cc9fe-534">Logs de diagnóstico avançados</span><span class="sxs-lookup"><span data-stu-id="cc9fe-534">Enhanced diagnostic logs</span></span>

<span data-ttu-id="cc9fe-535">O Módulo do ASP.NET Core é configurável para fornecer logs de diagnóstico avançados.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-535">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="cc9fe-536">Adicione o elemento `<handlerSettings>` ao elemento `<aspNetCore>` em *Web. config*. Definir a `debugLevel` como `TRACE` expõe uma fidelidade mais alta das informações de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-536">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="cc9fe-537">As pastas no caminho fornecido para o valor `<handlerSetting>` (*logs* no exemplo anterior) não são criadas automaticamente pelo módulo e devem existir previamente na implantação.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-537">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="cc9fe-538">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-538">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="cc9fe-539">Os valores do nível de depuração (`debugLevel`) podem incluir o nível e a localização.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-539">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="cc9fe-540">Níveis (na ordem do menos para o mais detalhado):</span><span class="sxs-lookup"><span data-stu-id="cc9fe-540">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="cc9fe-541">ERROR</span><span class="sxs-lookup"><span data-stu-id="cc9fe-541">ERROR</span></span>
* <span data-ttu-id="cc9fe-542">WARNING</span><span class="sxs-lookup"><span data-stu-id="cc9fe-542">WARNING</span></span>
* <span data-ttu-id="cc9fe-543">INFO</span><span class="sxs-lookup"><span data-stu-id="cc9fe-543">INFO</span></span>
* <span data-ttu-id="cc9fe-544">TRACE</span><span class="sxs-lookup"><span data-stu-id="cc9fe-544">TRACE</span></span>

<span data-ttu-id="cc9fe-545">Locais (vários locais são permitidos):</span><span class="sxs-lookup"><span data-stu-id="cc9fe-545">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="cc9fe-546">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="cc9fe-546">CONSOLE</span></span>
* <span data-ttu-id="cc9fe-547">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="cc9fe-547">EVENTLOG</span></span>
* <span data-ttu-id="cc9fe-548">FILE</span><span class="sxs-lookup"><span data-stu-id="cc9fe-548">FILE</span></span>

<span data-ttu-id="cc9fe-549">As configurações do manipulador também podem ser fornecidas por meio de variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-549">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="cc9fe-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; caminho para o arquivo de log de depuração.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-550">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="cc9fe-551">(Padrão: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="cc9fe-551">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="cc9fe-552">`ASPNETCORE_MODULE_DEBUG` &ndash; configuração do nível de depuração.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-552">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="cc9fe-553">**Não** deixe o log de depuração habilitado na implantação por mais tempo que o necessário para solucionar um problema.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-553">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="cc9fe-554">O tamanho do log não é limitado.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-554">The size of the log isn't limited.</span></span> <span data-ttu-id="cc9fe-555">Deixar o log de depuração habilitado pode esgotar o espaço em disco disponível e causar falha no servidor ou no serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-555">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="cc9fe-556">Veja [Configuração com web.config](#configuration-with-webconfig) para obter um exemplo do elemento `aspNetCore` no arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-556">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="cc9fe-557">A configuração de proxy usa o protocolo HTTP e um token de emparelhamento</span><span class="sxs-lookup"><span data-stu-id="cc9fe-557">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="cc9fe-558">*Só se aplica à hospedagem de fora do processo.*</span><span class="sxs-lookup"><span data-stu-id="cc9fe-558">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="cc9fe-559">O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-559">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="cc9fe-560">Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-560">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="cc9fe-561">Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-561">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="cc9fe-562">O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-562">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="cc9fe-563">O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-563">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="cc9fe-564">O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-564">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="cc9fe-565">Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-565">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="cc9fe-566">A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-566">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="cc9fe-567">Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-567">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="cc9fe-568">Módulo do ASP.NET Core com uma configuração do IIS compartilhada</span><span class="sxs-lookup"><span data-stu-id="cc9fe-568">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="cc9fe-569">O instalador do módulo do ASP.NET Core é executado com os privilégios da conta de **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-569">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="cc9fe-570">Já que a conta de sistema local não tem permissão para modificar o caminho do compartilhamento usado pela configuração compartilhada de IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações de módulo no arquivo *applicationHost.config* no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-570">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="cc9fe-571">Ao usar uma configuração compartilhada de IIS no mesmo computador que a instalação do IIS, execute o instalador do pacote de hospedagem do ASP.NET Core com o parâmetro `OPT_NO_SHARED_CONFIG_CHECK` definido como `1`:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-571">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="cc9fe-572">Quando o caminho para a configuração compartilhada não está no mesmo computador que a instalação do IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-572">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="cc9fe-573">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-573">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="cc9fe-574">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-574">Run the installer.</span></span>
1. <span data-ttu-id="cc9fe-575">Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-575">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="cc9fe-576">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-576">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="cc9fe-577">Versão do módulo e logs do instalador do pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="cc9fe-577">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="cc9fe-578">Para determinar a versão do Módulo do ASP.NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-578">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="cc9fe-579">No sistema de hospedagem, navegue até *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-579">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="cc9fe-580">Localize o arquivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-580">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="cc9fe-581">Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-581">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="cc9fe-582">Selecione a guia **detalhes** . A versão do **arquivo** e a **versão do produto** representam a versão instalada do módulo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-582">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="cc9fe-583">Os logs do instalador do pacote de hospedagem para o módulo são encontrados em *C:\\usuários\\% username%\\AppData\\Local\\Temp*. O arquivo é denominado *dd_DotNetCoreWinSvrHosting__\<carimbo de data/hora > _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-583">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="cc9fe-584">Locais dos arquivos de módulo, de esquema e de configuração</span><span class="sxs-lookup"><span data-stu-id="cc9fe-584">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="cc9fe-585">Módulo</span><span class="sxs-lookup"><span data-stu-id="cc9fe-585">Module</span></span>

<span data-ttu-id="cc9fe-586">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-586">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="cc9fe-587">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-587">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="cc9fe-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-588">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="cc9fe-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-589">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="cc9fe-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-590">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="cc9fe-591">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-591">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="cc9fe-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-592">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="cc9fe-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-593">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="cc9fe-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-594">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="cc9fe-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-595">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="cc9fe-596">Esquema</span><span class="sxs-lookup"><span data-stu-id="cc9fe-596">Schema</span></span>

<span data-ttu-id="cc9fe-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-597">**IIS**</span></span>

* <span data-ttu-id="cc9fe-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="cc9fe-598">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="cc9fe-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="cc9fe-599">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="cc9fe-600">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-600">**IIS Express**</span></span>

* <span data-ttu-id="cc9fe-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="cc9fe-601">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="cc9fe-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="cc9fe-602">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="cc9fe-603">Configuração</span><span class="sxs-lookup"><span data-stu-id="cc9fe-603">Configuration</span></span>

<span data-ttu-id="cc9fe-604">**IIS**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-604">**IIS**</span></span>

* <span data-ttu-id="cc9fe-605">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="cc9fe-605">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="cc9fe-606">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-606">**IIS Express**</span></span>

* <span data-ttu-id="cc9fe-607">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="cc9fe-607">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="cc9fe-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="cc9fe-608">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="cc9fe-609">Os arquivos podem ser encontrados pesquisando por *aspnetcore* no arquivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-609">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="cc9fe-610">O Módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para encaminhar solicitações da Web para aplicativos ASP.NET Core de back-end.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-610">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="cc9fe-611">Versões do Windows compatíveis:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-611">Supported Windows versions:</span></span>

* <span data-ttu-id="cc9fe-612">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="cc9fe-612">Windows 7 or later</span></span>
* <span data-ttu-id="cc9fe-613">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="cc9fe-613">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="cc9fe-614">O módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-614">The module only works with Kestrel.</span></span> <span data-ttu-id="cc9fe-615">O módulo é incompatível com [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-615">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="cc9fe-616">Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo também realiza o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-616">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="cc9fe-617">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo quando ele falha.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-617">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="cc9fe-618">Isso é basicamente o mesmo comportamento que o dos aplicativos ASP.NET 4.x que são executados dentro do processo do IIS e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-618">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="cc9fe-619">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-619">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Módulo do ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="cc9fe-621">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-621">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="cc9fe-622">O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-622">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="cc9fe-623">O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-623">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="cc9fe-624">O módulo especifica a porta por meio de uma variável de ambiente na inicialização e o [middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura o servidor para escutar em `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-624">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="cc9fe-625">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-625">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="cc9fe-626">O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-626">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="cc9fe-627">Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-627">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="cc9fe-628">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-628">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="cc9fe-629">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-629">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="cc9fe-630">A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-630">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="cc9fe-631">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-631">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="cc9fe-632">Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-632">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="cc9fe-633">O Módulo do ASP.NET Core também pode:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-633">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="cc9fe-634">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-634">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="cc9fe-635">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-635">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="cc9fe-636">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-636">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="cc9fe-637">Como instalar e usar o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc9fe-637">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="cc9fe-638">Para obter instruções de como instalar o Módulo do ASP.NET Core, confira [Instalar o Pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-638">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="cc9fe-639">Configuração com web.config</span><span class="sxs-lookup"><span data-stu-id="cc9fe-639">Configuration with web.config</span></span>

<span data-ttu-id="cc9fe-640">O Módulo do ASP.NET Core está configurado com a seção `aspNetCore` do nó `system.webServer` no arquivo *web.config* do site.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-640">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="cc9fe-641">O seguinte arquivo *web.config* é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o Módulo do ASP.NET Core para manipular solicitações de site:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-641">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="cc9fe-642">O seguinte *web.config* é publicado para uma [implantação autossuficiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="cc9fe-642">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="cc9fe-643">Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-643">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="cc9fe-644">O caminho salva logs de stdout para a pasta *LogFiles*, que é um local criado automaticamente pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-644">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="cc9fe-645">Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-645">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="cc9fe-646">Atributos do elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="cc9fe-646">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="cc9fe-647">Atributo</span><span class="sxs-lookup"><span data-stu-id="cc9fe-647">Attribute</span></span> | <span data-ttu-id="cc9fe-648">Descrição</span><span class="sxs-lookup"><span data-stu-id="cc9fe-648">Description</span></span> | <span data-ttu-id="cc9fe-649">Padrão</span><span class="sxs-lookup"><span data-stu-id="cc9fe-649">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="cc9fe-650">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-650">Optional string attribute.</span></span></p><p><span data-ttu-id="cc9fe-651">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-651">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="cc9fe-652">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-652">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cc9fe-653">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-653">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="cc9fe-654">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-654">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cc9fe-655">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-655">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="cc9fe-656">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-656">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="cc9fe-657">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-657">Optional integer attribute.</span></span></p><p><span data-ttu-id="cc9fe-658">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-658">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="cc9fe-659">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-659">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="cc9fe-660">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-660">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="cc9fe-661">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-661">Default: `1`</span></span><br><span data-ttu-id="cc9fe-662">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-662">Min: `1`</span></span><br><span data-ttu-id="cc9fe-663">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-663">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="cc9fe-664">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-664">Required string attribute.</span></span></p><p><span data-ttu-id="cc9fe-665">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-665">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="cc9fe-666">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-666">Relative paths are supported.</span></span> <span data-ttu-id="cc9fe-667">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-667">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="cc9fe-668">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-668">Optional integer attribute.</span></span></p><p><span data-ttu-id="cc9fe-669">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-669">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="cc9fe-670">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-670">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="cc9fe-671">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-671">Default: `10`</span></span><br><span data-ttu-id="cc9fe-672">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-672">Min: `0`</span></span><br><span data-ttu-id="cc9fe-673">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-673">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="cc9fe-674">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-674">Optional timespan attribute.</span></span></p><p><span data-ttu-id="cc9fe-675">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-675">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="cc9fe-676">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-676">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="cc9fe-677">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-677">Default: `00:02:00`</span></span><br><span data-ttu-id="cc9fe-678">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-678">Min: `00:00:00`</span></span><br><span data-ttu-id="cc9fe-679">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-679">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="cc9fe-680">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-680">Optional integer attribute.</span></span></p><p><span data-ttu-id="cc9fe-681">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-681">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="cc9fe-682">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-682">Default: `10`</span></span><br><span data-ttu-id="cc9fe-683">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-683">Min: `0`</span></span><br><span data-ttu-id="cc9fe-684">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-684">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="cc9fe-685">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-685">Optional integer attribute.</span></span></p><p><span data-ttu-id="cc9fe-686">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-686">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="cc9fe-687">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-687">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="cc9fe-688">O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-688">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="cc9fe-689">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-689">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="cc9fe-690">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-690">Default: `120`</span></span><br><span data-ttu-id="cc9fe-691">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-691">Min: `0`</span></span><br><span data-ttu-id="cc9fe-692">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="cc9fe-692">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="cc9fe-693">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-693">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="cc9fe-694">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-694">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="cc9fe-695">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-695">Optional string attribute.</span></span></p><p><span data-ttu-id="cc9fe-696">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-696">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="cc9fe-697">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-697">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="cc9fe-698">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-698">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="cc9fe-699">As pastas fornecidas no caminho devem existir para que o módulo crie o arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-699">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="cc9fe-700">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo ( *.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-700">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="cc9fe-701">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-701">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="cc9fe-702">Definindo variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="cc9fe-702">Setting environment variables</span></span>

<span data-ttu-id="cc9fe-703">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-703">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="cc9fe-704">Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-704">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="cc9fe-705">As variáveis de ambiente definidas nesta seção são conflitantes com as variáveis de ambiente do sistema definidas com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-705">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="cc9fe-706">Quando a variável de ambiente é definida no arquivo *web.config* e no nível do sistema do Windows, o valor do arquivo *web.config* fica anexado ao valor da variável de ambiente do sistema (por exemplo, `ASPNETCORE_ENVIRONMENT: Development;Development`), o que impede a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-706">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="cc9fe-707">O exemplo a seguir define duas variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-707">The following example sets two environment variables.</span></span> <span data-ttu-id="cc9fe-708">`ASPNETCORE_ENVIRONMENT` configura o ambiente do aplicativo para `Development`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-708">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="cc9fe-709">Um desenvolvedor pode definir esse valor temporariamente no arquivo *web.config* para forçar o carregamento da [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) ao depurar uma exceção de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-709">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="cc9fe-710">`CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-710">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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

> [!WARNING]
> <span data-ttu-id="cc9fe-711">Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-711">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="cc9fe-712">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="cc9fe-712">app_offline.htm</span></span>

<span data-ttu-id="cc9fe-713">Se um arquivo com o nome *app_offline.htm* é detectado no diretório raiz de um aplicativo, o Módulo do ASP.NET Core tenta desligar normalmente o aplicativo e parar o processamento de solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-713">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="cc9fe-714">Se o aplicativo ainda está em execução após o número de segundos definido em `shutdownTimeLimit`, o Módulo do ASP.NET Core encerra o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-714">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="cc9fe-715">Enquanto o arquivo *app_offline.htm* estiver presente, o Módulo do ASP.NET Core responderá às solicitações enviando o conteúdo do arquivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-715">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="cc9fe-716">Quando o arquivo *app_offline.htm* é removido, a próxima solicitação inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-716">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="cc9fe-717">Página de erro de inicialização</span><span class="sxs-lookup"><span data-stu-id="cc9fe-717">Start-up error page</span></span>

<span data-ttu-id="cc9fe-718">Se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-718">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="cc9fe-719">Para omitir esta página e reverter para a página de código de status 502 padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-719">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="cc9fe-720">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-720">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Página de código de status 502.5 – Falha do Processo](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="cc9fe-722">Criação de log e redirecionamento</span><span class="sxs-lookup"><span data-stu-id="cc9fe-722">Log creation and redirection</span></span>

<span data-ttu-id="cc9fe-723">O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-723">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="cc9fe-724">Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-724">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="cc9fe-725">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-725">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="cc9fe-726">Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-726">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="cc9fe-727">É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-727">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="cc9fe-728">O uso do log stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo ao hospedar no IIS ou ao usar o [suporte a tempo de desenvolvimento para o IIS com o Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), não durante a depuração local e a execução do aplicativo com IIS Express.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-728">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="cc9fe-729">Não use o log de stdout para fins gerais de registro em log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-729">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="cc9fe-730">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-730">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="cc9fe-731">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-731">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="cc9fe-732">Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-732">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="cc9fe-733">O nome do arquivo de log é composto por meio do acréscimo do carimbo de data/hora, da ID do processo e da extensão de arquivo ( *.log*) para o último segmento do caminho `stdoutLogFile` (normalmente *stdout*), delimitados por sublinhados.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-733">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="cc9fe-734">Se o caminho `stdoutLogFile` termina com *stdout*, um log para um aplicativo com um PID de 1934, criado em 5/2/2018 às 19:42:32, tem o nome de arquivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-734">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="cc9fe-735">O elemento `aspNetCore` de exemplo a seguir configura o log de stdout no caminho relativo `.\log\`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-735">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="cc9fe-736">Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-736">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="cc9fe-737">Ao publicar um aplicativo para implantação Azure App serviço, o SDK da Web define o valor de `stdoutLogFile` como `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-737">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="cc9fe-738">A variável de ambiente `%home` é predefinida para aplicativos hospedados pelo serviço Azure App.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-738">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="cc9fe-739">Para criar regras de filtro de log, consulte as seções de [configuração](xref:fundamentals/logging/index#log-filtering) e [filtragem de log](xref:fundamentals/logging/index#log-filtering) da documentação de log de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-739">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="cc9fe-740">Para obter mais informações sobre formatos de caminho, consulte [formatos de caminho de arquivo em sistemas Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="cc9fe-740">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="cc9fe-741">A configuração de proxy usa o protocolo HTTP e um token de emparelhamento</span><span class="sxs-lookup"><span data-stu-id="cc9fe-741">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="cc9fe-742">O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-742">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="cc9fe-743">Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-743">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="cc9fe-744">Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-744">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="cc9fe-745">O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-745">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="cc9fe-746">O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-746">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="cc9fe-747">O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-747">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="cc9fe-748">Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-748">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="cc9fe-749">A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-749">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="cc9fe-750">Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-750">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="cc9fe-751">Módulo do ASP.NET Core com uma configuração do IIS compartilhada</span><span class="sxs-lookup"><span data-stu-id="cc9fe-751">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="cc9fe-752">O instalador do módulo do ASP.NET Core é executado com os privilégios da conta de **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-752">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="cc9fe-753">Já que a conta de sistema local não tem permissão para modificar o caminho do compartilhamento usado pela configuração compartilhada de IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações de módulo no arquivo *applicationHost.config* no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-753">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="cc9fe-754">Ao usar uma configuração compartilhada de IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-754">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="cc9fe-755">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-755">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="cc9fe-756">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-756">Run the installer.</span></span>
1. <span data-ttu-id="cc9fe-757">Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-757">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="cc9fe-758">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-758">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="cc9fe-759">Versão do módulo e logs do instalador do pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="cc9fe-759">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="cc9fe-760">Para determinar a versão do Módulo do ASP.NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="cc9fe-760">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="cc9fe-761">No sistema de hospedagem, navegue até *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-761">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="cc9fe-762">Localize o arquivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-762">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="cc9fe-763">Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-763">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="cc9fe-764">Selecione a guia **detalhes** . A versão do **arquivo** e a **versão do produto** representam a versão instalada do módulo.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-764">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="cc9fe-765">Os logs do instalador do pacote de hospedagem para o módulo são encontrados em *C:\\usuários\\% username%\\AppData\\Local\\Temp*. O arquivo é denominado *dd_DotNetCoreWinSvrHosting__\<carimbo de data/hora > _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-765">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="cc9fe-766">Locais dos arquivos de módulo, de esquema e de configuração</span><span class="sxs-lookup"><span data-stu-id="cc9fe-766">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="cc9fe-767">Módulo</span><span class="sxs-lookup"><span data-stu-id="cc9fe-767">Module</span></span>

<span data-ttu-id="cc9fe-768">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-768">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="cc9fe-769">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-769">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="cc9fe-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-770">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="cc9fe-771">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-771">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="cc9fe-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-772">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="cc9fe-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="cc9fe-773">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="cc9fe-774">Esquema</span><span class="sxs-lookup"><span data-stu-id="cc9fe-774">Schema</span></span>

<span data-ttu-id="cc9fe-775">**IIS**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-775">**IIS**</span></span>

* <span data-ttu-id="cc9fe-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="cc9fe-776">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="cc9fe-777">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-777">**IIS Express**</span></span>

* <span data-ttu-id="cc9fe-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="cc9fe-778">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="cc9fe-779">Configuração</span><span class="sxs-lookup"><span data-stu-id="cc9fe-779">Configuration</span></span>

<span data-ttu-id="cc9fe-780">**IIS**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-780">**IIS**</span></span>

* <span data-ttu-id="cc9fe-781">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="cc9fe-781">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="cc9fe-782">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="cc9fe-782">**IIS Express**</span></span>

* <span data-ttu-id="cc9fe-783">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="cc9fe-783">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="cc9fe-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="cc9fe-784">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="cc9fe-785">Os arquivos podem ser encontrados pesquisando por *aspnetcore* no arquivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="cc9fe-785">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="cc9fe-786">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cc9fe-786">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="cc9fe-787">Repositório do GitHub do Módulo do ASP.NET Core (origem de referência)</span><span class="sxs-lookup"><span data-stu-id="cc9fe-787">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
