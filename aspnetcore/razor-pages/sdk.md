---
title: SDK do Razor do ASP.NET Core
author: Rick-Anderson
description: Saiba como as Páginas Razor no ASP.NET Core tornam a codificação de cenários centrados em página mais fácil e mais produtiva do que com o uso de MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 2284131ce2d45ec6bc01ce38f91e2c951b108605
ms.sourcegitcommit: f3b1bcfd108e5d53f73abc0bf2555890869d953b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80321005"
---
# <a name="aspnet-core-razor-sdk"></a>SDK do Razor do ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Visão geral

O [!INCLUDE[](~/includes/2.1-SDK.md)] inclui o SDK do MSBuild do `Microsoft.NET.Sdk.Razor` (SDK do Razor). O SDK do Razor:

::: moniker range=">= aspnetcore-3.0"

* É necessário para compilar, empacotar e publicar projetos que contenham arquivos [Razor](xref:mvc/views/razor) [para projetos do](xref:blazor/index) ASP.NET Core com base em MVC ou mais.
* Inclui um conjunto de destinos, propriedades e itens predefinidos que permitem personalizar a compilação de arquivos Razor ( *. cshtml* ou *. Razor*).

O SDK do Razor inclui `Content` itens com `Include` atributos definidos para os padrões de mascaramento `**\*.cshtml` e `**\*.razor`. Os arquivos correspondentes são publicados.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Padroniza a experiência de criação, empacotamento e publicação de projetos que contêm arquivos [Razor](xref:mvc/views/razor) para projetos baseados no ASP.NET Core MVC.
* Inclui um conjunto de destinos, propriedades e itens predefinidos que permitem personalizar a compilação de arquivos Razor.

O SDK do Razor inclui um `Content` item com um atributo `Include` definido como o padrão de mascaramento `**\*.cshtml`. Os arquivos correspondentes são publicados.

::: moniker-end

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Usar o SDK do Razor

A maioria dos aplicativos web não são necessárias para referenciar explicitamente o SDK do Razor.

::: moniker range=">= aspnetcore-3.0"

