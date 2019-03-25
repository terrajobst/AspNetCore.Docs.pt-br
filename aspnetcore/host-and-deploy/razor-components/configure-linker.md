---
title: Configurar o Vinculador para o Blazor
author: guardrex
description: Saiba como controlar o Vinculador de IL (Linguagem Intermediária) ao criar um aplicativo Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2019
uid: host-and-deploy/razor-components/configure-linker
ms.openlocfilehash: c73c972e22a51842c5d8dd209b7e1ed987f9090d
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58207925"
---
# <a name="configure-the-linker-for-blazor"></a>Configurar o Vinculador para o Blazor

Por [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

O Blazor executa a vinculação de [IL (linguagem intermediária)](/dotnet/standard/managed-code#intermediate-language--execution) durante cada build do Modo de Versão para remover IL desnecessária dos assemblies de saída do aplicativo.

Controle a vinculação do assembly com uma das seguintes abordagens:

* Desabilite a vinculação globalmente com uma [propriedade MSBuild](#disable-linking-with-a-msbuild-property).
* Controle a vinculação por assembly usando um [arquivo de configuração](#control-linking-with-a-configuration-file).

## <a name="disable-linking-with-a-msbuild-property"></a>Desabilitar a vinculação com uma propriedade MSBuild

A vinculação é habilitada por padrão no Modo de Versão quando um aplicativo é criado, o que inclui publicação. Para desabilitar a vinculação para todos os assemblies, defina a propriedade `<BlazorLinkOnBuild>` MSBuild como `false` no arquivo de projeto:

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Controlar a vinculação com um arquivo de configuração

Controle a vinculação por assembly fornecendo um arquivo de configuração XML e especificando o arquivo como um item MSBuild no arquivo de projeto:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

*Linker.xml*:

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

Confira mais informações em [Vinculador de IL: sintaxe do descritor de xml](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).
