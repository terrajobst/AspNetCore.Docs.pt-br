---
title: Hospedar o ASP.NET Core em um serviço Windows
author: guardrex
description: Saiba como hospedar um aplicativo ASP.NET Core em um serviço Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: b02e627af875f15a81d68b0d625a2eccf25c0657
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333799"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="719cd-103">Hospedar o ASP.NET Core em um serviço Windows</span><span class="sxs-lookup"><span data-stu-id="719cd-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="719cd-104">Por [Luke Latham](https://github.com/guardrex) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="719cd-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="719cd-105">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="719cd-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="719cd-106">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="719cd-106">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="719cd-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="719cd-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="719cd-108">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="719cd-108">Prerequisites</span></span>

* [<span data-ttu-id="719cd-109">SDK do ASP.NET Core 2.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="719cd-109">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="719cd-110">PowerShell 6.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="719cd-110">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a><span data-ttu-id="719cd-111">Modelo de serviço de trabalho</span><span class="sxs-lookup"><span data-stu-id="719cd-111">Worker Service template</span></span>

<span data-ttu-id="719cd-112">O modelo de Serviço de Trabalho do ASP.NET Core fornece um ponto inicial para escrever aplicativos de serviço de execução prolongada.</span><span class="sxs-lookup"><span data-stu-id="719cd-112">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="719cd-113">Para usar o modelo como base para um aplicativo de Serviço Windows:</span><span class="sxs-lookup"><span data-stu-id="719cd-113">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="719cd-114">Crie um aplicativo de serviço de trabalho usando o modelo do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="719cd-114">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="719cd-115">Siga as orientações na seção [Configuração de aplicativos](#app-configuration) para atualizar o aplicativo do Serviço de Trabalho para que ele possa ser executado como um Serviço Windows.</span><span class="sxs-lookup"><span data-stu-id="719cd-115">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

---

::: moniker-end

## <a name="app-configuration"></a><span data-ttu-id="719cd-116">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="719cd-116">App configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="719cd-117">`IHostBuilder.UseWindowsService`, fornecido pelo pacote [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices), é chamado na compilação do host.</span><span class="sxs-lookup"><span data-stu-id="719cd-117">`IHostBuilder.UseWindowsService`, provided by the [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) package, is called when building the host.</span></span> <span data-ttu-id="719cd-118">Se o aplicativo estiver sendo executado como um Serviço Windows, o método:</span><span class="sxs-lookup"><span data-stu-id="719cd-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="719cd-119">Define o tempo de vida do host como `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="719cd-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="719cd-120">Define a [raiz do conteúdo](xref:fundamentals/index#content-root).</span><span class="sxs-lookup"><span data-stu-id="719cd-120">Sets the [content root](xref:fundamentals/index#content-root).</span></span>
* <span data-ttu-id="719cd-121">Habilita o registro de log no log de eventos com o nome do aplicativo como o nome da fonte padrão.</span><span class="sxs-lookup"><span data-stu-id="719cd-121">Enables logging to the event log with the application name as the default source name.</span></span>
  * <span data-ttu-id="719cd-122">O nível do log pode ser configurado com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="719cd-122">The log level can be configured using the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>
  * <span data-ttu-id="719cd-123">Somente administradores podem criar novas fontes de evento.</span><span class="sxs-lookup"><span data-stu-id="719cd-123">Only administrators can create new event sources.</span></span> <span data-ttu-id="719cd-124">Quando uma fonte de evento não puder ser criada usando o nome do aplicativo, um aviso será registrado em log como a fonte do *Aplicativo* e os logs de eventos serão desabilitados.</span><span class="sxs-lookup"><span data-stu-id="719cd-124">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

[!code-csharp[](windows-service/samples/3.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="719cd-125">O aplicativo exige referências de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="719cd-125">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="719cd-126">Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="719cd-126">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="719cd-127">Verifique se o depurador está anexado ou se uma opção `--console` está presente.</span><span class="sxs-lookup"><span data-stu-id="719cd-127">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="719cd-128">Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="719cd-128">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="719cd-129">Se as condições forem falsas (o aplicativo for executado como um serviço):</span><span class="sxs-lookup"><span data-stu-id="719cd-129">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="719cd-130">Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="719cd-130">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="719cd-131">Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="719cd-131">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="719cd-132">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="719cd-132">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="719cd-133">Esta etapa é executada antes que o aplicativo seja configurado em `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="719cd-133">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="719cd-134">Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="719cd-134">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="719cd-135">Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.</span><span class="sxs-lookup"><span data-stu-id="719cd-135">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="719cd-136">Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="719cd-136">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="719cd-137">Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.</span><span class="sxs-lookup"><span data-stu-id="719cd-137">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="719cd-138">No exemplo a seguir do aplicativo de exemplo, `RunAsCustomService` é chamado em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para manipular eventos de tempo de vida no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="719cd-138">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="719cd-139">Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="719cd-139">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a><span data-ttu-id="719cd-140">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="719cd-140">Deployment type</span></span>

<span data-ttu-id="719cd-141">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="719cd-141">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="719cd-142">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="719cd-142">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="719cd-143">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="719cd-143">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="719cd-144">Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável ( *.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="719cd-144">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="719cd-145">Adicione os seguintes elementos de propriedade ao arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="719cd-145">Add the following property elements to the project file:</span></span>

* <span data-ttu-id="719cd-146">`<OutputType>` &ndash; O tipo de saída do aplicativo (`Exe` para o executável).</span><span class="sxs-lookup"><span data-stu-id="719cd-146">`<OutputType>` &ndash; The app's output type (`Exe` for executable).</span></span>
* <span data-ttu-id="719cd-147">`<LangVersion>` &ndash; A versão da linguagem C# (`latest` ou `preview`).</span><span class="sxs-lookup"><span data-stu-id="719cd-147">`<LangVersion>` &ndash; The C# language version (`latest` or `preview`).</span></span>

<span data-ttu-id="719cd-148">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="719cd-148">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="719cd-149">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="719cd-149">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <OutputType>Exe</OutputType>
  <LangVersion>preview</LangVersion>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="719cd-150">O [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="719cd-150">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="719cd-151">No exemplo a seguir, o RID é especificado como `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="719cd-151">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="719cd-152">A propriedade `<SelfContained>` é definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="719cd-152">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="719cd-153">Essas propriedades instruem o SDK a gerar um arquivo executável ( *.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="719cd-153">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="719cd-154">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="719cd-154">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="719cd-155">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="719cd-155">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

<span data-ttu-id="719cd-156">O [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="719cd-156">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="719cd-157">No exemplo a seguir, o RID é especificado como `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="719cd-157">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="719cd-158">A propriedade `<SelfContained>` é definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="719cd-158">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="719cd-159">Essas propriedades instruem o SDK a gerar um arquivo executável ( *.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="719cd-159">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="719cd-160">A propriedade `<UseAppHost>` é definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="719cd-160">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="719cd-161">Essa propriedade fornece o serviço com um caminho de ativação (um arquivo executável *.exe*) para FDD.</span><span class="sxs-lookup"><span data-stu-id="719cd-161">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="719cd-162">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="719cd-162">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="719cd-163">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="719cd-163">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="719cd-164">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="719cd-164">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="719cd-165">A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="719cd-165">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="719cd-166">O runtime e as dependências do aplicativo são implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="719cd-166">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="719cd-167">Um [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows está incluído no `<PropertyGroup>` que contém a estrutura de destino:</span><span class="sxs-lookup"><span data-stu-id="719cd-167">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="719cd-168">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="719cd-168">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="719cd-169">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="719cd-169">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="719cd-170">Use o nome da propriedade [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="719cd-170">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="719cd-171">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="719cd-171">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="719cd-172">Uma propriedade `<SelfContained>` está definida como `true`:</span><span class="sxs-lookup"><span data-stu-id="719cd-172">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a><span data-ttu-id="719cd-173">Conta de usuário do serviço</span><span class="sxs-lookup"><span data-stu-id="719cd-173">Service user account</span></span>

<span data-ttu-id="719cd-174">Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="719cd-174">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="719cd-175">Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="719cd-175">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```PowerShell
New-LocalUser -Name {NAME}
```

<span data-ttu-id="719cd-176">No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="719cd-176">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {NAME}"
```

<span data-ttu-id="719cd-177">Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.</span><span class="sxs-lookup"><span data-stu-id="719cd-177">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="719cd-178">A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.</span><span class="sxs-lookup"><span data-stu-id="719cd-178">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="719cd-179">Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="719cd-179">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="719cd-180">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="719cd-180">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="719cd-181">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="719cd-181">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="719cd-182">Fazer logon como direitos de um serviço</span><span class="sxs-lookup"><span data-stu-id="719cd-182">Log on as a service rights</span></span>

<span data-ttu-id="719cd-183">Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:</span><span class="sxs-lookup"><span data-stu-id="719cd-183">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="719cd-184">Abra a janela do editor da Política de Segurança Local executando *secpol.msc*.</span><span class="sxs-lookup"><span data-stu-id="719cd-184">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="719cd-185">Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.</span><span class="sxs-lookup"><span data-stu-id="719cd-185">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="719cd-186">Abra a política **Fazer logon como um serviço**.</span><span class="sxs-lookup"><span data-stu-id="719cd-186">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="719cd-187">Selecione **Adicionar Usuário ou Grupo**.</span><span class="sxs-lookup"><span data-stu-id="719cd-187">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="719cd-188">Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="719cd-188">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="719cd-189">Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="719cd-189">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="719cd-190">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="719cd-190">Select **Advanced**.</span></span> <span data-ttu-id="719cd-191">Escolha **Localizar Agora**.</span><span class="sxs-lookup"><span data-stu-id="719cd-191">Select **Find Now**.</span></span> <span data-ttu-id="719cd-192">Escolha a conta de usuário na lista.</span><span class="sxs-lookup"><span data-stu-id="719cd-192">Select the user account from the list.</span></span> <span data-ttu-id="719cd-193">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="719cd-193">Select **OK**.</span></span> <span data-ttu-id="719cd-194">Escolha **OK** novamente para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="719cd-194">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="719cd-195">Escolha **OK** ou **Aplicar** para aceitar as alterações.</span><span class="sxs-lookup"><span data-stu-id="719cd-195">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="719cd-196">Criar e gerenciar o Serviço Windows</span><span class="sxs-lookup"><span data-stu-id="719cd-196">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="719cd-197">Criar um serviço</span><span class="sxs-lookup"><span data-stu-id="719cd-197">Create a service</span></span>

<span data-ttu-id="719cd-198">Use comandos do PowerShell para registrar um serviço.</span><span class="sxs-lookup"><span data-stu-id="719cd-198">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="719cd-199">Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="719cd-199">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="719cd-200">`{EXE PATH}` &ndash; Caminho para a pasta do aplicativo no host (por exemplo, `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="719cd-200">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="719cd-201">Não inclua o executável do aplicativo no caminho.</span><span class="sxs-lookup"><span data-stu-id="719cd-201">Don't include the app's executable in the path.</span></span> <span data-ttu-id="719cd-202">A barra à direita não é necessária.</span><span class="sxs-lookup"><span data-stu-id="719cd-202">A trailing slash isn't required.</span></span>
* <span data-ttu-id="719cd-203">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Conta de usuário do serviço (por exemplo, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="719cd-203">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="719cd-204">`{NAME}` &ndash; Nome do serviço (por exemplo, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="719cd-204">`{NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="719cd-205">`{EXE FILE PATH}` &ndash; O caminho do executável do aplicativo (por exemplo, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="719cd-205">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="719cd-206">Inclua nome do arquivo do executável com a extensão.</span><span class="sxs-lookup"><span data-stu-id="719cd-206">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="719cd-207">`{DESCRIPTION}` &ndash; Descrição do serviço (por exemplo, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="719cd-207">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="719cd-208">`{DISPLAY NAME}` &ndash; Nome de exibição do serviço (por exemplo, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="719cd-208">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="719cd-209">Iniciar um serviço</span><span class="sxs-lookup"><span data-stu-id="719cd-209">Start a service</span></span>

<span data-ttu-id="719cd-210">Inicie o serviço com o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="719cd-210">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {NAME}
```

<span data-ttu-id="719cd-211">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="719cd-211">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="719cd-212">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="719cd-212">Determine a service's status</span></span>

<span data-ttu-id="719cd-213">Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="719cd-213">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {NAME}
```

<span data-ttu-id="719cd-214">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="719cd-214">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="719cd-215">Parar um serviço</span><span class="sxs-lookup"><span data-stu-id="719cd-215">Stop a service</span></span>

<span data-ttu-id="719cd-216">Pare um serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="719cd-216">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="719cd-217">Remover um serviço</span><span class="sxs-lookup"><span data-stu-id="719cd-217">Remove a service</span></span>

<span data-ttu-id="719cd-218">Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="719cd-218">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="719cd-219">Manipular eventos de início e de parada</span><span class="sxs-lookup"><span data-stu-id="719cd-219">Handle starting and stopping events</span></span>

<span data-ttu-id="719cd-220">Para tratar dos eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="719cd-220">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="719cd-221">Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="719cd-221">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="719cd-222">Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="719cd-222">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="719cd-223">Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="719cd-223">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="719cd-224">Para ver o local de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, confira o exemplo de código mostrado na seção [Tipo de implantação](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="719cd-224">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="719cd-225">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="719cd-225">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="719cd-226">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="719cd-226">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="719cd-227">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="719cd-227">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="719cd-228">Configurar pontos de extremidade</span><span class="sxs-lookup"><span data-stu-id="719cd-228">Configure endpoints</span></span>

<span data-ttu-id="719cd-229">Por padrão, o ASP.NET Core é associado a `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="719cd-229">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="719cd-230">Configure a URL e a porta definindo a variável de ambiente `ASPNETCORE_URLS`.</span><span class="sxs-lookup"><span data-stu-id="719cd-230">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="719cd-231">Para obter mais abordagens de configuração de porta e URL, consulte o artigo relevante do servidor:</span><span class="sxs-lookup"><span data-stu-id="719cd-231">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="719cd-232">As diretrizes anteriores abordam o suporte para pontos de extremidade HTTPS.</span><span class="sxs-lookup"><span data-stu-id="719cd-232">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="719cd-233">Por exemplo, configure o aplicativo para HTTPS quando a autenticação for usada com um serviço do Windows.</span><span class="sxs-lookup"><span data-stu-id="719cd-233">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="719cd-234">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="719cd-234">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="719cd-235">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="719cd-235">Current directory and content root</span></span>

<span data-ttu-id="719cd-236">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="719cd-236">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="719cd-237">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="719cd-237">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="719cd-238">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="719cd-238">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="719cd-239">Usar ContentRootPath ou ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="719cd-239">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="719cd-240">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) ou <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> para localizar os recursos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="719cd-240">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="719cd-241">Defina o caminho da raiz do conteúdo para a pasta do aplicativo</span><span class="sxs-lookup"><span data-stu-id="719cd-241">Set the content root path to the app's folder</span></span>

<span data-ttu-id="719cd-242">O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando um serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="719cd-242">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="719cd-243">Em vez de chamar `GetCurrentDirectory` para criar caminhos para arquivos de configurações, chame <xref:System.IO.Directory.SetCurrentDirectory*> com o caminho para a [raiz do conteúdo](xref:fundamentals/index#content-root)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="719cd-243">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="719cd-244">No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="719cd-244">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="719cd-245">Armazenar os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="719cd-245">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="719cd-246">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="719cd-246">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="719cd-247">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="719cd-247">Additional resources</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="719cd-248">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="719cd-248">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="719cd-249">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="719cd-249">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
