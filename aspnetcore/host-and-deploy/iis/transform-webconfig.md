---
title: Transformação do web.config
author: guardrex
description: Saiba como transformar o arquivo web.config ao publicar um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: d28c362a200ad433e316bc1af710231a169a30a4
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007310"
---
# <a name="transform-webconfig"></a><span data-ttu-id="fe271-103">Transformação do web.config</span><span class="sxs-lookup"><span data-stu-id="fe271-103">Transform web.config</span></span>

<span data-ttu-id="fe271-104">Por [Vijay Ramakrishnan](https://github.com/vijayrkn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fe271-104">By [Vijay Ramakrishnan](https://github.com/vijayrkn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fe271-105">As transformações no arquivo *web.config* podem ser aplicadas automaticamente quando um aplicativo é publicado com base em:</span><span class="sxs-lookup"><span data-stu-id="fe271-105">Transformations to the *web.config* file can be applied automatically when an app is published based on:</span></span>

* [<span data-ttu-id="fe271-106">Configuração do build</span><span class="sxs-lookup"><span data-stu-id="fe271-106">Build configuration</span></span>](#build-configuration)
* [<span data-ttu-id="fe271-107">Perfil</span><span class="sxs-lookup"><span data-stu-id="fe271-107">Profile</span></span>](#profile)
* [<span data-ttu-id="fe271-108">Ambiente</span><span class="sxs-lookup"><span data-stu-id="fe271-108">Environment</span></span>](#environment)
* [<span data-ttu-id="fe271-109">Personalizado</span><span class="sxs-lookup"><span data-stu-id="fe271-109">Custom</span></span>](#custom)

<span data-ttu-id="fe271-110">Essas transformações ocorrem para qualquer um dos seguintes cenários de geração *web.config*:</span><span class="sxs-lookup"><span data-stu-id="fe271-110">These transformations occur for either of the following *web.config* generation scenarios:</span></span>

* <span data-ttu-id="fe271-111">Gerado automaticamente pelo SDK `Microsoft.NET.Sdk.Web`.</span><span class="sxs-lookup"><span data-stu-id="fe271-111">Generated automatically by the `Microsoft.NET.Sdk.Web` SDK.</span></span>
* <span data-ttu-id="fe271-112">Fornecido pelo desenvolvedor na raiz de [conteúdo](xref:fundamentals/index#content-root) do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fe271-112">Provided by the developer in the [content root](xref:fundamentals/index#content-root) of the app.</span></span>

## <a name="build-configuration"></a><span data-ttu-id="fe271-113">Configurações de compilação</span><span class="sxs-lookup"><span data-stu-id="fe271-113">Build configuration</span></span>

<span data-ttu-id="fe271-114">Transformações de configuração de compilação são executadas primeiros.</span><span class="sxs-lookup"><span data-stu-id="fe271-114">Build configuration transforms are run first.</span></span>

<span data-ttu-id="fe271-115">Inclua um arquivo *web.{CONFIGURATION}.config* para cada [configuração de build (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) que exija uma transformação de *web.config*.</span><span class="sxs-lookup"><span data-stu-id="fe271-115">Include a *web.{CONFIGURATION}.config* file for each [build configuration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="fe271-116">No exemplo a seguir, uma variável de ambiente específica à configuração está definida em *web.Release.config*:</span><span class="sxs-lookup"><span data-stu-id="fe271-116">In the following example, a configuration-specific environment variable is set in *web.Release.config*:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Configuration_Specific" 
                               value="Configuration_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="fe271-117">A transformação é aplicada quando a configuração é definida como *Release*:</span><span class="sxs-lookup"><span data-stu-id="fe271-117">The transform is applied when the configuration is set to *Release*:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="fe271-118">A propriedade de MSBuild para a configuração é `$(Configuration)`.</span><span class="sxs-lookup"><span data-stu-id="fe271-118">The MSBuild property for the configuration is `$(Configuration)`.</span></span>

## <a name="profile"></a><span data-ttu-id="fe271-119">Perfil</span><span class="sxs-lookup"><span data-stu-id="fe271-119">Profile</span></span>

<span data-ttu-id="fe271-120">As transformações de perfil são executadas depois, após as transformações da [Configuração de build](#build-configuration).</span><span class="sxs-lookup"><span data-stu-id="fe271-120">Profile transformations are run second, after [Build configuration](#build-configuration) transforms.</span></span>

<span data-ttu-id="fe271-121">Inclua um arquivo *web.{PROFILE}.config* para cada configuração de perfil que exija uma transformação de *web.config*.</span><span class="sxs-lookup"><span data-stu-id="fe271-121">Include a *web.{PROFILE}.config* file for each profile configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="fe271-122">No exemplo a seguir, uma variável de ambiente específica ao perfil está definida em *web.FolderProfile.config* para um perfil de publicação de pasta:</span><span class="sxs-lookup"><span data-stu-id="fe271-122">In the following example, a profile-specific environment variable is set in *web.FolderProfile.config* for a folder publish profile:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Profile_Specific" 
                               value="Profile_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="fe271-123">A transformação é aplicada quando o perfil for *FolderProfile*:</span><span class="sxs-lookup"><span data-stu-id="fe271-123">The transform is applied when the profile is *FolderProfile*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

<span data-ttu-id="fe271-124">A propriedade de MSBuild para o nome do perfil é `$(PublishProfile)`.</span><span class="sxs-lookup"><span data-stu-id="fe271-124">The MSBuild property for the profile name is `$(PublishProfile)`.</span></span>

<span data-ttu-id="fe271-125">Se nenhum perfil for passado, o nome do perfil padrão será **FileSystem** e *web. FileSystem.config* será aplicado se o arquivo estiver presente na raiz de conteúdo do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="fe271-125">If no profile is passed, the default profile name is **FileSystem** and *web.FileSystem.config* is applied if the file is present in the app's content root.</span></span>

## <a name="environment"></a><span data-ttu-id="fe271-126">Ambiente</span><span class="sxs-lookup"><span data-stu-id="fe271-126">Environment</span></span>

<span data-ttu-id="fe271-127">As transformações de ambiente são executadas logo após as transformações da [Configuração de build](#build-configuration) e de [Perfil](#profile).</span><span class="sxs-lookup"><span data-stu-id="fe271-127">Environment transformations are run third, after [Build configuration](#build-configuration) and [Profile](#profile) transforms.</span></span>

<span data-ttu-id="fe271-128">Inclua um arquivo *web.{ENVIRONMENT}.config* para cada [ambiente](xref:fundamentals/environments) que exija uma transformação de *web.config*.</span><span class="sxs-lookup"><span data-stu-id="fe271-128">Include a *web.{ENVIRONMENT}.config* file for each [environment](xref:fundamentals/environments) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="fe271-129">No exemplo a seguir, uma variável de ambiente específica ao ambiente é definida em *web.Production.config* para o ambiente de Produção:</span><span class="sxs-lookup"><span data-stu-id="fe271-129">In the following example, a environment-specific environment variable is set in *web.Production.config* for the Production environment:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Environment_Specific" 
                               value="Environment_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="fe271-130">A transformação será aplicada quando o ambiente for *Produção*:</span><span class="sxs-lookup"><span data-stu-id="fe271-130">The transform is applied when the environment is *Production*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

<span data-ttu-id="fe271-131">A propriedade de MSBuild para o ambiente é `$(EnvironmentName)`.</span><span class="sxs-lookup"><span data-stu-id="fe271-131">The MSBuild property for the environment is `$(EnvironmentName)`.</span></span>

<span data-ttu-id="fe271-132">Ao publicar no Visual Studio, usando um perfil de publicação, consulte <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span><span class="sxs-lookup"><span data-stu-id="fe271-132">When publishing from Visual Studio and using a publish profile, see <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span></span>

<span data-ttu-id="fe271-133">A variável de ambiente `ASPNETCORE_ENVIRONMENT` é adicionada automaticamente ao arquivo *web.config* quando o nome do ambiente for especificado.</span><span class="sxs-lookup"><span data-stu-id="fe271-133">The `ASPNETCORE_ENVIRONMENT` environment variable is automatically added to the *web.config* file when the environment name is specified.</span></span>

## <a name="custom"></a><span data-ttu-id="fe271-134">Personalizado</span><span class="sxs-lookup"><span data-stu-id="fe271-134">Custom</span></span>

<span data-ttu-id="fe271-135">As transformações personalizadas são executadas por último, após as transformações da [Configuração de build](#build-configuration), de [Perfil](#profile) e de [Ambiente](#environment).</span><span class="sxs-lookup"><span data-stu-id="fe271-135">Custom transformations are run last, after [Build configuration](#build-configuration), [Profile](#profile), and [Environment](#environment) transforms.</span></span>

<span data-ttu-id="fe271-136">Inclua um arquivo *{CUSTOM_NAME}.transform* para cada configuração personalizada que exija uma transformação de *web.config*.</span><span class="sxs-lookup"><span data-stu-id="fe271-136">Include a *{CUSTOM_NAME}.transform* file for each custom configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="fe271-137">No exemplo a seguir, uma variável de ambiente de transformação personalizada está definida em *custom.transform*:</span><span class="sxs-lookup"><span data-stu-id="fe271-137">In the following example, a custom transform environment variable is set in *custom.transform*:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Custom_Specific" 
                               value="Custom_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="fe271-138">A transformação é aplicada quando a propriedade `CustomTransformFileName` é passada para o comando [dotnet publish](/dotnet/core/tools/dotnet-publish):</span><span class="sxs-lookup"><span data-stu-id="fe271-138">The transform is applied when the `CustomTransformFileName` property is passed to the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

<span data-ttu-id="fe271-139">A propriedade de MSBuild para o nome do perfil é `$(CustomTransformFileName)`.</span><span class="sxs-lookup"><span data-stu-id="fe271-139">The MSBuild property for the profile name is `$(CustomTransformFileName)`.</span></span>

## <a name="prevent-webconfig-transformation"></a><span data-ttu-id="fe271-140">Impedir a transformação de web.config</span><span class="sxs-lookup"><span data-stu-id="fe271-140">Prevent web.config transformation</span></span>

<span data-ttu-id="fe271-141">Para impedir transformações do arquivo *web.config*, defina a propriedade de MSBuild `$(IsWebConfigTransformDisabled)`:</span><span class="sxs-lookup"><span data-stu-id="fe271-141">To prevent transformations of the *web.config* file, set the MSBuild property `$(IsWebConfigTransformDisabled)`:</span></span>

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a><span data-ttu-id="fe271-142">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="fe271-142">Additional resources</span></span>

* [<span data-ttu-id="fe271-143">Sintaxe de transformação de web.config para implantação de projeto de aplicativo Web</span><span class="sxs-lookup"><span data-stu-id="fe271-143">Web.config Transformation Syntax for Web Application Project Deployment</span></span>](https://go.microsoft.com/fwlink/?LinkId=301874)
* <span data-ttu-id="fe271-144">[Sintaxe de transformação de web.config para implantação de projeto da Web usando o Visual Studio](https://docs.microsoft.com/previous-versions/aspnet/dd465326(v=vs.110))</span><span class="sxs-lookup"><span data-stu-id="fe271-144">[Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](https://docs.microsoft.com/previous-versions/aspnet/dd465326(v=vs.110))</span></span>
