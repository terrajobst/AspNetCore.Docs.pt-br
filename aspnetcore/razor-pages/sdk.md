---
title: SDK do Razor do ASP.NET Core
author: Rick-Anderson
description: Saiba como as Páginas Razor no ASP.NET Core tornam a codificação de cenários centrados em página mais fácil e mais produtiva do que com o uso de MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/23/2019
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 872d90662494735dc0e4caa01c46fcdcc2606bc6
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76809127"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="a7561-103">SDK do Razor do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7561-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="a7561-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a7561-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="a7561-105">{1&gt;Visão Geral&lt;1}</span><span class="sxs-lookup"><span data-stu-id="a7561-105">Overview</span></span>

<span data-ttu-id="a7561-106">O [!INCLUDE[](~/includes/2.1-SDK.md)] inclui o `Microsoft.NET.Sdk.Razor` MSBuild SDK (SDK do Razor).</span><span class="sxs-lookup"><span data-stu-id="a7561-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="a7561-107">O SDK do Razor:</span><span class="sxs-lookup"><span data-stu-id="a7561-107">The Razor SDK:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="a7561-108">É necessário para compilar, empacotar e publicar projetos que contenham arquivos [Razor](xref:mvc/views/razor) [para projetos do](xref:blazor/index) ASP.NET Core com base em MVC ou mais.</span><span class="sxs-lookup"><span data-stu-id="a7561-108">Is required to build, package, and publish projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based or [Blazor](xref:blazor/index) projects.</span></span>
* <span data-ttu-id="a7561-109">Inclui um conjunto de destinos, propriedades e itens predefinidos que permitem personalizar a compilação de arquivos Razor ( *. cshtml* ou *. Razor*).</span><span class="sxs-lookup"><span data-stu-id="a7561-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor (*.cshtml* or *.razor*) files.</span></span>

<span data-ttu-id="a7561-110">O SDK do Razor inclui `Content` itens com `Include` atributos definidos para os padrões de mascaramento `**\*.cshtml` e `**\*.razor`.</span><span class="sxs-lookup"><span data-stu-id="a7561-110">The Razor SDK includes `Content` items with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="a7561-111">Os arquivos correspondentes são publicados.</span><span class="sxs-lookup"><span data-stu-id="a7561-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="a7561-112">Padroniza a experiência de criação, empacotamento e publicação de projetos que contêm arquivos [Razor](xref:mvc/views/razor) para projetos baseados no ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="a7561-112">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="a7561-113">Inclui um conjunto de destinos, propriedades e itens predefinidos que permitem personalizar a compilação de arquivos Razor.</span><span class="sxs-lookup"><span data-stu-id="a7561-113">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

