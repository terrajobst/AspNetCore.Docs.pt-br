---
title: Configurar o vinculador para ASP.NET Core Blazor
author: guardrex
description: Saiba como controlar o vinculador de linguagem intermediária (IL) ao criar um aplicativo Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- Blazor
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 0bc987d72d2f684b1ecbd4a883e9a09fac7c801e
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317285"
---
# <a name="configure-the-linker-for-aspnet-core-opno-locblazor"></a><span data-ttu-id="e0c7c-103">Configurar o vinculador para ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e0c7c-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="e0c7c-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e0c7c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="e0c7c-105"> executa a vinculação de [Il (linguagem intermediária)](/dotnet/standard/managed-code#intermediate-language--execution) durante uma compilação para remover o Il desnecessário dos assemblies de saída do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e0c7c-105"> performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to remove unnecessary IL from the app's output assemblies.</span></span>

<span data-ttu-id="e0c7c-106">Controle a vinculação do assembly com uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e0c7c-106">Control assembly linking using either of the following approaches:</span></span>

* <span data-ttu-id="e0c7c-107">Desabilite a vinculação globalmente com uma [propriedade MSBuild](#disable-linking-with-a-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="e0c7c-107">Disable linking globally with a [MSBuild property](#disable-linking-with-a-msbuild-property).</span></span>
* <span data-ttu-id="e0c7c-108">Controle a vinculação por assembly usando um [arquivo de configuração](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="e0c7c-108">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="disable-linking-with-a-msbuild-property"></a><span data-ttu-id="e0c7c-109">Desabilitar a vinculação com uma propriedade MSBuild</span><span class="sxs-lookup"><span data-stu-id="e0c7c-109">Disable linking with a MSBuild property</span></span>

<span data-ttu-id="e0c7c-110">A vinculação é habilitada por padrão quando um aplicativo é criado, o que inclui a publicação.</span><span class="sxs-lookup"><span data-stu-id="e0c7c-110">Linking is enabled by default when an app is built, which includes publishing.</span></span> <span data-ttu-id="e0c7c-111">Para desabilitar a vinculação para todos os assemblies, defina a propriedade `BlazorLinkOnBuild` MSBuild como `false` no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="e0c7c-111">To disable linking for all assemblies, set the `BlazorLinkOnBuild` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="e0c7c-112">Controlar a vinculação com um arquivo de configuração</span><span class="sxs-lookup"><span data-stu-id="e0c7c-112">Control linking with a configuration file</span></span>

<span data-ttu-id="e0c7c-113">Controle a vinculação por assembly fornecendo um arquivo de configuração XML e especificando o arquivo como um item MSBuild no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="e0c7c-113">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="e0c7c-114">*Linker.xml*:</span><span class="sxs-lookup"><span data-stu-id="e0c7c-114">*Linker.xml*:</span></span>

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
      Fixes: https://github.com/aspnet/Blazor/issues/239
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

<span data-ttu-id="e0c7c-115">Para obter mais informações, consulte [vinculador Il: sintaxe do descritor XML](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span><span class="sxs-lookup"><span data-stu-id="e0c7c-115">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="e0c7c-116">Configurar o vinculador para internacionalização</span><span class="sxs-lookup"><span data-stu-id="e0c7c-116">Configure the linker for internationalization</span></span>

<span data-ttu-id="e0c7c-117">Por padrão, a configuração do vinculador Blazorpara aplicativos Webassembly Blazor retira informações de internacionalização, exceto as localidades explicitamente solicitadas.</span><span class="sxs-lookup"><span data-stu-id="e0c7c-117">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="e0c7c-118">Remover esses assemblies minimiza o tamanho do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="e0c7c-118">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="e0c7c-119">Para controlar quais assemblies de I18N são retidos, defina a propriedade `<MonoLinkerI18NAssemblies>` MSBuild no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="e0c7c-119">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="e0c7c-120">Valor da região</span><span class="sxs-lookup"><span data-stu-id="e0c7c-120">Region Value</span></span>     | <span data-ttu-id="e0c7c-121">Assembly de região mono</span><span class="sxs-lookup"><span data-stu-id="e0c7c-121">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="e0c7c-122">Todos os assemblies incluídos</span><span class="sxs-lookup"><span data-stu-id="e0c7c-122">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="e0c7c-123">*Nacional. CJK. dll*</span><span class="sxs-lookup"><span data-stu-id="e0c7c-123">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="e0c7c-124">*Nacional. Oriente Médio. dll*</span><span class="sxs-lookup"><span data-stu-id="e0c7c-124">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="e0c7c-125">`none` (padrão)</span><span class="sxs-lookup"><span data-stu-id="e0c7c-125">`none` (default)</span></span> | <span data-ttu-id="e0c7c-126">Nenhum</span><span class="sxs-lookup"><span data-stu-id="e0c7c-126">None</span></span>                    |
| `other`          | <span data-ttu-id="e0c7c-127">*Nacional. Outro. dll*</span><span class="sxs-lookup"><span data-stu-id="e0c7c-127">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="e0c7c-128">*Nacional. Raras. dll*</span><span class="sxs-lookup"><span data-stu-id="e0c7c-128">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="e0c7c-129">*Nacional. West. dll*</span><span class="sxs-lookup"><span data-stu-id="e0c7c-129">*I18N.West.dll*</span></span>         |

<span data-ttu-id="e0c7c-130">Use uma vírgula para separar vários valores (por exemplo, `mideast,west`).</span><span class="sxs-lookup"><span data-stu-id="e0c7c-130">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="e0c7c-131">Para obter mais informações, consulte [i18n: Pnetlib Internacionalization Framework biblioteca (repositório GitHub mono/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="e0c7c-131">For more information, see [I18N: Pnetlib Internationalization Framework Libary (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
