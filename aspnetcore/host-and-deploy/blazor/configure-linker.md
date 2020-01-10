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
ms.openlocfilehash: cdf506f0c0fa720df64e59342d352ef41271d24b
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75866040"
---
# <a name="configure-the-linker-for-aspnet-core-opno-locblazor"></a>Configurar o vinculador para ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor executa a vinculação de [Il (linguagem intermediária)](/dotnet/standard/managed-code#intermediate-language--execution) durante uma compilação para remover o Il desnecessário dos assemblies de saída do aplicativo.

Controle a vinculação do assembly com uma das seguintes abordagens:

* Desabilite a vinculação globalmente com uma [propriedade MSBuild](#disable-linking-with-a-msbuild-property).
* Controle a vinculação por assembly usando um [arquivo de configuração](#control-linking-with-a-configuration-file).

## <a name="disable-linking-with-a-msbuild-property"></a>Desabilitar a vinculação com uma propriedade MSBuild

A vinculação é habilitada por padrão quando um aplicativo é criado, o que inclui a publicação. Para desabilitar a vinculação para todos os assemblies, defina a propriedade `BlazorLinkOnBuild` MSBuild como `false` no arquivo de projeto:

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

Para obter mais informações, consulte [vinculador Il: sintaxe do descritor XML](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).

### <a name="configure-the-linker-for-internationalization"></a>Configurar o vinculador para internacionalização

Por padrão, a configuração do vinculador Blazorpara aplicativos Webassembly Blazor retira informações de internacionalização, exceto as localidades explicitamente solicitadas. Remover esses assemblies minimiza o tamanho do aplicativo.

Para controlar quais assemblies de I18N são retidos, defina a propriedade `<MonoLinkerI18NAssemblies>` MSBuild no arquivo de projeto:

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| Valor da região     | Assembly de região mono    |
| ---------------- | ----------------------- |
| `all`            | Todos os assemblies incluídos |
| `cjk`            | *Nacional. CJK. dll*          |
| `mideast`        | *Nacional. Oriente Médio. dll*      |
| `none` (padrão) | {1&gt;Nenhum&lt;1}                    |
| `other`          | *Nacional. Outro. dll*        |
| `rare`           | *Nacional. Raras. dll*         |
| `west`           | *Nacional. West. dll*         |

Use uma vírgula para separar vários valores (por exemplo, `mideast,west`).

Para obter mais informações, consulte [i18n: Pnetlib Internacionalization Framework biblioteca (repositório GitHub mono/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).