<span data-ttu-id="a7561-114">O SDK do Razor inclui um `Content` item com um atributo `Include` definido como o padrão de mascaramento `**\*.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="a7561-114">The Razor SDK includes a `Content` item with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="a7561-115">Os arquivos correspondentes são publicados.</span><span class="sxs-lookup"><span data-stu-id="a7561-115">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="a7561-116">{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="a7561-116">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="a7561-117">Usar o SDK do Razor</span><span class="sxs-lookup"><span data-stu-id="a7561-117">Use the Razor SDK</span></span>

<span data-ttu-id="a7561-118">A maioria dos aplicativos web não são necessárias para referenciar explicitamente o SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="a7561-118">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a7561-119">Para usar o SDK do Razor para criar bibliotecas de classes que contêm exibições do Razor ou Razor Pages, é recomendável começar com o modelo de projeto RCL (biblioteca de classes Razor).</span><span class="sxs-lookup"><span data-stu-id="a7561-119">To use the Razor SDK to build class libraries containing Razor views or Razor Pages, we recommend starting with the Razor class library (RCL) project template.</span></span> <span data-ttu-id="a7561-120">Um RCL que é usado para criar arquivos de mais e ( *. Razor*) requer minimamente uma referência ao pacote [Microsoft. AspNetCore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) .</span><span class="sxs-lookup"><span data-stu-id="a7561-120">An RCL that's used to build Blazor (*.razor*) files minimally requires a reference to the [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) package.</span></span> <span data-ttu-id="a7561-121">Um RCL que é usado para criar exibições ou páginas do Razor (arquivos *. cshtml* ) requer minimamente o direcionamento `netcoreapp3.0` ou posterior e tem uma `FrameworkReference` ao [metapacote Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) em seu arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="a7561-121">An RCL that's used to build Razor views or pages (*.cshtml* files) minimally requires targeting `netcoreapp3.0` or later and has a `FrameworkReference` to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in its project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a7561-122">Para usar o SDK do Razor para criar bibliotecas de classe contendo exibições Razor ou Páginas Razor:</span><span class="sxs-lookup"><span data-stu-id="a7561-122">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="a7561-123">Use `Microsoft.NET.Sdk.Razor` em vez de `Microsoft.NET.Sdk`:</span><span class="sxs-lookup"><span data-stu-id="a7561-123">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="a7561-124">Normalmente, uma referência de pacote para `Microsoft.AspNetCore.Mvc` é necessário para receber dependências adicionais que são necessárias para criar e compilar páginas Razor e exibições do Razor.</span><span class="sxs-lookup"><span data-stu-id="a7561-124">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="a7561-125">No mínimo, seu projeto deve adicionar referências de pacote para:</span><span class="sxs-lookup"><span data-stu-id="a7561-125">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  <span data-ttu-id="a7561-126">O `Microsoft.AspNetCore.Razor.Design` pacote fornece os destinos e tarefas de compilação do Razor para o projeto.</span><span class="sxs-lookup"><span data-stu-id="a7561-126">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="a7561-127">Os pacotes anteriores são incluídos em `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="a7561-127">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="a7561-128">A marcação a seguir mostra um arquivo de projeto que usa o SDK do Razor para criar arquivos Razor para um aplicativo páginas Razor do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a7561-128">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="a7561-129">O `Microsoft.AspNetCore.Razor.Design` e `Microsoft.AspNetCore.Mvc.Razor.Extensions` pacotes são incluídos na [metapacote Microsoft](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a7561-129">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="a7561-130">No entanto, o menor de versão `Microsoft.AspNetCore.App` referência de pacote fornece um metapacote para o aplicativo que não inclui a versão mais recente do `Microsoft.AspNetCore.Razor.Design`.</span><span class="sxs-lookup"><span data-stu-id="a7561-130">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="a7561-131">Projetos devem fazer referência a uma versão consistente do `Microsoft.AspNetCore.Razor.Design` (ou `Microsoft.AspNetCore.Mvc`) para que as correções mais recentes do tempo de compilação para Razor são incluídas.</span><span class="sxs-lookup"><span data-stu-id="a7561-131">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="a7561-132">Para obter mais informações, consulte [esse problema de GitHub](https://github.com/aspnet/Razor/issues/2553).</span><span class="sxs-lookup"><span data-stu-id="a7561-132">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="a7561-133">{1&gt;Propriedades&lt;1}</span><span class="sxs-lookup"><span data-stu-id="a7561-133">Properties</span></span>

<span data-ttu-id="a7561-134">As seguintes propriedades controlam o comportamento do SDK do Razor como parte de um build de projeto:</span><span class="sxs-lookup"><span data-stu-id="a7561-134">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="a7561-135">`RazorCompileOnBuild` &ndash; Quando `true`, compila e emite o assembly Razor como parte da criação do projeto.</span><span class="sxs-lookup"><span data-stu-id="a7561-135">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="a7561-136">Assume o padrão de `true`.</span><span class="sxs-lookup"><span data-stu-id="a7561-136">Defaults to `true`.</span></span>
* <span data-ttu-id="a7561-137">`RazorCompileOnPublish` &ndash; Quando `true`, compila e emite o assembly Razor como parte da publicação do projeto.</span><span class="sxs-lookup"><span data-stu-id="a7561-137">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="a7561-138">Assume o padrão de `true`.</span><span class="sxs-lookup"><span data-stu-id="a7561-138">Defaults to `true`.</span></span>

<span data-ttu-id="a7561-139">As propriedades e os itens na tabela a seguir são usados para configurar entradas e saídas para o SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="a7561-139">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="a7561-140">A partir do ASP.NET Core 3,0, as exibições do MVC ou Razor Pages não são servidas por padrão se as propriedades `RazorCompileOnBuild` ou `RazorCompileOnPublish` do MSBuild no arquivo de projeto estão desabilitadas.</span><span class="sxs-lookup"><span data-stu-id="a7561-140">Starting with ASP.NET Core 3.0, MVC Views or Razor Pages aren't served by default if the `RazorCompileOnBuild` or `RazorCompileOnPublish` MSBuild properties in the project file are disabled.</span></span> <span data-ttu-id="a7561-141">Os aplicativos devem adicionar uma referência explícita ao pacote [Microsoft. AspNetCore. Mvc. Razor. RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) se o aplicativo depender da compilação de tempo de execução para processar arquivos *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a7561-141">Applications must add an explicit reference to the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) package if the app relies on runtime compilation to process *.cshtml* files.</span></span>

::: moniker-end

| <span data-ttu-id="a7561-142">{1&gt;Itens&lt;1}</span><span class="sxs-lookup"><span data-stu-id="a7561-142">Items</span></span> | <span data-ttu-id="a7561-143">Descrição</span><span class="sxs-lookup"><span data-stu-id="a7561-143">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="a7561-144">Elementos de item (arquivos *. cshtml* ) que são entradas para geração de código.</span><span class="sxs-lookup"><span data-stu-id="a7561-144">Item elements (*.cshtml* files) that are inputs to code generation.</span></span> |
| `RazorComponent` | <span data-ttu-id="a7561-145">Elementos de item (arquivos *. Razor* ) que são entradas para a geração de código de componente Razor.</span><span class="sxs-lookup"><span data-stu-id="a7561-145">Item elements (*.razor* files) that are inputs to Razor component code generation.</span></span> |
| `RazorCompile` | <span data-ttu-id="a7561-146">Elementos de item (arquivos *. cs* ) que são entradas para destinos de compilação do Razor.</span><span class="sxs-lookup"><span data-stu-id="a7561-146">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="a7561-147">Use esta `ItemGroup` para especificar arquivos adicionais a serem compilados no assembly Razor.</span><span class="sxs-lookup"><span data-stu-id="a7561-147">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="a7561-148">Os elementos de item usados para a codificação geram atributos para o assembly Razor.</span><span class="sxs-lookup"><span data-stu-id="a7561-148">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="a7561-149">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="a7561-149">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="a7561-150">Elementos de item adicionados como recursos incorporados ao assembly Razor gerado.</span><span class="sxs-lookup"><span data-stu-id="a7561-150">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="a7561-151">propriedade</span><span class="sxs-lookup"><span data-stu-id="a7561-151">Property</span></span> | <span data-ttu-id="a7561-152">Descrição</span><span class="sxs-lookup"><span data-stu-id="a7561-152">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="a7561-153">Nome do arquivo (sem extensão) do assembly produzido pelo Razor.</span><span class="sxs-lookup"><span data-stu-id="a7561-153">File name (without extension) of the assembly produced by Razor.</span></span> |
| `RazorOutputPath` | <span data-ttu-id="a7561-154">O diretório de saída do Razor.</span><span class="sxs-lookup"><span data-stu-id="a7561-154">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="a7561-155">Usado para determinar o conjunto de ferramentas usado para criar o assembly do Razor.</span><span class="sxs-lookup"><span data-stu-id="a7561-155">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="a7561-156">Os valores válidos são `Implicit`, `RazorSDK` e `PrecompilationTool`.</span><span class="sxs-lookup"><span data-stu-id="a7561-156">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="a7561-157">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="a7561-157">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="a7561-158">O padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="a7561-158">Default is `true`.</span></span> <span data-ttu-id="a7561-159">Quando `true`, inclui arquivos *Web. config*, *. JSON*e *. cshtml* como conteúdo no projeto.</span><span class="sxs-lookup"><span data-stu-id="a7561-159">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="a7561-160">Quando referenciado por meio `Microsoft.NET.Sdk.Web`, arquivos sob *wwwroot* e arquivos de configuração também estão incluídos.</span><span class="sxs-lookup"><span data-stu-id="a7561-160">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="a7561-161">Quando `true`, inclui arquivos *.cshtml* de itens de `Content` em itens de `RazorGenerate`.</span><span class="sxs-lookup"><span data-stu-id="a7561-161">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="a7561-162">Quando `true`, gera uma *. CS* arquivo que contém atributos especificados por `RazorAssemblyAttribute` e inclui o arquivo na saída da compilação.</span><span class="sxs-lookup"><span data-stu-id="a7561-162">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="a7561-163">Quando `true`, adiciona um conjunto padrão de atributos de assembly em `RazorAssemblyAttribute`.</span><span class="sxs-lookup"><span data-stu-id="a7561-163">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="a7561-164">Quando `true`, cópias `RazorGenerate` itens ( *. cshtml*) arquivos para o diretório de publicação.</span><span class="sxs-lookup"><span data-stu-id="a7561-164">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="a7561-165">Normalmente, arquivos do Razor não são necessários para um aplicativo publicado se eles participam da compilação em tempo de compilação ou tempo de publicação.</span><span class="sxs-lookup"><span data-stu-id="a7561-165">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="a7561-166">Assume o padrão de `false`.</span><span class="sxs-lookup"><span data-stu-id="a7561-166">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="a7561-167">Quando `true`, copia os itens do assembly de referência no diretório de publicação.</span><span class="sxs-lookup"><span data-stu-id="a7561-167">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="a7561-168">Normalmente, os assemblies de referência não são necessários para um aplicativo publicado se a compilação do Razor ocorre em tempo de compilação ou tempo de publicação.</span><span class="sxs-lookup"><span data-stu-id="a7561-168">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="a7561-169">Definido como `true` se seu aplicativo publicado requer a compilação de tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="a7561-169">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="a7561-170">Por exemplo, defina o valor como `true` se o aplicativo modifica *. cshtml* arquivos em tempo de execução ou usa exibições inseridas.</span><span class="sxs-lookup"><span data-stu-id="a7561-170">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="a7561-171">Assume o padrão de `false`.</span><span class="sxs-lookup"><span data-stu-id="a7561-171">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="a7561-172">Quando `true`, todos os itens de conteúdo do Razor ( *. cshtml* arquivos) são marcados para inclusão no pacote do NuGet gerado.</span><span class="sxs-lookup"><span data-stu-id="a7561-172">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="a7561-173">Assume o padrão de `false`.</span><span class="sxs-lookup"><span data-stu-id="a7561-173">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="a7561-174">Quando `true`, adiciona itens de RazorGenerate ( *.cshtml*) como arquivos incorporados ao assembly Razor gerado.</span><span class="sxs-lookup"><span data-stu-id="a7561-174">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="a7561-175">Assume o padrão de `false`.</span><span class="sxs-lookup"><span data-stu-id="a7561-175">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="a7561-176">Quando `true`, usa um processo de servidor de build persistente para descarregar o trabalho de geração de código.</span><span class="sxs-lookup"><span data-stu-id="a7561-176">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="a7561-177">Seu valor padrão é `UseSharedCompilation`.</span><span class="sxs-lookup"><span data-stu-id="a7561-177">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="a7561-178">Quando `true`, o SDK gera atributos adicionais usados pelo MVC em tempo de execução para executar a descoberta de parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a7561-178">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |
| `DefaultWebContentItemExcludes` | <span data-ttu-id="a7561-179">Um padrão de mascaramento para elementos de item que devem ser excluídos do `Content` grupo de itens em projetos direcionados para o SDK Web ou Razor</span><span class="sxs-lookup"><span data-stu-id="a7561-179">A globbing pattern for item elements that are to be excluded from the `Content` item group in projects targeting the Web or Razor SDK</span></span> |
| `ExcludeConfigFilesFromBuildOutput` | <span data-ttu-id="a7561-180">Quando os arquivos `true`, *. config* e *. JSON* não são copiados para o diretório de saída da compilação.</span><span class="sxs-lookup"><span data-stu-id="a7561-180">When `true`, *.config* and *.json* files do not get copied to the build output directory.</span></span> |
| `AddRazorSupportForMvc` | <span data-ttu-id="a7561-181">Quando `true`, o configura o SDK do Razor para adicionar suporte para a configuração do MVC que é necessária ao criar aplicativos que contenham exibições do MVC ou Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a7561-181">When `true`, configures the Razor SDK to add support for the MVC configuration that is required when building applications containing MVC views or Razor Pages.</span></span> <span data-ttu-id="a7561-182">Essa propriedade é definida implicitamente para projetos .NET Core 3,0 ou posterior direcionados para o SDK da Web</span><span class="sxs-lookup"><span data-stu-id="a7561-182">This property is implicitly set for .NET Core 3.0 or later projects targeting the Web SDK</span></span> |
| `RazorLangVersion` | <span data-ttu-id="a7561-183">A versão do idioma do Razor para o destino.</span><span class="sxs-lookup"><span data-stu-id="a7561-183">The version of the Razor Language to target.</span></span> |

<span data-ttu-id="a7561-184">Para saber mais sobre as propriedades, confira [Propriedades do MSBuild](/visualstudio/msbuild/msbuild-properties).</span><span class="sxs-lookup"><span data-stu-id="a7561-184">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="a7561-185">Destinos</span><span class="sxs-lookup"><span data-stu-id="a7561-185">Targets</span></span>

<span data-ttu-id="a7561-186">O SDK do Razor define dois destinos primários:</span><span class="sxs-lookup"><span data-stu-id="a7561-186">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="a7561-187">`RazorGenerate` &ndash; Gera código *. CS* arquivos de `RazorGenerate` elementos de item.</span><span class="sxs-lookup"><span data-stu-id="a7561-187">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="a7561-188">Use a propriedade `RazorGenerateDependsOn` para especificar destinos adicionais que podem ser executados antes ou depois desse destino.</span><span class="sxs-lookup"><span data-stu-id="a7561-188">Use the `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="a7561-189">`RazorCompile` &ndash; Compila gerada *. CS* arquivos em um assembly Razor.</span><span class="sxs-lookup"><span data-stu-id="a7561-189">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="a7561-190">Use o `RazorCompileDependsOn` para especificar destinos adicionais que podem ser executados antes ou depois desse destino.</span><span class="sxs-lookup"><span data-stu-id="a7561-190">Use the `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="a7561-191">`RazorComponentGenerate` &ndash; código gera arquivos *. cs* para elementos de `RazorComponent` item.</span><span class="sxs-lookup"><span data-stu-id="a7561-191">`RazorComponentGenerate` &ndash; Code generates *.cs* files for `RazorComponent` item elements.</span></span> <span data-ttu-id="a7561-192">Use a propriedade `RazorComponentGenerateDependsOn` para especificar destinos adicionais que podem ser executados antes ou depois desse destino.</span><span class="sxs-lookup"><span data-stu-id="a7561-192">Use the `RazorComponentGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="a7561-193">Compilação de runtime de modos de exibição do Razor</span><span class="sxs-lookup"><span data-stu-id="a7561-193">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="a7561-194">Por padrão, o SDK do Razor não publica assemblies de referência que são necessários para realizar compilação no runtime.</span><span class="sxs-lookup"><span data-stu-id="a7561-194">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="a7561-195">Isso resulta em falhas de compilação quando o modelo de aplicativo se baseia na compilação em runtime&mdash;, por exemplo, o aplicativo usa exibições inseridas ou muda as exibições depois que o aplicativo é publicado.</span><span class="sxs-lookup"><span data-stu-id="a7561-195">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="a7561-196">Defina `CopyRefAssembliesToPublishDirectory` como `true` para continuar publicando assemblies de referência.</span><span class="sxs-lookup"><span data-stu-id="a7561-196">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="a7561-197">Para um aplicativo web, verifique se seu aplicativo for direcionado a `Microsoft.NET.Sdk.Web` SDK.</span><span class="sxs-lookup"><span data-stu-id="a7561-197">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="a7561-198">Versão do idioma Razor</span><span class="sxs-lookup"><span data-stu-id="a7561-198">Razor language version</span></span>

<span data-ttu-id="a7561-199">Ao direcionar o SDK do `Microsoft.NET.Sdk.Web`, a versão do idioma do Razor é inferida a partir da versão da estrutura de destino do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a7561-199">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the app's target framework version.</span></span> <span data-ttu-id="a7561-200">Para projetos direcionados ao SDK do `Microsoft.NET.Sdk.Razor` ou no caso raro de o aplicativo exigir uma versão diferente da linguagem Razor do que o valor inferido, uma versão pode ser configurada definindo a propriedade `<RazorLangVersion>` no arquivo de projeto do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="a7561-200">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

<span data-ttu-id="a7561-201">A versão do idioma do Razor está totalmente integrada à versão do tempo de execução para a qual foi criada.</span><span class="sxs-lookup"><span data-stu-id="a7561-201">Razor's language version is tightly integrated with the version of the runtime that it was built for.</span></span> <span data-ttu-id="a7561-202">O direcionamento de uma versão de idioma que não é projetada para o tempo de execução não é suportado e provavelmente produz erros de compilação.</span><span class="sxs-lookup"><span data-stu-id="a7561-202">Targeting a language version that isn't designed for the runtime is unsupported and likely produces build errors.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7561-203">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a7561-203">Additional resources</span></span>

* [<span data-ttu-id="a7561-204">Adições ao formato csproj para o .NET Core</span><span class="sxs-lookup"><span data-stu-id="a7561-204">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="a7561-205">Itens de projeto comuns do MSBuild</span><span class="sxs-lookup"><span data-stu-id="a7561-205">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
