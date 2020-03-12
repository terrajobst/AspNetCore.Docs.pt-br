---
title: Configurar o vinculador para ASP.NET Core Blazor
author: guardrex
description: Saiba como controlar o vinculador de linguagem intermediária (IL) ao criar um aplicativo Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/10/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: b08ec26fb8d139223c57774600bc3cb19a56ac49
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083293"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>Configurar o vinculador para o Blazor do ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Webassembly mais elaborado executa vinculação de [Il (linguagem intermediária)](/dotnet/standard/managed-code#intermediate-language--execution) durante uma compilação para cortar o Il desnecessário dos assemblies de saída do aplicativo. O vinculador está desabilitado ao compilar na configuração de depuração. Os aplicativos devem compilar na configuração de versão para habilitar o vinculador. É recomendável criar em versão ao implantar seus aplicativos Webassembly mais podestas. 

A vinculação de um aplicativo otimiza o tamanho, mas pode ter efeitos prejudiciais. Os aplicativos que usam reflexão ou recursos dinâmicos relacionados podem interromper quando cortados porque o vinculador não conhece esse comportamento dinâmico e não pode determinar em geral quais tipos são necessários para reflexão em tempo de execução. Para cortar esses aplicativos, o vinculador deve ser informado sobre quaisquer tipos exigidos pela reflexão no código e em pacotes ou estruturas das quais o aplicativo depende. 

Para garantir que o aplicativo cortado funcione corretamente depois de implantado, é importante testar as compilações de versão do aplicativo com frequência durante o desenvolvimento.

A vinculação de aplicativos mais avançados pode ser configurada usando estes recursos do MSBuild:

* Configure o vínculo global com uma [Propriedade do MSBuild](#control-linking-with-an-msbuild-property).
* Controle a vinculação por assembly usando um [arquivo de configuração](#control-linking-with-a-configuration-file).

## <a name="control-linking-with-an-msbuild-property"></a>Vinculação de controle com uma propriedade do MSBuild

A vinculação é habilitada quando um aplicativo é criado no `Release` configuração. Para alterar isso, configure a propriedade `BlazorWebAssemblyEnableLinking` MSBuild no arquivo de projeto:

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
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

Por padrão, a configuração do vinculador de mais de um revelador para aplicativos Webassembly mais poseriais retira informações de internacionalização, exceto pelas localidades explicitamente solicitadas. Remover esses assemblies minimiza o tamanho do aplicativo.

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
| `none` (padrão) | Nenhum                    |
| `other`          | *Nacional. Outro. dll*        |
| `rare`           | *Nacional. Raras. dll*         |
| `west`           | *Nacional. West. dll*         |

Use uma vírgula para separar vários valores (por exemplo, `mideast,west`).

Para obter mais informações, consulte [i18n: Pnetlib Internacionalization Framework library (repositório do GitHub mono/mono)](https://github.com/mono/mono/tree/master/mcs/class/I18N).
