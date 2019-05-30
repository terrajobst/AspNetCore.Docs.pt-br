---
title: Hospedar o ASP.NET Core em um serviço Windows
author: guardrex
description: Saiba como hospedar um aplicativo ASP.NET Core em um serviço Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 05/21/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: ab36bc1b2827c80bb1e7b9e8cee558b346a991f8
ms.sourcegitcommit: b8ed594ab9f47fa32510574f3e1b210cff000967
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66251430"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="3e0f8-103">Hospedar o ASP.NET Core em um serviço Windows</span><span class="sxs-lookup"><span data-stu-id="3e0f8-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="3e0f8-104">Por [Luke Latham](https://github.com/guardrex) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="3e0f8-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="3e0f8-105">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="3e0f8-106">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização do servidor.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-106">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="3e0f8-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3e0f8-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3e0f8-108">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="3e0f8-108">Prerequisites</span></span>

* [<span data-ttu-id="3e0f8-109">SDK do ASP.NET Core 2.1 ou posterior</span><span class="sxs-lookup"><span data-stu-id="3e0f8-109">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="3e0f8-110">PowerShell 6.2 ou posterior</span><span class="sxs-lookup"><span data-stu-id="3e0f8-110">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="3e0f8-111">Configuração do aplicativo</span><span class="sxs-lookup"><span data-stu-id="3e0f8-111">App configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3e0f8-112">`IHostBuilder.UseWindowsService`, fornecido pelo pacote [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices), é chamado na compilação do host.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-112">`IHostBuilder.UseWindowsService`, provided by the [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) package, is called when building the host.</span></span> <span data-ttu-id="3e0f8-113">Se o aplicativo estiver sendo executado como um Serviço Windows, o método:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-113">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="3e0f8-114">Define o tempo de vida do host como `WindowsServiceLifetime`.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-114">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="3e0f8-115">Define a raiz do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-115">Sets the content root.</span></span>
* <span data-ttu-id="3e0f8-116">Habilita o registro de log no log de eventos com o nome do aplicativo como o nome da fonte padrão.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-116">Enables logging to the event log with the application name as the default source name.</span></span>
  * <span data-ttu-id="3e0f8-117">O nível do log pode ser configurado com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.json*.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-117">The log level can be configured using the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>
  * <span data-ttu-id="3e0f8-118">Somente administradores podem criar novas fontes de evento.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-118">Only administrators can create new event sources.</span></span> <span data-ttu-id="3e0f8-119">Quando uma fonte de evento não puder ser criada usando o nome do aplicativo, um aviso será registrado em log como a fonte do *Aplicativo* e os logs de eventos serão desabilitados.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-119">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

[!code-csharp[](windows-service/samples/3.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3e0f8-120">O aplicativo exige referências de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) e [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-120">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="3e0f8-121">Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-121">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="3e0f8-122">Verifique se o depurador está anexado ou se uma opção `--console` está presente.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-122">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="3e0f8-123">Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-123">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="3e0f8-124">Se as condições forem falsas (o aplicativo for executado como um serviço):</span><span class="sxs-lookup"><span data-stu-id="3e0f8-124">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="3e0f8-125">Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-125">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="3e0f8-126">Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-126">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="3e0f8-127">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-127">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="3e0f8-128">Esta etapa é executada antes que o aplicativo seja configurado em `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-128">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="3e0f8-129">Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-129">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="3e0f8-130">Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-130">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="3e0f8-131">Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-131">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="3e0f8-132">Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-132">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="3e0f8-133">No exemplo a seguir do aplicativo de exemplo, `RunAsCustomService` é chamado em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para manipular eventos de tempo de vida no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-133">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="3e0f8-134">Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-134">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a><span data-ttu-id="3e0f8-135">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="3e0f8-135">Deployment type</span></span>

<span data-ttu-id="3e0f8-136">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-136">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="3e0f8-137">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="3e0f8-137">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="3e0f8-138">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-138">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="3e0f8-139">Quando o cenário FDD é adotado após a orientação deste artigo, o SDK produz um executável ( *.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-139">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3e0f8-140">Adicione os seguintes elementos de propriedade ao arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-140">Add the following property elements to the project file:</span></span>

* <span data-ttu-id="3e0f8-141">`<OutputType>` &ndash; O tipo de saída do aplicativo (`Exe` para o executável).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-141">`<OutputType>` &ndash; The app's output type (`Exe` for executable).</span></span>
* <span data-ttu-id="3e0f8-142">`<LangVersion>` &ndash; A versão da linguagem C# (`latest` ou `preview`).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-142">`<LangVersion>` &ndash; The C# language version (`latest` or `preview`).</span></span>

<span data-ttu-id="3e0f8-143">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-143">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="3e0f8-144">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-144">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

<span data-ttu-id="3e0f8-145">O [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-145">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="3e0f8-146">No exemplo a seguir, o RID é especificado como `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-146">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="3e0f8-147">A propriedade `<SelfContained>` é definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-147">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="3e0f8-148">Essas propriedades instruem o SDK a gerar um arquivo executável ( *.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-148">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="3e0f8-149">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-149">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="3e0f8-150">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-150">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

<span data-ttu-id="3e0f8-151">O [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-151">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="3e0f8-152">No exemplo a seguir, o RID é especificado como `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-152">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="3e0f8-153">A propriedade `<SelfContained>` é definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-153">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="3e0f8-154">Essas propriedades instruem o SDK a gerar um arquivo executável ( *.exe*) para Windows e um aplicativo que depende da estrutura compartilhada do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-154">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="3e0f8-155">A propriedade `<UseAppHost>` é definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-155">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="3e0f8-156">Essa propriedade fornece o serviço com um caminho de ativação (um arquivo executável *.exe*) para FDD.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-156">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="3e0f8-157">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-157">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="3e0f8-158">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-158">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="3e0f8-159">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="3e0f8-159">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="3e0f8-160">A SCD (Implantação Autossuficiente) não se baseia na presença de uma estrutura compartilhada no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-160">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="3e0f8-161">O tempo de execução e as dependências do aplicativo são implantados com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-161">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="3e0f8-162">Um [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows está incluído no `<PropertyGroup>` que contém a estrutura de destino:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-162">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="3e0f8-163">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-163">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="3e0f8-164">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-164">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="3e0f8-165">Use o nome da propriedade [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-165">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="3e0f8-166">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-166">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3e0f8-167">Uma propriedade `<SelfContained>` está definida como `true`:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-167">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a><span data-ttu-id="3e0f8-168">Conta de usuário do serviço</span><span class="sxs-lookup"><span data-stu-id="3e0f8-168">Service user account</span></span>

<span data-ttu-id="3e0f8-169">Para criar uma conta de usuário para um serviço, use o cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) de um shell de comando administrativo do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-169">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="3e0f8-170">Na Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763) ou posterior:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-170">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```PowerShell
New-LocalUser -Name {NAME}
```

<span data-ttu-id="3e0f8-171">No sistema operacional do Windows anterior à Atualização de outubro de 2018 do Windows 10 (versão 1809/build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="3e0f8-171">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {NAME}"
```

<span data-ttu-id="3e0f8-172">Forneça uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) quando solicitado.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-172">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="3e0f8-173">A conta só expirará se o parâmetro `-AccountExpires` for fornecido ao cmdlet [New LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) com uma data de validade <xref:System.DateTime>.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-173">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="3e0f8-174">Confira mais informações em [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) e [Contas de usuário do serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-174">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="3e0f8-175">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-175">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="3e0f8-176">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-176">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="3e0f8-177">Fazer logon como direitos de um serviço</span><span class="sxs-lookup"><span data-stu-id="3e0f8-177">Log on as a service rights</span></span>

<span data-ttu-id="3e0f8-178">Para estabelecer os direitos de *Fazer logon como um serviço* para uma conta de usuário do serviço:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-178">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="3e0f8-179">Abra a janela do editor da Política de Segurança Local executando *secpool.msc*.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-179">Open the Local Security Policy editor by running *secpool.msc*.</span></span>
1. <span data-ttu-id="3e0f8-180">Expanda o nó **Políticas Locais** e escolha **Atribuição de Direitos de Usuário**.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-180">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="3e0f8-181">Abra a política **Fazer logon como um serviço**.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-181">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="3e0f8-182">Selecione **Adicionar Usuário ou Grupo**.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-182">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="3e0f8-183">Forneça o nome do objeto (conta de usuário) usando uma das abordagens a seguir:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-183">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="3e0f8-184">Digite a conta de usuário (`{DOMAIN OR COMPUTER NAME\USER}`) no campo de nome do objeto e escolha **OK** para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-184">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="3e0f8-185">Selecione **Avançado**.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-185">Select **Advanced**.</span></span> <span data-ttu-id="3e0f8-186">Escolha **Localizar Agora**.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-186">Select **Find Now**.</span></span> <span data-ttu-id="3e0f8-187">Escolha a conta de usuário na lista.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-187">Select the user account from the list.</span></span> <span data-ttu-id="3e0f8-188">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-188">Select **OK**.</span></span> <span data-ttu-id="3e0f8-189">Escolha **OK** novamente para adicionar o usuário à política.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-189">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="3e0f8-190">Escolha **OK** ou **Aplicar** para aceitar as alterações.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-190">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="3e0f8-191">Criar e gerenciar o Serviço Windows</span><span class="sxs-lookup"><span data-stu-id="3e0f8-191">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="3e0f8-192">Criar um serviço</span><span class="sxs-lookup"><span data-stu-id="3e0f8-192">Create a service</span></span>

<span data-ttu-id="3e0f8-193">Use comandos do PowerShell para registrar um serviço.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-193">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="3e0f8-194">Em um shell de comando administrativo do PowerShell 6, execute os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-194">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="3e0f8-195">`{EXE PATH}` &ndash; Caminho para a pasta do aplicativo no host (por exemplo, `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-195">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="3e0f8-196">Não inclua o executável do aplicativo no caminho.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-196">Don't include the app's executable in the path.</span></span> <span data-ttu-id="3e0f8-197">A barra à direita não é necessária.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-197">A trailing slash isn't required.</span></span>
* <span data-ttu-id="3e0f8-198">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Conta de usuário do serviço (por exemplo, `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-198">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="3e0f8-199">`{NAME}` &ndash; Nome do serviço (por exemplo, `MyService`).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-199">`{NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="3e0f8-200">`{EXE FILE PATH}` &ndash; O caminho do executável do aplicativo (por exemplo, `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-200">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="3e0f8-201">Inclua nome do arquivo do executável com a extensão.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-201">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="3e0f8-202">`{DESCRIPTION}` &ndash; Descrição do serviço (por exemplo, `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-202">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="3e0f8-203">`{DISPLAY NAME}` &ndash; Nome de exibição do serviço (por exemplo, `My Service`).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-203">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="3e0f8-204">Iniciar um serviço</span><span class="sxs-lookup"><span data-stu-id="3e0f8-204">Start a service</span></span>

<span data-ttu-id="3e0f8-205">Inicie o serviço com o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-205">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {NAME}
```

<span data-ttu-id="3e0f8-206">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-206">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="3e0f8-207">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="3e0f8-207">Determine a service's status</span></span>

<span data-ttu-id="3e0f8-208">Para verificar o status de um serviço, use o seguinte comando do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-208">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {NAME}
```

<span data-ttu-id="3e0f8-209">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-209">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="3e0f8-210">Parar um serviço</span><span class="sxs-lookup"><span data-stu-id="3e0f8-210">Stop a service</span></span>

<span data-ttu-id="3e0f8-211">Pare um serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-211">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="3e0f8-212">Remover um serviço</span><span class="sxs-lookup"><span data-stu-id="3e0f8-212">Remove a service</span></span>

<span data-ttu-id="3e0f8-213">Após um pequeno atraso para interromper um serviço, remova o serviço com o seguinte comando do Powershell 6:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-213">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="3e0f8-214">Manipular eventos de início e de parada</span><span class="sxs-lookup"><span data-stu-id="3e0f8-214">Handle starting and stopping events</span></span>

<span data-ttu-id="3e0f8-215">Para tratar dos eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-215">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="3e0f8-216">Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-216">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="3e0f8-217">Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-217">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="3e0f8-218">Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-218">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="3e0f8-219">Para ver o local de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, confira o exemplo de código mostrado na seção [Tipo de implantação](#deployment-type).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-219">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="3e0f8-220">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="3e0f8-220">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="3e0f8-221">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-221">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="3e0f8-222">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-222">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="3e0f8-223">Configurar o HTTPS</span><span class="sxs-lookup"><span data-stu-id="3e0f8-223">Configure HTTPS</span></span>

<span data-ttu-id="3e0f8-224">Para configurar um serviço com um ponto de extremidade seguro:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-224">To configure a service with a secure endpoint:</span></span>

1. <span data-ttu-id="3e0f8-225">Crie um certificado X.509 para o sistema de hospedagem usando os mecanismos de implantação e aquisição do certificado da sua plataforma.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-225">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>

1. <span data-ttu-id="3e0f8-226">Especifique uma [Configuração do ponto de extremidade HTTPS do servidor Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) para usar o certificado.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-226">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="3e0f8-227">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-227">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="3e0f8-228">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="3e0f8-228">Current directory and content root</span></span>

<span data-ttu-id="3e0f8-229">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-229">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="3e0f8-230">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="3e0f8-230">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="3e0f8-231">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-231">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="3e0f8-232">Usar ContentRootPath ou ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="3e0f8-232">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="3e0f8-233">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) ou <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> para localizar os recursos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-233">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="3e0f8-234">Defina o caminho da raiz do conteúdo para a pasta do aplicativo</span><span class="sxs-lookup"><span data-stu-id="3e0f8-234">Set the content root path to the app's folder</span></span>

<span data-ttu-id="3e0f8-235">O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando um serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-235">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="3e0f8-236">Em vez de chamar `GetCurrentDirectory` para criar caminhos para arquivos de configuração, chame <xref:System.IO.Directory.SetCurrentDirectory*> com o caminho para a raiz do conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-236">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's content root.</span></span>

<span data-ttu-id="3e0f8-237">No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="3e0f8-237">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="3e0f8-238">Armazenar os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="3e0f8-238">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="3e0f8-239">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="3e0f8-239">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3e0f8-240">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3e0f8-240">Additional resources</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="3e0f8-241">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="3e0f8-241">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="3e0f8-242">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="3e0f8-242">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
