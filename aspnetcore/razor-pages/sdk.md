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
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="4722d-103">SDK do Razor do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4722d-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="4722d-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4722d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="4722d-105">Visão geral</span><span class="sxs-lookup"><span data-stu-id="4722d-105">Overview</span></span>

<span data-ttu-id="4722d-106">O [!INCLUDE[](~/includes/2.1-SDK.md)] inclui o `Microsoft.NET.Sdk.Razor` MSBuild SDK (SDK do Razor).</span><span class="sxs-lookup"><span data-stu-id="4722d-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="4722d-107">O SDK do Razor:</span><span class="sxs-lookup"><span data-stu-id="4722d-107">The Razor SDK:</span></span>

* <span data-ttu-id="4722d-108">Padroniza a experiência de criação, empacotamento e publicação de projetos que contêm arquivos [Razor](xref:mvc/views/razor) para projetos baseados no ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="4722d-108">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="4722d-109">Inclui um conjunto de destinos, propriedades e itens predefinidos que permitem personalizar a compilação de arquivos Razor.</span><span class="sxs-lookup"><span data-stu-id="4722d-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="4722d-110">O SDK do Razor inclui um `<Content>` elemento com um `Include` atributo definido como o `**\*.cshtml` padrão glob.</span><span class="sxs-lookup"><span data-stu-id="4722d-110">The Razor SDK includes a `<Content>` element with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="4722d-111">Arquivos correspondentes são publicados.</span><span class="sxs-lookup"><span data-stu-id="4722d-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4722d-112">Inclui o SDK do Razor `<Content>` elementos com `Include` os atributos definidos para o `**\*.cshtml` e `**\*.razor` padrões glob.</span><span class="sxs-lookup"><span data-stu-id="4722d-112">The Razor SDK includes `<Content>` elements with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="4722d-113">Arquivos correspondentes são publicados.</span><span class="sxs-lookup"><span data-stu-id="4722d-113">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="4722d-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="4722d-114">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="4722d-115">Usar o SDK do Razor</span><span class="sxs-lookup"><span data-stu-id="4722d-115">Use the Razor SDK</span></span>

<span data-ttu-id="4722d-116">A maioria dos aplicativos web não são necessárias para referenciar explicitamente o SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="4722d-116">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

