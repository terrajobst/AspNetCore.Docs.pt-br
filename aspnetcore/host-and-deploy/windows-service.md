---
title: Hospedar o ASP.NET Core em um serviço Windows
author: guardrex
description: Saiba como hospedar um aplicativo ASP.NET Core em um serviço Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: d4b540de50f4153f517f871f037521347fb5eb84
ms.sourcegitcommit: 990a4c2e623c202a27f60bdf3902f250359c13be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2020
ms.locfileid: "76971999"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="727ea-103">Hospedar o ASP.NET Core em um serviço Windows</span><span class="sxs-lookup"><span data-stu-id="727ea-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="727ea-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="727ea-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="727ea-105">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="727ea-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="727ea-106">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="727ea-106">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="727ea-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="727ea-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="727ea-108">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="727ea-108">Prerequisites</span></span>

* [<span data-ttu-id="727ea-109">SDK do ASP.NET Core 2.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="727ea-109">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="727ea-110">PowerShell 6.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="727ea-110">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a><span data-ttu-id="727ea-111">Modelo de serviço de trabalho</span><span class="sxs-lookup"><span data-stu-id="727ea-111">Worker Service template</span></span>

<span data-ttu-id="727ea-112">O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada.</span><span class="sxs-lookup"><span data-stu-id="727ea-112">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="727ea-113">Para usar o modelo como base para um aplicativo de Serviço Windows:</span><span class="sxs-lookup"><span data-stu-id="727ea-113">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="727ea-114">Crie um aplicativo de serviço de trabalho usando o modelo do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="727ea-114">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="727ea-115">Siga as orientações na seção [Configuração de aplicativos](#app-configuration) para atualizar o aplicativo do Serviço de Trabalho para que ele possa ser executado como um Serviço Windows.</span><span class="sxs-lookup"><span data-stu-id="727ea-115">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

::: moniker-end

## <a name="app-configuration"></a><span data-ttu-id="727ea-116">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="727ea-116">App configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="727ea-117">O aplicativo requer uma referência de pacote para [Microsoft. Extensions. host. WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="727ea-117">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="727ea-118">`IHostBuilder.UseWindowsService` é chamado ao compilar o host.</span><span class="sxs-lookup"><span data-stu-id="727ea-118">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="727ea-119">Se o aplicativo estiver sendo executado como um Serviço Windows, o método:</span><span class="sxs-lookup"><span data-stu-id="727ea-119">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="727ea-120">Define o tempo de vida do host como `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="727ea-120">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="727ea-121">Define a [raiz do conteúdo](xref:fundamentals/index#content-root) como [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="727ea-121">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="727ea-122">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="727ea-122">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="727ea-123">Habilita o registro de log no log de eventos com o nome do aplicativo como o nome da fonte padrão.</span><span class="sxs-lookup"><span data-stu-id="727ea-123">Enables logging to the event log with the application name as the default source name.</span></span>
  * <span data-ttu-id="727ea-124">O nível do log pode ser configurado com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="727ea-124">The log level can be configured using the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>
  * <span data-ttu-id="727ea-125">Somente administradores podem criar novas fontes de evento.</span><span class="sxs-lookup"><span data-stu-id="727ea-125">Only administrators can create new event sources.</span></span> <span data-ttu-id="727ea-126">Quando uma fonte de evento não puder ser criada usando o nome do aplicativo, um aviso será registrado em log como a fonte do *Aplicativo* e os logs de eventos serão desabilitados.</span><span class="sxs-lookup"><span data-stu-id="727ea-126">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="727ea-127">Em `CreateHostBuilder` de *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="727ea-127">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="727ea-128">Os aplicativos de exemplo a seguir acompanham este tópico:</span><span class="sxs-lookup"><span data-stu-id="727ea-128">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="727ea-129">Exemplo de serviço de trabalho em segundo plano &ndash; um exemplo de aplicativo não Web baseado no [modelo de serviço de trabalho](#worker-service-template) que usa [serviços hospedados](xref:fundamentals/host/hosted-services) para tarefas em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="727ea-129">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="727ea-130">Exemplo de serviço de aplicativo Web &ndash; um exemplo de aplicativo Web Razor Pages que é executado como um serviço do Windows com [serviços hospedados](xref:fundamentals/host/hosted-services) para tarefas em segundo plano.</span><span class="sxs-lookup"><span data-stu-id="727ea-130">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="727ea-131">Para obter diretrizes do MVC, consulte os artigos em <xref:mvc/overview> e <xref:migration/22-to-30>.</span><span class="sxs-lookup"><span data-stu-id="727ea-131">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="727ea-132">O aplicativo exige referências de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="727ea-132">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="727ea-133">Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="727ea-133">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="727ea-134">Verifique se o depurador está anexado ou se uma opção `--console` está presente.</span><span class="sxs-lookup"><span data-stu-id="727ea-134">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="727ea-135">Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="727ea-135">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="727ea-136">Se as condições forem falsas (o aplicativo for executado como um serviço):</span><span class="sxs-lookup"><span data-stu-id="727ea-136">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="727ea-137">Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="727ea-137">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="727ea-138">Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="727ea-138">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="727ea-139">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="727ea-139">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="727ea-140">Esta etapa é executada antes que o aplicativo seja configurado em `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="727ea-140">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="727ea-141">Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="727ea-141">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="727ea-142">Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.</span><span class="sxs-lookup"><span data-stu-id="727ea-142">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="727ea-143">Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="727ea-143">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="727ea-144">Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.</span><span class="sxs-lookup"><span data-stu-id="727ea-144">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="727ea-145">No exemplo a seguir do aplicativo de exemplo, `RunAsCustomService` é chamado em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para manipular eventos de tempo de vida no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="727ea-145">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="727ea-146">Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="727ea-146">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a><span data-ttu-id="727ea-147">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="727ea-147">Deployment type</span></span>

<span data-ttu-id="727ea-148">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="727ea-148">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="727ea-149">SDK</span><span class="sxs-lookup"><span data-stu-id="727ea-149">SDK</span></span>

<span data-ttu-id="727ea-150">Para um serviço baseado em aplicativo Web que usa as estruturas Razor Pages ou MVC, especifique o SDK Web no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="727ea-150">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="727ea-151">Se o serviço executar apenas tarefas em segundo plano (por exemplo, [serviços hospedados](xref:fundamentals/host/hosted-services)), ESPECIFIQUE o SDK do trabalhador no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="727ea-151">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="727ea-152">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="727ea-152">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="727ea-153">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="727ea-153">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="727ea-154">Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável ( *.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="727ea-154">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="727ea-155">Se você estiver usando o [SDK da Web](#sdk), um arquivo *Web. config* , que normalmente é produzido ao publicar um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="727ea-155">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="727ea-156">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="727ea-156">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="727ea-157">O [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="727ea-157">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="727ea-158">No exemplo a seguir, o RID é especificado como `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="727ea-158">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="727ea-159">A propriedade `<SelfContained>` é definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="727ea-159">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="727ea-160">Essas propriedades instruem o SDK a gerar um arquivo executável ( *.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="727ea-160">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="727ea-161">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="727ea-161">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="727ea-162">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="727ea-162">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="727ea-163">O [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="727ea-163">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="727ea-164">No exemplo a seguir, o RID é especificado como `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="727ea-164">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="727ea-165">A propriedade `<SelfContained>` é definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="727ea-165">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="727ea-166">Essas propriedades instruem o SDK a gerar um arquivo executável ( *.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="727ea-166">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="727ea-167">A propriedade `<UseAppHost>` é definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="727ea-167">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="727ea-168">Essa propriedade fornece o serviço com um caminho de ativação (um arquivo executável *.exe*) para FDD.</span><span class="sxs-lookup"><span data-stu-id="727ea-168">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="727ea-169">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="727ea-169">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="727ea-170">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="727ea-170">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="727ea-171">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="727ea-171">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="727ea-172">A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="727ea-172">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="727ea-173">O runtime e as dependências do aplicativo são implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="727ea-173">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="727ea-174">Um [RID (Identificador do Runtime do Windows)](/dotnet/core/rid-catalog) está incluído no `<PropertyGroup>` que contém a estrutura de destino:</span><span class="sxs-lookup"><span data-stu-id="727ea-174">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="727ea-175">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="727ea-175">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="727ea-176">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="727ea-176">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="727ea-177">Use o nome da propriedade [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="727ea-177">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="727ea-178">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="727ea-178">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="727ea-179">Uma propriedade `<SelfContained>` está definida como `true`:</span><span class="sxs-lookup"><span data-stu-id="727ea-179">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a><span data-ttu-id="727ea-180">Conta de usuário do serviço</span><span class="sxs-lookup"><span data-stu-id="727ea-180">Service user account</span></span>

<span data-ttu-id="727ea-181">Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="727ea-181">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="727ea-182">Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="727ea-182">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```PowerShell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="727ea-183">No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="727ea-183">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="727ea-184">Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.</span><span class="sxs-lookup"><span data-stu-id="727ea-184">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="727ea-185">A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.</span><span class="sxs-lookup"><span data-stu-id="727ea-185">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="727ea-186">Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="727ea-186">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="727ea-187">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="727ea-187">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="727ea-188">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="727ea-188">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="727ea-189">Fazer logon como direitos de um serviço</span><span class="sxs-lookup"><span data-stu-id="727ea-189">Log on as a service rights</span></span>

<span data-ttu-id="727ea-190">Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:</span><span class="sxs-lookup"><span data-stu-id="727ea-190">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="727ea-191">Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="727ea-191">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="727ea-192">Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.</span><span class="sxs-lookup"><span data-stu-id="727ea-192">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="727ea-193">Abra a política **Fazer logon como um serviço**.</span><span class="sxs-lookup"><span data-stu-id="727ea-193">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="727ea-194">Selecione **Adicionar Usuário ou Grupo**.</span><span class="sxs-lookup"><span data-stu-id="727ea-194">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="727ea-195">Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="727ea-195">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="727ea-196">Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="727ea-196">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="727ea-197">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="727ea-197">Select **Advanced**.</span></span> <span data-ttu-id="727ea-198">Escolha **Localizar Agora**.</span><span class="sxs-lookup"><span data-stu-id="727ea-198">Select **Find Now**.</span></span> <span data-ttu-id="727ea-199">Escolha a conta de usuário na lista.</span><span class="sxs-lookup"><span data-stu-id="727ea-199">Select the user account from the list.</span></span> <span data-ttu-id="727ea-200">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="727ea-200">Select **OK**.</span></span> <span data-ttu-id="727ea-201">Escolha **OK** novamente para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="727ea-201">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="727ea-202">Escolha **OK** ou **Aplicar** para aceitar as alterações.</span><span class="sxs-lookup"><span data-stu-id="727ea-202">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="727ea-203">Criar e gerenciar o Serviço Windows</span><span class="sxs-lookup"><span data-stu-id="727ea-203">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="727ea-204">Criar um serviço</span><span class="sxs-lookup"><span data-stu-id="727ea-204">Create a service</span></span>

<span data-ttu-id="727ea-205">Use comandos do PowerShell para registrar um serviço.</span><span class="sxs-lookup"><span data-stu-id="727ea-205">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="727ea-206">Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="727ea-206">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="727ea-207">`{EXE PATH}` &ndash; caminho para a pasta do aplicativo no host (por exemplo, `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="727ea-207">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="727ea-208">Não inclua o executável do aplicativo no caminho.</span><span class="sxs-lookup"><span data-stu-id="727ea-208">Don't include the app's executable in the path.</span></span> <span data-ttu-id="727ea-209">A barra à direita não é necessária.</span><span class="sxs-lookup"><span data-stu-id="727ea-209">A trailing slash isn't required.</span></span>
* <span data-ttu-id="727ea-210">`{DOMAIN OR COMPUTER NAME\USER}` conta de usuário do serviço de &ndash; (por exemplo, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="727ea-210">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="727ea-211">`{SERVICE NAME}` &ndash; nome do serviço (por exemplo, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="727ea-211">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="727ea-212">`{EXE FILE PATH}` &ndash; o caminho do executável do aplicativo (por exemplo, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="727ea-212">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="727ea-213">Inclua nome do arquivo do executável com a extensão.</span><span class="sxs-lookup"><span data-stu-id="727ea-213">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="727ea-214">`{DESCRIPTION}` descrição do serviço &ndash; (por exemplo, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="727ea-214">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="727ea-215">`{DISPLAY NAME}` nome de exibição do serviço &ndash; (por exemplo, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="727ea-215">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="727ea-216">Iniciar um serviço</span><span class="sxs-lookup"><span data-stu-id="727ea-216">Start a service</span></span>

<span data-ttu-id="727ea-217">Inicie o serviço com o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="727ea-217">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="727ea-218">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="727ea-218">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="727ea-219">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="727ea-219">Determine a service's status</span></span>

<span data-ttu-id="727ea-220">Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="727ea-220">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="727ea-221">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="727ea-221">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="727ea-222">Parar um serviço</span><span class="sxs-lookup"><span data-stu-id="727ea-222">Stop a service</span></span>

<span data-ttu-id="727ea-223">Pare um serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="727ea-223">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="727ea-224">Remover um serviço</span><span class="sxs-lookup"><span data-stu-id="727ea-224">Remove a service</span></span>

<span data-ttu-id="727ea-225">Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="727ea-225">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="727ea-226">Manipular eventos de início e de parada</span><span class="sxs-lookup"><span data-stu-id="727ea-226">Handle starting and stopping events</span></span>

<span data-ttu-id="727ea-227">Para tratar dos eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="727ea-227">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="727ea-228">Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="727ea-228">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="727ea-229">Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="727ea-229">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="727ea-230">Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="727ea-230">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="727ea-231">Para ver o local de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, confira o exemplo de código mostrado na seção [Tipo de implantação](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="727ea-231">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="727ea-232">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="727ea-232">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="727ea-233">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="727ea-233">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="727ea-234">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="727ea-234">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="727ea-235">Configurar pontos de extremidade</span><span class="sxs-lookup"><span data-stu-id="727ea-235">Configure endpoints</span></span>

<span data-ttu-id="727ea-236">Por padrão, o ASP.NET Core é associado a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="727ea-236">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="727ea-237">Configure a URL e a porta definindo a variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="727ea-237">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="727ea-238">Para obter mais abordagens de configuração de porta e URL, consulte o artigo relevante do servidor:</span><span class="sxs-lookup"><span data-stu-id="727ea-238">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="727ea-239">As diretrizes anteriores abordam o suporte para pontos de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="727ea-239">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="727ea-240">Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um serviço do Windows.</span><span class="sxs-lookup"><span data-stu-id="727ea-240">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="727ea-241">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="727ea-241">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="727ea-242">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="727ea-242">Current directory and content root</span></span>

<span data-ttu-id="727ea-243">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="727ea-243">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="727ea-244">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="727ea-244">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="727ea-245">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="727ea-245">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="727ea-246">Usar ContentRootPath ou ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="727ea-246">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="727ea-247">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) ou <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> para localizar os recursos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="727ea-247">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="727ea-248">Quando o aplicativo é executado como um serviço, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> define o <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> como [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).</span><span class="sxs-lookup"><span data-stu-id="727ea-248">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="727ea-249">Os arquivos de configurações padrão do aplicativo, *appSettings. JSON* e *appSettings. { Environment}. JSON*, são carregados a partir da raiz do conteúdo do aplicativo chamando [CreateDefaultBuilder durante a construção do host](xref:fundamentals/host/generic-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="727ea-249">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="727ea-250">Para outros arquivos de configurações carregados pelo código do desenvolvedor no <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, não há necessidade de chamar <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="727ea-250">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="727ea-251">No exemplo a seguir, o arquivo *custom_settings. JSON* existe na raiz do conteúdo do aplicativo e é carregado sem definir explicitamente um caminho base:</span><span class="sxs-lookup"><span data-stu-id="727ea-251">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="727ea-252">Não tente usar <xref:System.IO.Directory.GetCurrentDirectory*> para obter um caminho de recurso porque um aplicativo de serviço do Windows retorna a pasta *C:\\Windows\\system32* como seu diretório atual.</span><span class="sxs-lookup"><span data-stu-id="727ea-252">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="727ea-253">Defina o caminho da raiz do conteúdo para a pasta do aplicativo</span><span class="sxs-lookup"><span data-stu-id="727ea-253">Set the content root path to the app's folder</span></span>

<span data-ttu-id="727ea-254">O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando um serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="727ea-254">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="727ea-255">Em vez de chamar `GetCurrentDirectory` para criar caminhos para arquivos de configurações, chame <xref:System.IO.Directory.SetCurrentDirectory*> com o caminho para a [raiz do conteúdo](xref:fundamentals/index#content-root)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="727ea-255">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="727ea-256">No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="727ea-256">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="727ea-257">Armazenar os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="727ea-257">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="727ea-258">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="727ea-258">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="727ea-259">Solução de problemas</span><span class="sxs-lookup"><span data-stu-id="727ea-259">Troubleshoot</span></span>

<span data-ttu-id="727ea-260">Para solucionar problemas de um aplicativo de serviço do Windows, consulte <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="727ea-260">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="727ea-261">Erros comuns</span><span class="sxs-lookup"><span data-stu-id="727ea-261">Common errors</span></span>

* <span data-ttu-id="727ea-262">Uma versão antiga ou de pré-lançamento do PowerShell está em uso.</span><span class="sxs-lookup"><span data-stu-id="727ea-262">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="727ea-263">O serviço registrado não usa a saída **publicada** do aplicativo do comando [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="727ea-263">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="727ea-264">A saída do comando [dotnet Build](/dotnet/core/tools/dotnet-build) não tem suporte para implantação de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="727ea-264">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="727ea-265">Os ativos publicados são encontrados em uma das seguintes pastas, dependendo do tipo de implantação:</span><span class="sxs-lookup"><span data-stu-id="727ea-265">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="727ea-266">FDD do *compartimento/versão/{estrutura de destino}* (a)</span><span class="sxs-lookup"><span data-stu-id="727ea-266">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="727ea-267">*bin/Release/{Framework de destino} identificador de/{Runtime}/Publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="727ea-267">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="727ea-268">O serviço não está em estado de execução.</span><span class="sxs-lookup"><span data-stu-id="727ea-268">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="727ea-269">Os caminhos para os recursos que o aplicativo usa (por exemplo, certificados) estão incorretos.</span><span class="sxs-lookup"><span data-stu-id="727ea-269">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="727ea-270">O caminho base de um serviço do Windows é *c:\\Windows\\system32*.</span><span class="sxs-lookup"><span data-stu-id="727ea-270">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="727ea-271">O usuário não tem direitos de *logon como um serviço* .</span><span class="sxs-lookup"><span data-stu-id="727ea-271">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="727ea-272">A senha do usuário expirou ou foi passada incorretamente ao executar o comando `New-Service` PowerShell.</span><span class="sxs-lookup"><span data-stu-id="727ea-272">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="727ea-273">O aplicativo requer autenticação ASP.NET Core, mas não está configurado para conexões seguras (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="727ea-273">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="727ea-274">A porta da URL da solicitação está incorreta ou não está configurada corretamente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="727ea-274">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="727ea-275">Logs de eventos do sistema e do aplicativo</span><span class="sxs-lookup"><span data-stu-id="727ea-275">System and Application Event Logs</span></span>

<span data-ttu-id="727ea-276">Acesse os logs de eventos do sistema e do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="727ea-276">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="727ea-277">Abra o menu Iniciar, procure *Visualizador de eventos*e selecione o aplicativo **Visualizador de eventos** .</span><span class="sxs-lookup"><span data-stu-id="727ea-277">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="727ea-278">No **Visualizador de Eventos**, abra o nó **Logs do Windows**.</span><span class="sxs-lookup"><span data-stu-id="727ea-278">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="727ea-279">Selecione **sistema** para abrir o log de eventos do sistema.</span><span class="sxs-lookup"><span data-stu-id="727ea-279">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="727ea-280">Selecione **Aplicativo** para abrir o Log de Eventos do Aplicativo.</span><span class="sxs-lookup"><span data-stu-id="727ea-280">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="727ea-281">Procure erros associados ao aplicativo com falha.</span><span class="sxs-lookup"><span data-stu-id="727ea-281">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="727ea-282">Execute o aplicativo em um prompt de comando</span><span class="sxs-lookup"><span data-stu-id="727ea-282">Run the app at a command prompt</span></span>

<span data-ttu-id="727ea-283">Muitos erros de inicialização não produzem informações úteis nos logs de eventos.</span><span class="sxs-lookup"><span data-stu-id="727ea-283">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="727ea-284">Você pode encontrar a causa de alguns erros ao executar o aplicativo em um prompt de comando no sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="727ea-284">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="727ea-285">Para registrar em log detalhes adicionais do aplicativo, reduza o [nível de log](xref:fundamentals/logging/index#log-level) ou execute o aplicativo no [ambiente de desenvolvimento](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="727ea-285">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="727ea-286">Limpar caches de pacote</span><span class="sxs-lookup"><span data-stu-id="727ea-286">Clear package caches</span></span>

<span data-ttu-id="727ea-287">Um aplicativo em funcionamento pode falhar imediatamente após a atualização do SDK do .NET Core no computador de desenvolvimento ou a alteração das versões do pacote no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="727ea-287">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="727ea-288">Em alguns casos, pacotes incoerentes podem interromper um aplicativo ao executar atualizações principais.</span><span class="sxs-lookup"><span data-stu-id="727ea-288">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="727ea-289">A maioria desses problemas pode ser corrigida seguindo estas instruções:</span><span class="sxs-lookup"><span data-stu-id="727ea-289">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="727ea-290">Exclua as pastas *bin* e *obj*.</span><span class="sxs-lookup"><span data-stu-id="727ea-290">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="727ea-291">Limpe os caches de pacote executando [dotnet NuGet local All--Clear](/dotnet/core/tools/dotnet-nuget-locals) de um shell de comando.</span><span class="sxs-lookup"><span data-stu-id="727ea-291">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="727ea-292">A limpeza dos caches de pacote também pode ser realizada com a ferramenta [NuGet. exe](https://www.nuget.org/downloads) e a execução do comando `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="727ea-292">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="727ea-293">*nuget.exe* não é uma instalação fornecida com o sistema operacional Windows Desktop e devem ser obtidos separadamente do [site do NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="727ea-293">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="727ea-294">Restaure e recompile o projeto.</span><span class="sxs-lookup"><span data-stu-id="727ea-294">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="727ea-295">Exclua todos os arquivos na pasta de implantação no servidor antes de reimplantar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="727ea-295">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="727ea-296">Aplicativo lento ou travando</span><span class="sxs-lookup"><span data-stu-id="727ea-296">Slow or hanging app</span></span>

<span data-ttu-id="727ea-297">Um *despejo* é um instantâneo da memória do sistema e pode ajudar a determinar a causa de uma falha de aplicativo, de inicialização ou de um aplicativo lento.</span><span class="sxs-lookup"><span data-stu-id="727ea-297">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="727ea-298">O aplicativo falha ou encontra uma exceção</span><span class="sxs-lookup"><span data-stu-id="727ea-298">App crashes or encounters an exception</span></span>

<span data-ttu-id="727ea-299">Obter e analisar um despejo de memória do [WER (Relatório de Erros do Windows)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="727ea-299">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="727ea-300">Crie uma pasta para armazenar os arquivos de despejo de memória em `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="727ea-300">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="727ea-301">Execute o [script do PowerShell do EnableDumps](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) com o nome do executável do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="727ea-301">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="727ea-302">Execute o aplicativo sob as condições que causam a falha.</span><span class="sxs-lookup"><span data-stu-id="727ea-302">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="727ea-303">Após a falha, execute o [script DisableDumps do PowerShell](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="727ea-303">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="727ea-304">Depois que um aplicativo falhar e a coleta de despejo de memória for concluída, o aplicativo terá permissão para encerrar normalmente.</span><span class="sxs-lookup"><span data-stu-id="727ea-304">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="727ea-305">O script do PowerShell configura o WER para coletar até cinco despejos de memória por aplicativo.</span><span class="sxs-lookup"><span data-stu-id="727ea-305">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="727ea-306">Os despejos de memória podem usar uma grande quantidade de espaço em disco (até vários gigabytes cada).</span><span class="sxs-lookup"><span data-stu-id="727ea-306">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="727ea-307">O aplicativo trava, falha durante a inicialização ou executa normalmente</span><span class="sxs-lookup"><span data-stu-id="727ea-307">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="727ea-308">Quando um aplicativo *paralisa* (para de responder, mas não falha), falha durante a inicialização ou é executado normalmente, consulte [arquivos de despejo no modo de usuário: escolhendo a melhor ferramenta](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) para selecionar uma ferramenta apropriada para produzir o despejo.</span><span class="sxs-lookup"><span data-stu-id="727ea-308">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="727ea-309">Analisar o despejo de memória</span><span class="sxs-lookup"><span data-stu-id="727ea-309">Analyze the dump</span></span>

<span data-ttu-id="727ea-310">Um despejo de memória pode ser analisado usando várias abordagens.</span><span class="sxs-lookup"><span data-stu-id="727ea-310">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="727ea-311">Para obter mais informações, confira [Analisando um arquivo de despejo de memória do modo de usuário](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="727ea-311">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="727ea-312">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="727ea-312">Additional resources</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="727ea-313">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="727ea-313">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="727ea-314">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="727ea-314">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
