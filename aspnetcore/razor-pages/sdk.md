---
title: SDK do Razor do ASP.NET Core
author: Rick-Anderson
description: Saiba como as Páginas Razor no ASP.NET Core tornam a codificação de cenários centrados em página mais fácil e mais produtiva do que com o uso de MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/18/2019
uid: razor-pages/sdk
ms.openlocfilehash: fa69e4840377e0c1c8291c7ba9305a27bd3e6b82
ms.sourcegitcommit: 516f166c5f7cec54edf3d9c71e6e2ba53fb3b0e5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67196369"
---
# <a name="aspnet-core-razor-sdk"></a>SDK do Razor do ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Visão geral

O [!INCLUDE[](~/includes/2.1-SDK.md)] inclui o `Microsoft.NET.Sdk.Razor` MSBuild SDK (SDK do Razor). O SDK do Razor:

* Padroniza a experiência de criação, empacotamento e publicação de projetos que contêm arquivos [Razor](xref:mvc/views/razor) para projetos baseados no ASP.NET Core MVC.
* Inclui um conjunto de destinos, propriedades e itens predefinidos que permitem personalizar a compilação de arquivos Razor.

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

O SDK do Razor inclui um `<Content>` elemento com um `Include` atributo definido como o `**\*.cshtml` padrão glob. Arquivos correspondentes são publicados.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Inclui o SDK do Razor `<Content>` elementos com `Include` os atributos definidos para o `**\*.cshtml` e `**\*.razor` padrões glob. Arquivos correspondentes são publicados.

::: moniker-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Usar o SDK do Razor

A maioria dos aplicativos web não são necessárias para referenciar explicitamente o SDK do Razor.

Para usar o SDK do Razor para criar bibliotecas de classe contendo exibições Razor ou Páginas Razor:

* Use `Microsoft.NET.Sdk.Razor` em vez de `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Normalmente, uma referência de pacote para `Microsoft.AspNetCore.Mvc` é necessário para receber dependências adicionais que são necessárias para criar e compilar páginas Razor e exibições do Razor. No mínimo, seu projeto deve adicionar referências de pacote para:

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  O `Microsoft.AspNetCore.Razor.Design` pacote fornece os destinos e tarefas de compilação do Razor para o projeto.

  Os pacotes anteriores são incluídos em `Microsoft.AspNetCore.Mvc`. A marcação a seguir mostra um arquivo de projeto que usa o SDK do Razor para criar arquivos Razor para um aplicativo páginas Razor do ASP.NET Core:
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> O `Microsoft.AspNetCore.Razor.Design` e `Microsoft.AspNetCore.Mvc.Razor.Extensions` pacotes são incluídos na [metapacote Microsoft](xref:fundamentals/metapackage-app). No entanto, o menor de versão `Microsoft.AspNetCore.App` referência de pacote fornece um metapacote para o aplicativo que não inclui a versão mais recente do `Microsoft.AspNetCore.Razor.Design`. Projetos devem fazer referência a uma versão consistente do `Microsoft.AspNetCore.Razor.Design` (ou `Microsoft.AspNetCore.Mvc`) para que as correções mais recentes do tempo de compilação para Razor são incluídas. Para obter mais informações, consulte [esse problema de GitHub](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Propriedades

As seguintes propriedades controlam o comportamento do SDK do Razor como parte de um build de projeto:

* `RazorCompileOnBuild` &ndash; Quando `true`, compila e emite o assembly Razor como parte da criação do projeto. Assume o padrão de `true`.
* `RazorCompileOnPublish` &ndash; Quando `true`, compila e emite o assembly Razor como parte da publicação do projeto. Assume o padrão de `true`.

As propriedades e os itens na tabela a seguir são usados para configurar entradas e saídas para o SDK do Razor.

| Itens | Descrição |
| ----- | ----------- |
| `RazorGenerate` | Elementos de item (arquivos *.cshtml*) que são entradas para os destinos de geração de código. |
| `RazorCompile` | Elementos de item ( *. CS* arquivos) que são entradas para os destinos de compilação do Razor. Use esta `ItemGroup` para especificar arquivos adicionais a serem compilados no assembly Razor. |
| `RazorTargetAssemblyAttribute` | Os elementos de item usados para a codificação geram atributos para o assembly Razor. Por exemplo:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Elementos de item adicionados como recursos incorporados ao assembly Razor gerado. |

| Propriedade | Descrição |
| -------- | ----------- |
| `RazorTargetName` | Nome do arquivo (sem extensão) do assembly produzido pelo Razor. | 
| `RazorOutputPath` | O diretório de saída do Razor. |
| `RazorCompileToolset` | Usado para determinar o conjunto de ferramentas usado para criar o assembly do Razor. Os valores válidos são `Implicit`, `RazorSDK` e `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | O padrão é `true`. Quando `true`, inclui *Web. config*, *. JSON*, e *. cshtml* arquivos como o conteúdo do projeto. Quando referenciado por meio `Microsoft.NET.Sdk.Web`, arquivos sob *wwwroot* e arquivos de configuração também estão incluídos. |
| `EnableDefaultRazorGenerateItems` | Quando `true`, inclui arquivos *.cshtml* de itens de `Content` em itens de `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Quando `true`, gera uma *. CS* arquivo que contém atributos especificados por `RazorAssemblyAttribute` e inclui o arquivo na saída da compilação. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Quando `true`, adiciona um conjunto padrão de atributos de assembly em `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Quando `true`, cópias `RazorGenerate` itens ( *. cshtml*) arquivos para o diretório de publicação. Normalmente, arquivos do Razor não são necessários para um aplicativo publicado se eles participam da compilação em tempo de compilação ou tempo de publicação. Assume o padrão de `false`. |
| `CopyRefAssembliesToPublishDirectory` | Quando `true`, copia os itens do assembly de referência no diretório de publicação. Normalmente, os assemblies de referência não são necessários para um aplicativo publicado se a compilação do Razor ocorre em tempo de compilação ou tempo de publicação. Definido como `true` se seu aplicativo publicado requer a compilação de tempo de execução. Por exemplo, defina o valor como `true` se o aplicativo modifica *. cshtml* arquivos em tempo de execução ou usa exibições inseridas. Assume o padrão de `false`. |
| `IncludeRazorContentInPack` | Quando `true`, todos os itens de conteúdo do Razor ( *. cshtml* arquivos) são marcados para inclusão no pacote do NuGet gerado. Assume o padrão de `false`. |
| `EmbedRazorGenerateSources` | Quando `true`, adiciona itens de RazorGenerate ( *.cshtml*) como arquivos incorporados ao assembly Razor gerado. Assume o padrão de `false`. |
| `UseRazorBuildServer` | Quando `true`, usa um processo de servidor de build persistente para descarregar o trabalho de geração de código. Seu valor padrão é `UseSharedCompilation`. |

