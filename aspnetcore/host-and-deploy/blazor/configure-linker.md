---
title: Configurar o vinculador para ASP.NET Core Blazor
author: guardrex
description: Saiba como controlar o vinculador de linguagem intermediária (IL) ao criar um aplicativo Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 109da5ef400c3b9d64ccf3ceb33a5387ea6b5618
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218655"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="70b5f-103">Configurar o vinculador para o Blazor do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="70b5f-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="70b5f-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="70b5f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="70b5f-105">Webassembly mais elaborado executa vinculação de [Il (linguagem intermediária)](/dotnet/standard/managed-code#intermediate-language--execution) durante uma compilação para cortar o Il desnecessário dos assemblies de saída do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="70b5f-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="70b5f-106">O vinculador está desabilitado ao compilar na configuração de depuração.</span><span class="sxs-lookup"><span data-stu-id="70b5f-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="70b5f-107">Os aplicativos devem compilar na configuração de versão para habilitar o vinculador.</span><span class="sxs-lookup"><span data-stu-id="70b5f-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="70b5f-108">É recomendável criar em versão ao implantar seus aplicativos Webassembly mais podestas.</span><span class="sxs-lookup"><span data-stu-id="70b5f-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="70b5f-109">A vinculação de um aplicativo otimiza o tamanho, mas pode ter efeitos prejudiciais.</span><span class="sxs-lookup"><span data-stu-id="70b5f-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="70b5f-110">Os aplicativos que usam reflexão ou recursos dinâmicos relacionados podem interromper quando cortados porque o vinculador não conhece esse comportamento dinâmico e não pode determinar em geral quais tipos são necessários para reflexão em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="70b5f-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="70b5f-111">Para cortar esses aplicativos, o vinculador deve ser informado sobre quaisquer tipos exigidos pela reflexão no código e em pacotes ou estruturas das quais o aplicativo depende.</span><span class="sxs-lookup"><span data-stu-id="70b5f-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="70b5f-112">Para garantir que o aplicativo cortado funcione corretamente depois de implantado, é importante testar as compilações de versão do aplicativo com frequência durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="70b5f-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="70b5f-113">A vinculação de aplicativos mais avançados pode ser configurada usando estes recursos do MSBuild:</span><span class="sxs-lookup"><span data-stu-id="70b5f-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="70b5f-114">Configure o vínculo global com uma [Propriedade do MSBuild](#control-linking-with-an-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="70b5f-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="70b5f-115">Controle a vinculação por assembly usando um [arquivo de configuração](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="70b5f-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="70b5f-116">Vinculação de controle com uma propriedade do MSBuild</span><span class="sxs-lookup"><span data-stu-id="70b5f-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="70b5f-117">A vinculação é habilitada quando um aplicativo é compilado na configuração `Release`.</span><span class="sxs-lookup"><span data-stu-id="70b5f-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="70b5f-118">Para alterar isso, configure a propriedade `BlazorWebAssemblyEnableLinking` MSBuild no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="70b5f-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="70b5f-119">Controlar a vinculação com um arquivo de configuração</span><span class="sxs-lookup"><span data-stu-id="70b5f-119">Control linking with a configuration file</span></span>

<span data-ttu-id="70b5f-120">Controle a vinculação por assembly fornecendo um arquivo de configuração XML e especificando o arquivo como um item MSBuild no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="70b5f-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="70b5f-121">*LinkerConfig. xml*:</span><span class="sxs-lookup"><span data-stu-id="70b5f-121">*LinkerConfig.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="70b5f-122">Para obter mais informações, consulte [link XML File Examples (mono/linkr GitHub Repository)](https://github.com/mono/linker#link-xml-file-examples).</span><span class="sxs-lookup"><span data-stu-id="70b5f-122">For more information, see [Link xml file examples (mono/linker GitHub repository)](https://github.com/mono/linker#link-xml-file-examples).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="70b5f-123">Adicionar um arquivo de configuração do vinculador XML a uma biblioteca</span><span class="sxs-lookup"><span data-stu-id="70b5f-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="70b5f-124">Para configurar o vinculador para uma biblioteca específica, adicione um arquivo de configuração do vinculador XML à biblioteca como um recurso incorporado.</span><span class="sxs-lookup"><span data-stu-id="70b5f-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="70b5f-125">O recurso inserido deve ter o mesmo nome que o assembly.</span><span class="sxs-lookup"><span data-stu-id="70b5f-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="70b5f-126">No exemplo a seguir, o arquivo *LinkerConfig. xml* é especificado como um recurso inserido que tem o mesmo nome que o assembly da biblioteca:</span><span class="sxs-lookup"><span data-stu-id="70b5f-126">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="70b5f-127">Configurar o vinculador para internacionalização</span><span class="sxs-lookup"><span data-stu-id="70b5f-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="70b5f-128">Por padrão, a configuração do vinculador de mais de um revelador para aplicativos Webassembly mais poseriais retira informações de internacionalização, exceto pelas localidades explicitamente solicitadas.</span><span class="sxs-lookup"><span data-stu-id="70b5f-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="70b5f-129">Remover esses assemblies minimiza o tamanho do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="70b5f-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="70b5f-130">Para controlar quais assemblies de I18N são retidos, defina a propriedade `<MonoLinkerI18NAssemblies>` MSBuild no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="70b5f-130">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="70b5f-131">Valor da região</span><span class="sxs-lookup"><span data-stu-id="70b5f-131">Region Value</span></span>     | <span data-ttu-id="70b5f-132">Assembly de região mono</span><span class="sxs-lookup"><span data-stu-id="70b5f-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="70b5f-133">Todos os assemblies incluídos</span><span class="sxs-lookup"><span data-stu-id="70b5f-133">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="70b5f-134">*Nacional. CJK. dll*</span><span class="sxs-lookup"><span data-stu-id="70b5f-134">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="70b5f-135">*Nacional. Oriente Médio. dll*</span><span class="sxs-lookup"><span data-stu-id="70b5f-135">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="70b5f-136">`none` (padrão)</span><span class="sxs-lookup"><span data-stu-id="70b5f-136">`none` (default)</span></span> | <span data-ttu-id="70b5f-137">Nenhum</span><span class="sxs-lookup"><span data-stu-id="70b5f-137">None</span></span>                    |
| `other`          | <span data-ttu-id="70b5f-138">*Nacional. Outro. dll*</span><span class="sxs-lookup"><span data-stu-id="70b5f-138">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="70b5f-139">*Nacional. Raras. dll*</span><span class="sxs-lookup"><span data-stu-id="70b5f-139">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="70b5f-140">*Nacional. West. dll*</span><span class="sxs-lookup"><span data-stu-id="70b5f-140">*I18N.West.dll*</span></span>         |

<span data-ttu-id="70b5f-141">Use uma vírgula para separar vários valores (por exemplo, `mideast,west`).</span><span class="sxs-lookup"><span data-stu-id="70b5f-141">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="70b5f-142">Para obter mais informações, consulte [i18n: Pnetlib Internacionalization Framework library (repositório do GitHub mono/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="70b5f-142">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
