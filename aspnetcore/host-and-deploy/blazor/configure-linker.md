---
title: Configurar o vinculador para o Blazor do ASP.NET Core
author: guardrex
description: Saiba como controlar o Vinculador de IL (Linguagem Intermediária) ao criar um aplicativo Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: d3dd69e263e88ca1fc301eefc0da186a023aa96f
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211592"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="7c8ff-103">Configurar o vinculador para o Blazor do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c8ff-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="7c8ff-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7c8ff-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="7c8ff-105">O Blazor executa a vinculação de [IL (linguagem intermediária)](/dotnet/standard/managed-code#intermediate-language--execution) durante um build de Versão para remover a IL desnecessária dos assemblies de saída do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7c8ff-105">Blazor performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a Release build to remove unnecessary IL from the app's output assemblies.</span></span>

<span data-ttu-id="7c8ff-106">Controle a vinculação do assembly com uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="7c8ff-106">Control assembly linking using either of the following approaches:</span></span>

* <span data-ttu-id="7c8ff-107">Desabilite a vinculação globalmente com uma [propriedade MSBuild](#disable-linking-with-a-msbuild-property).</span><span class="sxs-lookup"><span data-stu-id="7c8ff-107">Disable linking globally with a [MSBuild property](#disable-linking-with-a-msbuild-property).</span></span>
* <span data-ttu-id="7c8ff-108">Controle a vinculação por assembly usando um [arquivo de configuração](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="7c8ff-108">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="disable-linking-with-a-msbuild-property"></a><span data-ttu-id="7c8ff-109">Desabilitar a vinculação com uma propriedade MSBuild</span><span class="sxs-lookup"><span data-stu-id="7c8ff-109">Disable linking with a MSBuild property</span></span>

<span data-ttu-id="7c8ff-110">A vinculação é habilitada por padrão no Modo de Versão quando um aplicativo é criado, o que inclui publicação.</span><span class="sxs-lookup"><span data-stu-id="7c8ff-110">Linking is enabled by default in Release mode when an app is built, which includes publishing.</span></span> <span data-ttu-id="7c8ff-111">Para desabilitar a vinculação para todos os assemblies, defina a propriedade `BlazorLinkOnBuild` MSBuild como `false` no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="7c8ff-111">To disable linking for all assemblies, set the `BlazorLinkOnBuild` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="7c8ff-112">Controlar a vinculação com um arquivo de configuração</span><span class="sxs-lookup"><span data-stu-id="7c8ff-112">Control linking with a configuration file</span></span>

<span data-ttu-id="7c8ff-113">Controle a vinculação por assembly fornecendo um arquivo de configuração XML e especificando o arquivo como um item MSBuild no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="7c8ff-113">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="7c8ff-114">*Linker.xml*:</span><span class="sxs-lookup"><span data-stu-id="7c8ff-114">*Linker.xml*:</span></span>

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

<span data-ttu-id="7c8ff-115">Confira mais informações em [Vinculador de IL: sintaxe do descritor de xml](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span><span class="sxs-lookup"><span data-stu-id="7c8ff-115">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>