Para saber mais sobre as propriedades, confira [Propriedades do MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Destinos

O SDK do Razor define dois destinos primários:

* `RazorGenerate` &ndash; Gera código *. CS* arquivos de `RazorGenerate` elementos de item. Use a propriedade `RazorGenerateDependsOn` para especificar destinos adicionais que podem ser executados antes ou depois desse destino.
* `RazorCompile` &ndash; Compila gerada *. CS* arquivos em um assembly Razor. Use `RazorCompileDependsOn` para especificar destinos adicionais que podem ser executados antes ou depois desse destino.

### <a name="runtime-compilation-of-razor-views"></a>Compilação de tempo de execução de modos de exibição do Razor

* Por padrão, o SDK do Razor não publica assemblies de referência que são necessários para realizar compilação no tempo de execução. Isso resulta em falhas de compilação quando o modelo de aplicativo se baseia na compilação em tempo de execução&mdash;, por exemplo, o aplicativo usa exibições inseridas ou muda as exibições depois que o aplicativo é publicado. Defina `CopyRefAssembliesToPublishDirectory` como `true` para continuar publicando assemblies de referência.

* Para um aplicativo web, verifique se seu aplicativo for direcionado a `Microsoft.NET.Sdk.Web` SDK.

## <a name="razor-language-version"></a>Versão da linguagem Razor

Ao direcionar o `Microsoft.NET.Sdk.Web` SDK, a versão da linguagem Razor é inferida da versão do framework de destino do aplicativo. Para projetos direcionados para o `Microsoft.NET.Sdk.Razor` SDK ou no caso raro em que o aplicativo requer uma versão de idioma diferente do Razor que o valor inferido, uma versão pode ser configurada definindo o `<RazorLangVersion>` propriedade no arquivo de projeto do aplicativo:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

## <a name="additional-resources"></a>Recursos adicionais

* [Adições ao formato csproj para o .NET Core](/dotnet/core/tools/csproj)
* [Itens de projeto comuns do MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
