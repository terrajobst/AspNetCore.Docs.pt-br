---
title: Configurar o vinculador para ASP.NET Core Blazor
author: guardrex
description: Saiba como controlar o vinculador de linguagem intermediária (IL) ao criar um aplicativo Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
no-loc:
- Blazor
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: b30669a7ca02c756fa10c8cf9973ef87e29e7bd4
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963610"
---
# <a name="configure-the-linker-for-aspnet-core-opno-locblazor"></a><span data-ttu-id="ab41b-103">Configurar o vinculador para ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="ab41b-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="ab41b-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ab41b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="ab41b-105"> executa a vinculação de [Il (linguagem intermediária)](/dotnet/standard/managed-code#intermediate-language--execution) durante uma compilação de versão para remover o Il desnecessário dos assemblies de saída do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ab41b-105"> performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a Release build to remove unnecessary IL from the app's output assemblies.</span></span>

<span data-ttu-id="ab41b-106">Controle a vinculação do assembly com uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="ab41b-106">Control assembly linking using either of the following approaches:</span></span>

* <span data-ttu-id="ab41b-107">Desabilite a vinculação globalmente com uma [propriedade MSBuild](#disable-linking-with-a-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="ab41b-107">Disable linking globally with a [MSBuild property](#disable-linking-with-a-msbuild-property).</span></span>
* <span data-ttu-id="ab41b-108">Controle a vinculação por assembly usando um [arquivo de configuração](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="ab41b-108">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="disable-linking-with-a-msbuild-property"></a><span data-ttu-id="ab41b-109">Desabilitar a vinculação com uma propriedade MSBuild</span><span class="sxs-lookup"><span data-stu-id="ab41b-109">Disable linking with a MSBuild property</span></span>

<span data-ttu-id="ab41b-110">A vinculação é habilitada por padrão no Modo de Versão quando um aplicativo é criado, o que inclui publicação.</span><span class="sxs-lookup"><span data-stu-id="ab41b-110">Linking is enabled by default in Release mode when an app is built, which includes publishing.</span></span> <span data-ttu-id="ab41b-111">Para desabilitar a vinculação para todos os assemblies, defina a propriedade `BlazorLinkOnBuild` MSBuild como `false` no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="ab41b-111">To disable linking for all assemblies, set the `BlazorLinkOnBuild` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="ab41b-112">Controlar a vinculação com um arquivo de configuração</span><span class="sxs-lookup"><span data-stu-id="ab41b-112">Control linking with a configuration file</span></span>

<span data-ttu-id="ab41b-113">Controle a vinculação por assembly fornecendo um arquivo de configuração XML e especificando o arquivo como um item MSBuild no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="ab41b-113">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="ab41b-114">*Linker.xml*:</span><span class="sxs-lookup"><span data-stu-id="ab41b-114">*Linker.xml*:</span></span>

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

<span data-ttu-id="ab41b-115">Para obter mais informações, consulte [vinculador Il: sintaxe do descritor XML](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span><span class="sxs-lookup"><span data-stu-id="ab41b-115">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>
