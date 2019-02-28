---
title: Configurar o Vinculador para o Blazor
author: guardrex
description: Saiba como controlar o Vinculador de IL (Linguagem Intermediária) ao criar um aplicativo Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2019
uid: host-and-deploy/razor-components/configure-linker
ms.openlocfilehash: 7c53e7912ec3b0ae471ea38777f874f55a32487d
ms.sourcegitcommit: 0945078a09c372f17e9b003758ed87e99c2449f4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56647935"
---
# <a name="configure-the-linker-for-blazor"></a><span data-ttu-id="aab1b-103">Configurar o Vinculador para o Blazor</span><span class="sxs-lookup"><span data-stu-id="aab1b-103">Configure the Linker for Blazor</span></span>

<span data-ttu-id="aab1b-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="aab1b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="aab1b-105">O Blazor executa a vinculação de [IL (linguagem intermediária)](/dotnet/standard/managed-code#intermediate-language--execution) durante cada build do Modo de Versão para remover IL desnecessária dos assemblies de saída.</span><span class="sxs-lookup"><span data-stu-id="aab1b-105">Blazor performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during each Release mode build to remove unnecessary IL from the output assemblies.</span></span>

<span data-ttu-id="aab1b-106">Você pode controlar a vinculação do assembly com uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="aab1b-106">You can control assembly linking with either of the following approaches:</span></span>

* <span data-ttu-id="aab1b-107">Desabilite a vinculação globalmente com uma propriedade MSBuild.</span><span class="sxs-lookup"><span data-stu-id="aab1b-107">Disable linking globally with an MSBuild property.</span></span>
* <span data-ttu-id="aab1b-108">Controle a vinculação por assembly usando um arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="aab1b-108">Control linking on a per-assembly basis with a configuration file.</span></span>

## <a name="disable-linking-with-an-msbuild-property"></a><span data-ttu-id="aab1b-109">Desabilitar a vinculação com uma propriedade MSBuild</span><span class="sxs-lookup"><span data-stu-id="aab1b-109">Disable linking with an MSBuild property</span></span>

<span data-ttu-id="aab1b-110">A vinculação é habilitada por padrão no Modo de Versão quando um aplicativo é criado, o que inclui publicação.</span><span class="sxs-lookup"><span data-stu-id="aab1b-110">Linking is enabled by default in Release mode when an app is built, which includes publishing.</span></span> <span data-ttu-id="aab1b-111">Para desabilitar a vinculação para todos os assemblies, defina a propriedade `<BlazorLinkOnBuild>` MSBuild como `false` no arquivo de projeto:</span><span class="sxs-lookup"><span data-stu-id="aab1b-111">To disable linking for all assemblies, set the `<BlazorLinkOnBuild>` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="aab1b-112">Controlar a vinculação com um arquivo de configuração</span><span class="sxs-lookup"><span data-stu-id="aab1b-112">Control linking with a configuration file</span></span>

<span data-ttu-id="aab1b-113">A vinculação pode ser controlada por assembly fornecendo um arquivo de configuração XML e especificando o arquivo como um item MSBuild no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="aab1b-113">Linking can be controlled on a per-assembly basis by providing an XML configuration file and specifying the file as an MSBuild item in the project file.</span></span>

<span data-ttu-id="aab1b-114">Veja a seguir um exemplo de arquivo de configuração (*Linker.xml*):</span><span class="sxs-lookup"><span data-stu-id="aab1b-114">The following is an example configuration file (*Linker.xml*):</span></span>

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

<span data-ttu-id="aab1b-115">Para saber mais sobre o formato de arquivo para o arquivo de configuração, veja [Vinculador de IL: sintaxe do descritor de xml](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span><span class="sxs-lookup"><span data-stu-id="aab1b-115">To learn more about the file format for the configuration file, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>

<span data-ttu-id="aab1b-116">Especifique o arquivo de configuração no arquivo de projeto com o item `BlazorLinkerDescriptor`:</span><span class="sxs-lookup"><span data-stu-id="aab1b-116">Specify the configuration file in the project file with the `BlazorLinkerDescriptor` item:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```