<span data-ttu-id="4722d-117">Para usar o SDK do Razor para criar bibliotecas de classe contendo exibições Razor ou Páginas Razor:</span><span class="sxs-lookup"><span data-stu-id="4722d-117">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="4722d-118">Use `Microsoft.NET.Sdk.Razor` em vez de `Microsoft.NET.Sdk`:</span><span class="sxs-lookup"><span data-stu-id="4722d-118">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="4722d-119">Normalmente, uma referência de pacote para `Microsoft.AspNetCore.Mvc` é necessário para receber dependências adicionais que são necessárias para criar e compilar páginas Razor e exibições do Razor.</span><span class="sxs-lookup"><span data-stu-id="4722d-119">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="4722d-120">No mínimo, seu projeto deve adicionar referências de pacote para:</span><span class="sxs-lookup"><span data-stu-id="4722d-120">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  <span data-ttu-id="4722d-121">O `Microsoft.AspNetCore.Razor.Design` pacote fornece os destinos e tarefas de compilação do Razor para o projeto.</span><span class="sxs-lookup"><span data-stu-id="4722d-121">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="4722d-122">Os pacotes anteriores são incluídos em `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="4722d-122">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="4722d-123">A marcação a seguir mostra um arquivo de projeto que usa o SDK do Razor para criar arquivos Razor para um aplicativo páginas Razor do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4722d-123">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="4722d-124">O `Microsoft.AspNetCore.Razor.Design` e `Microsoft.AspNetCore.Mvc.Razor.Extensions` pacotes são incluídos na [metapacote Microsoft](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="4722d-124">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="4722d-125">No entanto, o menor de versão `Microsoft.AspNetCore.App` referência de pacote fornece um metapacote para o aplicativo que não inclui a versão mais recente do `Microsoft.AspNetCore.Razor.Design`.</span><span class="sxs-lookup"><span data-stu-id="4722d-125">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="4722d-126">Projetos devem fazer referência a uma versão consistente do `Microsoft.AspNetCore.Razor.Design` (ou `Microsoft.AspNetCore.Mvc`) para que as correções mais recentes do tempo de compilação para Razor são incluídas.</span><span class="sxs-lookup"><span data-stu-id="4722d-126">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="4722d-127">Para obter mais informações, consulte [esse problema de GitHub](https://github.com/aspnet/Razor/issues/2553).</span><span class="sxs-lookup"><span data-stu-id="4722d-127">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="4722d-128">Propriedades</span><span class="sxs-lookup"><span data-stu-id="4722d-128">Properties</span></span>

<span data-ttu-id="4722d-129">As seguintes propriedades controlam o comportamento do SDK do Razor como parte de um build de projeto:</span><span class="sxs-lookup"><span data-stu-id="4722d-129">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="4722d-130">`RazorCompileOnBuild` &ndash; Quando `true`, compila e emite o assembly Razor como parte da criação do projeto.</span><span class="sxs-lookup"><span data-stu-id="4722d-130">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="4722d-131">Assume o padrão de `true`.</span><span class="sxs-lookup"><span data-stu-id="4722d-131">Defaults to `true`.</span></span>
* <span data-ttu-id="4722d-132">`RazorCompileOnPublish` &ndash; Quando `true`, compila e emite o assembly Razor como parte da publicação do projeto.</span><span class="sxs-lookup"><span data-stu-id="4722d-132">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="4722d-133">Assume o padrão de `true`.</span><span class="sxs-lookup"><span data-stu-id="4722d-133">Defaults to `true`.</span></span>

<span data-ttu-id="4722d-134">As propriedades e os itens na tabela a seguir são usados para configurar entradas e saídas para o SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="4722d-134">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

| <span data-ttu-id="4722d-135">Itens</span><span class="sxs-lookup"><span data-stu-id="4722d-135">Items</span></span> | <span data-ttu-id="4722d-136">Descrição</span><span class="sxs-lookup"><span data-stu-id="4722d-136">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="4722d-137">Elementos de item (arquivos *.cshtml*) que são entradas para os destinos de geração de código.</span><span class="sxs-lookup"><span data-stu-id="4722d-137">Item elements (*.cshtml* files) that are inputs to code generation targets.</span></span> |
| `RazorCompile` | <span data-ttu-id="4722d-138">Elementos de item ( *. CS* arquivos) que são entradas para os destinos de compilação do Razor.</span><span class="sxs-lookup"><span data-stu-id="4722d-138">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="4722d-139">Use esta `ItemGroup` para especificar arquivos adicionais a serem compilados no assembly Razor.</span><span class="sxs-lookup"><span data-stu-id="4722d-139">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="4722d-140">Os elementos de item usados para a codificação geram atributos para o assembly Razor.</span><span class="sxs-lookup"><span data-stu-id="4722d-140">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="4722d-141">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="4722d-141">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="4722d-142">Elementos de item adicionados como recursos incorporados ao assembly Razor gerado.</span><span class="sxs-lookup"><span data-stu-id="4722d-142">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="4722d-143">Propriedade</span><span class="sxs-lookup"><span data-stu-id="4722d-143">Property</span></span> | <span data-ttu-id="4722d-144">Descrição</span><span class="sxs-lookup"><span data-stu-id="4722d-144">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="4722d-145">Nome do arquivo (sem extensão) do assembly produzido pelo Razor.</span><span class="sxs-lookup"><span data-stu-id="4722d-145">File name (without extension) of the assembly produced by Razor.</span></span> | 
| `RazorOutputPath` | <span data-ttu-id="4722d-146">O diretório de saída do Razor.</span><span class="sxs-lookup"><span data-stu-id="4722d-146">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="4722d-147">Usado para determinar o conjunto de ferramentas usado para criar o assembly do Razor.</span><span class="sxs-lookup"><span data-stu-id="4722d-147">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="4722d-148">Os valores válidos são `Implicit`, `RazorSDK` e `PrecompilationTool`.</span><span class="sxs-lookup"><span data-stu-id="4722d-148">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="4722d-149">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="4722d-149">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="4722d-150">O padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="4722d-150">Default is `true`.</span></span> <span data-ttu-id="4722d-151">Quando `true`, inclui *Web. config*, *. JSON*, e *. cshtml* arquivos como o conteúdo do projeto.</span><span class="sxs-lookup"><span data-stu-id="4722d-151">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="4722d-152">Quando referenciado por meio `Microsoft.NET.Sdk.Web`, arquivos sob *wwwroot* e arquivos de configuração também estão incluídos.</span><span class="sxs-lookup"><span data-stu-id="4722d-152">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="4722d-153">Quando `true`, inclui arquivos *.cshtml* de itens de `Content` em itens de `RazorGenerate`.</span><span class="sxs-lookup"><span data-stu-id="4722d-153">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="4722d-154">Quando `true`, gera uma *. CS* arquivo que contém atributos especificados por `RazorAssemblyAttribute` e inclui o arquivo na saída da compilação.</span><span class="sxs-lookup"><span data-stu-id="4722d-154">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="4722d-155">Quando `true`, adiciona um conjunto padrão de atributos de assembly em `RazorAssemblyAttribute`.</span><span class="sxs-lookup"><span data-stu-id="4722d-155">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="4722d-156">Quando `true`, cópias `RazorGenerate` itens ( *. cshtml*) arquivos para o diretório de publicação.</span><span class="sxs-lookup"><span data-stu-id="4722d-156">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="4722d-157">Normalmente, arquivos do Razor não são necessários para um aplicativo publicado se eles participam da compilação em tempo de compilação ou tempo de publicação.</span><span class="sxs-lookup"><span data-stu-id="4722d-157">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="4722d-158">Assume o padrão de `false`.</span><span class="sxs-lookup"><span data-stu-id="4722d-158">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="4722d-159">Quando `true`, copia os itens do assembly de referência no diretório de publicação.</span><span class="sxs-lookup"><span data-stu-id="4722d-159">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="4722d-160">Normalmente, os assemblies de referência não são necessários para um aplicativo publicado se a compilação do Razor ocorre em tempo de compilação ou tempo de publicação.</span><span class="sxs-lookup"><span data-stu-id="4722d-160">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="4722d-161">Definido como `true` se seu aplicativo publicado requer a compilação de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="4722d-161">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="4722d-162">Por exemplo, defina o valor como `true` se o aplicativo modifica *. cshtml* arquivos em tempo de execução ou usa exibições inseridas.</span><span class="sxs-lookup"><span data-stu-id="4722d-162">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="4722d-163">Assume o padrão de `false`.</span><span class="sxs-lookup"><span data-stu-id="4722d-163">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="4722d-164">Quando `true`, todos os itens de conteúdo do Razor ( *. cshtml* arquivos) são marcados para inclusão no pacote do NuGet gerado.</span><span class="sxs-lookup"><span data-stu-id="4722d-164">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="4722d-165">Assume o padrão de `false`.</span><span class="sxs-lookup"><span data-stu-id="4722d-165">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="4722d-166">Quando `true`, adiciona itens de RazorGenerate ( *.cshtml*) como arquivos incorporados ao assembly Razor gerado.</span><span class="sxs-lookup"><span data-stu-id="4722d-166">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="4722d-167">Assume o padrão de `false`.</span><span class="sxs-lookup"><span data-stu-id="4722d-167">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="4722d-168">Quando `true`, usa um processo de servidor de build persistente para descarregar o trabalho de geração de código.</span><span class="sxs-lookup"><span data-stu-id="4722d-168">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="4722d-169">Seu valor padrão é `UseSharedCompilation`.</span><span class="sxs-lookup"><span data-stu-id="4722d-169">Defaults to the value of `UseSharedCompilation`.</span></span> |

<span data-ttu-id="4722d-170">Para saber mais sobre as propriedades, confira [Propriedades do MSBuild](/visualstudio/msbuild/msbuild-properties).</span><span class="sxs-lookup"><span data-stu-id="4722d-170">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="4722d-171">Destinos</span><span class="sxs-lookup"><span data-stu-id="4722d-171">Targets</span></span>

<span data-ttu-id="4722d-172">O SDK do Razor define dois destinos primários:</span><span class="sxs-lookup"><span data-stu-id="4722d-172">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="4722d-173">`RazorGenerate` &ndash; Gera código *. CS* arquivos de `RazorGenerate` elementos de item.</span><span class="sxs-lookup"><span data-stu-id="4722d-173">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="4722d-174">Use a propriedade `RazorGenerateDependsOn` para especificar destinos adicionais que podem ser executados antes ou depois desse destino.</span><span class="sxs-lookup"><span data-stu-id="4722d-174">Use `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="4722d-175">`RazorCompile` &ndash; Compila gerada *. CS* arquivos em um assembly Razor.</span><span class="sxs-lookup"><span data-stu-id="4722d-175">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="4722d-176">Use `RazorCompileDependsOn` para especificar destinos adicionais que podem ser executados antes ou depois desse destino.</span><span class="sxs-lookup"><span data-stu-id="4722d-176">Use `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="4722d-177">Compilação de tempo de execução de modos de exibição do Razor</span><span class="sxs-lookup"><span data-stu-id="4722d-177">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="4722d-178">Por padrão, o SDK do Razor não publica assemblies de referência que são necessários para realizar compilação no tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="4722d-178">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="4722d-179">Isso resulta em falhas de compilação quando o modelo de aplicativo se baseia na compilação em tempo de execução&mdash;, por exemplo, o aplicativo usa exibições inseridas ou muda as exibições depois que o aplicativo é publicado.</span><span class="sxs-lookup"><span data-stu-id="4722d-179">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="4722d-180">Defina `CopyRefAssembliesToPublishDirectory` como `true` para continuar publicando assemblies de referência.</span><span class="sxs-lookup"><span data-stu-id="4722d-180">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="4722d-181">Para um aplicativo web, verifique se seu aplicativo for direcionado a `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="4722d-181">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="4722d-182">Versão da linguagem Razor</span><span class="sxs-lookup"><span data-stu-id="4722d-182">Razor language version</span></span>

<span data-ttu-id="4722d-183">Ao direcionar o `Microsoft.NET.Sdk.Web` SDK, a versão da linguagem Razor é inferida da versão do framework de destino do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4722d-183">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the the app's target framework version.</span></span> <span data-ttu-id="4722d-184">Para projetos direcionados para o `Microsoft.NET.Sdk.Razor` SDK ou no caso raro em que o aplicativo requer uma versão de idioma diferente do Razor que o valor inferido, uma versão pode ser configurada definindo o `<RazorLangVersion>` propriedade no arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="4722d-184">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

## <a name="additional-resources"></a><span data-ttu-id="4722d-185">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4722d-185">Additional resources</span></span>

* [<span data-ttu-id="4722d-186">Adições ao formato csproj para o .NET Core</span><span class="sxs-lookup"><span data-stu-id="4722d-186">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="4722d-187">Itens de projeto comuns do MSBuild</span><span class="sxs-lookup"><span data-stu-id="4722d-187">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
