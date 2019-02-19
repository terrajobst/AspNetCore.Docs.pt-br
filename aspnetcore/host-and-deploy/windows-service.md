---
title: Hospedar o ASP.NET Core em um serviço Windows
author: guardrex
description: Saiba como hospedar um aplicativo ASP.NET Core em um serviço Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/13/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 081a631c9c3e74c01e15f4b0b272d650c162bd20
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248245"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="023e7-103">Hospedar o ASP.NET Core em um serviço Windows</span><span class="sxs-lookup"><span data-stu-id="023e7-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="023e7-104">Por [Luke Latham](https://github.com/guardrex) e [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="023e7-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="023e7-105">Um aplicativo ASP.NET Core pode ser hospedado no Windows somo um [Serviço Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sem usar o IIS.</span><span class="sxs-lookup"><span data-stu-id="023e7-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="023e7-106">Quando hospedado como um Serviço Windows, o aplicativo é iniciado automaticamente após a reinicialização.</span><span class="sxs-lookup"><span data-stu-id="023e7-106">When hosted as a Windows Service, the app automatically starts after reboots.</span></span>

<span data-ttu-id="023e7-107">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="023e7-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="deployment-type"></a><span data-ttu-id="023e7-108">Tipo de implantação</span><span class="sxs-lookup"><span data-stu-id="023e7-108">Deployment type</span></span>

<span data-ttu-id="023e7-109">Você pode criar uma implantação de Serviço Windows dependente de estrutura ou autocontida.</span><span class="sxs-lookup"><span data-stu-id="023e7-109">You can create either a framework-dependent or self-contained Windows Service deployment.</span></span> <span data-ttu-id="023e7-110">Para saber mais e obter conselhos sobre cenários de implantação, consulte [Implantação de aplicativos .NET Core](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="023e7-110">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment"></a><span data-ttu-id="023e7-111">Implantação dependente de estrutura</span><span class="sxs-lookup"><span data-stu-id="023e7-111">Framework-dependent deployment</span></span>

<span data-ttu-id="023e7-112">A FDD (Implantação Dependente de Estrutura) se baseia na presença de uma versão compartilhada em todo o sistema do .NET Core no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="023e7-112">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="023e7-113">Quando o cenário FDD é usado com um aplicativo de Serviço Windows do ASP.NET Core, o SDK produz um executável (*\*.exe*), chamado de *executável dependente de estrutura*.</span><span class="sxs-lookup"><span data-stu-id="023e7-113">When the FDD scenario is used with an ASP.NET Core Windows Service app, the SDK produces an executable (*\*.exe*), called a *framework-dependent executable*.</span></span>

### <a name="self-contained-deployment"></a><span data-ttu-id="023e7-114">Implantação autocontida</span><span class="sxs-lookup"><span data-stu-id="023e7-114">Self-contained deployment</span></span>

<span data-ttu-id="023e7-115">A SCD (Implantação Autocontida) não se baseia na presença de componentes compartilhados no sistema de destino.</span><span class="sxs-lookup"><span data-stu-id="023e7-115">Self-contained deployment (SCD) doesn't rely on the presence of shared components on the target system.</span></span> <span data-ttu-id="023e7-116">O tempo de execução e as dependências do aplicativo são implantados com o aplicativo para o sistema de hospedagem.</span><span class="sxs-lookup"><span data-stu-id="023e7-116">The runtime and the app's dependencies are deployed with the app to the hosting system.</span></span>

## <a name="convert-a-project-into-a-windows-service"></a><span data-ttu-id="023e7-117">Converter um projeto em um serviço Windows</span><span class="sxs-lookup"><span data-stu-id="023e7-117">Convert a project into a Windows Service</span></span>

<span data-ttu-id="023e7-118">Faça as seguintes alterações em um projeto ASP.NET Core existente para executar o aplicativo como um serviço:</span><span class="sxs-lookup"><span data-stu-id="023e7-118">Make the following changes to an existing ASP.NET Core project to run the app as a service:</span></span>

### <a name="project-file-updates"></a><span data-ttu-id="023e7-119">Atualizações de arquivo de projeto</span><span class="sxs-lookup"><span data-stu-id="023e7-119">Project file updates</span></span>

<span data-ttu-id="023e7-120">Com base na sua escolha de [tipo de implantação](#deployment-type), atualize o arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="023e7-120">Based on your choice of [deployment type](#deployment-type), update the project file:</span></span>

#### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="023e7-121">FDD (Implantação dependente de estrutura)</span><span class="sxs-lookup"><span data-stu-id="023e7-121">Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="023e7-122">Adicione um [RID (Identificador do Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ao `<PropertyGroup>` que contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="023e7-122">Add a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="023e7-123">No exemplo a seguir, o RID é especificado como `win7-x64`.</span><span class="sxs-lookup"><span data-stu-id="023e7-123">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="023e7-124">Adicione a propriedade `<SelfContained>` definida como `false`.</span><span class="sxs-lookup"><span data-stu-id="023e7-124">Add the `<SelfContained>` property set to `false`.</span></span> <span data-ttu-id="023e7-125">Essas propriedades instruem o SDK a gerar um arquivo executável (*.exe*) para Windows.</span><span class="sxs-lookup"><span data-stu-id="023e7-125">These properties instruct the SDK to generate an executable (*.exe*) file for Windows.</span></span>

<span data-ttu-id="023e7-126">O arquivo *web.config*, que normalmente é gerado durante a publicação de um aplicativo ASP.NET Core, é desnecessário para um aplicativo de serviços do Windows.</span><span class="sxs-lookup"><span data-stu-id="023e7-126">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="023e7-127">Para desabilitar a criação de um arquivo *web.config*, adicione a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="023e7-127">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

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

<span data-ttu-id="023e7-128">Adicione a propriedade `<UseAppHost>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="023e7-128">Add the `<UseAppHost>` property set to `true`.</span></span> <span data-ttu-id="023e7-129">Essa propriedade fornece o serviço com um caminho de ativação (um arquivo executável *.exe*) para FDD.</span><span class="sxs-lookup"><span data-stu-id="023e7-129">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

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

#### <a name="self-contained-deployment-scd"></a><span data-ttu-id="023e7-130">SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="023e7-130">Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="023e7-131">Confirme a presença de um [RID (Identificador de Tempo de Execução)](/dotnet/core/rid-catalog) do Windows ou adicione um RID ao `<PropertyGroup>` que contém a estrutura de destino.</span><span class="sxs-lookup"><span data-stu-id="023e7-131">Confirm the presence of a Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) or add a RID to the `<PropertyGroup>` that contains the target framework.</span></span> <span data-ttu-id="023e7-132">Desabilite a criação de um arquivo *web.config* adicionando a propriedade `<IsTransformWebConfigDisabled>` definida como `true`.</span><span class="sxs-lookup"><span data-stu-id="023e7-132">Disable the creation of a *web.config* file by adding the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="023e7-133">Para publicar para vários RIDs:</span><span class="sxs-lookup"><span data-stu-id="023e7-133">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="023e7-134">Forneça os RIDs em uma lista delimitada por ponto e vírgula.</span><span class="sxs-lookup"><span data-stu-id="023e7-134">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="023e7-135">Use o nome da propriedade `<RuntimeIdentifiers>` (plural).</span><span class="sxs-lookup"><span data-stu-id="023e7-135">Use the property name `<RuntimeIdentifiers>` (plural).</span></span>

  <span data-ttu-id="023e7-136">Para obter mais informações, consulte [Catálogo de RID do .NET Core](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="023e7-136">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="023e7-137">Adicionar uma referência de pacote para [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="023e7-137">Add a package reference for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices).</span></span>

<span data-ttu-id="023e7-138">Para habilitar o registro em log do Log de Eventos do Windows, adicione uma referência de pacote para [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="023e7-138">To enable Windows Event Log logging, add a package reference for [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="023e7-139">Para saber mais, consulte a seção [Manipular eventos de início e de parada](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="023e7-139">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

### <a name="programmain-updates"></a><span data-ttu-id="023e7-140">Atualizações de Program.Main</span><span class="sxs-lookup"><span data-stu-id="023e7-140">Program.Main updates</span></span>

<span data-ttu-id="023e7-141">Faça as seguintes alterações em `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="023e7-141">Make the following changes in `Program.Main`:</span></span>

* <span data-ttu-id="023e7-142">Para testar e depurar quando a execução estiver sendo feita fora de um serviço, adicione código para determinar se o aplicativo está sendo executado como um serviço ou aplicativo de console.</span><span class="sxs-lookup"><span data-stu-id="023e7-142">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="023e7-143">Verifique se o depurador está anexado ou se há um argumento de linha de comado do `--console` presente.</span><span class="sxs-lookup"><span data-stu-id="023e7-143">Inspect if the debugger is attached or a `--console` command-line argument is present.</span></span>

  <span data-ttu-id="023e7-144">Se uma dessas condições for verdadeira (o aplicativo não for executado como um serviço), chame <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> no Host da Web.</span><span class="sxs-lookup"><span data-stu-id="023e7-144">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> on the Web Host.</span></span>

  <span data-ttu-id="023e7-145">Se as condições forem falsas (o aplicativo for executado como um serviço):</span><span class="sxs-lookup"><span data-stu-id="023e7-145">If the conditions are false (the app is run as a service):</span></span>

  * <span data-ttu-id="023e7-146">Chame o <xref:System.IO.Directory.SetCurrentDirectory*> e use um caminho para o local publicado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="023e7-146">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="023e7-147">Não chame o <xref:System.IO.Directory.GetCurrentDirectory*> para obter o caminho porque um aplicativo de Serviço Windows retorna a pasta *C:\\WINDOWS\\system32* quando <xref:System.IO.Directory.GetCurrentDirectory*> é chamado.</span><span class="sxs-lookup"><span data-stu-id="023e7-147">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="023e7-148">Para saber mais, consulte a seção [Diretório atual e a raiz do conteúdo](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="023e7-148">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
  * <span data-ttu-id="023e7-149">Chame o <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> para executar o aplicativo como um serviço.</span><span class="sxs-lookup"><span data-stu-id="023e7-149">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

  <span data-ttu-id="023e7-150">Como o [Provedor de Configuração da Linha de Comando](xref:fundamentals/configuration/index#command-line-configuration-provider) requer pares nome-valor para argumentos de linha de comando, a opção `--console` é removida dos argumentos antes de o <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> recebê-los.</span><span class="sxs-lookup"><span data-stu-id="023e7-150">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives them.</span></span>

* <span data-ttu-id="023e7-151">Para gravar no Log de Eventos do Windows, adicione o Provedor de Log de Eventos a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span><span class="sxs-lookup"><span data-stu-id="023e7-151">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="023e7-152">Defina o nível de log com a chave `Logging:LogLevel:Default` no arquivo *appsettings.Production.JSON*.</span><span class="sxs-lookup"><span data-stu-id="023e7-152">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span> <span data-ttu-id="023e7-153">Para fins de teste e demonstração, o arquivo de configurações de Produção do aplicativo de exemplo define o nível de log para `Information`.</span><span class="sxs-lookup"><span data-stu-id="023e7-153">For demonstration and testing purposes, the sample app's Production settings file sets the logging level to `Information`.</span></span> <span data-ttu-id="023e7-154">Na produção, o valor normalmente é definido como `Error`.</span><span class="sxs-lookup"><span data-stu-id="023e7-154">In production, the value is typically set to `Error`.</span></span> <span data-ttu-id="023e7-155">Para obter mais informações, consulte <xref:fundamentals/logging/index#windows-eventlog-provider>.</span><span class="sxs-lookup"><span data-stu-id="023e7-155">For more information, see <xref:fundamentals/logging/index#windows-eventlog-provider>.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

### <a name="publish-the-app"></a><span data-ttu-id="023e7-156">Publique o aplicativo</span><span class="sxs-lookup"><span data-stu-id="023e7-156">Publish the app</span></span>

<span data-ttu-id="023e7-157">Publique o aplicativo usando [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), um [perfil de publicação do Visual Studio](xref:host-and-deploy/visual-studio-publish-profiles) ou o Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="023e7-157">Publish the app using [dotnet publish](/dotnet/articles/core/tools/dotnet-publish), a [Visual Studio publish profile](xref:host-and-deploy/visual-studio-publish-profiles), or Visual Studio Code.</span></span> <span data-ttu-id="023e7-158">Ao usar o Visual Studio, selecione o **FolderProfile** e configure o **Local de destino** antes de selecionar o botão **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="023e7-158">When using Visual Studio, select the **FolderProfile** and configure the **Target Location** before selecting the **Publish** button.</span></span>

<span data-ttu-id="023e7-159">Para publicar o aplicativo de exemplo usando as ferramentas da CLI (Interface de Linha de Comando), execute o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) em um prompt de comando da pasta do projeto, passando a configuração de uma versão para a opção [-c|--configuration](/dotnet/core/tools/dotnet-publish#options).</span><span class="sxs-lookup"><span data-stu-id="023e7-159">To publish the sample app using command-line interface (CLI) tools, run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command at a command prompt from the project folder with a Release configuration passed to the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option.</span></span> <span data-ttu-id="023e7-160">Use a opção [-o|--output](/dotnet/core/tools/dotnet-publish#options) com um caminho para publicar em uma pasta fora do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="023e7-160">Use the [-o|--output](/dotnet/core/tools/dotnet-publish#options) option with a path to publish to a folder outside of the app.</span></span>

#### <a name="publish-a-framework-dependent-deployment-fdd"></a><span data-ttu-id="023e7-161">Publicar uma FDD (Implantação Dependente de Estrutura)</span><span class="sxs-lookup"><span data-stu-id="023e7-161">Publish a Framework-dependent Deployment (FDD)</span></span>

<span data-ttu-id="023e7-162">No exemplo a seguir, o aplicativo é publicado na pasta *c:\\svc*:</span><span class="sxs-lookup"><span data-stu-id="023e7-162">In the following example, the app is published to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --output c:\svc
```

#### <a name="publish-a-self-contained-deployment-scd"></a><span data-ttu-id="023e7-163">Publicar uma SCD (Implantação Autossuficiente)</span><span class="sxs-lookup"><span data-stu-id="023e7-163">Publish a Self-contained Deployment (SCD)</span></span>

<span data-ttu-id="023e7-164">O RID deve ser especificado na propriedade `<RuntimeIdenfifier>` (ou `<RuntimeIdentifiers>`) do arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="023e7-164">The RID must be specified in the `<RuntimeIdenfifier>` (or `<RuntimeIdentifiers>`) property of the project file.</span></span> <span data-ttu-id="023e7-165">Insira o tempo de execução na opção [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) do comando `dotnet publish`.</span><span class="sxs-lookup"><span data-stu-id="023e7-165">Supply the runtime to the [-r|--runtime](/dotnet/core/tools/dotnet-publish#options) option of the `dotnet publish` command.</span></span>

<span data-ttu-id="023e7-166">No exemplo a seguir, o aplicativo é publicado para o tempo de execução `win7-x64` para a pasta *c:\\svc*:</span><span class="sxs-lookup"><span data-stu-id="023e7-166">In the following example, the app is published for the `win7-x64` runtime to the *c:\\svc* folder:</span></span>

```console
dotnet publish --configuration Release --runtime win7-x64 --output c:\svc
```

### <a name="create-a-user-account"></a><span data-ttu-id="023e7-167">Criar uma conta de usuário</span><span class="sxs-lookup"><span data-stu-id="023e7-167">Create a user account</span></span>

<span data-ttu-id="023e7-168">Crie uma conta de usuário para o serviço usando o comando `net user` de um shell de comando administrativo:</span><span class="sxs-lookup"><span data-stu-id="023e7-168">Create a user account for the service using the `net user` command from an administrative command shell:</span></span>

```console
net user {USER ACCOUNT} {PASSWORD} /add
```

<span data-ttu-id="023e7-169">A expiração da senha padrão ocorre em seis semanas.</span><span class="sxs-lookup"><span data-stu-id="023e7-169">The default password expiration is six weeks.</span></span>

<span data-ttu-id="023e7-170">Para o aplicativo de exemplo, crie uma conta de usuário com o nome `ServiceUser` e uma senha.</span><span class="sxs-lookup"><span data-stu-id="023e7-170">For the sample app, create a user account with the name `ServiceUser` and a password.</span></span> <span data-ttu-id="023e7-171">No comando a seguir, substitua `{PASSWORD}` por uma [senha forte](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).</span><span class="sxs-lookup"><span data-stu-id="023e7-171">In the following command, replace `{PASSWORD}` with a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements).</span></span>

```console
net user ServiceUser {PASSWORD} /add
```

<span data-ttu-id="023e7-172">Se você precisar adicionar o usuário a um grupo, use o comando `net localgroup`, onde `{GROUP}` é o nome do grupo:</span><span class="sxs-lookup"><span data-stu-id="023e7-172">If you need to add the user to a group, use the `net localgroup` command, where `{GROUP}` is the name of the group:</span></span>

```console
net localgroup {GROUP} {USER ACCOUNT} /add
```

<span data-ttu-id="023e7-173">Para obter mais informações, confira [Contas de Usuário do Serviço](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="023e7-173">For more information, see [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="023e7-174">Uma abordagem alternativa ao gerenciamento de usuários ao usar o Active Directory é usar Contas de Serviço Gerenciado.</span><span class="sxs-lookup"><span data-stu-id="023e7-174">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="023e7-175">Para saber mais, confira [Visão geral das Contas de Serviço Gerenciado em Grupo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="023e7-175">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

### <a name="set-permissions"></a><span data-ttu-id="023e7-176">Configurar permissões</span><span class="sxs-lookup"><span data-stu-id="023e7-176">Set permissions</span></span>

#### <a name="access-to-the-app-folder"></a><span data-ttu-id="023e7-177">Acesso à pasta do aplicativo</span><span class="sxs-lookup"><span data-stu-id="023e7-177">Access to the app folder</span></span>

<span data-ttu-id="023e7-178">Conceda acesso de gravação/leitura/execução à pasta do aplicativo usando o comando [icacls](/windows-server/administration/windows-commands/icacls) de um shell de comando administrativo:</span><span class="sxs-lookup"><span data-stu-id="023e7-178">Grant write/read/execute access to the app's folder using the [icacls](/windows-server/administration/windows-commands/icacls) command from an administrative command shell:</span></span>

```console
icacls "{PATH}" /grant {USER ACCOUNT}:(OI)(CI){PERMISSION FLAGS} /t
```

* <span data-ttu-id="023e7-179">`{PATH}` &ndash; Caminho para a pasta do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="023e7-179">`{PATH}` &ndash; Path to the app's folder.</span></span>
* <span data-ttu-id="023e7-180">`{USER ACCOUNT}` &ndash; A conta de usuário (SID).</span><span class="sxs-lookup"><span data-stu-id="023e7-180">`{USER ACCOUNT}` &ndash; The user account (SID).</span></span>
* <span data-ttu-id="023e7-181">`(OI)` &ndash; O sinalizador de Herança de Objeto propaga as permissão para os arquivos subordinados.</span><span class="sxs-lookup"><span data-stu-id="023e7-181">`(OI)` &ndash; The Object Inherit flag propagates permissions to subordinate files.</span></span>
* <span data-ttu-id="023e7-182">`(CI)` &ndash; O sinalizador de Herança de Contêiner propaga as permissão para as pastas subordinadas.</span><span class="sxs-lookup"><span data-stu-id="023e7-182">`(CI)` &ndash; The Container Inherit flag propagates permissions to subordinate folders.</span></span>
* <span data-ttu-id="023e7-183">`{PERMISSION FLAGS}` &ndash; Define as permissões de acesso do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="023e7-183">`{PERMISSION FLAGS}` &ndash; Sets the app's access permissions.</span></span>
  * <span data-ttu-id="023e7-184">Gravar (`W`)</span><span class="sxs-lookup"><span data-stu-id="023e7-184">Write (`W`)</span></span>
  * <span data-ttu-id="023e7-185">Ler (`R`)</span><span class="sxs-lookup"><span data-stu-id="023e7-185">Read (`R`)</span></span>
  * <span data-ttu-id="023e7-186">Executar (`X`)</span><span class="sxs-lookup"><span data-stu-id="023e7-186">Execute (`X`)</span></span>
  * <span data-ttu-id="023e7-187">Completo (`F`)</span><span class="sxs-lookup"><span data-stu-id="023e7-187">Full (`F`)</span></span>
  * <span data-ttu-id="023e7-188">Modificar (`M`)</span><span class="sxs-lookup"><span data-stu-id="023e7-188">Modify (`M`)</span></span>
* <span data-ttu-id="023e7-189">`/t` &ndash; Aplique recursivamente aos arquivos e pastas subordinadas existentes.</span><span class="sxs-lookup"><span data-stu-id="023e7-189">`/t` &ndash; Apply recursively to existing subordinate folders and files.</span></span>

<span data-ttu-id="023e7-190">Para o aplicativo de exemplo publicado na pasta *c:\\svc* e a conta `ServiceUser` com permissões de gravação/leitura/execução, use o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="023e7-190">For the sample app published to the *c:\\svc* folder and the `ServiceUser` account with write/read/execute permissions, use the following command:</span></span>

```console
icacls "c:\svc" /grant ServiceUser:(OI)(CI)WRX /t
```

<span data-ttu-id="023e7-191">Para obter mais informações, confira [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="023e7-191">For more information, see [icacls](/windows-server/administration/windows-commands/icacls).</span></span>

#### <a name="log-on-as-a-service"></a><span data-ttu-id="023e7-192">Fazer logon como serviço</span><span class="sxs-lookup"><span data-stu-id="023e7-192">Log on as a service</span></span>

<span data-ttu-id="023e7-193">Para conceder o privilégio [Fazer logon como serviço](/windows/security/threat-protection/security-policy-settings/log-on-as-a-service) à conta do usuário:</span><span class="sxs-lookup"><span data-stu-id="023e7-193">To grant the [Log on as a service](/windows/security/threat-protection/security-policy-settings/log-on-as-a-service) privilege to the user account:</span></span>

1. <span data-ttu-id="023e7-194">Localize as políticas de **Atribuição de Direitos de Usuário** no console Política de Segurança Local ou no console Editor de Política de Grupo Local.</span><span class="sxs-lookup"><span data-stu-id="023e7-194">Locate the **User Rights Assignment** policies in either the Local Security Policy console or Local Group Policy Editor console.</span></span> <span data-ttu-id="023e7-195">Para obter instruções, consulte: [Definir configurações de política de segurança](/windows/security/threat-protection/security-policy-settings/how-to-configure-security-policy-settings).</span><span class="sxs-lookup"><span data-stu-id="023e7-195">For instructions, see: [Configure security policy settings](/windows/security/threat-protection/security-policy-settings/how-to-configure-security-policy-settings).</span></span>
1. <span data-ttu-id="023e7-196">Localize a política `Log on as a service`.</span><span class="sxs-lookup"><span data-stu-id="023e7-196">Locate the `Log on as a service` policy.</span></span> <span data-ttu-id="023e7-197">Clique duas vezes na política para abri-la.</span><span class="sxs-lookup"><span data-stu-id="023e7-197">Double-click the policy to open it.</span></span>
1. <span data-ttu-id="023e7-198">Selecione **Adicionar Usuário ou Grupo**.</span><span class="sxs-lookup"><span data-stu-id="023e7-198">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="023e7-199">Selecione **Avançado** e selecione **Localizar Agora**.</span><span class="sxs-lookup"><span data-stu-id="023e7-199">Select **Advanced** and select **Find Now**.</span></span>
1. <span data-ttu-id="023e7-200">Selecione a conta de usuário criada antes na seção [Criar uma conta de usuário](#create-a-user-account).</span><span class="sxs-lookup"><span data-stu-id="023e7-200">Select the user account created in the [Create a user account](#create-a-user-account) section earlier.</span></span> <span data-ttu-id="023e7-201">Selecione **OK** para aceitar a seleção.</span><span class="sxs-lookup"><span data-stu-id="023e7-201">Select **OK** to accept the selection.</span></span>
1. <span data-ttu-id="023e7-202">Selecione **OK** depois de confirmar se o nome do objeto está correto.</span><span class="sxs-lookup"><span data-stu-id="023e7-202">Select **OK** after confirming that the object name is correct.</span></span>
1. <span data-ttu-id="023e7-203">Selecione **Aplicar**.</span><span class="sxs-lookup"><span data-stu-id="023e7-203">Select **Apply**.</span></span> <span data-ttu-id="023e7-204">Selecione **OK** para fechar a janela da política.</span><span class="sxs-lookup"><span data-stu-id="023e7-204">Select **OK** to close the policy window.</span></span>

## <a name="manage-the-service"></a><span data-ttu-id="023e7-205">Gerenciar o serviço</span><span class="sxs-lookup"><span data-stu-id="023e7-205">Manage the service</span></span>

### <a name="create-the-service"></a><span data-ttu-id="023e7-206">Criar o serviço</span><span class="sxs-lookup"><span data-stu-id="023e7-206">Create the service</span></span>

<span data-ttu-id="023e7-207">Use a ferramenta de linha de comando [sc.exe](https://technet.microsoft.com/library/bb490995) para criar o serviço de um shell de comando administrativo.</span><span class="sxs-lookup"><span data-stu-id="023e7-207">Use the [sc.exe](https://technet.microsoft.com/library/bb490995) command-line tool to create the service from an administrative command shell.</span></span> <span data-ttu-id="023e7-208">O valor `binPath` é o caminho para o executável do aplicativo, que inclui o nome do arquivo executável.</span><span class="sxs-lookup"><span data-stu-id="023e7-208">The `binPath` value is the path to the app's executable, which includes the executable file name.</span></span> <span data-ttu-id="023e7-209">**É necessário o espaço entre o sinal de igual e o caractere de aspas de cada parâmetro e valor.**</span><span class="sxs-lookup"><span data-stu-id="023e7-209">**The space between the equal sign and the quote character of each parameter and value is required.**</span></span>

```console
sc create {SERVICE NAME} binPath= "{PATH}" obj= "{DOMAIN}\{USER ACCOUNT}" password= "{PASSWORD}"
```

* <span data-ttu-id="023e7-210">`{SERVICE NAME}` &ndash; O nome a ser atribuído ao serviço no [Gerenciador de Controle de Serviço](/windows/desktop/services/service-control-manager).</span><span class="sxs-lookup"><span data-stu-id="023e7-210">`{SERVICE NAME}` &ndash; The name to assign to the service in [Service Control Manager](/windows/desktop/services/service-control-manager).</span></span>
* <span data-ttu-id="023e7-211">`{PATH}` &ndash; O caminho para o executável do serviço.</span><span class="sxs-lookup"><span data-stu-id="023e7-211">`{PATH}` &ndash; The path to the service executable.</span></span>
* <span data-ttu-id="023e7-212">`{DOMAIN}` &ndash; O domínio de um computador ingressado no domínio.</span><span class="sxs-lookup"><span data-stu-id="023e7-212">`{DOMAIN}` &ndash; The domain of a domain-joined machine.</span></span> <span data-ttu-id="023e7-213">Se o computador não estiver em um domínio, use o nome do computador local.</span><span class="sxs-lookup"><span data-stu-id="023e7-213">If the machine isn't domain-joined, use the local machine name.</span></span>
* <span data-ttu-id="023e7-214">`{USER ACCOUNT}` &ndash; A conta do usuário na qual o serviço é executado.</span><span class="sxs-lookup"><span data-stu-id="023e7-214">`{USER ACCOUNT}` &ndash; The user account under which the service runs.</span></span>
* <span data-ttu-id="023e7-215">`{PASSWORD}` &ndash; A senhas da conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="023e7-215">`{PASSWORD}` &ndash; The user account password.</span></span>

> [!WARNING]
> <span data-ttu-id="023e7-216">**Não** omita o parâmetro `obj`.</span><span class="sxs-lookup"><span data-stu-id="023e7-216">Do **not** omit the `obj` parameter.</span></span> <span data-ttu-id="023e7-217">O valor padrão para `obj` é a [conta LocalSystem](/windows/desktop/services/localsystem-account).</span><span class="sxs-lookup"><span data-stu-id="023e7-217">The default value for `obj` is the [LocalSystem account](/windows/desktop/services/localsystem-account) account.</span></span> <span data-ttu-id="023e7-218">Executar um serviço na conta `LocalSystem` apresenta um risco de segurança significativo.</span><span class="sxs-lookup"><span data-stu-id="023e7-218">Running a service under the `LocalSystem` account presents a significant security risk.</span></span> <span data-ttu-id="023e7-219">Sempre execute um serviço com uma conta de usuário com privilégios restritos.</span><span class="sxs-lookup"><span data-stu-id="023e7-219">Always run a service with a user account that has restricted privileges.</span></span>

<span data-ttu-id="023e7-220">Observe o seguinte aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="023e7-220">In the following example for the sample app:</span></span>

* <span data-ttu-id="023e7-221">O nome do serviço é **MyService**.</span><span class="sxs-lookup"><span data-stu-id="023e7-221">The service is named **MyService**.</span></span>
* <span data-ttu-id="023e7-222">O serviço publicado reside na pasta *c:\\svc*.</span><span class="sxs-lookup"><span data-stu-id="023e7-222">The published service resides in the *c:\\svc* folder.</span></span> <span data-ttu-id="023e7-223">O executável do aplicativo é chamado de *SampleApp.exe*.</span><span class="sxs-lookup"><span data-stu-id="023e7-223">The app executable is named *SampleApp.exe*.</span></span> <span data-ttu-id="023e7-224">Coloque o valor `binPath` entre aspas duplas (").</span><span class="sxs-lookup"><span data-stu-id="023e7-224">Enclose the `binPath` value in double quotation marks (").</span></span>
* <span data-ttu-id="023e7-225">O serviço é executado na conta `ServiceUser`.</span><span class="sxs-lookup"><span data-stu-id="023e7-225">The service runs under the `ServiceUser` account.</span></span> <span data-ttu-id="023e7-226">Substitua `{DOMAIN}` com o domínio ou nome do computador local da conta de usuário.</span><span class="sxs-lookup"><span data-stu-id="023e7-226">Replace `{DOMAIN}` with the user account's domain or local machine name.</span></span> <span data-ttu-id="023e7-227">Coloque o valor `obj` entre aspas duplas (").</span><span class="sxs-lookup"><span data-stu-id="023e7-227">Enclose the `obj` value in double quotation marks (").</span></span> <span data-ttu-id="023e7-228">Exemplo: se o sistema de hospedagem é um computador local denominado `MairaPC`, defina `obj` como `"MairaPC\ServiceUser"`.</span><span class="sxs-lookup"><span data-stu-id="023e7-228">Example: If the hosting system is a local machine named `MairaPC`, set `obj` to `"MairaPC\ServiceUser"`.</span></span>
* <span data-ttu-id="023e7-229">Substitua `{PASSWORD}` com a senha da conta do usuário.</span><span class="sxs-lookup"><span data-stu-id="023e7-229">Replace `{PASSWORD}` with the user account's password.</span></span> <span data-ttu-id="023e7-230">Coloque o valor `password` entre aspas duplas (").</span><span class="sxs-lookup"><span data-stu-id="023e7-230">Enclose the `password` value in double quotation marks (").</span></span>

```console
sc create MyService binPath= "c:\svc\sampleapp.exe" obj= "{DOMAIN}\ServiceUser" password= "{PASSWORD}"
```

> [!IMPORTANT]
> <span data-ttu-id="023e7-231">Certifique-se de que os espaços entre os sinais de igual e os valores dos parâmetros estão presentes.</span><span class="sxs-lookup"><span data-stu-id="023e7-231">Make sure that the spaces between the parameters' equal signs and the parameters' values are present.</span></span>

### <a name="start-the-service"></a><span data-ttu-id="023e7-232">Iniciar o serviço</span><span class="sxs-lookup"><span data-stu-id="023e7-232">Start the service</span></span>

<span data-ttu-id="023e7-233">Inicie o serviço com o comando `sc start {SERVICE NAME}`.</span><span class="sxs-lookup"><span data-stu-id="023e7-233">Start the service with the `sc start {SERVICE NAME}` command.</span></span>

<span data-ttu-id="023e7-234">Para iniciar o serviço de aplicativo de exemplo, use o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="023e7-234">To start the sample app service, use the following command:</span></span>

```console
sc start MyService
```

<span data-ttu-id="023e7-235">O comando leva alguns segundos para iniciar o serviço.</span><span class="sxs-lookup"><span data-stu-id="023e7-235">The command takes a few seconds to start the service.</span></span>

### <a name="determine-the-service-status"></a><span data-ttu-id="023e7-236">Determinar o status do serviço</span><span class="sxs-lookup"><span data-stu-id="023e7-236">Determine the service status</span></span>

<span data-ttu-id="023e7-237">Para verificar o status do serviço, use o comando `sc query {SERVICE NAME}`.</span><span class="sxs-lookup"><span data-stu-id="023e7-237">To check the status of the service, use the `sc query {SERVICE NAME}` command.</span></span> <span data-ttu-id="023e7-238">O status é relatado como um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="023e7-238">The status is reported as one of the following values:</span></span>

* `START_PENDING`
* `RUNNING`
* `STOP_PENDING`
* `STOPPED`

<span data-ttu-id="023e7-239">Use o seguinte comando para verificar o status do serviço de aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="023e7-239">Use the following command to check the status of the sample app service:</span></span>

```console
sc query MyService
```

### <a name="browse-a-web-app-service"></a><span data-ttu-id="023e7-240">Procurar um serviço de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="023e7-240">Browse a web app service</span></span>

<span data-ttu-id="023e7-241">Quando o serviço estiver no estado `RUNNING` e se o serviço for um aplicativo Web, procure o aplicativo em seu caminho (por padrão, `http://localhost:5000`, que redireciona para `https://localhost:5001` ao usar [Middleware de Redirecionamento HTTPS](xref:security/enforcing-ssl)).</span><span class="sxs-lookup"><span data-stu-id="023e7-241">When the service is in the `RUNNING` state and if the service is a web app, browse the app at its path (by default, `http://localhost:5000`, which redirects to `https://localhost:5001` when using [HTTPS Redirection Middleware](xref:security/enforcing-ssl)).</span></span>

<span data-ttu-id="023e7-242">Para o serviço de aplicativo de exemplo, procure o aplicativo em `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="023e7-242">For the sample app service, browse the app at `http://localhost:5000`.</span></span>

### <a name="stop-the-service"></a><span data-ttu-id="023e7-243">Parar o serviço</span><span class="sxs-lookup"><span data-stu-id="023e7-243">Stop the service</span></span>

<span data-ttu-id="023e7-244">Interrompa o serviço com o comando `sc stop {SERVICE NAME}`.</span><span class="sxs-lookup"><span data-stu-id="023e7-244">Stop the service with the `sc stop {SERVICE NAME}` command.</span></span>

<span data-ttu-id="023e7-245">O comando a seguir interrompe o serviço de aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="023e7-245">The following command stops the sample app service:</span></span>

```console
sc stop MyService
```

### <a name="delete-the-service"></a><span data-ttu-id="023e7-246">Excluir o serviço</span><span class="sxs-lookup"><span data-stu-id="023e7-246">Delete the service</span></span>

<span data-ttu-id="023e7-247">Após um pequeno atraso para interromper um serviço, desinstale o serviço com o comando `sc delete {SERVICE NAME}`.</span><span class="sxs-lookup"><span data-stu-id="023e7-247">After a short delay to stop a service, uninstall the service with the `sc delete {SERVICE NAME}` command.</span></span>

<span data-ttu-id="023e7-248">Verifique o status do serviço de aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="023e7-248">Check the status of the sample app service:</span></span>

```console
sc query MyService
```

<span data-ttu-id="023e7-249">Quando o serviço de aplicativo de exemplo estiver no estado `STOPPED`, use o seguinte comando para desinstalar o serviço de aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="023e7-249">When the sample app service is in the `STOPPED` state, use the following command to uninstall the sample app service:</span></span>

```console
sc delete MyService
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="023e7-250">Manipular eventos de início e de parada</span><span class="sxs-lookup"><span data-stu-id="023e7-250">Handle starting and stopping events</span></span>

<span data-ttu-id="023e7-251">Para manipular os eventos <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> e <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, faça as seguintes alterações adicionais:</span><span class="sxs-lookup"><span data-stu-id="023e7-251">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events, perform the following additional changes:</span></span>

1. <span data-ttu-id="023e7-252">Crie uma classe que derive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> com os métodos `OnStarting`, `OnStarted` e `OnStopping`:</span><span class="sxs-lookup"><span data-stu-id="023e7-252">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="023e7-253">Crie um método de extensão para <xref:Microsoft.AspNetCore.Hosting.IWebHost> que passe o `CustomWebHostService` para <xref:System.ServiceProcess.ServiceBase.Run*>:</span><span class="sxs-lookup"><span data-stu-id="023e7-253">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="023e7-254">Em `Program.Main`, chame o método de extensão `RunAsCustomService`, em vez de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span><span class="sxs-lookup"><span data-stu-id="023e7-254">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="023e7-255">Para ver o local do <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> no `Program.Main`, consulte o exemplo de código mostrado na seção [Converter um projeto em um Serviço Windows](#convert-a-project-into-a-windows-service).</span><span class="sxs-lookup"><span data-stu-id="023e7-255">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Convert a project into a Windows Service](#convert-a-project-into-a-windows-service) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="023e7-256">Servidor proxy e cenários de balanceador de carga</span><span class="sxs-lookup"><span data-stu-id="023e7-256">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="023e7-257">Serviços que interagem com solicitações da Internet ou de uma rede corporativa e estão atrás de um proxy ou de um balanceador de carga podem exigir configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="023e7-257">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="023e7-258">Para obter mais informações, consulte <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="023e7-258">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="023e7-259">Configurar o HTTPS</span><span class="sxs-lookup"><span data-stu-id="023e7-259">Configure HTTPS</span></span>

<span data-ttu-id="023e7-260">Para configurar o serviço com um ponto de extremidade seguro:</span><span class="sxs-lookup"><span data-stu-id="023e7-260">To configure the service with a secure endpoint:</span></span>

1. <span data-ttu-id="023e7-261">Crie um certificado X.509 para o sistema de hospedagem usando os mecanismos de implantação e aquisição do certificado da sua plataforma.</span><span class="sxs-lookup"><span data-stu-id="023e7-261">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>

1. <span data-ttu-id="023e7-262">Especifique uma [Configuração do ponto de extremidade HTTPS do servidor Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) para usar o certificado.</span><span class="sxs-lookup"><span data-stu-id="023e7-262">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="023e7-263">Não há suporte para uso do certificado de desenvolvimento HTTPS do ASP.NET Core para proteger um ponto de extremidade de serviço.</span><span class="sxs-lookup"><span data-stu-id="023e7-263">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="023e7-264">Diretório atual e a raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="023e7-264">Current directory and content root</span></span>

<span data-ttu-id="023e7-265">O diretório de trabalho atual retornado ao chamar <xref:System.IO.Directory.GetCurrentDirectory*> de um serviço Windows é a pasta *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="023e7-265">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="023e7-266">A pasta *system32* não é um local adequado para armazenar os arquivos de um serviço (por exemplo, os arquivos de configurações).</span><span class="sxs-lookup"><span data-stu-id="023e7-266">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="023e7-267">Use uma das seguintes abordagens para manter e acessar ativos e os arquivos de configuração de um serviço.</span><span class="sxs-lookup"><span data-stu-id="023e7-267">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="023e7-268">Defina o caminho da raiz do conteúdo para a pasta do aplicativo</span><span class="sxs-lookup"><span data-stu-id="023e7-268">Set the content root path to the app's folder</span></span>

<span data-ttu-id="023e7-269">O <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> é o mesmo caminho fornecido para o argumento `binPath` quando o serviço é criado.</span><span class="sxs-lookup"><span data-stu-id="023e7-269">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when the service is created.</span></span> <span data-ttu-id="023e7-270">Em vez de chamar `GetCurrentDirectory` para criar caminhos para arquivos de configuração, chame <xref:System.IO.Directory.SetCurrentDirectory*> com o caminho para a raiz do conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="023e7-270">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's content root.</span></span>

<span data-ttu-id="023e7-271">No `Program.Main`, determine o caminho para a pasta do executável do serviço e use o caminho para estabelecer a raiz do conteúdo do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="023e7-271">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-the-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="023e7-272">Armazene os arquivos do serviço em um local adequado no disco</span><span class="sxs-lookup"><span data-stu-id="023e7-272">Store the service's files in a suitable location on disk</span></span>

<span data-ttu-id="023e7-273">Especifique um caminho absoluto com <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> quando usar um <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> para a pasta que contém os arquivos.</span><span class="sxs-lookup"><span data-stu-id="023e7-273">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="023e7-274">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="023e7-274">Additional resources</span></span>

* <span data-ttu-id="023e7-275">[Configuração do ponto de extremidade Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclui a configuração HTTPS e suporte à SNI)</span><span class="sxs-lookup"><span data-stu-id="023e7-275">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>
