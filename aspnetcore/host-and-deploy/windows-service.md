---
title: Hospedar o ASP.NET Core em um serviço Windows
author: guardrex
description: Saiba como hospedar um aplicativo ASP.NET Core em um serviço Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 12/01/2018
uid: host-and-deploy/windows-service
ms.openlocfilehash: f53c303dc63e092f08e933fea79eb805523cde9b
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861388"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="3bc10-103">Hospedar o ASP.NET Core em um serviço Windows</span><span class="sxs-lookup"><span data-stu-id="3bc10-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="3bc10-104">Por [Luke Latham](https://github.com/guardrex) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="3bc10-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="3bc10-105">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="3bc10-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="3bc10-106">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização.</span><span class="sxs-lookup"><span data-stu-id="3bc10-106">When hosted as a Windows Service, the app automatically starts after reboots.</span></span>

<span data-ttu-id="3bc10-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3bc10-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="deployment-type"></a><span data-ttu-id="3bc10-108">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="3bc10-108">Deployment type</span></span>

<span data-ttu-id="3bc10-109">Você pode criar uma implantação de Serviço Windows dependente de estrutura ou autocontida.</span><span class="sxs-lookup"><span data-stu-id="3bc10-109">You can create either a framework-dependent or self-contained Windows Service deployment.</span></span> <span data-ttu-id="3bc10-110">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="3bc10-110">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment"></a><span data-ttu-id="3bc10-111">Implantação dependente de estrutura</span><span class="sxs-lookup"><span data-stu-id="3bc10-111">Framework-dependent deployment</span></span>

<span data-ttu-id="3bc10-112">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="3bc10-112">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="3bc10-113">Quando o cenário FDD é usado com um aplicativo de Serviço Windows do ASP.NET Core, o SDK produz um executável (*\*.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="3bc10-113">When the FDD scenario is used with an ASP.NET Core Windows Service app, the SDK produces an executable (*\*.exe*), called a *framework-dependent executable*.</span></span>

### <a name="self-contained-deployment"></a><span data-ttu-id="3bc10-114">Implantação autocontida</span><span class="sxs-lookup"><span data-stu-id="3bc10-114">Self-contained deployment</span></span>

<span data-ttu-id="3bc10-115">A SCD (Implantação Autocontida) não se baseia na presença de componentes compartilhados no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="3bc10-115">Self-contained deployment (SCD) doesn't rely on the presence of shared components on the target system.</span></span> <span data-ttu-id="3bc10-116">O tempo de execução e as dependências do aplicativo são implantados com o aplicativo para o sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="3bc10-116">The runtime and the app's dependencies are deployed with the app to the hosting system.</span></span>

## <a name="convert-a-project-into-a-windows-service"></a><span data-ttu-id="3bc10-117">Converter um projeto em um serviço Windows</span><span class="sxs-lookup"><span data-stu-id="3bc10-117">Convert a project into a Windows Service</span></span>

<span data-ttu-id="3bc10-118">Faça as seguintes alterações em um projeto ASP.NET Core existente para executar o aplicativo como um serviço:</span><span class="sxs-lookup"><span data-stu-id="3bc10-118">Make the following changes to an existing ASP.NET Core project to run the app as a service:</span></span>

### <a name="project-file-updates"></a><span data-ttu-id="3bc10-119">Atualizações de arquivo de projeto</span><span class="sxs-lookup"><span data-stu-id="3bc10-119">Project file updates</span></span>

<span data-ttu-id="3bc10-120">Com base na sua escolha de [tipo de implantação](#deployment-type), atualize o arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="3bc10-120">Based on your choice of [deployment type](#deployment-type), update the project file:</span></span>

#### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="3bc10-121">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="3bc10-121">Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="3bc10-122">Adicione um [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ao `<PropertyGroup>` que contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="3bc10-122">Add a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="3bc10-123">Adicione a propriedade `<SelfContained>` definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="3bc10-123">Add the `<SelfContained>` property set to `false`.</span></span> <span data-ttu-id="3bc10-124">Desabilite a criação de um arquivo *web.config* adicionando a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="3bc10-124">Disable the creation of a *web.config* file by adding the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

#### <a name="self-contained-deployment-scd"></a><span data-ttu-id="3bc10-125">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="3bc10-125">Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="3bc10-126">Confirme a presença de um [RID (Identificador de Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ou adicione um RID ao `<PropertyGroup>` que contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="3bc10-126">Confirm the presence of a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) or add a RID to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="3bc10-127">Desabilite a criação de um arquivo *web.config* adicionando a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="3bc10-127">Disable the creation of a *web.config* file by adding the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="3bc10-128">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="3bc10-128">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="3bc10-129">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="3bc10-129">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="3bc10-130">Use o nome da propriedade `<RuntimeIdentifiers>` (plural).</span><span class="sxs-lookup"><span data-stu-id="3bc10-130">Use the property name `<RuntimeIdentifiers>` (plural).</span></span>

  <span data-ttu-id="3bc10-131">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="3bc10-131">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="3bc10-132">Adicionar uma referência de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="3bc10-132">Add a package reference for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span></span>

<span data-ttu-id="3bc10-133">Para habilitar o registro em log do Log de Eventos do Windows, adicione uma referência de pacote para [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="3bc10-133">To enable Windows Event Log logging, add a package reference for [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="3bc10-134">Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="3bc10-134">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

### <a name="programmain-updates"></a><span data-ttu-id="3bc10-135">Atualizações de Program.Main</span><span class="sxs-lookup"><span data-stu-id="3bc10-135">Program.Main updates</span></span>

<span data-ttu-id="3bc10-136">Faça as seguintes alterações em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="3bc10-136">Make the following changes in `Program.Main`:</span></span>

* <span data-ttu-id="3bc10-137">Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="3bc10-137">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="3bc10-138">Verifique se o depurador está anexado ou se há um argumento de linha de comado do `--console` presente.</span><span class="sxs-lookup"><span data-stu-id="3bc10-138">Inspect if the debugger is attached or a `--console` command-line argument is present.</span></span>

  <span data-ttu-id="3bc10-139">Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> no Host da Web.</span><span class="sxs-lookup"><span data-stu-id="3bc10-139">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> on the Web Host.</span></span>

  <span data-ttu-id="3bc10-140">Se as condições forem falsas (o aplicativo for executado como um serviço):</span><span class="sxs-lookup"><span data-stu-id="3bc10-140">If the conditions are false (the app is run as a service):</span></span>

  * <span data-ttu-id="3bc10-141">Chame o <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> e use um caminho para o local publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bc10-141">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> and use a path to the app's published location.</span></span> <span data-ttu-id="3bc10-142">Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando `GetCurrentDirectory` é chamado.</span><span class="sxs-lookup"><span data-stu-id="3bc10-142">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when `GetCurrentDirectory` is called.</span></span> <span data-ttu-id="3bc10-143">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="3bc10-143">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
  * <span data-ttu-id="3bc10-144">Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="3bc10-144">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

  <span data-ttu-id="3bc10-145">Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.</span><span class="sxs-lookup"><span data-stu-id="3bc10-145">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives them.</span></span>

* <span data-ttu-id="3bc10-146">Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="3bc10-146">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="3bc10-147">Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.</span><span class="sxs-lookup"><span data-stu-id="3bc10-147">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span> <span data-ttu-id="3bc10-148">Para fins de teste e demonstração, o arquivo de configurações de Produção do aplicativo de exemplo define o nível de log para `Information`.</span><span class="sxs-lookup"><span data-stu-id="3bc10-148">For demonstration and testing purposes, the sample app's Production settings file sets the logging level to `Information`.</span></span> <span data-ttu-id="3bc10-149">Na produção, o valor normalmente é definido como `Error`.</span><span class="sxs-lookup"><span data-stu-id="3bc10-149">In production, the value is typically set to `Error`.</span></span> <span data-ttu-id="3bc10-150">Para obter mais informações, consulte <xref:fundamentals/logging/index#windows-eventlog-provider>.</span><span class="sxs-lookup"><span data-stu-id="3bc10-150">For more information, see <xref:fundamentals/logging/index#windows-eventlog-provider>.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

### <a name="publish-the-app"></a><span data-ttu-id="3bc10-151">Publique o aplicativo</span><span class="sxs-lookup"><span data-stu-id="3bc10-151">Publish the app</span></span>

<span data-ttu-id="3bc10-152">Publique o aplicativo usando [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), um [perfil de publicação do Visual Studio](xref:host-and-deploy/visual-studio-publish-profiles) ou o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3bc10-152">Publish the app using [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), a [Visual Studio publish profile](xref:host-and-deploy/visual-studio-publish-profiles), or Visual Studio Code.</span></span> <span data-ttu-id="3bc10-153">Ao usar o Visual Studio, selecione o **FolderProfile** e configure o **Local de destino** antes de selecionar o botão **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="3bc10-153">When using Visual Studio, select the **FolderProfile** and configure the **Target Location** before selecting the **Publish** button.</span></span>

<span data-ttu-id="3bc10-154">Para publicar o aplicativo de exemplo usando as ferramentas da CLI (Interface de Linha de Comando), execute o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) em um prompt de comando da pasta do projeto, passando a configuração de uma versão para a opção [-c|--configuration](/dotnet/core/tools/dotnet-publish#options).</span><span class="sxs-lookup"><span data-stu-id="3bc10-154">To publish the sample app using command-line interface (CLI) tools, run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command at a command prompt from the project folder with a Release configuration passed to the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option.</span></span> <span data-ttu-id="3bc10-155">Use a opção [-o|--output](/dotnet/core/tools/dotnet-publish#options) com um caminho para publicar em uma pasta fora do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bc10-155">Use the [-o|--output](/dotnet/core/tools/dotnet-publish#options) option with a path to publish to a folder outside of the app.</span></span>

#### <a name="publish-a-framework-dependent-deployment-fdd"></a><span data-ttu-id="3bc10-156">Publicar uma FDD (Implantação Dependente de Estrutura)</span><span class="sxs-lookup"><span data-stu-id="3bc10-156">Publish a Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="3bc10-157">No exemplo a seguir, o aplicativo é publicado na pasta *c:\\svc*:</span><span class="sxs-lookup"><span data-stu-id="3bc10-157">In the following example, the app is published to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --output c:\svc
```

#### <a name="publish-a-self-contained-deployment-scd"></a><span data-ttu-id="3bc10-158">Publicar uma SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="3bc10-158">Publish a Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="3bc10-159">O RID deve ser especificado na propriedade `<RuntimeIdenfifier>` (ou `<RuntimeIdentifiers>`) do arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="3bc10-159">The RID must be specified in the `<RuntimeIdenfifier>` (or `<RuntimeIdentifiers>`) property of the project file.</span></span> <span data-ttu-id="3bc10-160">Insira o tempo de execução na opção [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) do comando `dotnet publish`.</span><span class="sxs-lookup"><span data-stu-id="3bc10-160">Supply the runtime to the [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) option of the `dotnet publish` command.</span></span>

<span data-ttu-id="3bc10-161">No exemplo a seguir, o aplicativo é publicado para o tempo de execução `win7-x64` para a pasta *c:\\svc*:</span><span class="sxs-lookup"><span data-stu-id="3bc10-161">In the following example, the app is published for the `win7-x64` runtime to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
```

### <a name="create-a-user-account"></a><span data-ttu-id="3bc10-162">Criar uma conta de usuário</span><span class="sxs-lookup"><span data-stu-id="3bc10-162">Create a user account</span></span>

<span data-ttu-id="3bc10-163">Crie uma conta de usuário para o serviço usando o comando `net user`:</span><span class="sxs-lookup"><span data-stu-id="3bc10-163">Create a user account for the service using the `net user` command:</span></span>

```console
net user {USER ACCOUNT} {PASSWORD} /add
```

<span data-ttu-id="3bc10-164">Para o aplicativo de exemplo, crie uma conta de usuário com o nome `ServiceUser` e uma senha.</span><span class="sxs-lookup"><span data-stu-id="3bc10-164">For the sample app, create a user account with the name `ServiceUser` and a password.</span></span> <span data-ttu-id="3bc10-165">No comando a seguir, substitua `{PASSWORD}` por uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).</span><span class="sxs-lookup"><span data-stu-id="3bc10-165">In the following command, replace `{PASSWORD}` with a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).</span></span>

```console
net user ServiceUser {PASSWORD} /add
```

<span data-ttu-id="3bc10-166">Se você precisar adicionar o usuário a um grupo, use o comando `net localgroup`, onde `{GROUP}` é o nome do grupo:</span><span class="sxs-lookup"><span data-stu-id="3bc10-166">If you need to add the user to a group, use the `net localgroup` command, where `{GROUP}` is the name of the group:</span></span>

```console
net localgroup {GROUP} {USER ACCOUNT} /add
```

<span data-ttu-id="3bc10-167">Para obter mais informações, confira [Contas de Usuário do Serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="3bc10-167">For more information, see [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

### <a name="set-permissions"></a><span data-ttu-id="3bc10-168">Configurar permissões</span><span class="sxs-lookup"><span data-stu-id="3bc10-168">Set permissions</span></span>

<span data-ttu-id="3bc10-169">Conceda acesso de gravação/leitura/execução para a pasta do aplicativo usando o comando [icacls](/windows-server/administration/windows-commands/icacls):</span><span class="sxs-lookup"><span data-stu-id="3bc10-169">Grant write/read/execute access to the app's folder using the [icacls](/windows-server/administration/windows-commands/icacls) command:</span></span>

```console
icacls "{PATH}" /grant {USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS} /t
```

* <span data-ttu-id="3bc10-170">`{PATH}` &ndash; Caminho para a pasta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bc10-170">`{PATH}` &ndash; Path to the app's folder.</span></span>
* <span data-ttu-id="3bc10-171">`{USER ACCOUNT}` &ndash; A conta de usuário (SID).</span><span class="sxs-lookup"><span data-stu-id="3bc10-171">`{USER ACCOUNT}` &ndash; The user account (SID).</span></span>
* <span data-ttu-id="3bc10-172">`(OI)` &ndash; O sinalizador de Herança de Objeto propaga as permissão para os arquivos subordinados.</span><span class="sxs-lookup"><span data-stu-id="3bc10-172">`(OI)` &ndash; The Object Inherit flag propagates permissions to subordinate files.</span></span>
* <span data-ttu-id="3bc10-173">`(CI)` &ndash; O sinalizador de Herança de Contêiner propaga as permissão para as pastas subordinadas.</span><span class="sxs-lookup"><span data-stu-id="3bc10-173">`(CI)` &ndash; The Container Inherit flag propagates permissions to subordinate folders.</span></span>
* <span data-ttu-id="3bc10-174">`{PERMISSION FLAGS}` &ndash; Define as permissões de acesso do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bc10-174">`{PERMISSION FLAGS}` &ndash; Sets the app's access permissions.</span></span>
  * <span data-ttu-id="3bc10-175">Gravar (`W`)</span><span class="sxs-lookup"><span data-stu-id="3bc10-175">Write (`W`)</span></span>
  * <span data-ttu-id="3bc10-176">Ler (`R`)</span><span class="sxs-lookup"><span data-stu-id="3bc10-176">Read (`R`)</span></span>
  * <span data-ttu-id="3bc10-177">Executar (`X`)</span><span class="sxs-lookup"><span data-stu-id="3bc10-177">Execute (`X`)</span></span>
  * <span data-ttu-id="3bc10-178">Completo (`F`)</span><span class="sxs-lookup"><span data-stu-id="3bc10-178">Full (`F`)</span></span>
  * <span data-ttu-id="3bc10-179">Modificar (`M`)</span><span class="sxs-lookup"><span data-stu-id="3bc10-179">Modify (`M`)</span></span>
* <span data-ttu-id="3bc10-180">`/t` &ndash; Aplique recursivamente aos arquivos e pastas subordinadas existentes.</span><span class="sxs-lookup"><span data-stu-id="3bc10-180">`/t` &ndash; Apply recursively to existing subordinate folders and files.</span></span>

<span data-ttu-id="3bc10-181">Para o aplicativo de exemplo publicado na pasta *c:\\svc* e a conta `ServiceUser` com permissões de gravação/leitura/execução, use o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3bc10-181">For the sample app published to the *c:\\svc* folder and the `ServiceUser` account with write/read/execute permissions, use the following command:</span></span>

```console
icacls "c:\svc" /grant ServiceUser:(OI)(CI)WRX /t
```

<span data-ttu-id="3bc10-182">Para obter mais informações, confira [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="3bc10-182">For more information, see [icacls](/windows-server/administration/windows-commands/icacls).</span></span>

## <a name="manage-the-service"></a><span data-ttu-id="3bc10-183">Gerenciar o serviço</span><span class="sxs-lookup"><span data-stu-id="3bc10-183">Manage the service</span></span>

### <a name="create-the-service"></a><span data-ttu-id="3bc10-184">Criar o serviço</span><span class="sxs-lookup"><span data-stu-id="3bc10-184">Create the service</span></span>

<span data-ttu-id="3bc10-185">Use a ferramenta de linha de comando [sc.exe](https://technet.microsoft.com/library/bb490995) para criar o serviço.</span><span class="sxs-lookup"><span data-stu-id="3bc10-185">Use the [sc.exe](https://technet.microsoft.com/library/bb490995) command-line tool to create the service.</span></span> <span data-ttu-id="3bc10-186">O valor `binPath` é o caminho para o executável do aplicativo, que inclui o nome do arquivo executável.</span><span class="sxs-lookup"><span data-stu-id="3bc10-186">The `binPath` value is the path to the app's executable, which includes the executable file name.</span></span> <span data-ttu-id="3bc10-187">**É necessário o espaço entre o sinal de igual e o caractere de aspas de cada parâmetro e valor.**</span><span class="sxs-lookup"><span data-stu-id="3bc10-187">**The space between the equal sign and the quote character of each parameter and value is required.**</span></span>

```console
sc create {SERVICE NAME} binPath= "{PATH}" obj= "{DOMAIN}\{USER ACCOUNT}" password= "{PASSWORD}"
```

* <span data-ttu-id="3bc10-188">`{SERVICE NAME}` &ndash; O nome a ser atribuído ao serviço no [Gerenciador de Controle de Serviço](/windows/desktop/services/service-control-manager).</span><span class="sxs-lookup"><span data-stu-id="3bc10-188">`{SERVICE NAME}` &ndash; The name to assign to the service in [Service Control Manager](/windows/desktop/services/service-control-manager).</span></span>
* <span data-ttu-id="3bc10-189">`{PATH}` &ndash; O caminho para o executável do serviço.</span><span class="sxs-lookup"><span data-stu-id="3bc10-189">`{PATH}` &ndash; The path to the service executable.</span></span>
* <span data-ttu-id="3bc10-190">`{DOMAIN}` &ndash; O domínio de um computador ingressado no domínio.</span><span class="sxs-lookup"><span data-stu-id="3bc10-190">`{DOMAIN}` &ndash; The domain of a domain-joined machine.</span></span> <span data-ttu-id="3bc10-191">Se o computador não estiver ingressado no domínio, o nome do computador local.</span><span class="sxs-lookup"><span data-stu-id="3bc10-191">If the machine isn't domain-joined, the local machine name.</span></span>
* <span data-ttu-id="3bc10-192">`{USER ACCOUNT}` &ndash; A conta do usuário na qual o serviço é executado.</span><span class="sxs-lookup"><span data-stu-id="3bc10-192">`{USER ACCOUNT}` &ndash; The user account under which the service runs.</span></span>
* <span data-ttu-id="3bc10-193">`{PASSWORD}` &ndash; A senhas da conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="3bc10-193">`{PASSWORD}` &ndash; The user account password.</span></span>

> [!WARNING]
> <span data-ttu-id="3bc10-194">**Não** omita o parâmetro `obj`.</span><span class="sxs-lookup"><span data-stu-id="3bc10-194">Do **not** omit the `obj` parameter.</span></span> <span data-ttu-id="3bc10-195">O valor padrão para `obj` é a [conta LocalSystem](/windows/desktop/services/localsystem-account).</span><span class="sxs-lookup"><span data-stu-id="3bc10-195">The default value for `obj` is the [LocalSystem account](/windows/desktop/services/localsystem-account) account.</span></span> <span data-ttu-id="3bc10-196">Executar um serviço na conta `LocalSystem` apresenta um risco de segurança significativo.</span><span class="sxs-lookup"><span data-stu-id="3bc10-196">Running a service under the `LocalSystem` account presents a significant security risk.</span></span> <span data-ttu-id="3bc10-197">Sempre execute um serviço com uma conta de usuário com privilégios restritos.</span><span class="sxs-lookup"><span data-stu-id="3bc10-197">Always run a service with a user account that has restricted privileges.</span></span>

<span data-ttu-id="3bc10-198">Observe o seguinte aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="3bc10-198">In the following example for the sample app:</span></span>

* <span data-ttu-id="3bc10-199">O nome do serviço é **MyService**.</span><span class="sxs-lookup"><span data-stu-id="3bc10-199">The service is named **MyService**.</span></span>
* <span data-ttu-id="3bc10-200">O serviço publicado reside na pasta *c:\\svc*.</span><span class="sxs-lookup"><span data-stu-id="3bc10-200">The published service resides in the *c:\\svc* folder.</span></span> <span data-ttu-id="3bc10-201">O executável do aplicativo é chamado de *SampleApp.exe*.</span><span class="sxs-lookup"><span data-stu-id="3bc10-201">The app executable is named *SampleApp.exe*.</span></span> <span data-ttu-id="3bc10-202">Coloque o valor `binPath` entre aspas duplas (").</span><span class="sxs-lookup"><span data-stu-id="3bc10-202">Enclose the `binPath` value in double quotation marks (").</span></span>
* <span data-ttu-id="3bc10-203">O serviço é executado na conta `ServiceUser`.</span><span class="sxs-lookup"><span data-stu-id="3bc10-203">The service runs under the `ServiceUser` account.</span></span> <span data-ttu-id="3bc10-204">Substitua `{DOMAIN}` com o domínio ou nome do computador local da conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="3bc10-204">Replace `{DOMAIN}` with the user account's domain or local machine name.</span></span> <span data-ttu-id="3bc10-205">Coloque o valor `obj` entre aspas duplas (").</span><span class="sxs-lookup"><span data-stu-id="3bc10-205">Enclose the `obj` value in double quotation marks (").</span></span> <span data-ttu-id="3bc10-206">Exemplo: se o sistema de hospedagem é uma máquina local denominada `MairaPC`, defina `obj` para `"MairaPC\ServiceUser"`.</span><span class="sxs-lookup"><span data-stu-id="3bc10-206">Example: If the hosting system is a local machine named `MairaPC`, set `obj` to `"MairaPC\ServiceUser"`.</span></span>
* <span data-ttu-id="3bc10-207">Substitua `{PASSWORD}` com a senha da conta do usuário.</span><span class="sxs-lookup"><span data-stu-id="3bc10-207">Replace `{PASSWORD}` with the user account's password.</span></span> <span data-ttu-id="3bc10-208">Coloque o valor `password` entre aspas duplas (").</span><span class="sxs-lookup"><span data-stu-id="3bc10-208">Enclose the `password` value in double quotation marks (").</span></span>

```console
sc create MyService binPath= "c:\svc\sampleapp.exe" obj= "{DOMAIN}\ServiceUser" password= "{PASSWORD}"
```

> [!IMPORTANT]
> <span data-ttu-id="3bc10-209">Certifique-se de que os espaços entre os sinais de igual e os valores dos parâmetros estão presentes.</span><span class="sxs-lookup"><span data-stu-id="3bc10-209">Make sure that the spaces between the parameters' equal signs and the parameters' values are present.</span></span>

### <a name="start-the-service"></a><span data-ttu-id="3bc10-210">Iniciar o serviço</span><span class="sxs-lookup"><span data-stu-id="3bc10-210">Start the service</span></span>

<span data-ttu-id="3bc10-211">Inicie o serviço com o comando `sc start {SERVICE NAME}`.</span><span class="sxs-lookup"><span data-stu-id="3bc10-211">Start the service with the `sc start {SERVICE NAME}` command.</span></span>

<span data-ttu-id="3bc10-212">Para iniciar o serviço de aplicativo de exemplo, use o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="3bc10-212">To start the sample app service, use the following command:</span></span>

```console
sc start MyService
```

<span data-ttu-id="3bc10-213">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="3bc10-213">The command takes a few seconds to start the service.</span></span>

### <a name="determine-the-service-status"></a><span data-ttu-id="3bc10-214">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="3bc10-214">Determine the service status</span></span>

<span data-ttu-id="3bc10-215">Para verificar o status do serviço, use o comando `sc query {SERVICE NAME}`.</span><span class="sxs-lookup"><span data-stu-id="3bc10-215">To check the status of the service, use the `sc query {SERVICE NAME}` command.</span></span> <span data-ttu-id="3bc10-216">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="3bc10-216">The status is reported as one of the following values:</span></span>

* `START_PENDING`
* `RUNNING`
* `STOP_PENDING`
* `STOPPED`

<span data-ttu-id="3bc10-217">Use o seguinte comando para verificar o status do serviço de aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="3bc10-217">Use the following command to check the status of the sample app service:</span></span>

```console
sc query MyService
```

### <a name="browse-a-web-app-service"></a><span data-ttu-id="3bc10-218">Procurar um serviço de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="3bc10-218">Browse a web app service</span></span>

<span data-ttu-id="3bc10-219">Quando o serviço estiver no estado `RUNNING` e se o serviço for um aplicativo Web, procure o aplicativo em seu caminho (por padrão, `http://localhost:5000`, que redireciona para `https://localhost:5001` ao usar [Middleware de Redirecionamento HTTPS](xref:security/enforcing-ssl)).</span><span class="sxs-lookup"><span data-stu-id="3bc10-219">When the service is in the `RUNNING` state and if the service is a web app, browse the app at its path (by default, `http://localhost:5000`, which redirects to `https://localhost:5001` when using [HTTPS Redirection Middleware](xref:security/enforcing-ssl)).</span></span>

<span data-ttu-id="3bc10-220">Para o serviço de aplicativo de exemplo, procure o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="3bc10-220">For the sample app service, browse the app at `http://localhost:5000`.</span></span>

### <a name="stop-the-service"></a><span data-ttu-id="3bc10-221">Parar o serviço</span><span class="sxs-lookup"><span data-stu-id="3bc10-221">Stop the service</span></span>

<span data-ttu-id="3bc10-222">Interrompa o serviço com o comando `sc stop {SERVICE NAME}`.</span><span class="sxs-lookup"><span data-stu-id="3bc10-222">Stop the service with the `sc stop {SERVICE NAME}` command.</span></span>

<span data-ttu-id="3bc10-223">O comando a seguir interrompe o serviço de aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="3bc10-223">The following command stops the sample app service:</span></span>

```console
sc stop MyService
```

### <a name="delete-the-service"></a><span data-ttu-id="3bc10-224">Excluir o serviço</span><span class="sxs-lookup"><span data-stu-id="3bc10-224">Delete the service</span></span>

<span data-ttu-id="3bc10-225">Após um pequeno atraso para interromper um serviço, desinstale o serviço com o comando `sc delete {SERVICE NAME}`.</span><span class="sxs-lookup"><span data-stu-id="3bc10-225">After a short delay to stop a service, uninstall the service with the `sc delete {SERVICE NAME}` command.</span></span>

<span data-ttu-id="3bc10-226">Verifique o status do serviço de aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="3bc10-226">Check the status of the sample app service:</span></span>

```console
sc query MyService
```

<span data-ttu-id="3bc10-227">Quando o serviço de aplicativo de exemplo estiver no estado `STOPPED`, use o seguinte comando para desinstalar o serviço de aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="3bc10-227">When the sample app service is in the `STOPPED` state, use the following command to uninstall the sample app service:</span></span>

```console
sc delete MyService
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="3bc10-228">Manipular eventos de início e de parada</span><span class="sxs-lookup"><span data-stu-id="3bc10-228">Handle starting and stopping events</span></span>

<span data-ttu-id="3bc10-229">Para manipular os eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, faça as seguintes alterações adicionais:</span><span class="sxs-lookup"><span data-stu-id="3bc10-229">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events, perform the following additional changes:</span></span>

1. <span data-ttu-id="3bc10-230">Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="3bc10-230">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="3bc10-231">Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="3bc10-231">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="3bc10-232">Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="3bc10-232">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="3bc10-233">Para ver o local do <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, consulte o exemplo de código mostrado na seção [Converter um projeto em um Serviço Windows](#convert-a-project-into-a-windows-service).</span><span class="sxs-lookup"><span data-stu-id="3bc10-233">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Convert a project into a Windows Service](#convert-a-project-into-a-windows-service) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="3bc10-234">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="3bc10-234">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="3bc10-235">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="3bc10-235">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="3bc10-236">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="3bc10-236">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="3bc10-237">Configurar o HTTPS</span><span class="sxs-lookup"><span data-stu-id="3bc10-237">Configure HTTPS</span></span>

<span data-ttu-id="3bc10-238">Para configurar o serviço com um ponto de extremidade seguro:</span><span class="sxs-lookup"><span data-stu-id="3bc10-238">To configure the service with a secure endpoint:</span></span>

1. <span data-ttu-id="3bc10-239">Crie um certificado X.509 para o sistema de hospedagem usando os mecanismos de implantação e aquisição do certificado da sua plataforma.</span><span class="sxs-lookup"><span data-stu-id="3bc10-239">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>

1. <span data-ttu-id="3bc10-240">Especifique uma [Configuração do ponto de extremidade HTTPS do servidor Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) para usar o certificado.</span><span class="sxs-lookup"><span data-stu-id="3bc10-240">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="3bc10-241">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="3bc10-241">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="3bc10-242">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="3bc10-242">Current directory and content root</span></span>

<span data-ttu-id="3bc10-243">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="3bc10-243">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="3bc10-244">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="3bc10-244">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="3bc10-245">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="3bc10-245">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="3bc10-246">Defina o caminho da raiz do conteúdo para a pasta do aplicativo</span><span class="sxs-lookup"><span data-stu-id="3bc10-246">Set the content root path to the app's folder</span></span>

<span data-ttu-id="3bc10-247">O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando o serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="3bc10-247">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when the service is created.</span></span> <span data-ttu-id="3bc10-248">Em vez de chamar `GetCurrentDirectory` para criar caminhos para arquivos de configuração, chame <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> com o caminho para a raiz do conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3bc10-248">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> with the path to the app's content root.</span></span>

<span data-ttu-id="3bc10-249">No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="3bc10-249">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);

CreateWebHostBuilder(args)
    .UseContentRoot(pathToContentRoot)
    .Build()
    .RunAsService();
```

### <a name="store-the-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="3bc10-250">Armazene os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="3bc10-250">Store the service's files in a suitable location on disk</span></span>

<span data-ttu-id="3bc10-251">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="3bc10-251">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3bc10-252">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3bc10-252">Additional resources</span></span>

* <span data-ttu-id="3bc10-253">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="3bc10-253">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>
