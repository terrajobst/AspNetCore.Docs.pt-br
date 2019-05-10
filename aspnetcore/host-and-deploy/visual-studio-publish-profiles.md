---
title: Perfis de publicação do Visual Studio para a implantação do aplicativo ASP.NET Core
author: rick-anderson
description: Saiba como criar perfis de publicação no Visual Studio e usá-los para gerenciar implantações de aplicativo ASP.NET Core para vários destinos.
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: e1e8f99be18d6f395a146bda805f71c46cd0346d
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64889451"
---
# <a name="visual-studio-publish-profiles-for-aspnet-core-app-deployment"></a><span data-ttu-id="e78c0-103">Perfis de publicação do Visual Studio para a implantação do aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e78c0-103">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>

<span data-ttu-id="e78c0-104">Por [Sayed Hashimi de Ibrahim](https://github.com/sayedihashimi) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e78c0-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="e78c0-105">Para obter a versão 1.1 deste tópico, baixe [Perfis de publicação do Visual Studio para implantação de aplicativo do ASP.NET Core (versão 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/VS_Publish_Profiles_1.1.pdf).</span><span class="sxs-lookup"><span data-stu-id="e78c0-105">For the 1.1 version of this topic, download [Visual Studio publish profiles for ASP.NET Core app deployment (version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/VS_Publish_Profiles_1.1.pdf).</span></span>

::: moniker-end

<span data-ttu-id="e78c0-106">Este documento se concentra no uso do Visual Studio 2017 (ou posterior) para criar e usar perfis de publicação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-106">This document focuses on using Visual Studio 2017 or later to create and use publish profiles.</span></span> <span data-ttu-id="e78c0-107">Os perfis de publicação criados com o Visual Studio podem ser executados do MSBuild e do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e78c0-107">The publish profiles created with Visual Studio can be run from MSBuild and Visual Studio.</span></span> <span data-ttu-id="e78c0-108">Consulte [Publicar um aplicativo Web ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) para obter instruções sobre a publicação no Azure.</span><span class="sxs-lookup"><span data-stu-id="e78c0-108">See [Publish an ASP.NET Core web app to Azure App Service using Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) for instructions on publishing to Azure.</span></span>

<span data-ttu-id="e78c0-109">O arquivo de projeto a seguir foi criado com o comando `dotnet new mvc`:</span><span class="sxs-lookup"><span data-stu-id="e78c0-109">The following project file was created with the command `dotnet new mvc`:</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

::: moniker-end

<span data-ttu-id="e78c0-110">O atributo `<Project>` do elemento `Sdk` realiza as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="e78c0-110">The `<Project>` element's `Sdk` attribute accomplishes the following tasks:</span></span>

* <span data-ttu-id="e78c0-111">Importa o arquivo de propriedades de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props* no início.</span><span class="sxs-lookup"><span data-stu-id="e78c0-111">Imports the properties file from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.Props* at the beginning.</span></span>
* <span data-ttu-id="e78c0-112">Importa o arquivo de destino de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* no fim.</span><span class="sxs-lookup"><span data-stu-id="e78c0-112">Imports the targets file from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets* at the end.</span></span>

<span data-ttu-id="e78c0-113">O local padrão para `MSBuildSDKsPath` (com o Visual Studio Enterprise 2017) é a pasta *%programfiles(x86)%\Microsoft Visual Studio\2017\Enterprise\MSBuild\Sdks*.</span><span class="sxs-lookup"><span data-stu-id="e78c0-113">The default location for `MSBuildSDKsPath` (with Visual Studio 2017 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2017\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="e78c0-114">O SDK de `Microsoft.NET.Sdk.Web` depende de:</span><span class="sxs-lookup"><span data-stu-id="e78c0-114">The `Microsoft.NET.Sdk.Web` SDK depends on:</span></span>

* <span data-ttu-id="e78c0-115">*Microsoft.NET.Sdk.Web.ProjectSystem*</span><span class="sxs-lookup"><span data-stu-id="e78c0-115">*Microsoft.NET.Sdk.Web.ProjectSystem*</span></span>
* <span data-ttu-id="e78c0-116">*Microsoft.NET.Sdk.Publish*</span><span class="sxs-lookup"><span data-stu-id="e78c0-116">*Microsoft.NET.Sdk.Publish*</span></span>

<span data-ttu-id="e78c0-117">O que faz com que as propriedades e os destinos a seguir a sejam importados:</span><span class="sxs-lookup"><span data-stu-id="e78c0-117">Which causes the following properties and targets to be imported:</span></span>

* <span data-ttu-id="e78c0-118">*$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props*</span><span class="sxs-lookup"><span data-stu-id="e78c0-118">*$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.Props*</span></span>
* <span data-ttu-id="e78c0-119">*$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets*</span><span class="sxs-lookup"><span data-stu-id="e78c0-119">*$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web.ProjectSystem\Sdk\Sdk.targets*</span></span>
* <span data-ttu-id="e78c0-120">*$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props*</span><span class="sxs-lookup"><span data-stu-id="e78c0-120">*$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.Props*</span></span>
* <span data-ttu-id="e78c0-121">*$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets*</span><span class="sxs-lookup"><span data-stu-id="e78c0-121">*$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Publish\Sdk\Sdk.targets*</span></span>

<span data-ttu-id="e78c0-122">Destinos de publicação importam o conjunto certo de destinos com base no método de publicação usado.</span><span class="sxs-lookup"><span data-stu-id="e78c0-122">Publish targets import the right set of targets based on the publish method used.</span></span>

<span data-ttu-id="e78c0-123">Quando o MSBuild ou o Visual Studio carrega um projeto, as seguintes ações de nível alto ocorrem:</span><span class="sxs-lookup"><span data-stu-id="e78c0-123">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="e78c0-124">Compilar projeto</span><span class="sxs-lookup"><span data-stu-id="e78c0-124">Build project</span></span>
* <span data-ttu-id="e78c0-125">Computar arquivos a publicar</span><span class="sxs-lookup"><span data-stu-id="e78c0-125">Compute files to publish</span></span>
* <span data-ttu-id="e78c0-126">Publicar arquivos para o destino</span><span class="sxs-lookup"><span data-stu-id="e78c0-126">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="e78c0-127">Itens de projeto de computação</span><span class="sxs-lookup"><span data-stu-id="e78c0-127">Compute project items</span></span>

<span data-ttu-id="e78c0-128">Quando o projeto é carregado, os itens de projeto (arquivos) são computados.</span><span class="sxs-lookup"><span data-stu-id="e78c0-128">When the project is loaded, the project items (files) are computed.</span></span> <span data-ttu-id="e78c0-129">O atributo `item type` determina como o arquivo é processado.</span><span class="sxs-lookup"><span data-stu-id="e78c0-129">The `item type` attribute determines how the file is processed.</span></span> <span data-ttu-id="e78c0-130">Por padrão, os arquivos *.cs* são incluídos na lista de itens `Compile`.</span><span class="sxs-lookup"><span data-stu-id="e78c0-130">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="e78c0-131">Os arquivos na lista de itens `Compile` são compilados.</span><span class="sxs-lookup"><span data-stu-id="e78c0-131">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="e78c0-132">A lista de itens `Content` contém arquivos que são publicados juntamente com as saídas de build.</span><span class="sxs-lookup"><span data-stu-id="e78c0-132">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="e78c0-133">Por padrão, os arquivos correspondendo ao padrão `wwwroot/**` são incluídos no item `Content`.</span><span class="sxs-lookup"><span data-stu-id="e78c0-133">By default, files matching the pattern `wwwroot/**` are included in the `Content` item.</span></span> <span data-ttu-id="e78c0-134">O [padrão glob](https://gruntjs.com/configuring-tasks#globbing-patterns) `wwwroot/\*\*` corresponde a todos os arquivos na pasta *wwwroot* **e** subpastas.</span><span class="sxs-lookup"><span data-stu-id="e78c0-134">The `wwwroot/\*\*` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder **and** subfolders.</span></span> <span data-ttu-id="e78c0-135">Para adicionar explicitamente um arquivo à lista de publicação, adicione o arquivo diretamente no arquivo *.csproj* conforme mostrado em [Arquivos de Inclusão](#include-files).</span><span class="sxs-lookup"><span data-stu-id="e78c0-135">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in [Include Files](#include-files).</span></span>

<span data-ttu-id="e78c0-136">Ao selecionar o botão **Publicar** no Visual Studio ou ao publicar da linha de comando:</span><span class="sxs-lookup"><span data-stu-id="e78c0-136">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="e78c0-137">Os itens/propriedades são calculados (os arquivos necessários para compilar).</span><span class="sxs-lookup"><span data-stu-id="e78c0-137">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="e78c0-138">**Somente Visual Studio**: os pacotes do NuGet são restaurados.</span><span class="sxs-lookup"><span data-stu-id="e78c0-138">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="e78c0-139">(A restauração precisa ser explícita pelo usuário na CLI.)</span><span class="sxs-lookup"><span data-stu-id="e78c0-139">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="e78c0-140">O projeto é compilado.</span><span class="sxs-lookup"><span data-stu-id="e78c0-140">The project builds.</span></span>
* <span data-ttu-id="e78c0-141">Os itens de publicação são computados (os arquivos necessários para a publicação).</span><span class="sxs-lookup"><span data-stu-id="e78c0-141">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="e78c0-142">O projeto é publicado (os arquivos computados são copiados para o destino de publicação).</span><span class="sxs-lookup"><span data-stu-id="e78c0-142">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="e78c0-143">Quando um projeto do ASP.NET Core faz referência a `Microsoft.NET.Sdk.Web` no arquivo de projeto, um arquivo *app_offline.htm* é colocado na raiz do diretório do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="e78c0-143">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="e78c0-144">Quando o arquivo estiver presente, o módulo do ASP.NET Core apenas desligará o aplicativo e servirá o arquivo *app_offline.htm* durante a implantação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-144">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="e78c0-145">Para obter mais informações, consulte [Referência de configuração do módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="e78c0-145">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="e78c0-146">Publicação de linha de comando básica</span><span class="sxs-lookup"><span data-stu-id="e78c0-146">Basic command-line publishing</span></span>

<span data-ttu-id="e78c0-147">A publicação de linha de comando funciona em todas as plataformas compatíveis com o .NET Core e não requer o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e78c0-147">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="e78c0-148">Nas amostras abaixo, o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) é executado no diretório do projeto (que contém o arquivo *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="e78c0-148">In the samples below, the [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="e78c0-149">Se você não estiver na pasta do projeto, passe explicitamente no caminho do arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="e78c0-149">If not in the project folder, explicitly pass in the project file path.</span></span> <span data-ttu-id="e78c0-150">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e78c0-150">For example:</span></span>

```console
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="e78c0-151">Execute os comandos a seguir para criar e publicar um aplicativo Web:</span><span class="sxs-lookup"><span data-stu-id="e78c0-151">Run the following commands to create and publish a web app:</span></span>

```console
dotnet new mvc
dotnet publish
```

<span data-ttu-id="e78c0-152">O comando [dotnet publish](/dotnet/core/tools/dotnet-publish) gera uma saída semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="e78c0-152">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command produces output similar to the following:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version 15.3.409.57025 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp2.0\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp2.0\publish\
```

<span data-ttu-id="e78c0-153">A pasta de publicação padrão é `bin\$(Configuration)\netcoreapp<version>\publish`.</span><span class="sxs-lookup"><span data-stu-id="e78c0-153">The default publish folder is `bin\$(Configuration)\netcoreapp<version>\publish`.</span></span> <span data-ttu-id="e78c0-154">O padrão para `$(Configuration)` é *Debug*.</span><span class="sxs-lookup"><span data-stu-id="e78c0-154">The default for `$(Configuration)` is *Debug*.</span></span> <span data-ttu-id="e78c0-155">Na amostra anterior, o `<TargetFramework>` é `netcoreapp2.0`.</span><span class="sxs-lookup"><span data-stu-id="e78c0-155">In the preceding sample, the `<TargetFramework>` is `netcoreapp2.0`.</span></span>

<span data-ttu-id="e78c0-156">`dotnet publish -h` exibe informações de ajuda para publicação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-156">`dotnet publish -h` displays help information for publish.</span></span>

<span data-ttu-id="e78c0-157">O comando a seguir especifica um build de `Release` e o diretório de publicação:</span><span class="sxs-lookup"><span data-stu-id="e78c0-157">The following command specifies a `Release` build and the publishing directory:</span></span>

```console
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="e78c0-158">O comando [dotnet publish](/dotnet/core/tools/dotnet-publish) chama MSBuild, que invoca o destino `Publish`.</span><span class="sxs-lookup"><span data-stu-id="e78c0-158">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="e78c0-159">Quaisquer parâmetros passados para `dotnet publish` são passados para o MSBuild.</span><span class="sxs-lookup"><span data-stu-id="e78c0-159">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="e78c0-160">O parâmetro `-c` é mapeado para a propriedade do MSBuild `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="e78c0-160">The `-c` parameter maps to the `Configuration` MSBuild property.</span></span> <span data-ttu-id="e78c0-161">O parâmetro `-o` é mapeado para `OutputPath`.</span><span class="sxs-lookup"><span data-stu-id="e78c0-161">The `-o` parameter maps to `OutputPath`.</span></span>

<span data-ttu-id="e78c0-162">Propriedades do MSBuild podem ser passadas usando qualquer um dos seguintes formatos:</span><span class="sxs-lookup"><span data-stu-id="e78c0-162">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="e78c0-163">O comando a seguir publica um build de `Release` para um compartilhamento de rede:</span><span class="sxs-lookup"><span data-stu-id="e78c0-163">The following command publishes a `Release` build to a network share:</span></span>

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

<span data-ttu-id="e78c0-164">O compartilhamento de rede é especificado com barras "/" (*//r8/*) e funciona em todas as plataformas com suporte do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e78c0-164">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

<span data-ttu-id="e78c0-165">Confirme que o aplicativo publicado para implantação não está em execução.</span><span class="sxs-lookup"><span data-stu-id="e78c0-165">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="e78c0-166">Os arquivos da pasta *publish* são bloqueados quando o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="e78c0-166">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="e78c0-167">A implantação não pode ocorrer porque arquivos bloqueados não podem ser copiados.</span><span class="sxs-lookup"><span data-stu-id="e78c0-167">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="e78c0-168">Perfis de publicação</span><span class="sxs-lookup"><span data-stu-id="e78c0-168">Publish profiles</span></span>

<span data-ttu-id="e78c0-169">Esta seção usa o Visual Studio 2017 (ou posterior) para criar um perfil de publicação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-169">This section uses Visual Studio 2017 or later to create a publishing profile.</span></span> <span data-ttu-id="e78c0-170">Uma vez criado, é possível publicar do Visual Studio ou da linha de comando.</span><span class="sxs-lookup"><span data-stu-id="e78c0-170">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span>

<span data-ttu-id="e78c0-171">Perfis de publicação podem simplificar o processo de publicação e podem existir em qualquer número.</span><span class="sxs-lookup"><span data-stu-id="e78c0-171">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span> <span data-ttu-id="e78c0-172">Crie um perfil de publicação no Visual Studio escolhendo um dos seguintes caminhos:</span><span class="sxs-lookup"><span data-stu-id="e78c0-172">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="e78c0-173">Clique com o botão direito do mouse no projeto no Gerenciador de Soluções e selecione **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="e78c0-173">Right-click the project in Solution Explorer and select **Publish**.</span></span>
* <span data-ttu-id="e78c0-174">Selecione **Publicar {NOME DO PROJETO}** no menu **Build**.</span><span class="sxs-lookup"><span data-stu-id="e78c0-174">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="e78c0-175">A guia **Publicar** da página de capacidades do aplicativo é exibida.</span><span class="sxs-lookup"><span data-stu-id="e78c0-175">The **Publish** tab of the app capacities page is displayed.</span></span> <span data-ttu-id="e78c0-176">Se o projeto não tem um perfil de publicação, a página a seguir é exibida:</span><span class="sxs-lookup"><span data-stu-id="e78c0-176">If the project lacks a publish profile, the following page is displayed:</span></span>

![A guia Publicar da página de capacidades do aplicativo](visual-studio-publish-profiles/_static/az.png)

<span data-ttu-id="e78c0-178">Quando a opção **Pasta** é selecionada, especifique um caminho de pasta para armazenar os ativos publicados.</span><span class="sxs-lookup"><span data-stu-id="e78c0-178">When **Folder** is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="e78c0-179">A pasta padrão é *bin\Release\PublishOutput*.</span><span class="sxs-lookup"><span data-stu-id="e78c0-179">The default folder is *bin\Release\PublishOutput*.</span></span> <span data-ttu-id="e78c0-180">Clique no botão **Criar Perfil** para concluir.</span><span class="sxs-lookup"><span data-stu-id="e78c0-180">Click the **Create Profile** button to finish.</span></span>

<span data-ttu-id="e78c0-181">Depois de um perfil de publicação ser criado, a guia **Publicar** é alterada.</span><span class="sxs-lookup"><span data-stu-id="e78c0-181">Once a publish profile is created, the **Publish** tab changes.</span></span> <span data-ttu-id="e78c0-182">O perfil recém-criado é exibido em uma lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="e78c0-182">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="e78c0-183">Clique em **Criar novo perfil** para fazer isso.</span><span class="sxs-lookup"><span data-stu-id="e78c0-183">Click **Create new profile** to create another new profile.</span></span>

![A guia Publicar da página de capacidades do aplicativo mostrando FolderProfile](visual-studio-publish-profiles/_static/create_new.png)

<span data-ttu-id="e78c0-185">O Assistente de publicação dá suporte aos destinos de publicação a seguir:</span><span class="sxs-lookup"><span data-stu-id="e78c0-185">The Publish wizard supports the following publish targets:</span></span>

* <span data-ttu-id="e78c0-186">Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="e78c0-186">Azure App Service</span></span>
* <span data-ttu-id="e78c0-187">Máquinas Virtuais do Azure</span><span class="sxs-lookup"><span data-stu-id="e78c0-187">Azure Virtual Machines</span></span>
* <span data-ttu-id="e78c0-188">IIS, FTP, etc. (para qualquer servidor Web)</span><span class="sxs-lookup"><span data-stu-id="e78c0-188">IIS, FTP, etc. (for any web server)</span></span>
* <span data-ttu-id="e78c0-189">Pasta</span><span class="sxs-lookup"><span data-stu-id="e78c0-189">Folder</span></span>
* <span data-ttu-id="e78c0-190">Importar Perfil</span><span class="sxs-lookup"><span data-stu-id="e78c0-190">Import Profile</span></span>

<span data-ttu-id="e78c0-191">Para obter mais informações, confira [Quais opções de publicação são adequadas para mim](/visualstudio/ide/not-in-toc/web-publish-options).</span><span class="sxs-lookup"><span data-stu-id="e78c0-191">For more information, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="e78c0-192">Quando você cria um perfil de publicação com o Visual Studio, um arquivo do MSBuild *Properties/PublishProfiles/{NOME DO PERFIL}.pubxml* é criado.</span><span class="sxs-lookup"><span data-stu-id="e78c0-192">When creating a publish profile with Visual Studio, a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file is created.</span></span> <span data-ttu-id="e78c0-193">O *.pubxml* é um arquivo do MSBuild e contém definições de configuração de publicação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-193">The *.pubxml* file is a MSBuild file and contains publish configuration settings.</span></span> <span data-ttu-id="e78c0-194">Esse arquivo pode ser alterado para personalizar o processo de build e de publicação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-194">This file can be changed to customize the build and publish process.</span></span> <span data-ttu-id="e78c0-195">Esse arquivo é lido pelo processo de publicação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-195">This file is read by the publishing process.</span></span> <span data-ttu-id="e78c0-196">`<LastUsedBuildConfiguration>` é especial porque é uma propriedade global e não deverá estar em nenhum arquivo importado no build.</span><span class="sxs-lookup"><span data-stu-id="e78c0-196">`<LastUsedBuildConfiguration>` is special because it's a global property and shouldn't be in any file that's imported in the build.</span></span> <span data-ttu-id="e78c0-197">Para obter mais informações, confira [MSBuild: como definir a propriedade de configuração](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span><span class="sxs-lookup"><span data-stu-id="e78c0-197">See [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) for more information.</span></span>

<span data-ttu-id="e78c0-198">Ao publicar em um destino do Azure, o arquivo *.pubxml* contém o identificador de assinatura do Azure.</span><span class="sxs-lookup"><span data-stu-id="e78c0-198">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="e78c0-199">Com esse tipo de destino, não é recomendável adicionar esse arquivo ao controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="e78c0-199">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="e78c0-200">Ao publicar em um destino não Azure, é seguro fazer check-in do arquivo *.pubxml*.</span><span class="sxs-lookup"><span data-stu-id="e78c0-200">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="e78c0-201">Informações confidenciais (como a senha de publicação) são criptografadas em um nível por usuário/computador.</span><span class="sxs-lookup"><span data-stu-id="e78c0-201">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="e78c0-202">Elas são armazenadas no arquivo *Properties/PublishProfiles/{NOME DO PERFIL}.pubxml.user*.</span><span class="sxs-lookup"><span data-stu-id="e78c0-202">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="e78c0-203">Já que esse arquivo pode armazenar informações confidenciais, o check-in dele não deve ser realizado no controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="e78c0-203">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="e78c0-204">Para obter uma visão geral de como publicar um aplicativo Web do ASP.NET Core, veja [Hospedar e implantar](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="e78c0-204">For an overview of how to publish a web app on ASP.NET Core, see [Host and deploy](xref:host-and-deploy/index).</span></span> <span data-ttu-id="e78c0-205">As tarefas e os destinos de MSBuild necessários para publicar um aplicativo ASP.NET Core são de software livre no https://github.com/aspnet/websdk.</span><span class="sxs-lookup"><span data-stu-id="e78c0-205">The MSBuild tasks and targets necessary to publish an ASP.NET Core app are open-source at https://github.com/aspnet/websdk.</span></span>

<span data-ttu-id="e78c0-206">O `dotnet publish` pode usar os perfis de publicação de pasta, MSDeploy e [Kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="e78c0-206">`dotnet publish` can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles:</span></span>

<span data-ttu-id="e78c0-207">Pasta (funciona em modo multiplataforma):</span><span class="sxs-lookup"><span data-stu-id="e78c0-207">Folder (works cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="e78c0-208">MSDeploy (atualmente só funciona no Windows, uma vez que o MSDeploy não é multiplataforma):</span><span class="sxs-lookup"><span data-stu-id="e78c0-208">MSDeploy (currently this only works in Windows since MSDeploy isn't cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="e78c0-209">Pacote MSDeploy (atualmente só funciona no Windows, uma vez que o MSDeploy não é multiplataforma):</span><span class="sxs-lookup"><span data-stu-id="e78c0-209">MSDeploy package (currently this only works in Windows since MSDeploy isn't cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="e78c0-210">Nos exemplos anteriores, **não** passe o `deployonbuild` para `dotnet publish`.</span><span class="sxs-lookup"><span data-stu-id="e78c0-210">In the preceding samples, **don't** pass `deployonbuild` to `dotnet publish`.</span></span>

<span data-ttu-id="e78c0-211">Para obter mais informações, confira [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="e78c0-211">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="e78c0-212">O `dotnet publish` dá suporte às APIs do Kudu, para publicar no Azure de qualquer plataforma.</span><span class="sxs-lookup"><span data-stu-id="e78c0-212">`dotnet publish` supports Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="e78c0-213">A publicação do Visual Studio dá suporte às APIs do Kudu, mas ela é compatível com o WebSDK para publicação multiplataforma para o Azure.</span><span class="sxs-lookup"><span data-stu-id="e78c0-213">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>

<span data-ttu-id="e78c0-214">Adicione um perfil de publicação à pasta *Properties/PublishProfiles* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="e78c0-214">Add a publish profile to the *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="e78c0-215">Execute o seguinte comando para compactar os conteúdos de publicação e publicá-los no Azure usando as APIs do Kudu:</span><span class="sxs-lookup"><span data-stu-id="e78c0-215">Run the following command to zip up the publish contents and publish it to Azure using the Kudu APIs:</span></span>

```console
dotnet publish /p:PublishProfile=Azure /p:Configuration=Release
```

<span data-ttu-id="e78c0-216">Defina as seguintes propriedades de MSBuild ao usar um perfil de publicação:</span><span class="sxs-lookup"><span data-stu-id="e78c0-216">Set the following MSBuild properties when using a publish profile:</span></span>

* `DeployOnBuild=true`
* `PublishProfile={PUBLISH PROFILE}`

<span data-ttu-id="e78c0-217">Ao publicar com um perfil chamado *FolderProfile*, é possível executar qualquer um dos comandos abaixo:</span><span class="sxs-lookup"><span data-stu-id="e78c0-217">When publishing with a profile named *FolderProfile*, either of the commands below can be executed:</span></span>

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="e78c0-218">Ao invocar [dotnet build](/dotnet/core/tools/dotnet-build), ele chama `msbuild` para executar o processo de build e de publicação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-218">When invoking [dotnet build](/dotnet/core/tools/dotnet-build), it calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="e78c0-219">Chamar `dotnet build` ou `msbuild` é equivalente ao passar um perfil de pasta.</span><span class="sxs-lookup"><span data-stu-id="e78c0-219">Calling either `dotnet build` or `msbuild` is equivalent when passing in a folder profile.</span></span> <span data-ttu-id="e78c0-220">Ao chamar o MSBuild diretamente no Windows, a versão do .NET Framework do MSBuild é usada.</span><span class="sxs-lookup"><span data-stu-id="e78c0-220">When calling MSBuild directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="e78c0-221">O MSDeploy é atualmente limitado a computadores Windows para a publicação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-221">MSDeploy is currently limited to Windows machines for publishing.</span></span> <span data-ttu-id="e78c0-222">Chamar `dotnet build` em um perfil não de pasta invoca o MSBuild que, por sua vez, usa MSDeploy em perfis não de pasta.</span><span class="sxs-lookup"><span data-stu-id="e78c0-222">Calling `dotnet build` on a non-folder profile invokes MSBuild, and MSBuild uses MSDeploy on non-folder profiles.</span></span> <span data-ttu-id="e78c0-223">Chamar `dotnet build` em um perfil não de pasta invoca o MSBuild (usando MSDeploy) e resulta em uma falha (mesmo quando em execução em uma plataforma do Windows).</span><span class="sxs-lookup"><span data-stu-id="e78c0-223">Calling `dotnet build` on a non-folder profile invokes MSBuild (using MSDeploy) and results in a failure (even when running on a Windows platform).</span></span> <span data-ttu-id="e78c0-224">Para publicar com um perfil não de pasta, chame o MSBuild diretamente.</span><span class="sxs-lookup"><span data-stu-id="e78c0-224">To publish with a non-folder profile, call MSBuild directly.</span></span>

<span data-ttu-id="e78c0-225">A pasta de perfil de publicação a seguir foi criada com o Visual Studio e publica em um compartilhamento de rede:</span><span class="sxs-lookup"><span data-stu-id="e78c0-225">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="e78c0-226">Observe que `<LastUsedBuildConfiguration>` é definido como `Release`.</span><span class="sxs-lookup"><span data-stu-id="e78c0-226">Note `<LastUsedBuildConfiguration>` is set to `Release`.</span></span> <span data-ttu-id="e78c0-227">Ao publicar no Visual Studio, o valor da propriedade de configuração `<LastUsedBuildConfiguration>` é definido usando o valor quando o processo de publicação é iniciado.</span><span class="sxs-lookup"><span data-stu-id="e78c0-227">When publishing from Visual Studio, the `<LastUsedBuildConfiguration>` configuration property value is set using the value when the publish process is started.</span></span> <span data-ttu-id="e78c0-228">A propriedade de configuração `<LastUsedBuildConfiguration>` é especial e não deve ser substituída em um arquivo do MSBuild importado.</span><span class="sxs-lookup"><span data-stu-id="e78c0-228">The `<LastUsedBuildConfiguration>` configuration property is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="e78c0-229">Essa propriedade pode ser substituída da linha de comando.</span><span class="sxs-lookup"><span data-stu-id="e78c0-229">This property can be overridden from the command line.</span></span>

<span data-ttu-id="e78c0-230">Usando a CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="e78c0-230">Using the .NET Core CLI:</span></span>

```console
dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

<span data-ttu-id="e78c0-231">Usando o MSBuild:</span><span class="sxs-lookup"><span data-stu-id="e78c0-231">Using MSBuild:</span></span>

```console
msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="e78c0-232">Publicar em um ponto de extremidade do MSDeploy da linha de comando</span><span class="sxs-lookup"><span data-stu-id="e78c0-232">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="e78c0-233">O exemplo a seguir usa um aplicativo Web do ASP.NET Core, criado pelo Visual Studio, denominada *AzureWebApp*.</span><span class="sxs-lookup"><span data-stu-id="e78c0-233">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="e78c0-234">Um perfil de publicação dos aplicativos do Azure é adicionado ao Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e78c0-234">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="e78c0-235">Para obter mais informações sobre como criar um perfil, consulte a seção [Perfis de publicação](#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="e78c0-235">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="e78c0-236">Para implantar o aplicativo usando um perfil de publicação, execute o comando `msbuild` de um **Prompt de Comando do Desenvolvedor** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e78c0-236">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="e78c0-237">O prompt de comando está disponível na pasta do *Visual Studio* do menu **Página Inicial** na barra de tarefas do Windows.</span><span class="sxs-lookup"><span data-stu-id="e78c0-237">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="e78c0-238">Para facilitar o acesso, você pode adicionar o prompt de comando ao menu **Ferramentas** no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e78c0-238">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="e78c0-239">Para saber mais, confira [Prompt de comando do desenvolvedor para Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="e78c0-239">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="e78c0-240">O MSBuild usa a sintaxe de comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="e78c0-240">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="e78c0-241">{CAMINHO} &ndash; Caminho para o arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e78c0-241">{PATH} &ndash; Path to the app's project file.</span></span>
* <span data-ttu-id="e78c0-242">{PERFIL} &ndash; Nome do perfil de publicação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-242">{PROFILE} &ndash; Name of the publish profile.</span></span>
* <span data-ttu-id="e78c0-243">{NOME DE USUÁRIO} &ndash; Nome de usuário do MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="e78c0-243">{USERNAME} &ndash; MSDeploy username.</span></span> <span data-ttu-id="e78c0-244">O {NOME DE USUÁRIO} pode ser encontrado no perfil de publicação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-244">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="e78c0-245">{SENHA} &ndash; Senha do MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="e78c0-245">{PASSWORD} &ndash; MSDeploy password.</span></span> <span data-ttu-id="e78c0-246">Obtenha a {SENHA} do arquivo *{PERFIL}.PublishSettings*.</span><span class="sxs-lookup"><span data-stu-id="e78c0-246">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="e78c0-247">Baixe o arquivo *.PublishSettings* de uma das seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="e78c0-247">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="e78c0-248">Gerenciador de Soluções: Selecione **Exibição** > **Cloud Explorer**.</span><span class="sxs-lookup"><span data-stu-id="e78c0-248">Solution Explorer: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="e78c0-249">Conecte-se à sua assinatura do Azure.</span><span class="sxs-lookup"><span data-stu-id="e78c0-249">Connect with your Azure subscription.</span></span> <span data-ttu-id="e78c0-250">Abra **Serviços de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="e78c0-250">Open **App Services**.</span></span> <span data-ttu-id="e78c0-251">Clique com o botão direito do mouse no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e78c0-251">Right-click the app.</span></span> <span data-ttu-id="e78c0-252">Selecione **Baixar Perfil de Publicação**.</span><span class="sxs-lookup"><span data-stu-id="e78c0-252">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="e78c0-253">Portal do Azure: selecione **Obter perfil de publicação** no painel **Visão geral** de seu aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="e78c0-253">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="e78c0-254">O exemplo a seguir usa um perfil de publicação denominado *AzureWebApp – Implantação da Web*:</span><span class="sxs-lookup"><span data-stu-id="e78c0-254">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="e78c0-255">Um perfil de publicação também pode ser usado com o comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) da CLI do .NET Core em um prompt de comando do Windows:</span><span class="sxs-lookup"><span data-stu-id="e78c0-255">A publish profile can also be used with the .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command prompt:</span></span>

```console
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!NOTE]
> <span data-ttu-id="e78c0-256">O comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) está disponível em várias plataformas e pode compilar aplicativos ASP.NET Core no macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="e78c0-256">The [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command is available cross-platform and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="e78c0-257">No entanto, o MSBuild no macOS e Linux não é capaz de implantar um aplicativo no Azure ou em outro ponto de extremidade do MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="e78c0-257">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoint.</span></span> <span data-ttu-id="e78c0-258">O MSDeploy está disponível apenas no Windows.</span><span class="sxs-lookup"><span data-stu-id="e78c0-258">MSDeploy is only available on Windows.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="e78c0-259">Definir o ambiente</span><span class="sxs-lookup"><span data-stu-id="e78c0-259">Set the environment</span></span>

<span data-ttu-id="e78c0-260">Inclua a propriedade `<EnvironmentName>` no perfil de publicação (*.pubxml*) ou no arquivo de projeto para definir o [ambiente](xref:fundamentals/environments) do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="e78c0-260">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="e78c0-261">Se você precisar de transformações do *web.config* (por exemplo, definir variáveis ​​de ambiente com base na configuração, no perfil ou no ambiente), confira <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="e78c0-261">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="e78c0-262">Excluir arquivos</span><span class="sxs-lookup"><span data-stu-id="e78c0-262">Exclude files</span></span>

<span data-ttu-id="e78c0-263">Ao publicar aplicativos Web do ASP.NET Core, os artefatos de build e o conteúdo da pasta *wwwroot* são incluídos.</span><span class="sxs-lookup"><span data-stu-id="e78c0-263">When publishing ASP.NET Core web apps, the build artifacts and contents of the *wwwroot* folder are included.</span></span> <span data-ttu-id="e78c0-264">`msbuild` dá suporte a [padrões de caractere curinga](https://gruntjs.com/configuring-tasks#globbing-patterns).</span><span class="sxs-lookup"><span data-stu-id="e78c0-264">`msbuild` supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="e78c0-265">Por exemplo, o elemento `<Content>` a seguir exclui todos os arquivos de texto (*.txt*) da pasta *wwwroot/content* e de todas as suas subpastas.</span><span class="sxs-lookup"><span data-stu-id="e78c0-265">For example, the following `<Content>` element excludes all text (*.txt*) files from the *wwwroot/content* folder and all its subfolders.</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="e78c0-266">A marcação anterior pode ser adicionada a um perfil de publicação ou ao arquivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="e78c0-266">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="e78c0-267">Quando adicionada ao arquivo *.csproj*, a regra será adicionada a todos os perfis de publicação no projeto.</span><span class="sxs-lookup"><span data-stu-id="e78c0-267">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="e78c0-268">O elemento `<MsDeploySkipRules>` a seguir exclui todos os arquivos da pasta *wwwroot/content*:</span><span class="sxs-lookup"><span data-stu-id="e78c0-268">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot/content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="e78c0-269">`<MsDeploySkipRules>` não exclui os destinos de *ignorados* do site de implantação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-269">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="e78c0-270">Pastas e arquivos de destino de `<Content>` são excluídos do site de implantação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-270">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="e78c0-271">Por exemplo, suponha que um aplicativo Web implantado tinha os seguintes arquivos:</span><span class="sxs-lookup"><span data-stu-id="e78c0-271">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="e78c0-272">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e78c0-272">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="e78c0-273">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e78c0-273">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="e78c0-274">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e78c0-274">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="e78c0-275">Nos elementos `<MsDeploySkipRules>` a seguir, esses arquivos não seriam excluídos no site de implantação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-275">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="e78c0-276">Os elementos `<MsDeploySkipRules>` anteriores impedem que os arquivos *ignorados* sejam implantados.</span><span class="sxs-lookup"><span data-stu-id="e78c0-276">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="e78c0-277">Ele não excluirá esses arquivos depois que eles forem implantados.</span><span class="sxs-lookup"><span data-stu-id="e78c0-277">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="e78c0-278">O elemento `<Content>` a seguir exclui os arquivos de destino no site de implantação:</span><span class="sxs-lookup"><span data-stu-id="e78c0-278">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="e78c0-279">O uso da implantação de linha de comando com o elemento `<Content>` anterior produz a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="e78c0-279">Using command-line deployment with the preceding `<Content>` element yields the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\netcoreapp1.1\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a><span data-ttu-id="e78c0-280">Incluir arquivos</span><span class="sxs-lookup"><span data-stu-id="e78c0-280">Include files</span></span>

<span data-ttu-id="e78c0-281">A marcação a seguir inclui uma pasta *images* fora do diretório do projeto para a pasta *wwwroot/images* do site de publicação:</span><span class="sxs-lookup"><span data-stu-id="e78c0-281">The following markup includes an *images* folder outside the project directory to the *wwwroot/images* folder of the publish site:</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="e78c0-282">A marcação pode ser adicionada ao arquivo *.csproj* ou ao perfil de publicação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-282">The markup can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="e78c0-283">Se ela for adicionada ao arquivo *.csproj*, ela será incluída em cada perfil de publicação no projeto.</span><span class="sxs-lookup"><span data-stu-id="e78c0-283">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>

<span data-ttu-id="e78c0-284">A marcação realçada a seguir mostra como:</span><span class="sxs-lookup"><span data-stu-id="e78c0-284">The following highlighted markup shows how to:</span></span>

* <span data-ttu-id="e78c0-285">Copiar um arquivo de fora do projeto para a pasta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="e78c0-285">Copy a file from outside the project into the *wwwroot* folder.</span></span>
* <span data-ttu-id="e78c0-286">Excluir a pasta *wwwroot\Content*.</span><span class="sxs-lookup"><span data-stu-id="e78c0-286">Exclude the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="e78c0-287">Excluir *Views\Home\About2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e78c0-287">Exclude *Views\Home\About2.cshtml*.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework />
    <ProjectGuid>afa9f185-7ce0-4935-9da1-ab676229d68a</ProjectGuid>
    <publishUrl>bin\Release\PublishOutput</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
  <ItemGroup>
    <ResolvedFileToPublish Include="..\ReadMe2.MD">
      <RelativePath>wwwroot\ReadMe2.MD</RelativePath>
    </ResolvedFileToPublish>

    <Content Update="wwwroot\Content\**\*" CopyToPublishDirectory="Never" />
    <Content Update="Views\Home\About2.cshtml" CopyToPublishDirectory="Never" />

  </ItemGroup>
</Project>
```

<span data-ttu-id="e78c0-288">Consulte o [Leiame do WebSDK](https://github.com/aspnet/websdk) para obter mais amostras de implantação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-288">See the [WebSDK Readme](https://github.com/aspnet/websdk) for more deployment samples.</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="e78c0-289">Executar um destino antes ou depois da publicação</span><span class="sxs-lookup"><span data-stu-id="e78c0-289">Run a target before or after publishing</span></span>

<span data-ttu-id="e78c0-290">Os destinos `BeforePublish` e `AfterPublish` internos executam um destino antes ou após o destino de publicação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-290">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="e78c0-291">Adicione os elementos a seguir ao perfil de publicação para registrar em log as mensagens de console antes e após a publicação:</span><span class="sxs-lookup"><span data-stu-id="e78c0-291">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="e78c0-292">Publicar em um servidor usando um certificado não confiável</span><span class="sxs-lookup"><span data-stu-id="e78c0-292">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="e78c0-293">Adicione a propriedade `<AllowUntrustedCertificate>` com um valor `True` ao perfil de publicação:</span><span class="sxs-lookup"><span data-stu-id="e78c0-293">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="e78c0-294">O serviço Kudu</span><span class="sxs-lookup"><span data-stu-id="e78c0-294">The Kudu service</span></span>

<span data-ttu-id="e78c0-295">Para exibir os arquivos em uma implantação de aplicativo Web do Serviço de Aplicativo do Azure, use o [serviço Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span><span class="sxs-lookup"><span data-stu-id="e78c0-295">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="e78c0-296">Acrescente o token `scm` ao nome do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="e78c0-296">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="e78c0-297">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e78c0-297">For example:</span></span>

| <span data-ttu-id="e78c0-298">URL</span><span class="sxs-lookup"><span data-stu-id="e78c0-298">URL</span></span>                                    | <span data-ttu-id="e78c0-299">Resultado</span><span class="sxs-lookup"><span data-stu-id="e78c0-299">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="e78c0-300">Aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="e78c0-300">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="e78c0-301">Serviço Kudu</span><span class="sxs-lookup"><span data-stu-id="e78c0-301">Kudu service</span></span> |

<span data-ttu-id="e78c0-302">Selecione o item de menu [Console de Depuração](https://github.com/projectkudu/kudu/wiki/Kudu-console) para exibir, editar, excluir ou adicionar arquivos.</span><span class="sxs-lookup"><span data-stu-id="e78c0-302">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e78c0-303">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e78c0-303">Additional resources</span></span>

* <span data-ttu-id="e78c0-304">A [Implantação da Web](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifica a implantação de aplicativos Web e sites da Web em servidores IIS.</span><span class="sxs-lookup"><span data-stu-id="e78c0-304">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="e78c0-305">[https://github.com/aspnet/websdk](https://github.com/aspnet/websdk/issues): problemas de arquivos e recursos de solicitação para implantação.</span><span class="sxs-lookup"><span data-stu-id="e78c0-305">[https://github.com/aspnet/websdk](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="e78c0-306">Publicar um aplicativo Web ASP.NET em uma VM do Azure usando o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e78c0-306">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
