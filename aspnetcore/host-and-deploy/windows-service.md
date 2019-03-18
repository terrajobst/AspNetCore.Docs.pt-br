---
title: Hospedar o ASP.NET Core em um serviço Windows
author: guardrex
description: Saiba como hospedar um aplicativo ASP.NET Core em um serviço Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/08/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: ecc7f3a8cd813c2803d03294e38d726905eeb1b8
ms.sourcegitcommit: 34bf9fc6ea814c039401fca174642f0acb14be3c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57841415"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="9337e-103">Hospedar o ASP.NET Core em um serviço Windows</span><span class="sxs-lookup"><span data-stu-id="9337e-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="9337e-104">Por [Luke Latham](https://github.com/guardrex) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="9337e-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="9337e-105">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="9337e-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="9337e-106">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização.</span><span class="sxs-lookup"><span data-stu-id="9337e-106">When hosted as a Windows Service, the app automatically starts after reboots.</span></span>

<span data-ttu-id="9337e-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9337e-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9337e-108">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="9337e-108">Prerequisites</span></span>

* [<span data-ttu-id="9337e-109">PowerShell 6</span><span class="sxs-lookup"><span data-stu-id="9337e-109">PowerShell 6</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="deployment-type"></a><span data-ttu-id="9337e-110">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="9337e-110">Deployment type</span></span>

<span data-ttu-id="9337e-111">Você pode criar uma implantação de Serviço Windows dependente de estrutura ou autocontida.</span><span class="sxs-lookup"><span data-stu-id="9337e-111">You can create either a framework-dependent or self-contained Windows Service deployment.</span></span> <span data-ttu-id="9337e-112">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="9337e-112">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment"></a><span data-ttu-id="9337e-113">Implantação dependente de estrutura</span><span class="sxs-lookup"><span data-stu-id="9337e-113">Framework-dependent deployment</span></span>

<span data-ttu-id="9337e-114">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="9337e-114">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="9337e-115">Quando o cenário FDD é usado com um aplicativo de Serviço Windows do ASP.NET Core, o SDK produz um executável (*\*.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="9337e-115">When the FDD scenario is used with an ASP.NET Core Windows Service app, the SDK produces an executable (*\*.exe*), called a *framework-dependent executable*.</span></span>

### <a name="self-contained-deployment"></a><span data-ttu-id="9337e-116">Implantação autocontida</span><span class="sxs-lookup"><span data-stu-id="9337e-116">Self-contained deployment</span></span>

<span data-ttu-id="9337e-117">A SCD (Implantação Autocontida) não se baseia na presença de componentes compartilhados no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="9337e-117">Self-contained deployment (SCD) doesn't rely on the presence of shared components on the target system.</span></span> <span data-ttu-id="9337e-118">O tempo de execução e as dependências do aplicativo são implantados com o aplicativo para o sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="9337e-118">The runtime and the app's dependencies are deployed with the app to the hosting system.</span></span>

## <a name="convert-a-project-into-a-windows-service"></a><span data-ttu-id="9337e-119">Converter um projeto em um serviço Windows</span><span class="sxs-lookup"><span data-stu-id="9337e-119">Convert a project into a Windows Service</span></span>

<span data-ttu-id="9337e-120">Faça as seguintes alterações em um projeto ASP.NET Core existente para executar o aplicativo como um serviço:</span><span class="sxs-lookup"><span data-stu-id="9337e-120">Make the following changes to an existing ASP.NET Core project to run the app as a service:</span></span>

### <a name="project-file-updates"></a><span data-ttu-id="9337e-121">Atualizações de arquivo de projeto</span><span class="sxs-lookup"><span data-stu-id="9337e-121">Project file updates</span></span>

<span data-ttu-id="9337e-122">Com base na sua escolha de [tipo de implantação](#deployment-type), atualize o arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="9337e-122">Based on your choice of [deployment type](#deployment-type), update the project file:</span></span>

#### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="9337e-123">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="9337e-123">Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="9337e-124">Adicione um [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ao `<PropertyGroup>` que contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="9337e-124">Add a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="9337e-125">No exemplo a seguir, o RID é especificado como `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="9337e-125">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="9337e-126">Adicione a propriedade `<SelfContained>` definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="9337e-126">Add the `<SelfContained>` property set to `false`.</span></span> <span data-ttu-id="9337e-127">Essas propriedades instruem o SDK a gerar um arquivo executável (*.exe*) para Windows.</span><span class="sxs-lookup"><span data-stu-id="9337e-127">These properties instruct the SDK to generate an executable (*.exe*) file for Windows.</span></span>

<span data-ttu-id="9337e-128">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="9337e-128">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="9337e-129">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="9337e-129">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

::: moniker range=">= aspnetcore-2.2"

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

<span data-ttu-id="9337e-130">Adicione a propriedade `<UseAppHost>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="9337e-130">Add the `<UseAppHost>` property set to `true`.</span></span> <span data-ttu-id="9337e-131">Essa propriedade fornece o serviço com um caminho de ativação (um arquivo executável *.exe*) para FDD.</span><span class="sxs-lookup"><span data-stu-id="9337e-131">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

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

#### <a name="self-contained-deployment-scd"></a><span data-ttu-id="9337e-132">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="9337e-132">Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="9337e-133">Confirme a presença de um [RID (Identificador de Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ou adicione um RID ao `<PropertyGroup>` que contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="9337e-133">Confirm the presence of a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) or add a RID to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="9337e-134">Desabilite a criação de um arquivo *web.config* adicionando a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="9337e-134">Disable the creation of a *web.config* file by adding the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="9337e-135">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="9337e-135">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="9337e-136">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="9337e-136">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="9337e-137">Use o nome da propriedade `<RuntimeIdentifiers>` (plural).</span><span class="sxs-lookup"><span data-stu-id="9337e-137">Use the property name `<RuntimeIdentifiers>` (plural).</span></span>

  <span data-ttu-id="9337e-138">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="9337e-138">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="9337e-139">Adicionar uma referência de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="9337e-139">Add a package reference for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span></span>

<span data-ttu-id="9337e-140">Para habilitar o registro em log do Log de Eventos do Windows, adicione uma referência de pacote para [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="9337e-140">To enable Windows Event Log logging, add a package reference for [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="9337e-141">Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="9337e-141">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

### <a name="programmain-updates"></a><span data-ttu-id="9337e-142">Atualizações de Program.Main</span><span class="sxs-lookup"><span data-stu-id="9337e-142">Program.Main updates</span></span>

<span data-ttu-id="9337e-143">Faça as seguintes alterações em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="9337e-143">Make the following changes in `Program.Main`:</span></span>

* <span data-ttu-id="9337e-144">Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="9337e-144">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="9337e-145">Verifique se o depurador está anexado ou se há um argumento de linha de comado do `--console` presente.</span><span class="sxs-lookup"><span data-stu-id="9337e-145">Inspect if the debugger is attached or a `--console` command-line argument is present.</span></span>

  <span data-ttu-id="9337e-146">Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> no Host da Web.</span><span class="sxs-lookup"><span data-stu-id="9337e-146">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> on the Web Host.</span></span>

  <span data-ttu-id="9337e-147">Se as condições forem falsas (o aplicativo for executado como um serviço):</span><span class="sxs-lookup"><span data-stu-id="9337e-147">If the conditions are false (the app is run as a service):</span></span>

  * <span data-ttu-id="9337e-148">Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9337e-148">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="9337e-149">Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="9337e-149">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="9337e-150">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="9337e-150">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
  * <span data-ttu-id="9337e-151">Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="9337e-151">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

  <span data-ttu-id="9337e-152">Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.</span><span class="sxs-lookup"><span data-stu-id="9337e-152">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives them.</span></span>

* <span data-ttu-id="9337e-153">Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="9337e-153">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="9337e-154">Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.</span><span class="sxs-lookup"><span data-stu-id="9337e-154">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span> <span data-ttu-id="9337e-155">Para fins de teste e demonstração, o arquivo de configurações de Produção do aplicativo de exemplo define o nível de log para `Information`.</span><span class="sxs-lookup"><span data-stu-id="9337e-155">For demonstration and testing purposes, the sample app's Production settings file sets the logging level to `Information`.</span></span> <span data-ttu-id="9337e-156">Na produção, o valor normalmente é definido como `Error`.</span><span class="sxs-lookup"><span data-stu-id="9337e-156">In production, the value is typically set to `Error`.</span></span> <span data-ttu-id="9337e-157">Para obter mais informações, consulte <xref:fundamentals/logging/index#windows-eventlog-provider>.</span><span class="sxs-lookup"><span data-stu-id="9337e-157">For more information, see <xref:fundamentals/logging/index#windows-eventlog-provider>.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="publish-the-app"></a><span data-ttu-id="9337e-158">Publique o aplicativo</span><span class="sxs-lookup"><span data-stu-id="9337e-158">Publish the app</span></span>

<span data-ttu-id="9337e-159">Publique o aplicativo usando [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), um [perfil de publicação do Visual Studio](xref:host-and-deploy/visual-studio-publish-profiles) ou o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9337e-159">Publish the app using [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), a [Visual Studio publish profile](xref:host-and-deploy/visual-studio-publish-profiles), or Visual Studio Code.</span></span> <span data-ttu-id="9337e-160">Ao usar o Visual Studio, selecione o **FolderProfile** e configure o **Local de destino** antes de selecionar o botão **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="9337e-160">When using Visual Studio, select the **FolderProfile** and configure the **Target Location** before selecting the **Publish** button.</span></span>

<span data-ttu-id="9337e-161">Para publicar o aplicativo de exemplo usando as ferramentas da CLI (Interface de Linha de Comando), execute o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) em um prompt de comando da pasta do projeto, passando a configuração de uma versão para a opção [-c|--configuration](/dotnet/core/tools/dotnet-publish#options).</span><span class="sxs-lookup"><span data-stu-id="9337e-161">To publish the sample app using command-line interface (CLI) tools, run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command at a command prompt from the project folder with a Release configuration passed to the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option.</span></span> <span data-ttu-id="9337e-162">Use a opção [-o|--output](/dotnet/core/tools/dotnet-publish#options) com um caminho para publicar em uma pasta fora do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9337e-162">Use the [-o|--output](/dotnet/core/tools/dotnet-publish#options) option with a path to publish to a folder outside of the app.</span></span>

### <a name="publish-a-framework-dependent-deployment-fdd"></a><span data-ttu-id="9337e-163">Publicar uma FDD (Implantação Dependente de Estrutura)</span><span class="sxs-lookup"><span data-stu-id="9337e-163">Publish a Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="9337e-164">No exemplo a seguir, o aplicativo é publicado na pasta *c:\\svc*:</span><span class="sxs-lookup"><span data-stu-id="9337e-164">In the following example, the app is published to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --output c:\svc
```

### <a name="publish-a-self-contained-deployment-scd"></a><span data-ttu-id="9337e-165">Publicar uma SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="9337e-165">Publish a Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="9337e-166">O RID deve ser especificado na propriedade `<RuntimeIdenfifier>` (ou `<RuntimeIdentifiers>`) do arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="9337e-166">The RID must be specified in the `<RuntimeIdenfifier>` (or `<RuntimeIdentifiers>`) property of the project file.</span></span> <span data-ttu-id="9337e-167">Insira o tempo de execução na opção [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) do comando `dotnet publish`.</span><span class="sxs-lookup"><span data-stu-id="9337e-167">Supply the runtime to the [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) option of the `dotnet publish` command.</span></span>

<span data-ttu-id="9337e-168">No exemplo a seguir, o aplicativo é publicado para o tempo de execução `win7-x64` para a pasta *c:\\svc*:</span><span class="sxs-lookup"><span data-stu-id="9337e-168">In the following example, the app is published for the `win7-x64` runtime to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
```

## <a name="create-a-user-account"></a><span data-ttu-id="9337e-169">Criar uma conta de usuário</span><span class="sxs-lookup"><span data-stu-id="9337e-169">Create a user account</span></span>

<span data-ttu-id="9337e-170">Crie uma conta de usuário para o serviço usando o comando `net user` de um shell de comando administrativo do PowerShell 6:</span><span class="sxs-lookup"><span data-stu-id="9337e-170">Create a user account for the service using the `net user` command from an administrative PowerShell 6 command shell:</span></span>

```powershell
net user {USER ACCOUNT} {PASSWORD} /add
```

<span data-ttu-id="9337e-171">A expiração da senha padrão ocorre em seis semanas.</span><span class="sxs-lookup"><span data-stu-id="9337e-171">The default password expiration is six weeks.</span></span>

<span data-ttu-id="9337e-172">Para o aplicativo de exemplo, crie uma conta de usuário com o nome `ServiceUser` e uma senha.</span><span class="sxs-lookup"><span data-stu-id="9337e-172">For the sample app, create a user account with the name `ServiceUser` and a password.</span></span> <span data-ttu-id="9337e-173">No comando a seguir, substitua `{PASSWORD}` por uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).</span><span class="sxs-lookup"><span data-stu-id="9337e-173">In the following command, replace `{PASSWORD}` with a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).</span></span>

```powershell
net user ServiceUser {PASSWORD} /add
```

<span data-ttu-id="9337e-174">Se você precisar adicionar o usuário a um grupo, use o comando `net localgroup`, onde `{GROUP}` é o nome do grupo:</span><span class="sxs-lookup"><span data-stu-id="9337e-174">If you need to add the user to a group, use the `net localgroup` command, where `{GROUP}` is the name of the group:</span></span>

```powershell
net localgroup {GROUP} {USER ACCOUNT} /add
```

<span data-ttu-id="9337e-175">Para obter mais informações, confira [Contas de Usuário do Serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="9337e-175">For more information, see [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="9337e-176">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="9337e-176">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="9337e-177">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="9337e-177">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="set-permission-log-on-as-a-service"></a><span data-ttu-id="9337e-178">Definir permissão: Fazer logon como serviço</span><span class="sxs-lookup"><span data-stu-id="9337e-178">Set permission: Log on as a service</span></span>

<span data-ttu-id="9337e-179">Conceda acesso de gravação/leitura/execução para a pasta do aplicativo usando o comando [icacls](/windows-server/administration/windows-commands/icacls):</span><span class="sxs-lookup"><span data-stu-id="9337e-179">Grant write/read/execute access to the app's folder using the [icacls](/windows-server/administration/windows-commands/icacls) command:</span></span>

```powershell
icacls "{PATH}" /grant {USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS} /t
```

* <span data-ttu-id="9337e-180">`{PATH}` &ndash; Caminho para a pasta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9337e-180">`{PATH}` &ndash; Path to the app's folder.</span></span>
* <span data-ttu-id="9337e-181">`{USER ACCOUNT}` &ndash; A conta de usuário (SID).</span><span class="sxs-lookup"><span data-stu-id="9337e-181">`{USER ACCOUNT}` &ndash; The user account (SID).</span></span>
* <span data-ttu-id="9337e-182">`(OI)` &ndash; O sinalizador de Herança de Objeto propaga as permissão para os arquivos subordinados.</span><span class="sxs-lookup"><span data-stu-id="9337e-182">`(OI)` &ndash; The Object Inherit flag propagates permissions to subordinate files.</span></span>
* <span data-ttu-id="9337e-183">`(CI)` &ndash; O sinalizador de Herança de Contêiner propaga as permissão para as pastas subordinadas.</span><span class="sxs-lookup"><span data-stu-id="9337e-183">`(CI)` &ndash; The Container Inherit flag propagates permissions to subordinate folders.</span></span>
* <span data-ttu-id="9337e-184">`{PERMISSION FLAGS}` &ndash; Define as permissões de acesso do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9337e-184">`{PERMISSION FLAGS}` &ndash; Sets the app's access permissions.</span></span>
  * <span data-ttu-id="9337e-185">Gravar (`W`)</span><span class="sxs-lookup"><span data-stu-id="9337e-185">Write (`W`)</span></span>
  * <span data-ttu-id="9337e-186">Ler (`R`)</span><span class="sxs-lookup"><span data-stu-id="9337e-186">Read (`R`)</span></span>
  * <span data-ttu-id="9337e-187">Executar (`X`)</span><span class="sxs-lookup"><span data-stu-id="9337e-187">Execute (`X`)</span></span>
  * <span data-ttu-id="9337e-188">Completo (`F`)</span><span class="sxs-lookup"><span data-stu-id="9337e-188">Full (`F`)</span></span>
  * <span data-ttu-id="9337e-189">Modificar (`M`)</span><span class="sxs-lookup"><span data-stu-id="9337e-189">Modify (`M`)</span></span>
* <span data-ttu-id="9337e-190">`/t` &ndash; Aplique recursivamente aos arquivos e pastas subordinadas existentes.</span><span class="sxs-lookup"><span data-stu-id="9337e-190">`/t` &ndash; Apply recursively to existing subordinate folders and files.</span></span>

<span data-ttu-id="9337e-191">Para o aplicativo de exemplo publicado na pasta *c:\\svc* e a conta `ServiceUser` com permissões de gravação/leitura/execução, use o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="9337e-191">For the sample app published to the *c:\\svc* folder and the `ServiceUser` account with write/read/execute permissions, use the following command:</span></span>

```powershell
icacls "c:\svc" /grant ServiceUser:(OI)(CI)WRX /t
```

<span data-ttu-id="9337e-192">Para obter mais informações, confira [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="9337e-192">For more information, see [icacls](/windows-server/administration/windows-commands/icacls).</span></span>

## <a name="create-the-service"></a><span data-ttu-id="9337e-193">Criar o serviço</span><span class="sxs-lookup"><span data-stu-id="9337e-193">Create the service</span></span>

<span data-ttu-id="9337e-194">Use o script [RegisterService.ps1](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/scripts) do PowerShell para registrar o serviço.</span><span class="sxs-lookup"><span data-stu-id="9337e-194">Use the [RegisterService.ps1](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/scripts) PowerShell script to register the service.</span></span> <span data-ttu-id="9337e-195">Em um prompt de comando administrativo do PowerShell 6, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="9337e-195">From an administrative PowerShell 6 command prompt, execute the following command:</span></span>

```powershell
.\RegisterService.ps1 
    -Name {NAME} 
    -DisplayName "{DISPLAY NAME}" 
    -Description "{DESCRIPTION}" 
    -Path "{PATH}" 
    -Exe {ASSEMBLY}.exe 
    -User {DOMAIN\USER}
```

<span data-ttu-id="9337e-196">Observe o seguinte aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="9337e-196">In the following example for the sample app:</span></span>

* <span data-ttu-id="9337e-197">O nome do serviço é **MyService**.</span><span class="sxs-lookup"><span data-stu-id="9337e-197">The service is named **MyService**.</span></span>
* <span data-ttu-id="9337e-198">O serviço publicado reside na pasta *c:\\svc*.</span><span class="sxs-lookup"><span data-stu-id="9337e-198">The published service resides in the *c:\\svc* folder.</span></span> <span data-ttu-id="9337e-199">O executável do aplicativo é chamado de *SampleApp.exe*.</span><span class="sxs-lookup"><span data-stu-id="9337e-199">The app executable is named *SampleApp.exe*.</span></span>
* <span data-ttu-id="9337e-200">O serviço é executado na conta `ServiceUser`.</span><span class="sxs-lookup"><span data-stu-id="9337e-200">The service runs under the `ServiceUser` account.</span></span> <span data-ttu-id="9337e-201">No exemplo a seguir, o nome do computador local é `Desktop-PC`.</span><span class="sxs-lookup"><span data-stu-id="9337e-201">In the following example, the local machine name is `Desktop-PC`.</span></span>

```powershell
.\RegisterService.ps1 
    -Name MyService 
    -DisplayName "My Cool Service" 
    -Description "This is the Sample App service." 
    -Path "c:\svc" 
    -Exe SampleApp.exe 
    -User Desktop-PC\ServiceUser
```

## <a name="manage-the-service"></a><span data-ttu-id="9337e-202">Gerenciar o serviço</span><span class="sxs-lookup"><span data-stu-id="9337e-202">Manage the service</span></span>

### <a name="start-the-service"></a><span data-ttu-id="9337e-203">Iniciar o serviço</span><span class="sxs-lookup"><span data-stu-id="9337e-203">Start the service</span></span>

<span data-ttu-id="9337e-204">Inicie o serviço com o comando `Start-Service -Name {NAME}` do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="9337e-204">Start the service with the `Start-Service -Name {NAME}` PowerShell 6 command.</span></span>

<span data-ttu-id="9337e-205">Para iniciar o serviço de aplicativo de exemplo, use o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="9337e-205">To start the sample app service, use the following command:</span></span>

```powershell
Start-Service -Name MyService
```

<span data-ttu-id="9337e-206">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="9337e-206">The command takes a few seconds to start the service.</span></span>

### <a name="determine-the-service-status"></a><span data-ttu-id="9337e-207">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="9337e-207">Determine the service status</span></span>

<span data-ttu-id="9337e-208">Para verificar o status do serviço, use o comando `Get-Service -Name {NAME}` do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="9337e-208">To check the status of the service, use the `Get-Service -Name {NAME}` PowerShell 6 command.</span></span> <span data-ttu-id="9337e-209">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="9337e-209">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

<span data-ttu-id="9337e-210">Use o seguinte comando para verificar o status do serviço de aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="9337e-210">Use the following command to check the status of the sample app service:</span></span>

```powershell
Get-Service -Name MyService
```

### <a name="browse-a-web-app-service"></a><span data-ttu-id="9337e-211">Procurar um serviço de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="9337e-211">Browse a web app service</span></span>

<span data-ttu-id="9337e-212">Quando o serviço estiver no estado `RUNNING` e se o serviço for um aplicativo Web, procure o aplicativo em seu caminho (por padrão, `http://localhost:5000`, que redireciona para `https://localhost:5001` ao usar [Middleware de Redirecionamento HTTPS](xref:security/enforcing-ssl)).</span><span class="sxs-lookup"><span data-stu-id="9337e-212">When the service is in the `RUNNING` state and if the service is a web app, browse the app at its path (by default, `http://localhost:5000`, which redirects to `https://localhost:5001` when using [HTTPS Redirection Middleware](xref:security/enforcing-ssl)).</span></span>

<span data-ttu-id="9337e-213">Para o serviço de aplicativo de exemplo, procure o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="9337e-213">For the sample app service, browse the app at `http://localhost:5000`.</span></span>

### <a name="stop-the-service"></a><span data-ttu-id="9337e-214">Parar o serviço</span><span class="sxs-lookup"><span data-stu-id="9337e-214">Stop the service</span></span>

<span data-ttu-id="9337e-215">Pare o serviço com o comando `Stop-Service -Name {NAME}` do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="9337e-215">Stop the service with the `Stop-Service -Name {NAME}` Powershell 6 command.</span></span>

<span data-ttu-id="9337e-216">O comando a seguir interrompe o serviço de aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="9337e-216">The following command stops the sample app service:</span></span>

```powershell
Stop-Service -Name MyService
```

### <a name="remove-the-service"></a><span data-ttu-id="9337e-217">Remover o serviço</span><span class="sxs-lookup"><span data-stu-id="9337e-217">Remove the service</span></span>

<span data-ttu-id="9337e-218">Após um pequeno atraso para interromper um serviço, remova o serviço com o comando `Remove-Service -Name {NAME}` do PowerShell 6.</span><span class="sxs-lookup"><span data-stu-id="9337e-218">After a short delay to stop a service, remove the service with the `Remove-Service -Name {NAME}` Powershell 6 command.</span></span>

<span data-ttu-id="9337e-219">Verifique o status do serviço de aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="9337e-219">Check the status of the sample app service:</span></span>

```powershell
Remove-Service -Name MyService
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="9337e-220">Manipular eventos de início e de parada</span><span class="sxs-lookup"><span data-stu-id="9337e-220">Handle starting and stopping events</span></span>

<span data-ttu-id="9337e-221">Para manipular os eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, faça as seguintes alterações adicionais:</span><span class="sxs-lookup"><span data-stu-id="9337e-221">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events, perform the following additional changes:</span></span>

1. <span data-ttu-id="9337e-222">Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="9337e-222">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="9337e-223">Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="9337e-223">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="9337e-224">Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="9337e-224">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="9337e-225">Para ver o local do <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, consulte o exemplo de código mostrado na seção [Converter um projeto em um Serviço Windows](#convert-a-project-into-a-windows-service).</span><span class="sxs-lookup"><span data-stu-id="9337e-225">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Convert a project into a Windows Service](#convert-a-project-into-a-windows-service) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="9337e-226">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="9337e-226">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="9337e-227">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="9337e-227">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="9337e-228">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="9337e-228">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="9337e-229">Configurar o HTTPS</span><span class="sxs-lookup"><span data-stu-id="9337e-229">Configure HTTPS</span></span>

<span data-ttu-id="9337e-230">Para configurar o serviço com um ponto de extremidade seguro:</span><span class="sxs-lookup"><span data-stu-id="9337e-230">To configure the service with a secure endpoint:</span></span>

1. <span data-ttu-id="9337e-231">Crie um certificado X.509 para o sistema de hospedagem usando os mecanismos de implantação e aquisição do certificado da sua plataforma.</span><span class="sxs-lookup"><span data-stu-id="9337e-231">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>

1. <span data-ttu-id="9337e-232">Especifique uma [Configuração do ponto de extremidade HTTPS do servidor Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) para usar o certificado.</span><span class="sxs-lookup"><span data-stu-id="9337e-232">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="9337e-233">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="9337e-233">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="9337e-234">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="9337e-234">Current directory and content root</span></span>

<span data-ttu-id="9337e-235">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="9337e-235">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="9337e-236">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="9337e-236">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="9337e-237">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="9337e-237">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="9337e-238">Defina o caminho da raiz do conteúdo para a pasta do aplicativo</span><span class="sxs-lookup"><span data-stu-id="9337e-238">Set the content root path to the app's folder</span></span>

<span data-ttu-id="9337e-239">O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando o serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="9337e-239">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when the service is created.</span></span> <span data-ttu-id="9337e-240">Em vez de chamar `GetCurrentDirectory` para criar caminhos para arquivos de configuração, chame <xref:System.IO.Directory.SetCurrentDirectory*> com o caminho para a raiz do conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9337e-240">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's content root.</span></span>

<span data-ttu-id="9337e-241">No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="9337e-241">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-the-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="9337e-242">Armazene os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="9337e-242">Store the service's files in a suitable location on disk</span></span>

<span data-ttu-id="9337e-243">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="9337e-243">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9337e-244">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9337e-244">Additional resources</span></span>

* <span data-ttu-id="9337e-245">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="9337e-245">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>