Para usar o SDK do Razor para criar bibliotecas de classes que contêm exibições do Razor ou Razor Pages, é recomendável começar com o modelo de projeto RCL (biblioteca de classes Razor). Um RCL que é usado para criar arquivos de mais e ( *. Razor*) requer minimamente uma referência ao pacote [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . Um RCL que é usado para criar exibições ou páginas do Razor (arquivos *. cshtml* ) requer minimamente o direcionamento `netcoreapp3.0` ou posterior e tem uma `FrameworkReference` ao [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) em seu arquivo de projeto.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Para usar o SDK do Razor para criar bibliotecas de classe contendo exibições Razor ou Páginas Razor:

* Use `Microsoft.NET.Sdk.Razor` em vez de `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Normalmente, uma referência de pacote a `Microsoft.AspNetCore.Mvc` é necessária para receber dependências adicionais que são necessárias para compilar e compilar Razor Pages e exibições do Razor. No mínimo, seu projeto deve adicionar referências de pacote para:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  O pacote de `Microsoft.AspNetCore.Razor.Design` fornece as tarefas de compilação do Razor e os destinos para o projeto.

  Os pacotes anteriores são incluídos em `Microsoft.AspNetCore.Mvc`. A marcação a seguir mostra um arquivo de projeto que usa o SDK do Razor para criar arquivos Razor para um aplicativo páginas Razor do ASP.NET Core:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Os pacotes `Microsoft.AspNetCore.Razor.Design` e `Microsoft.AspNetCore.Mvc.Razor.Extensions` estão incluídos no [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app). No entanto, a referência de pacote sem versão `Microsoft.AspNetCore.App` fornece um metapacote para o aplicativo que não inclui a versão mais recente do `Microsoft.AspNetCore.Razor.Design`. Os projetos devem fazer referência a uma versão consistente do `Microsoft.AspNetCore.Razor.Design` (ou `Microsoft.AspNetCore.Mvc`) para que as correções de tempo de compilação mais recentes para o Razor sejam incluídas. Saiba mais neste [tópico do GitHub](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>{1&gt;Propriedades&lt;1}

As seguintes propriedades controlam o comportamento do SDK do Razor como parte de um build de projeto:

* `RazorCompileOnBuild` &ndash; quando `true`, compila e emite o assembly Razor como parte da criação do projeto. Usa `true` como padrão.
* `RazorCompileOnPublish` &ndash; quando `true`, compila e emite o assembly Razor como parte da publicação do projeto. Usa `true` como padrão.

As propriedades e os itens na tabela a seguir são usados para configurar entradas e saídas para o SDK do Razor.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> A partir do ASP.NET Core 3,0, as exibições do MVC ou Razor Pages não são servidas por padrão se as propriedades `RazorCompileOnBuild` ou `RazorCompileOnPublish` do MSBuild no arquivo de projeto estão desabilitadas. Os aplicativos devem adicionar uma referência explícita ao pacote [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) se o aplicativo depender da compilação de tempo de execução para processar arquivos *. cshtml* .

::: moniker-end

| Itens | Descrição |
| ----- | ----------- |
| `RazorGenerate` | Elementos de item (arquivos *. cshtml* ) que são entradas para geração de código. |
| `RazorComponent` | Elementos de item (arquivos *. Razor* ) que são entradas para a geração de código de componente Razor. |
| `RazorCompile` | Elementos de item (arquivos *. cs* ) que são entradas para destinos de compilação do Razor. Use esta `ItemGroup` para especificar arquivos adicionais a serem compilados no assembly Razor. |
| `RazorTargetAssemblyAttribute` | Os elementos de item usados para a codificação geram atributos para o assembly Razor. Por exemplo:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Elementos de item adicionados como recursos incorporados ao assembly Razor gerado. |

::: moniker range=">= aspnetcore-3.0"

| Propriedade | Descrição |
| -------- | ----------- |
| `RazorTargetName` | Nome do arquivo (sem extensão) do assembly produzido pelo Razor. |
| `RazorOutputPath` | O diretório de saída do Razor. |
| `RazorCompileToolset` | Usado para determinar o conjunto de ferramentas usado para criar o assembly do Razor. Os valores válidos são `Implicit`, `RazorSDK` e `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | O padrão é `true`. Quando `true`, inclui arquivos *Web. config*, *. JSON*e *. cshtml* como conteúdo no projeto. Quando referenciado via `Microsoft.NET.Sdk.Web`, os arquivos em arquivos *wwwroot* e config também são incluídos. |
| `EnableDefaultRazorGenerateItems` | Quando `true`, inclui arquivos *.cshtml* de itens de `Content` em itens de `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Quando `true`, o gera um arquivo *. cs* contendo atributos especificados por `RazorAssemblyAttribute` e inclui o arquivo na saída de compilação. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Quando `true`, adiciona um conjunto padrão de atributos de assembly em `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Quando `true`, o copia os arquivos de `RazorGenerate` itens ( *. cshtml*) para o diretório de publicação. Normalmente, arquivos do Razor não são necessários para um aplicativo publicado se eles participam da compilação em tempo de compilação ou tempo de publicação. Usa `false` como padrão. |
| `PreserveCompilationReferences` | Quando `true`, copia os itens do assembly de referência no diretório de publicação. Normalmente, os assemblies de referência não são necessários para um aplicativo publicado se a compilação do Razor ocorre em tempo de compilação ou tempo de publicação. Defina como `true` se seu aplicativo publicado exigir compilação em tempo de execução. Por exemplo, defina o valor como `true` se o aplicativo modificar arquivos *. cshtml* em tempo de execução ou usar exibições inseridas. Usa `false` como padrão. |
| `IncludeRazorContentInPack` | Quando `true`, todos os itens de conteúdo do Razor (arquivos *. cshtml* ) são marcados para inclusão no pacote NuGet gerado. Usa `false` como padrão. |
| `EmbedRazorGenerateSources` | Quando `true`, adiciona itens de RazorGenerate ( *.cshtml*) como arquivos incorporados ao assembly Razor gerado. Usa `false` como padrão. |
| `UseRazorBuildServer` | Quando `true`, usa um processo de servidor de build persistente para descarregar o trabalho de geração de código. Seu valor padrão é `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Quando `true`, o SDK gera atributos adicionais usados pelo MVC em tempo de execução para executar a descoberta de parte do aplicativo. |
| `DefaultWebContentItemExcludes` | Um padrão de mascaramento para elementos de item que devem ser excluídos do `Content` grupo de itens em projetos direcionados para o SDK Web ou Razor |
| `ExcludeConfigFilesFromBuildOutput` | Quando os arquivos `true`, *. config* e *. JSON* não são copiados para o diretório de saída da compilação. |
| `AddRazorSupportForMvc` | Quando `true`, o configura o SDK do Razor para adicionar suporte para a configuração do MVC que é necessária ao criar aplicativos que contenham exibições do MVC ou Razor Pages. Essa propriedade é definida implicitamente para projetos .NET Core 3,0 ou posterior direcionados para o SDK da Web |
| `RazorLangVersion` | A versão do idioma do Razor para o destino. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Propriedade | Descrição |
| -------- | ----------- |
| `RazorTargetName` | Nome do arquivo (sem extensão) do assembly produzido pelo Razor. |
| `RazorOutputPath` | O diretório de saída do Razor. |
| `RazorCompileToolset` | Usado para determinar o conjunto de ferramentas usado para criar o assembly do Razor. Os valores válidos são `Implicit`, `RazorSDK` e `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | O padrão é `true`. Quando `true`, inclui arquivos *Web. config*, *. JSON*e *. cshtml* como conteúdo no projeto. Quando referenciado via `Microsoft.NET.Sdk.Web`, os arquivos em arquivos *wwwroot* e config também são incluídos. |
| `EnableDefaultRazorGenerateItems` | Quando `true`, inclui arquivos *.cshtml* de itens de `Content` em itens de `RazorGenerate`. |
| `GenerateRazorTargetAssemblyInfo` | Quando `true`, o gera um arquivo *. cs* contendo atributos especificados por `RazorAssemblyAttribute` e inclui o arquivo na saída de compilação. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Quando `true`, adiciona um conjunto padrão de atributos de assembly em `RazorAssemblyAttribute`. |
| `CopyRazorGenerateFilesToPublishDirectory` | Quando `true`, o copia os arquivos de `RazorGenerate` itens ( *. cshtml*) para o diretório de publicação. Normalmente, arquivos do Razor não são necessários para um aplicativo publicado se eles participam da compilação em tempo de compilação ou tempo de publicação. Usa `false` como padrão. |
| `CopyRefAssembliesToPublishDirectory` | Quando `true`, copia os itens do assembly de referência no diretório de publicação. Normalmente, os assemblies de referência não são necessários para um aplicativo publicado se a compilação do Razor ocorre em tempo de compilação ou tempo de publicação. Defina como `true` se seu aplicativo publicado exigir compilação em tempo de execução. Por exemplo, defina o valor como `true` se o aplicativo modificar arquivos *. cshtml* em tempo de execução ou usar exibições inseridas. Usa `false` como padrão. |
| `IncludeRazorContentInPack` | Quando `true`, todos os itens de conteúdo do Razor (arquivos *. cshtml* ) são marcados para inclusão no pacote NuGet gerado. Usa `false` como padrão. |
| `EmbedRazorGenerateSources` | Quando `true`, adiciona itens de RazorGenerate ( *.cshtml*) como arquivos incorporados ao assembly Razor gerado. Usa `false` como padrão. |
| `UseRazorBuildServer` | Quando `true`, usa um processo de servidor de build persistente para descarregar o trabalho de geração de código. Seu valor padrão é `UseSharedCompilation`. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Quando `true`, o SDK gera atributos adicionais usados pelo MVC em tempo de execução para executar a descoberta de parte do aplicativo. |
| `DefaultWebContentItemExcludes` | Um padrão de mascaramento para elementos de item que devem ser excluídos do `Content` grupo de itens em projetos direcionados para o SDK Web ou Razor |
| `ExcludeConfigFilesFromBuildOutput` | Quando os arquivos `true`, *. config* e *. JSON* não são copiados para o diretório de saída da compilação. |
| `AddRazorSupportForMvc` | Quando `true`, o configura o SDK do Razor para adicionar suporte para a configuração do MVC que é necessária ao criar aplicativos que contenham exibições do MVC ou Razor Pages. Essa propriedade é definida implicitamente para projetos .NET Core 3,0 ou posterior direcionados para o SDK da Web |
| `RazorLangVersion` | A versão do idioma do Razor para o destino. |

::: moniker-end

Para saber mais sobre as propriedades, confira [Propriedades do MSBuild](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Destinos

O SDK do Razor define dois destinos primários:

* `RazorGenerate` &ndash; código gera arquivos *. cs* de elementos `RazorGenerate` item. Use a propriedade `RazorGenerateDependsOn` para especificar destinos adicionais que podem ser executados antes ou depois desse destino.
* `RazorCompile` &ndash; compila arquivos *. cs* gerados em um assembly Razor. Use o `RazorCompileDependsOn` para especificar destinos adicionais que podem ser executados antes ou depois desse destino.
* `RazorComponentGenerate` &ndash; código gera arquivos *. cs* para elementos de `RazorComponent` item. Use a propriedade `RazorComponentGenerateDependsOn` para especificar destinos adicionais que podem ser executados antes ou depois desse destino.

### <a name="runtime-compilation-of-razor-views"></a>Compilação de runtime de modos de exibição do Razor

* Por padrão, o SDK do Razor não publica assemblies de referência que são necessários para realizar compilação no runtime. Isso resulta em falhas de compilação quando o modelo de aplicativo se baseia na compilação em runtime&mdash;, por exemplo, o aplicativo usa exibições inseridas ou muda as exibições depois que o aplicativo é publicado. Defina `CopyRefAssembliesToPublishDirectory` como `true` para continuar publicando assemblies de referência.

* Para um aplicativo Web, verifique se seu aplicativo está direcionando o SDK do `Microsoft.NET.Sdk.Web`.

## <a name="razor-language-version"></a>Versão do idioma Razor

Ao direcionar o SDK do `Microsoft.NET.Sdk.Web`, a versão do idioma do Razor é inferida a partir da versão da estrutura de destino do aplicativo. Para projetos direcionados ao SDK do `Microsoft.NET.Sdk.Razor` ou no caso raro de o aplicativo exigir uma versão diferente da linguagem Razor do que o valor inferido, uma versão pode ser configurada definindo a propriedade `<RazorLangVersion>` no arquivo de projeto do aplicativo:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

A versão do idioma do Razor está totalmente integrada à versão do tempo de execução para a qual foi criada. O direcionamento de uma versão de idioma que não é projetada para o tempo de execução não é suportado e provavelmente produz erros de compilação.

## <a name="additional-resources"></a>Recursos adicionais

* [Adições ao formato csproj para o .NET Core](/dotnet/core/tools/csproj)
* [Itens de projeto comuns do MSBuild](/visualstudio/msbuild/common-msbuild-project-items)
