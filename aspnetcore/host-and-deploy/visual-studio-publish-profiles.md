---
title: Perfis de publicação do Visual Studio para a implantação do aplicativo ASP.NET Core
author: rick-anderson
description: Saiba como criar perfis de publicação no Visual Studio e usá-los para gerenciar implantações de aplicativo ASP.NET Core para vários destinos.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/18/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: ac243a3898553b2e14a6c15d311afaf62f112a24
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207822"
---
# <a name="visual-studio-publish-profiles-for-aspnet-core-app-deployment"></a><span data-ttu-id="0d4a9-103">Perfis de publicação do Visual Studio para a implantação do aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d4a9-103">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>

<span data-ttu-id="0d4a9-104">Por [Sayed Hashimi de Ibrahim](https://github.com/sayedihashimi) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0d4a9-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0d4a9-105">Este documento se concentra no uso do Visual Studio 2017 (ou posterior) para criar e usar perfis de publicação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-105">This document focuses on using Visual Studio 2017 or later to create and use publish profiles.</span></span> <span data-ttu-id="0d4a9-106">Os perfis de publicação criados com o Visual Studio podem ser executados do MSBuild e do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-106">The publish profiles created with Visual Studio can be run from MSBuild and Visual Studio.</span></span> <span data-ttu-id="0d4a9-107">Consulte [Publicar um aplicativo Web ASP.NET Core no Serviço de Aplicativo do Azure usando o Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) para obter instruções sobre a publicação no Azure.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-107">See [Publish an ASP.NET Core web app to Azure App Service using Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) for instructions on publishing to Azure.</span></span>

<span data-ttu-id="0d4a9-108">O comando `dotnet new mvc` produz um arquivo de projeto contendo o seguinte elemento `<Project>` de nível superior:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-108">The `dotnet new mvc` command produces a project file containing the following top-level `<Project>` element:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="0d4a9-109">O atributo `Sdk` do elemento `<Project>` anterior importa as [propriedades](/visualstudio/msbuild/msbuild-properties) e os [destinos](/visualstudio/msbuild/msbuild-targets) do MSBuild de *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* e *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="0d4a9-110">O local padrão para `$(MSBuildSDKsPath)` (com o Visual Studio 2019 Enterprise) é a pasta *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks*.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="0d4a9-111">`Microsoft.NET.Sdk.Web` (SDK da Web) depende de outros SDKs, incluindo `Microsoft.NET.Sdk` (SDK do .NET Core) e `Microsoft.NET.Sdk.Razor` ([SDK do Razor](xref:razor-pages/sdk)).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-111">`Microsoft.NET.Sdk.Web` (Web SDK) depends on other SDKs, including `Microsoft.NET.Sdk` (.NET Core SDK) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="0d4a9-112">As propriedades e os destinos do MSBuild associados a cada SDK dependente são importados.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="0d4a9-113">Os destinos de publicação importam o conjunto apropriado de destinos com base no método de publicação usado.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="0d4a9-114">Quando o MSBuild ou o Visual Studio carrega um projeto, as seguintes ações de nível alto ocorrem:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="0d4a9-115">Compilar projeto</span><span class="sxs-lookup"><span data-stu-id="0d4a9-115">Build project</span></span>
* <span data-ttu-id="0d4a9-116">Computar arquivos a publicar</span><span class="sxs-lookup"><span data-stu-id="0d4a9-116">Compute files to publish</span></span>
* <span data-ttu-id="0d4a9-117">Publicar arquivos para o destino</span><span class="sxs-lookup"><span data-stu-id="0d4a9-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="0d4a9-118">Itens de projeto de computação</span><span class="sxs-lookup"><span data-stu-id="0d4a9-118">Compute project items</span></span>

<span data-ttu-id="0d4a9-119">Quando o projeto é carregado, os [itens de projeto do MSBuild](/visualstudio/msbuild/common-msbuild-project-items) (arquivos) são computados.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="0d4a9-120">O tipo de item determina como o arquivo é processado.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="0d4a9-121">Por padrão, os arquivos *.cs* são incluídos na lista de itens `Compile`.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="0d4a9-122">Os arquivos na lista de itens `Compile` são compilados.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="0d4a9-123">A lista de itens `Content` contém arquivos que são publicados juntamente com as saídas de build.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="0d4a9-124">Por padrão, os arquivos que correspondem aos padrões `wwwroot\**`, `**\*.config` e `**\*.json` são incluídos na lista de itens `Content`.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="0d4a9-125">Por exemplo, o [padrão de recurso de curinga](https://gruntjs.com/configuring-tasks#globbing-patterns) `wwwroot\**` corresponde a todos os arquivos na pasta *wwwroot* **e** suas subpastas.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder **and** its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0d4a9-126">O SDK da Web importa o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-126">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="0d4a9-127">Como resultado, os arquivos que correspondem aos padrões `**\*.cshtml` e `**\*.razor` também são incluídos na lista de itens `Content`.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="0d4a9-128">O SDK da Web importa o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-128">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="0d4a9-129">Como resultado, os arquivos que correspondem ao padrão `**\*.cshtml` também são incluídos na lista de itens `Content`.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="0d4a9-130">Para adicionar explicitamente um arquivo à lista de publicação, adicione o arquivo diretamente no arquivo *.csproj*, conforme mostrado na seção [Incluir Arquivos](#include-files).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="0d4a9-131">Ao selecionar o botão **Publicar** no Visual Studio ou ao publicar da linha de comando:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="0d4a9-132">Os itens/propriedades são calculados (os arquivos necessários para compilar).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="0d4a9-133">**Somente Visual Studio**: os pacotes do NuGet são restaurados.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-133">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="0d4a9-134">(A restauração precisa ser explícita pelo usuário na CLI.)</span><span class="sxs-lookup"><span data-stu-id="0d4a9-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="0d4a9-135">O projeto é compilado.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-135">The project builds.</span></span>
* <span data-ttu-id="0d4a9-136">Os itens de publicação são computados (os arquivos necessários para a publicação).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="0d4a9-137">O projeto é publicado (os arquivos computados são copiados para o destino de publicação).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="0d4a9-138">Quando um projeto do ASP.NET Core faz referência a `Microsoft.NET.Sdk.Web` no arquivo de projeto, um arquivo *app_offline.htm* é colocado na raiz do diretório do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="0d4a9-139">Quando o arquivo estiver presente, o módulo do ASP.NET Core apenas desligará o aplicativo e servirá o arquivo *app_offline.htm* durante a implantação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="0d4a9-140">Para obter mais informações, consulte [Referência de configuração do módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="0d4a9-141">Publicação de linha de comando básica</span><span class="sxs-lookup"><span data-stu-id="0d4a9-141">Basic command-line publishing</span></span>

<span data-ttu-id="0d4a9-142">A publicação de linha de comando funciona em todas as plataformas compatíveis com o .NET Core e não requer o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="0d4a9-143">Nas amostras a seguir, o comando [dotnet publish](/dotnet/core/tools/dotnet-publish) é executado no diretório do projeto (que contém o arquivo *.csproj*).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-143">In the following samples, the [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="0d4a9-144">Se você não estiver na pasta do projeto, passe explicitamente no caminho do arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-144">If not in the project folder, explicitly pass in the project file path.</span></span> <span data-ttu-id="0d4a9-145">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-145">For example:</span></span>

```console
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="0d4a9-146">Execute os comandos a seguir para criar e publicar um aplicativo Web:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-146">Run the following commands to create and publish a web app:</span></span>

```console
dotnet new mvc
dotnet publish
```

<span data-ttu-id="0d4a9-147">O comando [dotnet publish](/dotnet/core/tools/dotnet-publish) gera uma saída semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-147">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command produces output similar to the following:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {version} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\publish\
```

<span data-ttu-id="0d4a9-148">A pasta de publicação padrão é `bin\$(Configuration)\netcoreapp<version>\publish`.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-148">The default publish folder is `bin\$(Configuration)\netcoreapp<version>\publish`.</span></span> <span data-ttu-id="0d4a9-149">O padrão para `$(Configuration)` é *Debug*.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-149">The default for `$(Configuration)` is *Debug*.</span></span> <span data-ttu-id="0d4a9-150">Na amostra anterior, o `<TargetFramework>` é `netcoreapp{X.Y}`.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-150">In the preceding sample, the `<TargetFramework>` is `netcoreapp{X.Y}`.</span></span>

<span data-ttu-id="0d4a9-151">`dotnet publish -h` exibe informações de ajuda para publicação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-151">`dotnet publish -h` displays help information for publish.</span></span>

<span data-ttu-id="0d4a9-152">O comando a seguir especifica um build de `Release` e o diretório de publicação:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-152">The following command specifies a `Release` build and the publishing directory:</span></span>

```console
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="0d4a9-153">O comando [dotnet publish](/dotnet/core/tools/dotnet-publish) chama MSBuild, que invoca o destino `Publish`.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-153">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="0d4a9-154">Quaisquer parâmetros passados para `dotnet publish` são passados para o MSBuild.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-154">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="0d4a9-155">O parâmetro `-c` é mapeado para a propriedade do MSBuild `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-155">The `-c` parameter maps to the `Configuration` MSBuild property.</span></span> <span data-ttu-id="0d4a9-156">O parâmetro `-o` é mapeado para `OutputPath`.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-156">The `-o` parameter maps to `OutputPath`.</span></span>

<span data-ttu-id="0d4a9-157">Propriedades do MSBuild podem ser passadas usando qualquer um dos seguintes formatos:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-157">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="0d4a9-158">O comando a seguir publica um build de `Release` para um compartilhamento de rede:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-158">The following command publishes a `Release` build to a network share:</span></span>

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

<span data-ttu-id="0d4a9-159">O compartilhamento de rede é especificado com barras "/" ( *//r8/* ) e funciona em todas as plataformas com suporte do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-159">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

<span data-ttu-id="0d4a9-160">Confirme que o aplicativo publicado para implantação não está em execução.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-160">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="0d4a9-161">Os arquivos da pasta *publish* são bloqueados quando o aplicativo está em execução.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-161">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="0d4a9-162">A implantação não pode ocorrer porque arquivos bloqueados não podem ser copiados.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-162">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="0d4a9-163">Perfis de publicação</span><span class="sxs-lookup"><span data-stu-id="0d4a9-163">Publish profiles</span></span>

<span data-ttu-id="0d4a9-164">Esta seção usa o Visual Studio 2017 (ou posterior) para criar um perfil de publicação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-164">This section uses Visual Studio 2017 or later to create a publishing profile.</span></span> <span data-ttu-id="0d4a9-165">Uma vez criado, é possível publicar do Visual Studio ou da linha de comando.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-165">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span>

<span data-ttu-id="0d4a9-166">Perfis de publicação podem simplificar o processo de publicação e podem existir em qualquer número.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-166">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span> <span data-ttu-id="0d4a9-167">Crie um perfil de publicação no Visual Studio escolhendo um dos seguintes caminhos:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-167">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="0d4a9-168">No Visual Studio, clique com o botão direito do mouse no nome do projeto no **Gerenciador de Soluções** e selecione **Publicar**.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-168">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="0d4a9-169">Selecione **Publicar {NOME DO PROJETO}** no menu **Build**.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-169">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="0d4a9-170">A guia **Publicar** da página de capacidades do aplicativo é exibida.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-170">The **Publish** tab of the app capacities page is displayed.</span></span> <span data-ttu-id="0d4a9-171">Se o projeto não tem um perfil de publicação, a página a seguir é exibida:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-171">If the project lacks a publish profile, the following page is displayed:</span></span>

![A guia Publicar da página de capacidades do aplicativo](visual-studio-publish-profiles/_static/az.png)

<span data-ttu-id="0d4a9-173">Quando a opção **Pasta** é selecionada, especifique um caminho de pasta para armazenar os ativos publicados.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-173">When **Folder** is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="0d4a9-174">A pasta padrão é *bin\Release\PublishOutput*.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-174">The default folder is *bin\Release\PublishOutput*.</span></span> <span data-ttu-id="0d4a9-175">Clique no botão **Criar Perfil** para concluir.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-175">Click the **Create Profile** button to finish.</span></span>

<span data-ttu-id="0d4a9-176">Depois de um perfil de publicação ser criado, a guia **Publicar** é alterada.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-176">Once a publish profile is created, the **Publish** tab changes.</span></span> <span data-ttu-id="0d4a9-177">O perfil recém-criado é exibido em uma lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-177">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="0d4a9-178">Clique em **Criar novo perfil** para fazer isso.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-178">Click **Create new profile** to create another new profile.</span></span>

![A guia Publicar da página de capacidades do aplicativo mostrando FolderProfile](visual-studio-publish-profiles/_static/create_new.png)

<span data-ttu-id="0d4a9-180">O Assistente de publicação dá suporte aos destinos de publicação a seguir:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-180">The Publish wizard supports the following publish targets:</span></span>

* <span data-ttu-id="0d4a9-181">Serviço de Aplicativo do Azure</span><span class="sxs-lookup"><span data-stu-id="0d4a9-181">Azure App Service</span></span>
* <span data-ttu-id="0d4a9-182">Máquinas Virtuais do Azure</span><span class="sxs-lookup"><span data-stu-id="0d4a9-182">Azure Virtual Machines</span></span>
* <span data-ttu-id="0d4a9-183">IIS, FTP, etc. (para qualquer servidor Web)</span><span class="sxs-lookup"><span data-stu-id="0d4a9-183">IIS, FTP, etc. (for any web server)</span></span>
* <span data-ttu-id="0d4a9-184">Pasta</span><span class="sxs-lookup"><span data-stu-id="0d4a9-184">Folder</span></span>
* <span data-ttu-id="0d4a9-185">Importar Perfil</span><span class="sxs-lookup"><span data-stu-id="0d4a9-185">Import Profile</span></span>

<span data-ttu-id="0d4a9-186">Para obter mais informações, confira [Quais opções de publicação são adequadas para mim](/visualstudio/ide/not-in-toc/web-publish-options).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-186">For more information, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="0d4a9-187">Quando você cria um perfil de publicação com o Visual Studio, um arquivo do MSBuild *Properties/PublishProfiles/{NOME DO PERFIL}.pubxml* é criado.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-187">When creating a publish profile with Visual Studio, a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file is created.</span></span> <span data-ttu-id="0d4a9-188">O *.pubxml* é um arquivo do MSBuild e contém definições de configuração de publicação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-188">The *.pubxml* file is a MSBuild file and contains publish configuration settings.</span></span> <span data-ttu-id="0d4a9-189">Esse arquivo pode ser alterado para personalizar o processo de build e de publicação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-189">This file can be changed to customize the build and publish process.</span></span> <span data-ttu-id="0d4a9-190">Esse arquivo é lido pelo processo de publicação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-190">This file is read by the publishing process.</span></span> <span data-ttu-id="0d4a9-191">`<LastUsedBuildConfiguration>` é especial porque é uma propriedade global e não deverá estar em nenhum arquivo importado no build.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-191">`<LastUsedBuildConfiguration>` is special because it's a global property and shouldn't be in any file that's imported in the build.</span></span> <span data-ttu-id="0d4a9-192">Para obter mais informações, confira [MSBuild: como definir a propriedade de configuração](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-192">See [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) for more information.</span></span>

<span data-ttu-id="0d4a9-193">Ao publicar em um destino do Azure, o arquivo *.pubxml* contém o identificador de assinatura do Azure.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-193">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="0d4a9-194">Com esse tipo de destino, não é recomendável adicionar esse arquivo ao controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-194">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="0d4a9-195">Ao publicar em um destino não Azure, é seguro fazer check-in do arquivo *.pubxml*.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-195">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="0d4a9-196">Informações confidenciais (como a senha de publicação) são criptografadas em um nível por usuário/computador.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-196">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="0d4a9-197">Elas são armazenadas no arquivo *Properties/PublishProfiles/{NOME DO PERFIL}.pubxml.user*.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-197">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="0d4a9-198">Já que esse arquivo pode armazenar informações confidenciais, o check-in dele não deve ser realizado no controle do código-fonte.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-198">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="0d4a9-199">Para obter uma visão geral de como publicar um aplicativo Web do ASP.NET Core, veja [Hospedar e implantar](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-199">For an overview of how to publish a web app on ASP.NET Core, see [Host and deploy](xref:host-and-deploy/index).</span></span> <span data-ttu-id="0d4a9-200">As tarefas e os destinos de MSBuild necessários para publicar um aplicativo ASP.NET Core são open-source no [repositório aspnet/websdk](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-200">The MSBuild tasks and targets necessary to publish an ASP.NET Core app are open-source at the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="0d4a9-201">O `dotnet publish` pode usar os perfis de publicação de pasta, MSDeploy e [Kudu](https://github.com/projectkudu/kudu/wiki):</span><span class="sxs-lookup"><span data-stu-id="0d4a9-201">`dotnet publish` can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles:</span></span>

<span data-ttu-id="0d4a9-202">Pasta (funciona em modo multiplataforma):</span><span class="sxs-lookup"><span data-stu-id="0d4a9-202">Folder (works cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="0d4a9-203">MSDeploy (atualmente só funciona no Windows, uma vez que o MSDeploy não é multiplataforma):</span><span class="sxs-lookup"><span data-stu-id="0d4a9-203">MSDeploy (currently this only works in Windows since MSDeploy isn't cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="0d4a9-204">Pacote MSDeploy (atualmente só funciona no Windows, uma vez que o MSDeploy não é multiplataforma):</span><span class="sxs-lookup"><span data-stu-id="0d4a9-204">MSDeploy package (currently this only works in Windows since MSDeploy isn't cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="0d4a9-205">Nos exemplos anteriores, não passe `deployonbuild` para `dotnet publish`.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-205">In the preceding examples, don't pass `deployonbuild` to `dotnet publish`.</span></span>

<span data-ttu-id="0d4a9-206">Para obter mais informações, confira [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-206">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="0d4a9-207">O `dotnet publish` dá suporte às APIs do Kudu, para publicar no Azure de qualquer plataforma.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-207">`dotnet publish` supports Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="0d4a9-208">A publicação do Visual Studio dá suporte às APIs do Kudu, mas ela é compatível com o WebSDK para publicação multiplataforma para o Azure.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-208">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>

<span data-ttu-id="0d4a9-209">Adicione um perfil de publicação à pasta *Properties/PublishProfiles* com o seguinte conteúdo:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-209">Add a publish profile to the *Properties/PublishProfiles* folder with the following content:</span></span>

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

<span data-ttu-id="0d4a9-210">Execute o seguinte comando para compactar os conteúdos de publicação e publicá-los no Azure usando as APIs do Kudu:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-210">Run the following command to zip up the publish contents and publish it to Azure using the Kudu APIs:</span></span>

```console
dotnet publish /p:PublishProfile=Azure /p:Configuration=Release
```

<span data-ttu-id="0d4a9-211">Defina as seguintes propriedades de MSBuild ao usar um perfil de publicação:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-211">Set the following MSBuild properties when using a publish profile:</span></span>

* `DeployOnBuild=true`
* `PublishProfile={PUBLISH PROFILE}`

<span data-ttu-id="0d4a9-212">Ao publicar com um perfil chamado *FolderProfile*, é possível executar qualquer um dos comandos abaixo:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-212">When publishing with a profile named *FolderProfile*, either of the commands below can be executed:</span></span>

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="0d4a9-213">Ao invocar [dotnet build](/dotnet/core/tools/dotnet-build), ele chama `msbuild` para executar o processo de build e de publicação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-213">When invoking [dotnet build](/dotnet/core/tools/dotnet-build), it calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="0d4a9-214">Chamar `dotnet build` ou `msbuild` é equivalente ao passar um perfil de pasta.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-214">Calling either `dotnet build` or `msbuild` is equivalent when passing in a folder profile.</span></span> <span data-ttu-id="0d4a9-215">Ao chamar o MSBuild diretamente no Windows, a versão do .NET Framework do MSBuild é usada.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-215">When calling MSBuild directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="0d4a9-216">O MSDeploy é atualmente limitado a computadores Windows para a publicação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-216">MSDeploy is currently limited to Windows machines for publishing.</span></span> <span data-ttu-id="0d4a9-217">Chamar `dotnet build` em um perfil não de pasta invoca o MSBuild que, por sua vez, usa MSDeploy em perfis não de pasta.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-217">Calling `dotnet build` on a non-folder profile invokes MSBuild, and MSBuild uses MSDeploy on non-folder profiles.</span></span> <span data-ttu-id="0d4a9-218">Chamar `dotnet build` em um perfil não de pasta invoca o MSBuild (usando MSDeploy) e resulta em uma falha (mesmo quando em execução em uma plataforma do Windows).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-218">Calling `dotnet build` on a non-folder profile invokes MSBuild (using MSDeploy) and results in a failure (even when running on a Windows platform).</span></span> <span data-ttu-id="0d4a9-219">Para publicar com um perfil não de pasta, chame o MSBuild diretamente.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-219">To publish with a non-folder profile, call MSBuild directly.</span></span>

<span data-ttu-id="0d4a9-220">A pasta de perfil de publicação a seguir foi criada com o Visual Studio e publica em um compartilhamento de rede:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-220">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

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

<span data-ttu-id="0d4a9-221">Nos exemplos anteriores, `<LastUsedBuildConfiguration>` é definido como `Release`.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-221">In the preceding example, `<LastUsedBuildConfiguration>` is set to `Release`.</span></span> <span data-ttu-id="0d4a9-222">Ao publicar no Visual Studio, o valor da propriedade de configuração `<LastUsedBuildConfiguration>` é definido usando o valor quando o processo de publicação é iniciado.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-222">When publishing from Visual Studio, the `<LastUsedBuildConfiguration>` configuration property value is set using the value when the publish process is started.</span></span> <span data-ttu-id="0d4a9-223">A propriedade de configuração `<LastUsedBuildConfiguration>` é especial e não deve ser substituída em um arquivo do MSBuild importado.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-223">The `<LastUsedBuildConfiguration>` configuration property is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="0d4a9-224">Essa propriedade pode ser substituída da linha de comando.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-224">This property can be overridden from the command line.</span></span>

<span data-ttu-id="0d4a9-225">Usando a CLI do .NET Core:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-225">Using the .NET Core CLI:</span></span>

```console
dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

<span data-ttu-id="0d4a9-226">Usando o MSBuild:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-226">Using MSBuild:</span></span>

```console
msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="0d4a9-227">Publicar em um ponto de extremidade do MSDeploy da linha de comando</span><span class="sxs-lookup"><span data-stu-id="0d4a9-227">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="0d4a9-228">O exemplo a seguir usa um aplicativo Web do ASP.NET Core, criado pelo Visual Studio, denominada *AzureWebApp*.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-228">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="0d4a9-229">Um perfil de publicação dos aplicativos do Azure é adicionado ao Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-229">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="0d4a9-230">Para obter mais informações sobre como criar um perfil, consulte a seção [Perfis de publicação](#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-230">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="0d4a9-231">Para implantar o aplicativo usando um perfil de publicação, execute o comando `msbuild` de um **Prompt de Comando do Desenvolvedor** do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-231">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="0d4a9-232">O prompt de comando está disponível na pasta do *Visual Studio* do menu **Página Inicial** na barra de tarefas do Windows.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-232">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="0d4a9-233">Para facilitar o acesso, você pode adicionar o prompt de comando ao menu **Ferramentas** no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-233">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="0d4a9-234">Para saber mais, confira [Prompt de comando do desenvolvedor para Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-234">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="0d4a9-235">O MSBuild usa a sintaxe de comando a seguir:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-235">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="0d4a9-236">{CAMINHO} &ndash; Caminho para o arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-236">{PATH} &ndash; Path to the app's project file.</span></span>
* <span data-ttu-id="0d4a9-237">{PERFIL} &ndash; Nome do perfil de publicação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-237">{PROFILE} &ndash; Name of the publish profile.</span></span>
* <span data-ttu-id="0d4a9-238">{NOME DE USUÁRIO} &ndash; Nome de usuário do MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-238">{USERNAME} &ndash; MSDeploy username.</span></span> <span data-ttu-id="0d4a9-239">O {NOME DE USUÁRIO} pode ser encontrado no perfil de publicação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-239">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="0d4a9-240">{SENHA} &ndash; Senha do MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-240">{PASSWORD} &ndash; MSDeploy password.</span></span> <span data-ttu-id="0d4a9-241">Obtenha a {SENHA} do arquivo *{PERFIL}.PublishSettings*.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-241">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="0d4a9-242">Baixe o arquivo *.PublishSettings* de uma das seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-242">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="0d4a9-243">Gerenciador de Soluções: Selecione **Exibição** > **Cloud Explorer**.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-243">Solution Explorer: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="0d4a9-244">Conecte-se à sua assinatura do Azure.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-244">Connect with your Azure subscription.</span></span> <span data-ttu-id="0d4a9-245">Abra **Serviços de Aplicativos**.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-245">Open **App Services**.</span></span> <span data-ttu-id="0d4a9-246">Clique com o botão direito do mouse no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-246">Right-click the app.</span></span> <span data-ttu-id="0d4a9-247">Selecione **Baixar Perfil de Publicação**.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-247">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="0d4a9-248">Portal do Azure: selecione **Obter perfil de publicação** no painel **Visão geral** de seu aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-248">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="0d4a9-249">O exemplo a seguir usa um perfil de publicação denominado *AzureWebApp – Implantação da Web*:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-249">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="0d4a9-250">Um perfil de publicação também pode ser usado com o comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) da CLI do .NET Core em um prompt de comando do Windows:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-250">A publish profile can also be used with the .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command prompt:</span></span>

```console
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!NOTE]
> <span data-ttu-id="0d4a9-251">O comando [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) está disponível em várias plataformas e pode compilar aplicativos ASP.NET Core no macOS e Linux.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-251">The [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command is available cross-platform and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="0d4a9-252">No entanto, o MSBuild no macOS e Linux não é capaz de implantar um aplicativo no Azure ou em outro ponto de extremidade do MSDeploy.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-252">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoint.</span></span> <span data-ttu-id="0d4a9-253">O MSDeploy está disponível apenas no Windows.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-253">MSDeploy is only available on Windows.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="0d4a9-254">Definir o ambiente</span><span class="sxs-lookup"><span data-stu-id="0d4a9-254">Set the environment</span></span>

<span data-ttu-id="0d4a9-255">Inclua a propriedade `<EnvironmentName>` no perfil de publicação ( *.pubxml*) ou no arquivo de projeto para definir o [ambiente](xref:fundamentals/environments) do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-255">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="0d4a9-256">Se você precisar de transformações do *web.config* (por exemplo, definir variáveis ​​de ambiente com base na configuração, no perfil ou no ambiente), confira <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="0d4a9-257">Excluir arquivos</span><span class="sxs-lookup"><span data-stu-id="0d4a9-257">Exclude files</span></span>

<span data-ttu-id="0d4a9-258">Ao publicar aplicativos Web ASP.NET Core, os seguintes ativos são incluídos:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="0d4a9-259">Artefatos de compilação</span><span class="sxs-lookup"><span data-stu-id="0d4a9-259">Build artifacts</span></span>
* <span data-ttu-id="0d4a9-260">As pastas e os arquivos que correspondem aos seguintes padrões de recurso de curinga:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="0d4a9-261">`**\*.config` (por exemplo, *web.config*)</span><span class="sxs-lookup"><span data-stu-id="0d4a9-261">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="0d4a9-262">`**\*.json` (por exemplo, *appsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="0d4a9-262">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="0d4a9-263">O MSBuild dá suporte aos [padrões de recurso de curinga](https://gruntjs.com/configuring-tasks#globbing-patterns).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="0d4a9-264">Por exemplo, o elemento `<Content>` a seguir suprime a cópia dos arquivos de texto ( *.txt*) na pasta *wwwroot\content* e suas subpastas:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-264">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="0d4a9-265">A marcação anterior pode ser adicionada a um perfil de publicação ou ao arquivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="0d4a9-266">Quando adicionada ao arquivo *.csproj*, a regra será adicionada a todos os perfis de publicação no projeto.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="0d4a9-267">O elemento `<MsDeploySkipRules>` a seguir exclui todos os arquivos da pasta *wwwroot\content*:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="0d4a9-268">`<MsDeploySkipRules>` não exclui os destinos de *ignorados* do site de implantação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="0d4a9-269">Pastas e arquivos de destino de `<Content>` são excluídos do site de implantação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="0d4a9-270">Por exemplo, suponha que um aplicativo Web implantado tinha os seguintes arquivos:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="0d4a9-271">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0d4a9-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="0d4a9-272">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0d4a9-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="0d4a9-273">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0d4a9-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="0d4a9-274">Nos elementos `<MsDeploySkipRules>` a seguir, esses arquivos não seriam excluídos no site de implantação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

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

<span data-ttu-id="0d4a9-275">Os elementos `<MsDeploySkipRules>` anteriores impedem que os arquivos *ignorados* sejam implantados.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="0d4a9-276">Ele não excluirá esses arquivos depois que eles forem implantados.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="0d4a9-277">O elemento `<Content>` a seguir exclui os arquivos de destino no site de implantação:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="0d4a9-278">O uso da implantação de linha de comando com o elemento `<Content>` anterior produz a seguinte saída:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-278">Using command-line deployment with the preceding `<Content>` element yields the following output:</span></span>

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

## <a name="include-files"></a><span data-ttu-id="0d4a9-279">Incluir arquivos</span><span class="sxs-lookup"><span data-stu-id="0d4a9-279">Include files</span></span>

<span data-ttu-id="0d4a9-280">A seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-280">The following markup:</span></span>

* <span data-ttu-id="0d4a9-281">Inclui uma pasta *images* fora do diretório do projeto na pasta *wwwroot/images* do site de publicação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-281">Includes an *images* folder outside the project directory to the *wwwroot/images* folder of the publish site.</span></span>
* <span data-ttu-id="0d4a9-282">Pode ser adicionada ao arquivo *.csproj* ou ao perfil de publicação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-282">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="0d4a9-283">Se ela for adicionada ao arquivo *.csproj*, ela será incluída em cada perfil de publicação no projeto.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-283">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="0d4a9-284">A marcação realçada a seguir mostra como:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-284">The following highlighted markup shows how to:</span></span>

* <span data-ttu-id="0d4a9-285">Copiar um arquivo de fora do projeto para a pasta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-285">Copy a file from outside the project into the *wwwroot* folder.</span></span>
* <span data-ttu-id="0d4a9-286">Excluir a pasta *wwwroot\Content*.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-286">Exclude the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="0d4a9-287">Excluir *Views\Home\About2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-287">Exclude *Views\Home\About2.cshtml*.</span></span>

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

<span data-ttu-id="0d4a9-288">Confira o [Leiame do repositório do SDK da Web](https://github.com/aspnet/websdk) para obter mais amostras de implantação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-288">See the [Web SDK repository Readme](https://github.com/aspnet/websdk) for more deployment samples.</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="0d4a9-289">Executar um destino antes ou depois da publicação</span><span class="sxs-lookup"><span data-stu-id="0d4a9-289">Run a target before or after publishing</span></span>

<span data-ttu-id="0d4a9-290">Os destinos `BeforePublish` e `AfterPublish` internos executam um destino antes ou após o destino de publicação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-290">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="0d4a9-291">Adicione os elementos a seguir ao perfil de publicação para registrar em log as mensagens de console antes e após a publicação:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-291">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="0d4a9-292">Publicar em um servidor usando um certificado não confiável</span><span class="sxs-lookup"><span data-stu-id="0d4a9-292">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="0d4a9-293">Adicione a propriedade `<AllowUntrustedCertificate>` com um valor `True` ao perfil de publicação:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-293">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="0d4a9-294">O serviço Kudu</span><span class="sxs-lookup"><span data-stu-id="0d4a9-294">The Kudu service</span></span>

<span data-ttu-id="0d4a9-295">Para exibir os arquivos em uma implantação de aplicativo Web do Serviço de Aplicativo do Azure, use o [serviço Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span><span class="sxs-lookup"><span data-stu-id="0d4a9-295">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="0d4a9-296">Acrescente o token `scm` ao nome do aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-296">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="0d4a9-297">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0d4a9-297">For example:</span></span>

| <span data-ttu-id="0d4a9-298">URL</span><span class="sxs-lookup"><span data-stu-id="0d4a9-298">URL</span></span>                                    | <span data-ttu-id="0d4a9-299">Resultado</span><span class="sxs-lookup"><span data-stu-id="0d4a9-299">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="0d4a9-300">Aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="0d4a9-300">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="0d4a9-301">Serviço Kudu</span><span class="sxs-lookup"><span data-stu-id="0d4a9-301">Kudu service</span></span> |

<span data-ttu-id="0d4a9-302">Selecione o item de menu [Console de Depuração](https://github.com/projectkudu/kudu/wiki/Kudu-console) para exibir, editar, excluir ou adicionar arquivos.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-302">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0d4a9-303">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0d4a9-303">Additional resources</span></span>

* <span data-ttu-id="0d4a9-304">A [Implantação da Web](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifica a implantação de aplicativos Web e sites da Web em servidores IIS.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-304">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="0d4a9-305">[Repositório do GitHub do SDK da Web](https://github.com/aspnet/websdk/issues): problemas de arquivos e recursos de solicitação para implantação.</span><span class="sxs-lookup"><span data-stu-id="0d4a9-305">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="0d4a9-306">Publicar um aplicativo Web ASP.NET em uma VM do Azure usando o Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d4a9-306">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
