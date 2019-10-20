---
title: Módulo do ASP.NET Core
author: guardrex
description: Saiba como configurar o módulo do ASP.NET Core para hospedar aplicativos do ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/13/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 917ee462a8f9592120685b53d059a661cb4a7452
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333887"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="03a65-103">Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03a65-103">ASP.NET Core Module</span></span>

<span data-ttu-id="03a65-104">Por [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="03a65-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="03a65-105">O módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para:</span><span class="sxs-lookup"><span data-stu-id="03a65-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="03a65-106">Hospedar um aplicativo ASP.NET Core dentro do processo de trabalho do IIS (`w3wp.exe`), chamado o [modelo de hospedagem no processo](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="03a65-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="03a65-107">Encaminhar solicitações da Web a um aplicativo ASP.NET Core de back-end que executa o [servidor Kestrel](xref:fundamentals/servers/kestrel), chamado o [modelo de hospedagem de fora do processo](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="03a65-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="03a65-108">Versões do Windows compatíveis:</span><span class="sxs-lookup"><span data-stu-id="03a65-108">Supported Windows versions:</span></span>

* <span data-ttu-id="03a65-109">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="03a65-109">Windows 7 or later</span></span>
* <span data-ttu-id="03a65-110">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="03a65-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="03a65-111">Ao fazer uma hospedagem em processo, o módulo usa uma implementação de servidor em processo do IIS, chamado Servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="03a65-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="03a65-112">Ao hospedar de fora do processo, o módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="03a65-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="03a65-113">O módulo não funciona com [http. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="03a65-113">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="03a65-114">Modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="03a65-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="03a65-115">Modelo de hospedagem em processo</span><span class="sxs-lookup"><span data-stu-id="03a65-115">In-process hosting model</span></span>

<span data-ttu-id="03a65-116">ASP.NET Core aplicativos assumem como padrão o modelo de hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="03a65-116">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="03a65-117">As seguintes características se aplicam ao hospedar em processo:</span><span class="sxs-lookup"><span data-stu-id="03a65-117">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="03a65-118">O Servidor HTTP do IIS (`IISHttpServer`) é usado, em vez do servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="03a65-118">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="03a65-119">Em processo, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:</span><span class="sxs-lookup"><span data-stu-id="03a65-119">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="03a65-120">Registrar o `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="03a65-120">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="03a65-121">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="03a65-121">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="03a65-122">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="03a65-122">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="03a65-123">O [requestTimeout atributo](#attributes-of-the-aspnetcore-element) não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="03a65-123">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="03a65-124">Não há suporte para o compartilhamento do pool de aplicativos entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="03a65-124">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="03a65-125">Use um pool de aplicativos por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-125">Use one app pool per app.</span></span>

* <span data-ttu-id="03a65-126">Ao usar a [Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) ou inserir manualmente um [arquivo app_offline.htm na implantação](xref:host-and-deploy/iis/index#locked-deployment-files), o aplicativo talvez não seja desligado imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="03a65-126">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="03a65-127">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-127">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="03a65-128">A arquitetura (número de bit) do aplicativo e o tempo de execução instalado (x64 ou x86) devem corresponder à arquitetura do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="03a65-128">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="03a65-129">As desconexões do cliente são detectadas.</span><span class="sxs-lookup"><span data-stu-id="03a65-129">Client disconnects are detected.</span></span> <span data-ttu-id="03a65-130">O token de cancelamento [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) é cancelado quando o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="03a65-130">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="03a65-131">No ASP.NET Core 2.2.1 ou anterior, <xref:System.IO.Directory.GetCurrentDirectory*> retorna o diretório de trabalho do processo iniciado pelo IIS em vez de o diretório do aplicativo (por exemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="03a65-131">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="03a65-132">Para obter o código de exemplo que define o diretório atual do aplicativo, confira a [classe CurrentDirectoryHelpers](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="03a65-132">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="03a65-133">Chame o método `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="03a65-133">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="03a65-134">As chamadas seguintes a <xref:System.IO.Directory.GetCurrentDirectory*> fornecem o diretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-134">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="03a65-135">Ao hospedar em processo, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="03a65-135">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="03a65-136">Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="03a65-136">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="03a65-137">Quando a transformação de declarações com uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, chame <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para adicionar serviços de autenticação:</span><span class="sxs-lookup"><span data-stu-id="03a65-137">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="03a65-138">Não há suporte para [implantações de pacote da Web (arquivo único)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) .</span><span class="sxs-lookup"><span data-stu-id="03a65-138">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="03a65-139">Modelo de hospedagem de fora do processo</span><span class="sxs-lookup"><span data-stu-id="03a65-139">Out-of-process hosting model</span></span>

<span data-ttu-id="03a65-140">Para configurar um aplicativo para hospedagem fora do processo, defina o valor da propriedade `<AspNetCoreHostingModel>` como `OutOfProcess` no arquivo de projeto ( *. csproj*):</span><span class="sxs-lookup"><span data-stu-id="03a65-140">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="03a65-141">A hospedagem em processo é definida com `InProcess`, que é o valor padrão.</span><span class="sxs-lookup"><span data-stu-id="03a65-141">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="03a65-142">O servidor [Kestrel](xref:fundamentals/servers/kestrel) é usado, em vez do servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="03a65-142">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="03a65-143">Fora do processo, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:</span><span class="sxs-lookup"><span data-stu-id="03a65-143">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="03a65-144">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="03a65-144">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="03a65-145">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="03a65-145">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="03a65-146">Alterações no modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="03a65-146">Hosting model changes</span></span>

<span data-ttu-id="03a65-147">Se a configuração `hostingModel` for alterada no arquivo *web.config* (explicado na seção [Configuração a Web.config](#configuration-with-webconfig)), o módulo reciclará o processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="03a65-147">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="03a65-148">Para o IIS Express, o módulo não recicla o processo de trabalho, mas em vez disso, dispara um desligamento normal do processo atual do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="03a65-148">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="03a65-149">A próxima solicitação para o aplicativo gera um novo processo do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="03a65-149">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="03a65-150">Nome do processo</span><span class="sxs-lookup"><span data-stu-id="03a65-150">Process name</span></span>

<span data-ttu-id="03a65-151">`Process.GetCurrentProcess().ProcessName` relata `w3wp`/`iisexpress` (em processo) ou `dotnet` (fora do processo).</span><span class="sxs-lookup"><span data-stu-id="03a65-151">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="03a65-152">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="03a65-152">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="03a65-153">Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="03a65-153">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="03a65-154">O Módulo do ASP.NET Core também pode:</span><span class="sxs-lookup"><span data-stu-id="03a65-154">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="03a65-155">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="03a65-155">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="03a65-156">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="03a65-156">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="03a65-157">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="03a65-157">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="03a65-158">Como instalar e usar o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03a65-158">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="03a65-159">Para obter instruções de como instalar o Módulo do ASP.NET Core, confira [Instalar o Pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="03a65-159">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="03a65-160">Configuração com web.config</span><span class="sxs-lookup"><span data-stu-id="03a65-160">Configuration with web.config</span></span>

<span data-ttu-id="03a65-161">O Módulo do ASP.NET Core está configurado com a seção `aspNetCore` do nó `system.webServer` no arquivo *web.config* do site.</span><span class="sxs-lookup"><span data-stu-id="03a65-161">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="03a65-162">O seguinte arquivo *web.config* é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o Módulo do ASP.NET Core para manipular solicitações de site:</span><span class="sxs-lookup"><span data-stu-id="03a65-162">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="03a65-163">O seguinte *web.config* é publicado para uma [implantação autossuficiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="03a65-163">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="03a65-164">A propriedade <xref:System.Configuration.SectionInformation.InheritInChildApplications*> é definida como `false` para indicar que as configurações especificadas no elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) não são herdadas por aplicativos que residem em um subdiretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-164">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="03a65-165">Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="03a65-165">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="03a65-166">O caminho salva logs de stdout para a pasta *LogFiles*, que é um local criado automaticamente pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="03a65-166">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="03a65-167">Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="03a65-167">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="03a65-168">Atributos do elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="03a65-168">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="03a65-169">Atributo</span><span class="sxs-lookup"><span data-stu-id="03a65-169">Attribute</span></span> | <span data-ttu-id="03a65-170">Descrição</span><span class="sxs-lookup"><span data-stu-id="03a65-170">Description</span></span> | <span data-ttu-id="03a65-171">Padrão</span><span class="sxs-lookup"><span data-stu-id="03a65-171">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="03a65-172">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-172">Optional string attribute.</span></span></p><p><span data-ttu-id="03a65-173">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="03a65-173">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="03a65-174">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-174">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="03a65-175">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="03a65-175">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="03a65-176">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-176">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="03a65-177">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="03a65-177">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="03a65-178">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="03a65-178">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="03a65-179">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-179">Optional string attribute.</span></span></p><p><span data-ttu-id="03a65-180">Especifica o modelo de hospedagem como em processo (`InProcess`) ou de fora do processo (`OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="03a65-180">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `InProcess` |
| `processesPerApplication` | <p><span data-ttu-id="03a65-181">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-181">Optional integer attribute.</span></span></p><p><span data-ttu-id="03a65-182">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-182">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="03a65-183">&dagger;Para hospedagem em processo, o valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="03a65-183">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="03a65-184">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="03a65-184">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="03a65-185">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="03a65-185">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="03a65-186">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="03a65-186">Default: `1`</span></span><br><span data-ttu-id="03a65-187">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="03a65-187">Min: `1`</span></span><br><span data-ttu-id="03a65-188">Máx.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="03a65-188">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="03a65-189">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="03a65-189">Required string attribute.</span></span></p><p><span data-ttu-id="03a65-190">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="03a65-190">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="03a65-191">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="03a65-191">Relative paths are supported.</span></span> <span data-ttu-id="03a65-192">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="03a65-192">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="03a65-193">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-193">Optional integer attribute.</span></span></p><p><span data-ttu-id="03a65-194">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="03a65-194">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="03a65-195">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="03a65-195">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="03a65-196">Sem suporte com hospedagem padrão.</span><span class="sxs-lookup"><span data-stu-id="03a65-196">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="03a65-197">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="03a65-197">Default: `10`</span></span><br><span data-ttu-id="03a65-198">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="03a65-198">Min: `0`</span></span><br><span data-ttu-id="03a65-199">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="03a65-199">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="03a65-200">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-200">Optional timespan attribute.</span></span></p><p><span data-ttu-id="03a65-201">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="03a65-201">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="03a65-202">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="03a65-202">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="03a65-203">Não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="03a65-203">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="03a65-204">Para a hospedagem em processo, o módulo aguarda o aplicativo processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="03a65-204">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="03a65-205">Os valores válidos para segmentos de minutos e segundos da cadeia de caracteres estão no intervalo 0 a 59.</span><span class="sxs-lookup"><span data-stu-id="03a65-205">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="03a65-206">O uso de **60** no valor para minutos ou segundos resulta em um *500 - Erro Interno do Servidor*.</span><span class="sxs-lookup"><span data-stu-id="03a65-206">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="03a65-207">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="03a65-207">Default: `00:02:00`</span></span><br><span data-ttu-id="03a65-208">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="03a65-208">Min: `00:00:00`</span></span><br><span data-ttu-id="03a65-209">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="03a65-209">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="03a65-210">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-210">Optional integer attribute.</span></span></p><p><span data-ttu-id="03a65-211">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="03a65-211">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="03a65-212">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="03a65-212">Default: `10`</span></span><br><span data-ttu-id="03a65-213">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="03a65-213">Min: `0`</span></span><br><span data-ttu-id="03a65-214">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="03a65-214">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="03a65-215">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-215">Optional integer attribute.</span></span></p><p><span data-ttu-id="03a65-216">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="03a65-216">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="03a65-217">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="03a65-217">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="03a65-218">O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</span><span class="sxs-lookup"><span data-stu-id="03a65-218">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="03a65-219">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="03a65-219">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="03a65-220">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="03a65-220">Default: `120`</span></span><br><span data-ttu-id="03a65-221">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="03a65-221">Min: `0`</span></span><br><span data-ttu-id="03a65-222">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="03a65-222">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="03a65-223">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-223">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="03a65-224">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="03a65-224">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="03a65-225">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-225">Optional string attribute.</span></span></p><p><span data-ttu-id="03a65-226">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="03a65-226">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="03a65-227">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="03a65-227">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="03a65-228">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="03a65-228">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="03a65-229">Todas as pastas fornecidas no caminho são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="03a65-229">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="03a65-230">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo ( *.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="03a65-230">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="03a65-231">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="03a65-231">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="03a65-232">Definir variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="03a65-232">Set environment variables</span></span>

<span data-ttu-id="03a65-233">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="03a65-233">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="03a65-234">Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="03a65-234">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="03a65-235">Variáveis de ambiente definidas nesta seção têm precedência sobre variáveis de ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="03a65-235">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="03a65-236">O exemplo a seguir define duas variáveis de ambiente em *Web. config*.  `ASPNETCORE_ENVIRONMENT` configura o ambiente do aplicativo para `Development`.</span><span class="sxs-lookup"><span data-stu-id="03a65-236">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="03a65-237">Um desenvolvedor pode definir esse valor temporariamente no arquivo *web.config* para forçar o carregamento da [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) ao depurar uma exceção de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-237">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="03a65-238">`CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-238">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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

> [!NOTE]
> <span data-ttu-id="03a65-239">Em vez de configurar o ambiente diretamente no *web.config*, você pode incluir a propriedade `<EnvironmentName>` no perfil de publicação ( *.pubxml*) ou no perfil de projeto.</span><span class="sxs-lookup"><span data-stu-id="03a65-239">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="03a65-240">Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="03a65-240">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="03a65-241">Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.</span><span class="sxs-lookup"><span data-stu-id="03a65-241">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="03a65-242">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="03a65-242">app_offline.htm</span></span>

<span data-ttu-id="03a65-243">Se um arquivo com o nome *app_offline.htm* é detectado no diretório raiz de um aplicativo, o Módulo do ASP.NET Core tenta desligar normalmente o aplicativo e parar o processamento de solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="03a65-243">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="03a65-244">Se o aplicativo ainda está em execução após o número de segundos definido em `shutdownTimeLimit`, o Módulo do ASP.NET Core encerra o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="03a65-244">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="03a65-245">Enquanto o arquivo *app_offline.htm* estiver presente, o Módulo do ASP.NET Core responderá às solicitações enviando o conteúdo do arquivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="03a65-245">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="03a65-246">Quando o arquivo *app_offline.htm* é removido, a próxima solicitação inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-246">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="03a65-247">Ao usar o modelo de hospedagem de fora do processo, talvez o aplicativo não desligue imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="03a65-247">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="03a65-248">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-248">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="03a65-249">Página de erro de inicialização</span><span class="sxs-lookup"><span data-stu-id="03a65-249">Start-up error page</span></span>

<span data-ttu-id="03a65-250">A hospedagem em processo e fora do processo produzem páginas de erro personalizadas quando falham ao iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-250">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="03a65-251">Se o Módulo do ASP.NET Core falhar ao encontrar o manipulador de solicitação em processo ou fora do processo, uma página de código de status *500.0 – falha ao carregar manipulador no processo/fora do processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="03a65-251">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="03a65-252">No caso da hospedagem em processo, se o módulo do ASP.NET Core falhar ao iniciar o aplicativo, uma página de código de status *500.30 – falha ao iniciar* será exibida.</span><span class="sxs-lookup"><span data-stu-id="03a65-252">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="03a65-253">Para hospedagem fora do processo, se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="03a65-253">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="03a65-254">Para suprimir essa página e reverter para a página de código de status 5xx padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="03a65-254">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="03a65-255">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="03a65-255">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="03a65-256">Criação de log e redirecionamento</span><span class="sxs-lookup"><span data-stu-id="03a65-256">Log creation and redirection</span></span>

<span data-ttu-id="03a65-257">O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos.</span><span class="sxs-lookup"><span data-stu-id="03a65-257">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="03a65-258">Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="03a65-258">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="03a65-259">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="03a65-259">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="03a65-260">Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo.</span><span class="sxs-lookup"><span data-stu-id="03a65-260">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="03a65-261">É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.</span><span class="sxs-lookup"><span data-stu-id="03a65-261">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="03a65-262">Usar o log de stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-262">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="03a65-263">Não use o log de stdout para fins gerais de registro em log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-263">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="03a65-264">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="03a65-264">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="03a65-265">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="03a65-265">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="03a65-266">Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="03a65-266">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="03a65-267">O nome do arquivo de log é composto por meio do acréscimo do carimbo de data/hora, da ID do processo e da extensão de arquivo ( *.log*) para o último segmento do caminho `stdoutLogFile` (normalmente *stdout*), delimitados por sublinhados.</span><span class="sxs-lookup"><span data-stu-id="03a65-267">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="03a65-268">Se o caminho `stdoutLogFile` termina com *stdout*, um log para um aplicativo com um PID de 1934, criado em 5/2/2018 às 19:42:32, tem o nome de arquivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="03a65-268">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="03a65-269">Se `stdoutLogEnabled` for falso, os erros que ocorrerem na inicialização do aplicativo serão capturados e emitidos no log de eventos até 30 KB.</span><span class="sxs-lookup"><span data-stu-id="03a65-269">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="03a65-270">Após a inicialização, todos os logs adicionais são descartados.</span><span class="sxs-lookup"><span data-stu-id="03a65-270">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="03a65-271">O elemento de `aspNetCore` de exemplo a seguir em um arquivo *Web. config* configura o log de stdout para um aplicativo hospedado no serviço Azure app.</span><span class="sxs-lookup"><span data-stu-id="03a65-271">The following sample `aspNetCore` element in a *web.config* file configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="03a65-272">Um caminho local ou um caminho de compartilhamento de rede é aceitável para o registro em log local.</span><span class="sxs-lookup"><span data-stu-id="03a65-272">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="03a65-273">Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="03a65-273">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="03a65-274">Logs de diagnóstico avançados</span><span class="sxs-lookup"><span data-stu-id="03a65-274">Enhanced diagnostic logs</span></span>

<span data-ttu-id="03a65-275">O Módulo do ASP.NET Core é configurável para fornecer logs de diagnóstico avançados.</span><span class="sxs-lookup"><span data-stu-id="03a65-275">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="03a65-276">Adicione o elemento `<handlerSettings>` ao elemento `<aspNetCore>` em *Web. config*. Definir a `debugLevel` como `TRACE` expõe uma fidelidade mais alta das informações de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="03a65-276">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="03a65-277">Todas as pastas no caminho (*logs* no exemplo anterior) são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="03a65-277">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="03a65-278">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="03a65-278">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="03a65-279">Os valores do nível de depuração (`debugLevel`) podem incluir o nível e a localização.</span><span class="sxs-lookup"><span data-stu-id="03a65-279">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="03a65-280">Níveis (na ordem do menos para o mais detalhado):</span><span class="sxs-lookup"><span data-stu-id="03a65-280">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="03a65-281">ERROR</span><span class="sxs-lookup"><span data-stu-id="03a65-281">ERROR</span></span>
* <span data-ttu-id="03a65-282">WARNING</span><span class="sxs-lookup"><span data-stu-id="03a65-282">WARNING</span></span>
* <span data-ttu-id="03a65-283">INFO</span><span class="sxs-lookup"><span data-stu-id="03a65-283">INFO</span></span>
* <span data-ttu-id="03a65-284">TRACE</span><span class="sxs-lookup"><span data-stu-id="03a65-284">TRACE</span></span>

<span data-ttu-id="03a65-285">Locais (vários locais são permitidos):</span><span class="sxs-lookup"><span data-stu-id="03a65-285">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="03a65-286">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="03a65-286">CONSOLE</span></span>
* <span data-ttu-id="03a65-287">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="03a65-287">EVENTLOG</span></span>
* <span data-ttu-id="03a65-288">FILE</span><span class="sxs-lookup"><span data-stu-id="03a65-288">FILE</span></span>

<span data-ttu-id="03a65-289">As configurações do manipulador também podem ser fornecidas por meio de variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="03a65-289">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="03a65-290">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; caminho para o arquivo de log de depuração.</span><span class="sxs-lookup"><span data-stu-id="03a65-290">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="03a65-291">(Padrão: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="03a65-291">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="03a65-292">`ASPNETCORE_MODULE_DEBUG` &ndash; configuração do nível de depuração.</span><span class="sxs-lookup"><span data-stu-id="03a65-292">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="03a65-293">**Não** deixe o log de depuração habilitado na implantação por mais tempo que o necessário para solucionar um problema.</span><span class="sxs-lookup"><span data-stu-id="03a65-293">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="03a65-294">O tamanho do log não é limitado.</span><span class="sxs-lookup"><span data-stu-id="03a65-294">The size of the log isn't limited.</span></span> <span data-ttu-id="03a65-295">Deixar o log de depuração habilitado pode esgotar o espaço em disco disponível e causar falha no servidor ou no serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-295">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="03a65-296">Veja [Configuração com web.config](#configuration-with-webconfig) para obter um exemplo do elemento `aspNetCore` no arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="03a65-296">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="03a65-297">Modificar o tamanho da pilha</span><span class="sxs-lookup"><span data-stu-id="03a65-297">Modify the stack size</span></span>

<span data-ttu-id="03a65-298">*Aplica-se somente ao usar o modelo de hospedagem em processo.*</span><span class="sxs-lookup"><span data-stu-id="03a65-298">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="03a65-299">Configure o tamanho da pilha gerenciada usando a configuração de `stackSize` em bytes em *Web. config*. O tamanho padrão é `1048576` bytes (1 MB).</span><span class="sxs-lookup"><span data-stu-id="03a65-299">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="03a65-300">A configuração de proxy usa o protocolo HTTP e um token de emparelhamento</span><span class="sxs-lookup"><span data-stu-id="03a65-300">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="03a65-301">*Só se aplica à hospedagem de fora do processo.*</span><span class="sxs-lookup"><span data-stu-id="03a65-301">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="03a65-302">O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="03a65-302">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="03a65-303">Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="03a65-303">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="03a65-304">Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem.</span><span class="sxs-lookup"><span data-stu-id="03a65-304">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="03a65-305">O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="03a65-305">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="03a65-306">O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy.</span><span class="sxs-lookup"><span data-stu-id="03a65-306">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="03a65-307">O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="03a65-307">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="03a65-308">Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada.</span><span class="sxs-lookup"><span data-stu-id="03a65-308">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="03a65-309">A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="03a65-309">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="03a65-310">Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.</span><span class="sxs-lookup"><span data-stu-id="03a65-310">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="03a65-311">Módulo do ASP.NET Core com uma configuração do IIS compartilhada</span><span class="sxs-lookup"><span data-stu-id="03a65-311">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="03a65-312">O instalador do módulo do ASP.NET Core é executado com os privilégios da conta de **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="03a65-312">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="03a65-313">Já que a conta de sistema local não tem permissão para modificar o caminho do compartilhamento usado pela configuração compartilhada de IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações de módulo no arquivo *applicationHost.config* no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="03a65-313">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="03a65-314">Ao usar uma configuração compartilhada de IIS no mesmo computador que a instalação do IIS, execute o instalador do pacote de hospedagem do ASP.NET Core com o parâmetro `OPT_NO_SHARED_CONFIG_CHECK` definido como `1`:</span><span class="sxs-lookup"><span data-stu-id="03a65-314">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="03a65-315">Quando o caminho para a configuração compartilhada não está no mesmo computador que a instalação do IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="03a65-315">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="03a65-316">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="03a65-316">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="03a65-317">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="03a65-317">Run the installer.</span></span>
1. <span data-ttu-id="03a65-318">Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="03a65-318">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="03a65-319">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="03a65-319">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="03a65-320">Versão do módulo e logs do instalador do pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="03a65-320">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="03a65-321">Para determinar a versão do Módulo do ASP.NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="03a65-321">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="03a65-322">No sistema de hospedagem, navegue até *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="03a65-322">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="03a65-323">Localize o arquivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="03a65-323">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="03a65-324">Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="03a65-324">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="03a65-325">Selecione a guia **detalhes** . A versão do **arquivo** e a **versão do produto** representam a versão instalada do módulo.</span><span class="sxs-lookup"><span data-stu-id="03a65-325">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="03a65-326">Os logs do instalador do pacote de hospedagem para o módulo são encontrados em *C: \\Users \\% username% \\AppData \\Local \\Temp*. O arquivo é denominado *dd_DotNetCoreWinSvrHosting__ \<timestamp > _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="03a65-326">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="03a65-327">Locais dos arquivos de módulo, de esquema e de configuração</span><span class="sxs-lookup"><span data-stu-id="03a65-327">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="03a65-328">Módulo</span><span class="sxs-lookup"><span data-stu-id="03a65-328">Module</span></span>

<span data-ttu-id="03a65-329">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="03a65-329">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="03a65-330">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-330">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="03a65-331">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-331">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="03a65-332">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-332">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="03a65-333">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-333">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="03a65-334">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="03a65-334">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="03a65-335">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-335">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="03a65-336">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-336">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="03a65-337">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-337">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="03a65-338">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-338">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="03a65-339">Esquema</span><span class="sxs-lookup"><span data-stu-id="03a65-339">Schema</span></span>

<span data-ttu-id="03a65-340">**IIS**</span><span class="sxs-lookup"><span data-stu-id="03a65-340">**IIS**</span></span>

* <span data-ttu-id="03a65-341">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="03a65-341">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="03a65-342">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="03a65-342">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="03a65-343">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="03a65-343">**IIS Express**</span></span>

* <span data-ttu-id="03a65-344">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="03a65-344">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="03a65-345">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="03a65-345">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="03a65-346">Configuração</span><span class="sxs-lookup"><span data-stu-id="03a65-346">Configuration</span></span>

<span data-ttu-id="03a65-347">**IIS**</span><span class="sxs-lookup"><span data-stu-id="03a65-347">**IIS**</span></span>

* <span data-ttu-id="03a65-348">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="03a65-348">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="03a65-349">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="03a65-349">**IIS Express**</span></span>

* <span data-ttu-id="03a65-350">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="03a65-350">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="03a65-351">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="03a65-351">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="03a65-352">Os arquivos podem ser encontrados pesquisando por *aspnetcore* no arquivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="03a65-352">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="03a65-353">O módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para:</span><span class="sxs-lookup"><span data-stu-id="03a65-353">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="03a65-354">Hospedar um aplicativo ASP.NET Core dentro do processo de trabalho do IIS (`w3wp.exe`), chamado o [modelo de hospedagem no processo](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="03a65-354">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="03a65-355">Encaminhar solicitações da Web a um aplicativo ASP.NET Core de back-end que executa o [servidor Kestrel](xref:fundamentals/servers/kestrel), chamado o [modelo de hospedagem de fora do processo](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="03a65-355">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="03a65-356">Versões do Windows compatíveis:</span><span class="sxs-lookup"><span data-stu-id="03a65-356">Supported Windows versions:</span></span>

* <span data-ttu-id="03a65-357">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="03a65-357">Windows 7 or later</span></span>
* <span data-ttu-id="03a65-358">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="03a65-358">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="03a65-359">Ao fazer uma hospedagem em processo, o módulo usa uma implementação de servidor em processo do IIS, chamado Servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="03a65-359">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="03a65-360">Ao hospedar de fora do processo, o módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="03a65-360">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="03a65-361">O módulo não funciona com [http. sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="03a65-361">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="03a65-362">Modelos de hospedagem</span><span class="sxs-lookup"><span data-stu-id="03a65-362">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="03a65-363">Modelo de hospedagem em processo</span><span class="sxs-lookup"><span data-stu-id="03a65-363">In-process hosting model</span></span>

<span data-ttu-id="03a65-364">Para configurar um aplicativo para hospedagem em processo, adicione a propriedade `<AspNetCoreHostingModel>` ao arquivo de projeto do aplicativo com um valor de `InProcess` (a hospedagem de fora do processo é definida com `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="03a65-364">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="03a65-365">Não há suporte para o modelo de hospedagem em processo para aplicativos ASP.NET Core direcionados ao .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="03a65-365">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="03a65-366">Se a propriedade `<AspNetCoreHostingModel>` não estiver presente no arquivo, o valor padrão será `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="03a65-366">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="03a65-367">As seguintes características se aplicam ao hospedar em processo:</span><span class="sxs-lookup"><span data-stu-id="03a65-367">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="03a65-368">O Servidor HTTP do IIS (`IISHttpServer`) é usado, em vez do servidor [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="03a65-368">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="03a65-369">Em processo, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> para:</span><span class="sxs-lookup"><span data-stu-id="03a65-369">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="03a65-370">Registrar o `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="03a65-370">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="03a65-371">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="03a65-371">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="03a65-372">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="03a65-372">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="03a65-373">O [requestTimeout atributo](#attributes-of-the-aspnetcore-element) não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="03a65-373">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="03a65-374">Não há suporte para o compartilhamento do pool de aplicativos entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="03a65-374">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="03a65-375">Use um pool de aplicativos por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-375">Use one app pool per app.</span></span>

* <span data-ttu-id="03a65-376">Ao usar a [Implantação da Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) ou inserir manualmente um [arquivo app_offline.htm na implantação](xref:host-and-deploy/iis/index#locked-deployment-files), o aplicativo talvez não seja desligado imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="03a65-376">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="03a65-377">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-377">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="03a65-378">A arquitetura (número de bit) do aplicativo e o tempo de execução instalado (x64 ou x86) devem corresponder à arquitetura do pool de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="03a65-378">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="03a65-379">As desconexões do cliente são detectadas.</span><span class="sxs-lookup"><span data-stu-id="03a65-379">Client disconnects are detected.</span></span> <span data-ttu-id="03a65-380">O token de cancelamento [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) é cancelado quando o cliente se desconecta.</span><span class="sxs-lookup"><span data-stu-id="03a65-380">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="03a65-381">No ASP.NET Core 2.2.1 ou anterior, <xref:System.IO.Directory.GetCurrentDirectory*> retorna o diretório de trabalho do processo iniciado pelo IIS em vez de o diretório do aplicativo (por exemplo, *C:\Windows\System32\inetsrv* para *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="03a65-381">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="03a65-382">Para obter o código de exemplo que define o diretório atual do aplicativo, confira a [classe CurrentDirectoryHelpers](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="03a65-382">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="03a65-383">Chame o método `SetCurrentDirectory`.</span><span class="sxs-lookup"><span data-stu-id="03a65-383">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="03a65-384">As chamadas seguintes a <xref:System.IO.Directory.GetCurrentDirectory*> fornecem o diretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-384">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="03a65-385">Ao hospedar em processo, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> não é chamado internamente para inicializar um usuário.</span><span class="sxs-lookup"><span data-stu-id="03a65-385">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="03a65-386">Portanto, uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> usada para transformar as declarações após cada autenticação não é ativada por padrão.</span><span class="sxs-lookup"><span data-stu-id="03a65-386">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="03a65-387">Quando a transformação de declarações com uma implementação <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, chame <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> para adicionar serviços de autenticação:</span><span class="sxs-lookup"><span data-stu-id="03a65-387">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="03a65-388">Modelo de hospedagem de fora do processo</span><span class="sxs-lookup"><span data-stu-id="03a65-388">Out-of-process hosting model</span></span>

<span data-ttu-id="03a65-389">Para configurar um aplicativo para hospedagem fora do processo, use qualquer uma das abordagens a seguir no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="03a65-389">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="03a65-390">não especifique a propriedade `<AspNetCoreHostingModel>`.</span><span class="sxs-lookup"><span data-stu-id="03a65-390">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="03a65-391">Se a propriedade `<AspNetCoreHostingModel>` não estiver presente no arquivo, o valor padrão será `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="03a65-391">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="03a65-392">Defina o valor da propriedade `<AspNetCoreHostingModel>` como `OutOfProcess` (a hospedagem no processo é definida com `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="03a65-392">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="03a65-393">O servidor [Kestrel](xref:fundamentals/servers/kestrel) é usado, em vez do servidor HTTP do IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="03a65-393">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="03a65-394">Fora do processo, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) chama <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> para:</span><span class="sxs-lookup"><span data-stu-id="03a65-394">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="03a65-395">Configurar a porta e o caminho base nos quais o servidor deve escutar ao ser executado por trás do Módulo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="03a65-395">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="03a65-396">Configurar o host para capturar erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="03a65-396">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="03a65-397">Alterações no modelo de hospedagem</span><span class="sxs-lookup"><span data-stu-id="03a65-397">Hosting model changes</span></span>

<span data-ttu-id="03a65-398">Se a configuração `hostingModel` for alterada no arquivo *web.config* (explicado na seção [Configuração a Web.config](#configuration-with-webconfig)), o módulo reciclará o processo de trabalho do IIS.</span><span class="sxs-lookup"><span data-stu-id="03a65-398">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="03a65-399">Para o IIS Express, o módulo não recicla o processo de trabalho, mas em vez disso, dispara um desligamento normal do processo atual do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="03a65-399">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="03a65-400">A próxima solicitação para o aplicativo gera um novo processo do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="03a65-400">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="03a65-401">Nome do processo</span><span class="sxs-lookup"><span data-stu-id="03a65-401">Process name</span></span>

<span data-ttu-id="03a65-402">`Process.GetCurrentProcess().ProcessName` relata `w3wp`/`iisexpress` (em processo) ou `dotnet` (fora do processo).</span><span class="sxs-lookup"><span data-stu-id="03a65-402">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="03a65-403">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="03a65-403">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="03a65-404">Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="03a65-404">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="03a65-405">O Módulo do ASP.NET Core também pode:</span><span class="sxs-lookup"><span data-stu-id="03a65-405">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="03a65-406">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="03a65-406">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="03a65-407">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="03a65-407">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="03a65-408">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="03a65-408">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="03a65-409">Como instalar e usar o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03a65-409">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="03a65-410">Para obter instruções de como instalar o Módulo do ASP.NET Core, confira [Instalar o Pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="03a65-410">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="03a65-411">Configuração com web.config</span><span class="sxs-lookup"><span data-stu-id="03a65-411">Configuration with web.config</span></span>

<span data-ttu-id="03a65-412">O Módulo do ASP.NET Core está configurado com a seção `aspNetCore` do nó `system.webServer` no arquivo *web.config* do site.</span><span class="sxs-lookup"><span data-stu-id="03a65-412">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="03a65-413">O seguinte arquivo *web.config* é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o Módulo do ASP.NET Core para manipular solicitações de site:</span><span class="sxs-lookup"><span data-stu-id="03a65-413">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="03a65-414">O seguinte *web.config* é publicado para uma [implantação autossuficiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="03a65-414">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="03a65-415">A propriedade <xref:System.Configuration.SectionInformation.InheritInChildApplications*> é definida como `false` para indicar que as configurações especificadas no elemento [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) não são herdadas por aplicativos que residem em um subdiretório do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-415">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="03a65-416">Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="03a65-416">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="03a65-417">O caminho salva logs de stdout para a pasta *LogFiles*, que é um local criado automaticamente pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="03a65-417">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="03a65-418">Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="03a65-418">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="03a65-419">Atributos do elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="03a65-419">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="03a65-420">Atributo</span><span class="sxs-lookup"><span data-stu-id="03a65-420">Attribute</span></span> | <span data-ttu-id="03a65-421">Descrição</span><span class="sxs-lookup"><span data-stu-id="03a65-421">Description</span></span> | <span data-ttu-id="03a65-422">Padrão</span><span class="sxs-lookup"><span data-stu-id="03a65-422">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="03a65-423">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-423">Optional string attribute.</span></span></p><p><span data-ttu-id="03a65-424">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="03a65-424">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="03a65-425">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-425">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="03a65-426">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="03a65-426">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="03a65-427">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-427">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="03a65-428">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="03a65-428">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="03a65-429">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="03a65-429">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="03a65-430">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-430">Optional string attribute.</span></span></p><p><span data-ttu-id="03a65-431">Especifica o modelo de hospedagem como em processo (`InProcess`) ou de fora do processo (`OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="03a65-431">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="03a65-432">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-432">Optional integer attribute.</span></span></p><p><span data-ttu-id="03a65-433">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-433">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="03a65-434">&dagger;Para hospedagem em processo, o valor está limitado a `1`.</span><span class="sxs-lookup"><span data-stu-id="03a65-434">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="03a65-435">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="03a65-435">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="03a65-436">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="03a65-436">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="03a65-437">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="03a65-437">Default: `1`</span></span><br><span data-ttu-id="03a65-438">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="03a65-438">Min: `1`</span></span><br><span data-ttu-id="03a65-439">Máx.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="03a65-439">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="03a65-440">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="03a65-440">Required string attribute.</span></span></p><p><span data-ttu-id="03a65-441">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="03a65-441">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="03a65-442">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="03a65-442">Relative paths are supported.</span></span> <span data-ttu-id="03a65-443">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="03a65-443">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="03a65-444">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-444">Optional integer attribute.</span></span></p><p><span data-ttu-id="03a65-445">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="03a65-445">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="03a65-446">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="03a65-446">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="03a65-447">Sem suporte com hospedagem padrão.</span><span class="sxs-lookup"><span data-stu-id="03a65-447">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="03a65-448">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="03a65-448">Default: `10`</span></span><br><span data-ttu-id="03a65-449">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="03a65-449">Min: `0`</span></span><br><span data-ttu-id="03a65-450">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="03a65-450">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="03a65-451">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-451">Optional timespan attribute.</span></span></p><p><span data-ttu-id="03a65-452">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="03a65-452">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="03a65-453">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="03a65-453">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="03a65-454">Não se aplica à hospedagem em processo.</span><span class="sxs-lookup"><span data-stu-id="03a65-454">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="03a65-455">Para a hospedagem em processo, o módulo aguarda o aplicativo processar a solicitação.</span><span class="sxs-lookup"><span data-stu-id="03a65-455">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="03a65-456">Os valores válidos para segmentos de minutos e segundos da cadeia de caracteres estão no intervalo 0 a 59.</span><span class="sxs-lookup"><span data-stu-id="03a65-456">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="03a65-457">O uso de **60** no valor para minutos ou segundos resulta em um *500 - Erro Interno do Servidor*.</span><span class="sxs-lookup"><span data-stu-id="03a65-457">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="03a65-458">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="03a65-458">Default: `00:02:00`</span></span><br><span data-ttu-id="03a65-459">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="03a65-459">Min: `00:00:00`</span></span><br><span data-ttu-id="03a65-460">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="03a65-460">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="03a65-461">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-461">Optional integer attribute.</span></span></p><p><span data-ttu-id="03a65-462">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="03a65-462">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="03a65-463">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="03a65-463">Default: `10`</span></span><br><span data-ttu-id="03a65-464">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="03a65-464">Min: `0`</span></span><br><span data-ttu-id="03a65-465">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="03a65-465">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="03a65-466">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-466">Optional integer attribute.</span></span></p><p><span data-ttu-id="03a65-467">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="03a65-467">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="03a65-468">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="03a65-468">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="03a65-469">O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</span><span class="sxs-lookup"><span data-stu-id="03a65-469">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="03a65-470">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="03a65-470">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="03a65-471">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="03a65-471">Default: `120`</span></span><br><span data-ttu-id="03a65-472">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="03a65-472">Min: `0`</span></span><br><span data-ttu-id="03a65-473">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="03a65-473">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="03a65-474">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-474">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="03a65-475">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="03a65-475">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="03a65-476">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-476">Optional string attribute.</span></span></p><p><span data-ttu-id="03a65-477">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="03a65-477">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="03a65-478">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="03a65-478">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="03a65-479">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="03a65-479">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="03a65-480">Todas as pastas fornecidas no caminho são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="03a65-480">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="03a65-481">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo ( *.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="03a65-481">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="03a65-482">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="03a65-482">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="03a65-483">Definindo variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="03a65-483">Setting environment variables</span></span>

<span data-ttu-id="03a65-484">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="03a65-484">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="03a65-485">Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="03a65-485">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="03a65-486">Variáveis de ambiente definidas nesta seção têm precedência sobre variáveis de ambiente do sistema.</span><span class="sxs-lookup"><span data-stu-id="03a65-486">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="03a65-487">O exemplo a seguir define duas variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="03a65-487">The following example sets two environment variables.</span></span> <span data-ttu-id="03a65-488">`ASPNETCORE_ENVIRONMENT` configura o ambiente do aplicativo para `Development`.</span><span class="sxs-lookup"><span data-stu-id="03a65-488">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="03a65-489">Um desenvolvedor pode definir esse valor temporariamente no arquivo *web.config* para forçar o carregamento da [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) ao depurar uma exceção de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-489">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="03a65-490">`CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-490">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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

> [!NOTE]
> <span data-ttu-id="03a65-491">Em vez de configurar o ambiente diretamente no *web.config*, você pode incluir a propriedade `<EnvironmentName>` no perfil de publicação ( *.pubxml*) ou no perfil de projeto.</span><span class="sxs-lookup"><span data-stu-id="03a65-491">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="03a65-492">Esta abordagem define o ambiente no arquivo *web.config* quando o projeto é publicado:</span><span class="sxs-lookup"><span data-stu-id="03a65-492">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="03a65-493">Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.</span><span class="sxs-lookup"><span data-stu-id="03a65-493">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="03a65-494">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="03a65-494">app_offline.htm</span></span>

<span data-ttu-id="03a65-495">Se um arquivo com o nome *app_offline.htm* é detectado no diretório raiz de um aplicativo, o Módulo do ASP.NET Core tenta desligar normalmente o aplicativo e parar o processamento de solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="03a65-495">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="03a65-496">Se o aplicativo ainda está em execução após o número de segundos definido em `shutdownTimeLimit`, o Módulo do ASP.NET Core encerra o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="03a65-496">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="03a65-497">Enquanto o arquivo *app_offline.htm* estiver presente, o Módulo do ASP.NET Core responderá às solicitações enviando o conteúdo do arquivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="03a65-497">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="03a65-498">Quando o arquivo *app_offline.htm* é removido, a próxima solicitação inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-498">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="03a65-499">Ao usar o modelo de hospedagem de fora do processo, talvez o aplicativo não desligue imediatamente se houver uma conexão aberta.</span><span class="sxs-lookup"><span data-stu-id="03a65-499">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="03a65-500">Por exemplo, uma conexão websocket pode atrasar o desligamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-500">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="03a65-501">Página de erro de inicialização</span><span class="sxs-lookup"><span data-stu-id="03a65-501">Start-up error page</span></span>

<span data-ttu-id="03a65-502">A hospedagem em processo e fora do processo produzem páginas de erro personalizadas quando falham ao iniciar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-502">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="03a65-503">Se o Módulo do ASP.NET Core falhar ao encontrar o manipulador de solicitação em processo ou fora do processo, uma página de código de status *500.0 – falha ao carregar manipulador no processo/fora do processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="03a65-503">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="03a65-504">No caso da hospedagem em processo, se o módulo do ASP.NET Core falhar ao iniciar o aplicativo, uma página de código de status *500.30 – falha ao iniciar* será exibida.</span><span class="sxs-lookup"><span data-stu-id="03a65-504">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="03a65-505">Para hospedagem fora do processo, se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="03a65-505">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="03a65-506">Para suprimir essa página e reverter para a página de código de status 5xx padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="03a65-506">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="03a65-507">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="03a65-507">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="03a65-508">Criação de log e redirecionamento</span><span class="sxs-lookup"><span data-stu-id="03a65-508">Log creation and redirection</span></span>

<span data-ttu-id="03a65-509">O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos.</span><span class="sxs-lookup"><span data-stu-id="03a65-509">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="03a65-510">Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="03a65-510">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="03a65-511">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="03a65-511">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="03a65-512">Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo.</span><span class="sxs-lookup"><span data-stu-id="03a65-512">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="03a65-513">É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.</span><span class="sxs-lookup"><span data-stu-id="03a65-513">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="03a65-514">Usar o log de stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-514">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="03a65-515">Não use o log de stdout para fins gerais de registro em log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-515">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="03a65-516">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="03a65-516">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="03a65-517">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="03a65-517">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="03a65-518">Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="03a65-518">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="03a65-519">O nome do arquivo de log é composto por meio do acréscimo do carimbo de data/hora, da ID do processo e da extensão de arquivo ( *.log*) para o último segmento do caminho `stdoutLogFile` (normalmente *stdout*), delimitados por sublinhados.</span><span class="sxs-lookup"><span data-stu-id="03a65-519">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="03a65-520">Se o caminho `stdoutLogFile` termina com *stdout*, um log para um aplicativo com um PID de 1934, criado em 5/2/2018 às 19:42:32, tem o nome de arquivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="03a65-520">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="03a65-521">Se `stdoutLogEnabled` for falso, os erros que ocorrerem na inicialização do aplicativo serão capturados e emitidos no log de eventos até 30 KB.</span><span class="sxs-lookup"><span data-stu-id="03a65-521">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="03a65-522">Após a inicialização, todos os logs adicionais são descartados.</span><span class="sxs-lookup"><span data-stu-id="03a65-522">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="03a65-523">O elemento `aspNetCore` de exemplo a seguir configura o registro em log de stdout para um aplicativo hospedado no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="03a65-523">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="03a65-524">Um caminho local ou um caminho de compartilhamento de rede é aceitável para o registro em log local.</span><span class="sxs-lookup"><span data-stu-id="03a65-524">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="03a65-525">Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="03a65-525">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="03a65-526">Logs de diagnóstico avançados</span><span class="sxs-lookup"><span data-stu-id="03a65-526">Enhanced diagnostic logs</span></span>

<span data-ttu-id="03a65-527">O Módulo do ASP.NET Core é configurável para fornecer logs de diagnóstico avançados.</span><span class="sxs-lookup"><span data-stu-id="03a65-527">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="03a65-528">Adicione o elemento `<handlerSettings>` ao elemento `<aspNetCore>` em *Web. config*. Definir a `debugLevel` como `TRACE` expõe uma fidelidade mais alta das informações de diagnóstico:</span><span class="sxs-lookup"><span data-stu-id="03a65-528">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="03a65-529">As pastas no caminho fornecido para o valor `<handlerSetting>` (*logs* no exemplo anterior) não são criadas automaticamente pelo módulo e devem existir previamente na implantação.</span><span class="sxs-lookup"><span data-stu-id="03a65-529">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="03a65-530">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="03a65-530">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="03a65-531">Os valores do nível de depuração (`debugLevel`) podem incluir o nível e a localização.</span><span class="sxs-lookup"><span data-stu-id="03a65-531">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="03a65-532">Níveis (na ordem do menos para o mais detalhado):</span><span class="sxs-lookup"><span data-stu-id="03a65-532">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="03a65-533">ERROR</span><span class="sxs-lookup"><span data-stu-id="03a65-533">ERROR</span></span>
* <span data-ttu-id="03a65-534">WARNING</span><span class="sxs-lookup"><span data-stu-id="03a65-534">WARNING</span></span>
* <span data-ttu-id="03a65-535">INFO</span><span class="sxs-lookup"><span data-stu-id="03a65-535">INFO</span></span>
* <span data-ttu-id="03a65-536">TRACE</span><span class="sxs-lookup"><span data-stu-id="03a65-536">TRACE</span></span>

<span data-ttu-id="03a65-537">Locais (vários locais são permitidos):</span><span class="sxs-lookup"><span data-stu-id="03a65-537">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="03a65-538">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="03a65-538">CONSOLE</span></span>
* <span data-ttu-id="03a65-539">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="03a65-539">EVENTLOG</span></span>
* <span data-ttu-id="03a65-540">FILE</span><span class="sxs-lookup"><span data-stu-id="03a65-540">FILE</span></span>

<span data-ttu-id="03a65-541">As configurações do manipulador também podem ser fornecidas por meio de variáveis de ambiente:</span><span class="sxs-lookup"><span data-stu-id="03a65-541">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="03a65-542">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; caminho para o arquivo de log de depuração.</span><span class="sxs-lookup"><span data-stu-id="03a65-542">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="03a65-543">(Padrão: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="03a65-543">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="03a65-544">`ASPNETCORE_MODULE_DEBUG` &ndash; configuração do nível de depuração.</span><span class="sxs-lookup"><span data-stu-id="03a65-544">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="03a65-545">**Não** deixe o log de depuração habilitado na implantação por mais tempo que o necessário para solucionar um problema.</span><span class="sxs-lookup"><span data-stu-id="03a65-545">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="03a65-546">O tamanho do log não é limitado.</span><span class="sxs-lookup"><span data-stu-id="03a65-546">The size of the log isn't limited.</span></span> <span data-ttu-id="03a65-547">Deixar o log de depuração habilitado pode esgotar o espaço em disco disponível e causar falha no servidor ou no serviço de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-547">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="03a65-548">Veja [Configuração com web.config](#configuration-with-webconfig) para obter um exemplo do elemento `aspNetCore` no arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="03a65-548">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="03a65-549">A configuração de proxy usa o protocolo HTTP e um token de emparelhamento</span><span class="sxs-lookup"><span data-stu-id="03a65-549">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="03a65-550">*Só se aplica à hospedagem de fora do processo.*</span><span class="sxs-lookup"><span data-stu-id="03a65-550">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="03a65-551">O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="03a65-551">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="03a65-552">Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="03a65-552">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="03a65-553">Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem.</span><span class="sxs-lookup"><span data-stu-id="03a65-553">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="03a65-554">O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="03a65-554">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="03a65-555">O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy.</span><span class="sxs-lookup"><span data-stu-id="03a65-555">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="03a65-556">O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="03a65-556">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="03a65-557">Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada.</span><span class="sxs-lookup"><span data-stu-id="03a65-557">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="03a65-558">A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="03a65-558">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="03a65-559">Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.</span><span class="sxs-lookup"><span data-stu-id="03a65-559">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="03a65-560">Módulo do ASP.NET Core com uma configuração do IIS compartilhada</span><span class="sxs-lookup"><span data-stu-id="03a65-560">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="03a65-561">O instalador do módulo do ASP.NET Core é executado com os privilégios da conta de **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="03a65-561">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="03a65-562">Já que a conta de sistema local não tem permissão para modificar o caminho do compartilhamento usado pela configuração compartilhada de IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações de módulo no arquivo *applicationHost.config* no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="03a65-562">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="03a65-563">Ao usar uma configuração compartilhada de IIS no mesmo computador que a instalação do IIS, execute o instalador do pacote de hospedagem do ASP.NET Core com o parâmetro `OPT_NO_SHARED_CONFIG_CHECK` definido como `1`:</span><span class="sxs-lookup"><span data-stu-id="03a65-563">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="03a65-564">Quando o caminho para a configuração compartilhada não está no mesmo computador que a instalação do IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="03a65-564">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="03a65-565">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="03a65-565">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="03a65-566">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="03a65-566">Run the installer.</span></span>
1. <span data-ttu-id="03a65-567">Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="03a65-567">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="03a65-568">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="03a65-568">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="03a65-569">Versão do módulo e logs do instalador do pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="03a65-569">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="03a65-570">Para determinar a versão do Módulo do ASP.NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="03a65-570">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="03a65-571">No sistema de hospedagem, navegue até *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="03a65-571">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="03a65-572">Localize o arquivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="03a65-572">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="03a65-573">Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="03a65-573">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="03a65-574">Selecione a guia **detalhes** . A versão do **arquivo** e a **versão do produto** representam a versão instalada do módulo.</span><span class="sxs-lookup"><span data-stu-id="03a65-574">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="03a65-575">Os logs do instalador do pacote de hospedagem para o módulo são encontrados em *C: \\Users \\% username% \\AppData \\Local \\Temp*. O arquivo é denominado *dd_DotNetCoreWinSvrHosting__ \<timestamp > _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="03a65-575">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="03a65-576">Locais dos arquivos de módulo, de esquema e de configuração</span><span class="sxs-lookup"><span data-stu-id="03a65-576">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="03a65-577">Módulo</span><span class="sxs-lookup"><span data-stu-id="03a65-577">Module</span></span>

<span data-ttu-id="03a65-578">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="03a65-578">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="03a65-579">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-579">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="03a65-580">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-580">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="03a65-581">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-581">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="03a65-582">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-582">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="03a65-583">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="03a65-583">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="03a65-584">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-584">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="03a65-585">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-585">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="03a65-586">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-586">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="03a65-587">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-587">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="03a65-588">Esquema</span><span class="sxs-lookup"><span data-stu-id="03a65-588">Schema</span></span>

<span data-ttu-id="03a65-589">**IIS**</span><span class="sxs-lookup"><span data-stu-id="03a65-589">**IIS**</span></span>

* <span data-ttu-id="03a65-590">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="03a65-590">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="03a65-591">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="03a65-591">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="03a65-592">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="03a65-592">**IIS Express**</span></span>

* <span data-ttu-id="03a65-593">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="03a65-593">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="03a65-594">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="03a65-594">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="03a65-595">Configuração</span><span class="sxs-lookup"><span data-stu-id="03a65-595">Configuration</span></span>

<span data-ttu-id="03a65-596">**IIS**</span><span class="sxs-lookup"><span data-stu-id="03a65-596">**IIS**</span></span>

* <span data-ttu-id="03a65-597">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="03a65-597">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="03a65-598">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="03a65-598">**IIS Express**</span></span>

* <span data-ttu-id="03a65-599">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="03a65-599">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="03a65-600">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="03a65-600">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="03a65-601">Os arquivos podem ser encontrados pesquisando por *aspnetcore* no arquivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="03a65-601">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="03a65-602">O Módulo do ASP.NET Core é um módulo nativo do IIS que se conecta ao pipeline do IIS para encaminhar solicitações da Web para aplicativos ASP.NET Core de back-end.</span><span class="sxs-lookup"><span data-stu-id="03a65-602">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="03a65-603">Versões do Windows compatíveis:</span><span class="sxs-lookup"><span data-stu-id="03a65-603">Supported Windows versions:</span></span>

* <span data-ttu-id="03a65-604">Windows 7 ou posterior</span><span class="sxs-lookup"><span data-stu-id="03a65-604">Windows 7 or later</span></span>
* <span data-ttu-id="03a65-605">Windows Server 2008 R2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="03a65-605">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="03a65-606">O módulo só funciona com o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="03a65-606">The module only works with Kestrel.</span></span> <span data-ttu-id="03a65-607">O módulo é incompatível com [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="03a65-607">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="03a65-608">Como os aplicativos ASP.NET Core são executados em um processo separado do processo de trabalho do IIS, o módulo também realiza o gerenciamento de processos.</span><span class="sxs-lookup"><span data-stu-id="03a65-608">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="03a65-609">O módulo inicia o processo para o aplicativo ASP.NET Core quando a primeira solicitação chega e reinicia o aplicativo quando ele falha.</span><span class="sxs-lookup"><span data-stu-id="03a65-609">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="03a65-610">Isso é basicamente o mesmo comportamento que o dos aplicativos ASP.NET 4.x que são executados dentro do processo do IIS e são gerenciados pelo [WAS (Serviço de Ativação de Processos do Windows)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="03a65-610">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="03a65-611">O diagrama a seguir ilustra a relação entre o IIS, o Módulo do ASP.NET Core e um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="03a65-611">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Módulo do ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="03a65-613">As solicitações chegam da Web para o driver do HTTP.sys no modo kernel.</span><span class="sxs-lookup"><span data-stu-id="03a65-613">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="03a65-614">O driver roteia as solicitações ao IIS na porta configurada do site, normalmente, a 80 (HTTP) ou a 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="03a65-614">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="03a65-615">O módulo encaminha as solicitações ao Kestrel em uma porta aleatória do aplicativo, que não seja a porta 80 ou 443.</span><span class="sxs-lookup"><span data-stu-id="03a65-615">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="03a65-616">O módulo especifica a porta por meio de uma variável de ambiente na inicialização e o [middleware de integração do IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura o servidor para escutar em `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="03a65-616">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="03a65-617">Outras verificações são executadas e as solicitações que não se originam do módulo são rejeitadas.</span><span class="sxs-lookup"><span data-stu-id="03a65-617">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="03a65-618">O módulo não é compatível com encaminhamento de HTTPS, portanto, as solicitações são encaminhadas por HTTP, mesmo se recebidas pelo IIS por HTTPS.</span><span class="sxs-lookup"><span data-stu-id="03a65-618">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="03a65-619">Depois que o Kestrel coleta a solicitação do módulo, a solicitação é enviada por push ao pipeline do middleware do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="03a65-619">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="03a65-620">O pipeline do middleware manipula a solicitação e a passa como uma instância de `HttpContext` para a lógica do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-620">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="03a65-621">O middleware adicionado pela integração do IIS atualiza o esquema, o IP remoto e pathbase para encaminhar a solicitação para o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="03a65-621">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="03a65-622">A resposta do aplicativo é retornada ao IIS, que a retorna por push para o cliente HTTP que iniciou a solicitação.</span><span class="sxs-lookup"><span data-stu-id="03a65-622">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="03a65-623">Muitos módulos nativos, como a Autenticação do Windows, permanecem ativos.</span><span class="sxs-lookup"><span data-stu-id="03a65-623">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="03a65-624">Para saber mais sobre módulos do IIS ativos com o Módulo do ASP.NET Core, confira <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="03a65-624">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="03a65-625">O Módulo do ASP.NET Core também pode:</span><span class="sxs-lookup"><span data-stu-id="03a65-625">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="03a65-626">Definir variáveis de ambiente para o processo de trabalho.</span><span class="sxs-lookup"><span data-stu-id="03a65-626">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="03a65-627">Registrar a saída StdOut no armazenamento de arquivo para a solução de problemas de inicialização.</span><span class="sxs-lookup"><span data-stu-id="03a65-627">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="03a65-628">Encaminhar tokens de autenticação do Windows.</span><span class="sxs-lookup"><span data-stu-id="03a65-628">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="03a65-629">Como instalar e usar o Módulo do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03a65-629">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="03a65-630">Para obter instruções de como instalar o Módulo do ASP.NET Core, confira [Instalar o Pacote de Hospedagem do .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="03a65-630">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="03a65-631">Configuração com web.config</span><span class="sxs-lookup"><span data-stu-id="03a65-631">Configuration with web.config</span></span>

<span data-ttu-id="03a65-632">O Módulo do ASP.NET Core está configurado com a seção `aspNetCore` do nó `system.webServer` no arquivo *web.config* do site.</span><span class="sxs-lookup"><span data-stu-id="03a65-632">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="03a65-633">O seguinte arquivo *web.config* é publicado para uma [implantação dependente de estrutura](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) e configura o Módulo do ASP.NET Core para manipular solicitações de site:</span><span class="sxs-lookup"><span data-stu-id="03a65-633">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="03a65-634">O seguinte *web.config* é publicado para uma [implantação autossuficiente](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="03a65-634">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="03a65-635">Quando um aplicativo é implantado no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/), o caminho `stdoutLogFile` é definido para `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="03a65-635">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="03a65-636">O caminho salva logs de stdout para a pasta *LogFiles*, que é um local criado automaticamente pelo serviço.</span><span class="sxs-lookup"><span data-stu-id="03a65-636">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="03a65-637">Para saber mais sobre a configuração de subaplicativos do IIS, confira <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="03a65-637">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="03a65-638">Atributos do elemento aspNetCore</span><span class="sxs-lookup"><span data-stu-id="03a65-638">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="03a65-639">Atributo</span><span class="sxs-lookup"><span data-stu-id="03a65-639">Attribute</span></span> | <span data-ttu-id="03a65-640">Descrição</span><span class="sxs-lookup"><span data-stu-id="03a65-640">Description</span></span> | <span data-ttu-id="03a65-641">Padrão</span><span class="sxs-lookup"><span data-stu-id="03a65-641">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="03a65-642">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-642">Optional string attribute.</span></span></p><p><span data-ttu-id="03a65-643">Argumentos para o executável especificado em **processPath**.</span><span class="sxs-lookup"><span data-stu-id="03a65-643">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="03a65-644">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-644">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="03a65-645">Se for true, a página **502.5 – Falha do Processo** será suprimida e a página de código de status 502, configurada no *web.config*, terá precedência.</span><span class="sxs-lookup"><span data-stu-id="03a65-645">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="03a65-646">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-646">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="03a65-647">Se for true, o token será encaminhado para o processo filho escutando em %ASPNETCORE_PORT% como um cabeçalho 'MS-ASPNETCORE-WINAUTHTOKEN' por solicitação.</span><span class="sxs-lookup"><span data-stu-id="03a65-647">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="03a65-648">É responsabilidade desse processo chamar CloseHandle nesse token por solicitação.</span><span class="sxs-lookup"><span data-stu-id="03a65-648">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="03a65-649">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-649">Optional integer attribute.</span></span></p><p><span data-ttu-id="03a65-650">Especifica o número de instâncias do processo especificado na configuração **processPath** que pode ser ativada por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-650">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="03a65-651">A configuração `processesPerApplication` é desencorajada.</span><span class="sxs-lookup"><span data-stu-id="03a65-651">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="03a65-652">Esse atributo será removido em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="03a65-652">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="03a65-653">Padrão: `1`</span><span class="sxs-lookup"><span data-stu-id="03a65-653">Default: `1`</span></span><br><span data-ttu-id="03a65-654">Mín.: `1`</span><span class="sxs-lookup"><span data-stu-id="03a65-654">Min: `1`</span></span><br><span data-ttu-id="03a65-655">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="03a65-655">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="03a65-656">Atributo de cadeia de caracteres obrigatório.</span><span class="sxs-lookup"><span data-stu-id="03a65-656">Required string attribute.</span></span></p><p><span data-ttu-id="03a65-657">Caminho para o executável que inicia um processo que escuta solicitações HTTP.</span><span class="sxs-lookup"><span data-stu-id="03a65-657">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="03a65-658">Caminhos relativos são compatíveis.</span><span class="sxs-lookup"><span data-stu-id="03a65-658">Relative paths are supported.</span></span> <span data-ttu-id="03a65-659">Se o caminho começa com `.`, o caminho é considerado relativo à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="03a65-659">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="03a65-660">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-660">Optional integer attribute.</span></span></p><p><span data-ttu-id="03a65-661">Especifica o número de vezes que o processo especificado em **processPath** pode falhar por minuto.</span><span class="sxs-lookup"><span data-stu-id="03a65-661">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="03a65-662">Se esse limite for excedido, o módulo interromperá a inicialização do processo pelo restante do minuto.</span><span class="sxs-lookup"><span data-stu-id="03a65-662">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="03a65-663">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="03a65-663">Default: `10`</span></span><br><span data-ttu-id="03a65-664">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="03a65-664">Min: `0`</span></span><br><span data-ttu-id="03a65-665">Máx.: `100`</span><span class="sxs-lookup"><span data-stu-id="03a65-665">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="03a65-666">Atributo de intervalo de tempo opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-666">Optional timespan attribute.</span></span></p><p><span data-ttu-id="03a65-667">Especifica a duração para a qual o Módulo do ASP.NET Core aguarda uma resposta do processo que escuta em %ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="03a65-667">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="03a65-668">Em versões do Módulo do ASP.NET Core que acompanham a versão do ASP.NET Core 2.1 ou posterior, o `requestTimeout` é especificado em horas, minutos e segundos.</span><span class="sxs-lookup"><span data-stu-id="03a65-668">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="03a65-669">Padrão: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="03a65-669">Default: `00:02:00`</span></span><br><span data-ttu-id="03a65-670">Mín.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="03a65-670">Min: `00:00:00`</span></span><br><span data-ttu-id="03a65-671">Máx.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="03a65-671">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="03a65-672">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-672">Optional integer attribute.</span></span></p><p><span data-ttu-id="03a65-673">Duração em segundos que o módulo espera para o executável desligar normalmente quando o arquivo *app_offline.htm* é detectado.</span><span class="sxs-lookup"><span data-stu-id="03a65-673">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="03a65-674">Padrão: `10`</span><span class="sxs-lookup"><span data-stu-id="03a65-674">Default: `10`</span></span><br><span data-ttu-id="03a65-675">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="03a65-675">Min: `0`</span></span><br><span data-ttu-id="03a65-676">Máx.: `600`</span><span class="sxs-lookup"><span data-stu-id="03a65-676">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="03a65-677">Atributo inteiro opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-677">Optional integer attribute.</span></span></p><p><span data-ttu-id="03a65-678">Duração em segundos que o módulo espera para o arquivo executável iniciar um processo escutando na porta.</span><span class="sxs-lookup"><span data-stu-id="03a65-678">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="03a65-679">Se esse tempo limite é excedido, o módulo encerra o processo.</span><span class="sxs-lookup"><span data-stu-id="03a65-679">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="03a65-680">O módulo tentará reiniciar o processo quando ele receber uma nova solicitação e continuará a tentar reiniciar o processo em solicitações subsequentes de entrada, a menos que o aplicativo falhe em iniciar um número de vezes igual a **rapidFailsPerMinute** no último minuto sem interrupção.</span><span class="sxs-lookup"><span data-stu-id="03a65-680">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="03a65-681">Um valor de 0 (zero) **não** é considerado um tempo limite infinito.</span><span class="sxs-lookup"><span data-stu-id="03a65-681">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="03a65-682">Padrão: `120`</span><span class="sxs-lookup"><span data-stu-id="03a65-682">Default: `120`</span></span><br><span data-ttu-id="03a65-683">Mín.: `0`</span><span class="sxs-lookup"><span data-stu-id="03a65-683">Min: `0`</span></span><br><span data-ttu-id="03a65-684">Máx.: `3600`</span><span class="sxs-lookup"><span data-stu-id="03a65-684">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="03a65-685">Atributo booliano opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-685">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="03a65-686">Se for true, **stdout** e **stderr** para o processo especificado em **processPath** serão redirecionados para o arquivo especificado em **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="03a65-686">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="03a65-687">Atributo de cadeia de caracteres opcional.</span><span class="sxs-lookup"><span data-stu-id="03a65-687">Optional string attribute.</span></span></p><p><span data-ttu-id="03a65-688">Especifica o caminho relativo ou absoluto para o qual **stdout** e **stderr** do processo especificado em **processPath** são registrados em log.</span><span class="sxs-lookup"><span data-stu-id="03a65-688">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="03a65-689">Os caminhos relativos são relativos à raiz do site.</span><span class="sxs-lookup"><span data-stu-id="03a65-689">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="03a65-690">Qualquer caminho começando com `.` é relativo à raiz do site e todos os outros caminhos são tratados como caminhos absolutos.</span><span class="sxs-lookup"><span data-stu-id="03a65-690">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="03a65-691">As pastas fornecidas no caminho devem existir para que o módulo crie o arquivo de log.</span><span class="sxs-lookup"><span data-stu-id="03a65-691">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="03a65-692">Usando delimitadores de sublinhado, um carimbo de data/hora, uma ID de processo e a extensão de arquivo ( *.log*) são adicionados ao último segmento do caminho **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="03a65-692">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="03a65-693">Se `.\logs\stdout` é fornecido como um valor, um log de exemplo stdout é salvo como *stdout_20180205194132_1934.log* na pasta *logs* quando salvos em 5/2/2018, às 19:41:32, com uma ID de processo de 1934.</span><span class="sxs-lookup"><span data-stu-id="03a65-693">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="03a65-694">Definindo variáveis de ambiente</span><span class="sxs-lookup"><span data-stu-id="03a65-694">Setting environment variables</span></span>

<span data-ttu-id="03a65-695">Variáveis de ambiente podem ser especificadas para o processo no atributo `processPath`.</span><span class="sxs-lookup"><span data-stu-id="03a65-695">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="03a65-696">Especificar uma variável de ambiente com o elemento filho `<environmentVariable>` de um elemento de coleção `<environmentVariables>`.</span><span class="sxs-lookup"><span data-stu-id="03a65-696">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="03a65-697">As variáveis de ambiente definidas nesta seção são conflitantes com as variáveis de ambiente do sistema definidas com o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="03a65-697">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="03a65-698">Quando a variável de ambiente é definida no arquivo *web.config* e no nível do sistema do Windows, o valor do arquivo *web.config* fica anexado ao valor da variável de ambiente do sistema (por exemplo, `ASPNETCORE_ENVIRONMENT: Development;Development`), o que impede a inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-698">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="03a65-699">O exemplo a seguir define duas variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="03a65-699">The following example sets two environment variables.</span></span> <span data-ttu-id="03a65-700">`ASPNETCORE_ENVIRONMENT` configura o ambiente do aplicativo para `Development`.</span><span class="sxs-lookup"><span data-stu-id="03a65-700">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="03a65-701">Um desenvolvedor pode definir esse valor temporariamente no arquivo *web.config* para forçar o carregamento da [Página de Exceções do Desenvolvedor](xref:fundamentals/error-handling) ao depurar uma exceção de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-701">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="03a65-702">`CONFIG_DIR` é um exemplo de uma variável de ambiente definida pelo usuário, em que o desenvolvedor escreveu código que lê o valor de inicialização para formar um caminho no qual carregar o arquivo de configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-702">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="03a65-703">Defina a variável de ambiente apenas `ASPNETCORE_ENVIRONMENT` para `Development` em servidores de preparo e de teste que não estão acessíveis a redes não confiáveis, tais como a Internet.</span><span class="sxs-lookup"><span data-stu-id="03a65-703">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="03a65-704">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="03a65-704">app_offline.htm</span></span>

<span data-ttu-id="03a65-705">Se um arquivo com o nome *app_offline.htm* é detectado no diretório raiz de um aplicativo, o Módulo do ASP.NET Core tenta desligar normalmente o aplicativo e parar o processamento de solicitações de entrada.</span><span class="sxs-lookup"><span data-stu-id="03a65-705">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="03a65-706">Se o aplicativo ainda está em execução após o número de segundos definido em `shutdownTimeLimit`, o Módulo do ASP.NET Core encerra o processo em execução.</span><span class="sxs-lookup"><span data-stu-id="03a65-706">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="03a65-707">Enquanto o arquivo *app_offline.htm* estiver presente, o Módulo do ASP.NET Core responderá às solicitações enviando o conteúdo do arquivo *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="03a65-707">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="03a65-708">Quando o arquivo *app_offline.htm* é removido, a próxima solicitação inicia o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-708">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="03a65-709">Página de erro de inicialização</span><span class="sxs-lookup"><span data-stu-id="03a65-709">Start-up error page</span></span>

<span data-ttu-id="03a65-710">Se o Módulo do ASP.NET Core falhar ao iniciar o processo de back-end ou se o processo de back-end iniciar, mas falhar ao escutar na porta configurada, uma página de código de status *502.5 – falha no processo* será exibida.</span><span class="sxs-lookup"><span data-stu-id="03a65-710">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="03a65-711">Para omitir esta página e reverter para a página de código de status 502 padrão do IIS, use o atributo `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="03a65-711">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="03a65-712">Para obter mais informações sobre como configurar mensagens de erro personalizadas, veja [Erros HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="03a65-712">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![Página de código de status 502.5 – Falha do Processo](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="03a65-714">Criação de log e redirecionamento</span><span class="sxs-lookup"><span data-stu-id="03a65-714">Log creation and redirection</span></span>

<span data-ttu-id="03a65-715">O Módulo do ASP.NET Core redireciona as saídas de console stdout e stderr para o disco se os atributos `stdoutLogEnabled` e `stdoutLogFile` do elemento `aspNetCore` forem definidos.</span><span class="sxs-lookup"><span data-stu-id="03a65-715">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="03a65-716">Todas as pastas no caminho `stdoutLogFile` são criadas pelo módulo quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="03a65-716">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="03a65-717">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="03a65-717">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="03a65-718">Logs não sofrem rotação, a menos que ocorra a reciclagem/reinicialização do processo.</span><span class="sxs-lookup"><span data-stu-id="03a65-718">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="03a65-719">É responsabilidade do hoster limitar o espaço em disco consumido pelos logs.</span><span class="sxs-lookup"><span data-stu-id="03a65-719">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="03a65-720">Usar o log de stdout é recomendado apenas para solucionar problemas de inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-720">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="03a65-721">Não use o log de stdout para fins gerais de registro em log do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="03a65-721">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="03a65-722">Para registro em log de rotina em um aplicativo ASP.NET Core, use uma biblioteca de registro em log que limita o tamanho do arquivo de log e realiza a rotação de logs.</span><span class="sxs-lookup"><span data-stu-id="03a65-722">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="03a65-723">Para obter mais informações, veja [provedores de log de terceiros](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="03a65-723">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="03a65-724">Uma extensão de arquivo e um carimbo de data/hora são adicionados automaticamente quando o arquivo de log é criado.</span><span class="sxs-lookup"><span data-stu-id="03a65-724">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="03a65-725">O nome do arquivo de log é composto por meio do acréscimo do carimbo de data/hora, da ID do processo e da extensão de arquivo ( *.log*) para o último segmento do caminho `stdoutLogFile` (normalmente *stdout*), delimitados por sublinhados.</span><span class="sxs-lookup"><span data-stu-id="03a65-725">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="03a65-726">Se o caminho `stdoutLogFile` termina com *stdout*, um log para um aplicativo com um PID de 1934, criado em 5/2/2018 às 19:42:32, tem o nome de arquivo *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="03a65-726">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="03a65-727">O elemento `aspNetCore` de exemplo a seguir configura o registro em log de stdout para um aplicativo hospedado no Serviço de Aplicativo do Azure.</span><span class="sxs-lookup"><span data-stu-id="03a65-727">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="03a65-728">Um caminho local ou um caminho de compartilhamento de rede é aceitável para o registro em log local.</span><span class="sxs-lookup"><span data-stu-id="03a65-728">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="03a65-729">Confirme se a identidade do usuário AppPool tem permissão para gravar no caminho fornecido.</span><span class="sxs-lookup"><span data-stu-id="03a65-729">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

<span data-ttu-id="03a65-730">As pastas no caminho fornecido para o valor `<handlerSetting>` (*logs* no exemplo anterior) não são criadas automaticamente pelo módulo e devem existir previamente na implantação.</span><span class="sxs-lookup"><span data-stu-id="03a65-730">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="03a65-731">O pool de aplicativos deve ter acesso de gravação ao local em que os logs foram gravados (use `IIS AppPool\<app_pool_name>` para fornecer permissão de gravação).</span><span class="sxs-lookup"><span data-stu-id="03a65-731">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="03a65-732">Veja [Configuração com web.config](#configuration-with-webconfig) para obter um exemplo do elemento `aspNetCore` no arquivo *web.config*.</span><span class="sxs-lookup"><span data-stu-id="03a65-732">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="03a65-733">A configuração de proxy usa o protocolo HTTP e um token de emparelhamento</span><span class="sxs-lookup"><span data-stu-id="03a65-733">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="03a65-734">O proxy criado entre o Módulo do ASP.NET Core e o Kestrel usa o protocolo HTTP.</span><span class="sxs-lookup"><span data-stu-id="03a65-734">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="03a65-735">Não há nenhum risco de interceptação do tráfego entre o módulo e o Kestrel em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="03a65-735">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="03a65-736">Um token de emparelhamento é usado para assegurar que as solicitações recebidas pelo Kestrel foram transmitidas por proxy pelo IIS e que não são provenientes de outra origem.</span><span class="sxs-lookup"><span data-stu-id="03a65-736">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="03a65-737">O token de emparelhamento é criado e definido em uma variável de ambiente (`ASPNETCORE_TOKEN`) pelo módulo.</span><span class="sxs-lookup"><span data-stu-id="03a65-737">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="03a65-738">O token de emparelhamento também é definido em um cabeçalho (`MS-ASPNETCORE-TOKEN`) em cada solicitação com proxy.</span><span class="sxs-lookup"><span data-stu-id="03a65-738">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="03a65-739">O Middleware do IIS verifica cada solicitação recebida para confirmar se o valor de cabeçalho do token de emparelhamento corresponde ao valor da variável de ambiente.</span><span class="sxs-lookup"><span data-stu-id="03a65-739">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="03a65-740">Se os valores do token forem incompatíveis, a solicitação será registrada em log e rejeitada.</span><span class="sxs-lookup"><span data-stu-id="03a65-740">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="03a65-741">A variável de ambiente do token de emparelhamento e o tráfego entre o módulo e o Kestrel não são acessíveis em um local fora do servidor.</span><span class="sxs-lookup"><span data-stu-id="03a65-741">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="03a65-742">Sem saber o valor do token de emparelhamento, um invasor não pode enviar solicitações que ignoram a verificação no Middleware do IIS.</span><span class="sxs-lookup"><span data-stu-id="03a65-742">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="03a65-743">Módulo do ASP.NET Core com uma configuração do IIS compartilhada</span><span class="sxs-lookup"><span data-stu-id="03a65-743">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="03a65-744">O instalador do módulo do ASP.NET Core é executado com os privilégios da conta de **TrustedInstaller**.</span><span class="sxs-lookup"><span data-stu-id="03a65-744">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="03a65-745">Já que a conta de sistema local não tem permissão para modificar o caminho do compartilhamento usado pela configuração compartilhada de IIS, o instalador gera um erro de acesso negado ao tentar definir as configurações de módulo no arquivo *applicationHost.config* no compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="03a65-745">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="03a65-746">Ao usar uma configuração compartilhada de IIS, siga estas etapas:</span><span class="sxs-lookup"><span data-stu-id="03a65-746">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="03a65-747">Desabilite a configuração compartilhada de IIS.</span><span class="sxs-lookup"><span data-stu-id="03a65-747">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="03a65-748">Execute o instalador.</span><span class="sxs-lookup"><span data-stu-id="03a65-748">Run the installer.</span></span>
1. <span data-ttu-id="03a65-749">Exportar o arquivo *applicationHost.config* atualizado para o compartilhamento.</span><span class="sxs-lookup"><span data-stu-id="03a65-749">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="03a65-750">Reabilite a Configuração Compartilhada do IIS.</span><span class="sxs-lookup"><span data-stu-id="03a65-750">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="03a65-751">Versão do módulo e logs do instalador do pacote de hospedagem</span><span class="sxs-lookup"><span data-stu-id="03a65-751">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="03a65-752">Para determinar a versão do Módulo do ASP.NET Core instalado:</span><span class="sxs-lookup"><span data-stu-id="03a65-752">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="03a65-753">No sistema de hospedagem, navegue até *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="03a65-753">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="03a65-754">Localize o arquivo *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="03a65-754">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="03a65-755">Clique com o botão direito do mouse no arquivo e selecione **Propriedades** no menu contextual.</span><span class="sxs-lookup"><span data-stu-id="03a65-755">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="03a65-756">Selecione a guia **detalhes** . A versão do **arquivo** e a **versão do produto** representam a versão instalada do módulo.</span><span class="sxs-lookup"><span data-stu-id="03a65-756">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="03a65-757">Os logs do instalador do pacote de hospedagem para o módulo são encontrados em *C: \\Users \\% username% \\AppData \\Local \\Temp*. O arquivo é denominado *dd_DotNetCoreWinSvrHosting__ \<timestamp > _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="03a65-757">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="03a65-758">Locais dos arquivos de módulo, de esquema e de configuração</span><span class="sxs-lookup"><span data-stu-id="03a65-758">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="03a65-759">Módulo</span><span class="sxs-lookup"><span data-stu-id="03a65-759">Module</span></span>

<span data-ttu-id="03a65-760">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="03a65-760">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="03a65-761">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-761">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="03a65-762">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-762">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="03a65-763">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="03a65-763">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="03a65-764">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-764">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="03a65-765">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="03a65-765">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="03a65-766">Esquema</span><span class="sxs-lookup"><span data-stu-id="03a65-766">Schema</span></span>

<span data-ttu-id="03a65-767">**IIS**</span><span class="sxs-lookup"><span data-stu-id="03a65-767">**IIS**</span></span>

* <span data-ttu-id="03a65-768">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="03a65-768">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="03a65-769">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="03a65-769">**IIS Express**</span></span>

* <span data-ttu-id="03a65-770">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="03a65-770">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="03a65-771">Configuração</span><span class="sxs-lookup"><span data-stu-id="03a65-771">Configuration</span></span>

<span data-ttu-id="03a65-772">**IIS**</span><span class="sxs-lookup"><span data-stu-id="03a65-772">**IIS**</span></span>

* <span data-ttu-id="03a65-773">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="03a65-773">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="03a65-774">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="03a65-774">**IIS Express**</span></span>

* <span data-ttu-id="03a65-775">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="03a65-775">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="03a65-776">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="03a65-776">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="03a65-777">Os arquivos podem ser encontrados pesquisando por *aspnetcore* no arquivo *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="03a65-777">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="03a65-778">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="03a65-778">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="03a65-779">Repositório do GitHub do Módulo do ASP.NET Core (origem de referência)</span><span class="sxs-lookup"><span data-stu-id="03a65-779">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
