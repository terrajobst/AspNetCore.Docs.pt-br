---
title: Compilação de arquivo do Razor no ASP.NET Core
author: rick-anderson
description: Saiba como a compilação de arquivos do Razor ocorre em um aplicativo ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: 0b3aea584de63cb8032e4ca112d2441349bdfbb3
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345477"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="b24a0-103">Compilação de arquivo do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b24a0-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="b24a0-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b24a0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="b24a0-105">Um arquivo do Razor é compilado em tempo de execução, quando o modo de exibição do MVC associado é chamado.</span><span class="sxs-lookup"><span data-stu-id="b24a0-105">A Razor file is compiled at runtime, when the associated MVC view is invoked.</span></span> <span data-ttu-id="b24a0-106">Não há suporte para a publicação de arquivos do Razor de tempo de build.</span><span class="sxs-lookup"><span data-stu-id="b24a0-106">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="b24a0-107">Como opção, os arquivos do Razor podem ser compilados durante a publicação e implantados com o aplicativo &mdash; usando a ferramenta de pré-compilação.</span><span class="sxs-lookup"><span data-stu-id="b24a0-107">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b24a0-108">Um arquivo do Razor é compilado em tempo de execução, quando o modo de exibição do MVC ou da Página do Razor associada é chamado.</span><span class="sxs-lookup"><span data-stu-id="b24a0-108">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="b24a0-109">Não há suporte para a publicação de arquivos do Razor de tempo de build.</span><span class="sxs-lookup"><span data-stu-id="b24a0-109">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="b24a0-110">Como opção, os arquivos do Razor podem ser compilados durante a publicação e implantados com o aplicativo &mdash; usando a ferramenta de pré-compilação.</span><span class="sxs-lookup"><span data-stu-id="b24a0-110">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="b24a0-111">Um arquivo do Razor é compilado em tempo de execução, quando o modo de exibição do MVC ou da Página do Razor associada é chamado.</span><span class="sxs-lookup"><span data-stu-id="b24a0-111">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="b24a0-112">Os arquivos do Razor são compilados em tempo de build e de publicação usando o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="b24a0-112">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b24a0-113">Os arquivos do Razor são compilados em tempo de build e de publicação usando o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="b24a0-113">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="b24a0-114">A compilação do tempo de execução pode ser opcionalmente habilitada através da configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b24a0-114">Runtime compilation may be optionally enabled by configuring your application.</span></span>

::: moniker-end

## <a name="razor-compilation"></a><span data-ttu-id="b24a0-115">Compilação do Razor</span><span class="sxs-lookup"><span data-stu-id="b24a0-115">Razor compilation</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="b24a0-116">A compilação em tempo de build e de publicação de arquivos do Razor está habilitada por padrão pelo SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="b24a0-116">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="b24a0-117">Quando habilitada, a compilação de tempo de execução complementará a compilação de tempo de build, permitindo que os arquivos do Razor sejam atualizados se eles forem editados.</span><span class="sxs-lookup"><span data-stu-id="b24a0-117">When enabled, runtime compilation, will complement build time compilation allowing Razor files to be updated if they are editied.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="b24a0-118">A compilação em tempo de build e de publicação de arquivos do Razor está habilitada por padrão pelo SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="b24a0-118">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="b24a0-119">Há suporte para edição de arquivos do Razor depois que eles são atualizados em tempo de build.</span><span class="sxs-lookup"><span data-stu-id="b24a0-119">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="b24a0-120">Por padrão, somente os arquivos *Views.dll* compilados, mas nenhum arquivo *.cshtml* ou assembly de referência necessário para compilar arquivos do Razor, são implantados com seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b24a0-120">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b24a0-121">A ferramenta de pré-compilação foi preterida e será removida no ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="b24a0-121">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="b24a0-122">É recomendado migrar para o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="b24a0-122">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="b24a0-123">O SDK do Razor é eficaz somente quando não há propriedades específicas de pré-compilação definidas no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="b24a0-123">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="b24a0-124">Por exemplo, definir a propriedade `MvcRazorCompileOnPublish` do arquivo *.csproj* como `true` desabilita o SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="b24a0-124">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="b24a0-125">Se o projeto for direcionado ao .NET Framework, instale o pacote NuGet [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/):</span><span class="sxs-lookup"><span data-stu-id="b24a0-125">If your project targets .NET Framework, install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package:</span></span>

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

<span data-ttu-id="b24a0-126">Se o projeto for direcionado ao .NET Core, nenhuma alteração será necessária.</span><span class="sxs-lookup"><span data-stu-id="b24a0-126">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="b24a0-127">Os modelos de projeto do ASP.NET Core 2.x definem implicitamente a propriedade `MvcRazorCompileOnPublish` como `true` por padrão.</span><span class="sxs-lookup"><span data-stu-id="b24a0-127">The ASP.NET Core 2.x project templates implicitly set the `MvcRazorCompileOnPublish` property to `true` by default.</span></span> <span data-ttu-id="b24a0-128">Consequentemente, esse elemento pode ser removido com segurança do arquivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="b24a0-128">Consequently, this element can be safely removed from the *.csproj* file.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b24a0-129">A ferramenta de pré-compilação foi preterida e será removida no ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="b24a0-129">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="b24a0-130">É recomendado migrar para o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="b24a0-130">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="b24a0-131">A pré-compilação do arquivo do Razor não está disponível durante a execução de uma [SCD (implantação autossuficiente)](/dotnet/core/deploying/#self-contained-deployments-scd) no ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="b24a0-131">Razor file precompilation is unavailable when performing a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="b24a0-132">Defina a propriedade `MvcRazorCompileOnPublish` como `true` e instale o pacote NuGet [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/).</span><span class="sxs-lookup"><span data-stu-id="b24a0-132">Set the `MvcRazorCompileOnPublish` property to `true`, and install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package.</span></span> <span data-ttu-id="b24a0-133">A seguinte amostra *.csproj* realça essas configurações:</span><span class="sxs-lookup"><span data-stu-id="b24a0-133">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="b24a0-134">Prepare o aplicativo para uma [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd) com o [comando de publicação da CLI do .NET Core](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="b24a0-134">Prepare the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd) with the [.NET Core CLI publish command](/dotnet/core/tools/dotnet-publish).</span></span> <span data-ttu-id="b24a0-135">Por exemplo, execute o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="b24a0-135">For example, execute the following command at the project root:</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="b24a0-136">Um arquivo *\<<nome_do_projeto>.PrecompiledViews.dll*, que contém os arquivos do Razor compilados, é produzido quando a pré-compilação é bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="b24a0-136">A *\<project_name>.PrecompiledViews.dll* file, containing the compiled Razor files, is produced when precompilation succeeds.</span></span> <span data-ttu-id="b24a0-137">Por exemplo, a captura de tela abaixo mostra o conteúdo de *Index.cshtml* dentro de *WebApplication1.PrecompiledViews.dll*:</span><span class="sxs-lookup"><span data-stu-id="b24a0-137">For example, the screenshot below depicts the contents of *Index.cshtml* within *WebApplication1.PrecompiledViews.dll*:</span></span>

![Exibições do Razor dentro da DLL](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="runtime-compilation"></a><span data-ttu-id="b24a0-139">Compilação de tempo de execução</span><span class="sxs-lookup"><span data-stu-id="b24a0-139">Runtime compilation</span></span>

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="b24a0-140">A compilação de tempo de build é complementada pela compilação de tempo de execução de arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="b24a0-140">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="b24a0-141">O ASP.NET Core MVC recompilará arquivos do Razor quando o conteúdo de um arquivo *.cshtml* for alterado.</span><span class="sxs-lookup"><span data-stu-id="b24a0-141">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b24a0-142">A compilação de tempo de build é complementada pela compilação de tempo de execução de arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="b24a0-142">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="b24a0-143">O <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> obtém ou define um valor que determina se os arquivos do Razor (Exibições Razor e Razor Pages) são recompilados e atualizados, quando alterados em disco.</span><span class="sxs-lookup"><span data-stu-id="b24a0-143">The <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> gets or sets a value that determines if Razor files (Razor views and Razor Pages) are recompiled and updated if files change on disk.</span></span>

<span data-ttu-id="b24a0-144">O valor padrão é `true` para:</span><span class="sxs-lookup"><span data-stu-id="b24a0-144">The default value is `true` for:</span></span>

* <span data-ttu-id="b24a0-145">Se a versão de compatibilidade do aplicativo é definida como <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> ou anterior</span><span class="sxs-lookup"><span data-stu-id="b24a0-145">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> or earlier</span></span>
* <span data-ttu-id="b24a0-146">Se a versão de compatibilidade do aplicativo é definida para <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> ou posterior e o aplicativo está no ambiente de desenvolvimento <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*>.</span><span class="sxs-lookup"><span data-stu-id="b24a0-146">If the app's compatibility version is set to set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> or later and the app is in the Development environment <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*>.</span></span> <span data-ttu-id="b24a0-147">Em outras palavras, arquivos do Razor não serão recompilados no ambiente de não desenvolvimento, a menos que <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> seja definido explicitamente.</span><span class="sxs-lookup"><span data-stu-id="b24a0-147">In other words, Razor files would not recompile in non-Development environment unless <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> is explicitly set.</span></span>

<span data-ttu-id="b24a0-148">Para ver exemplos e obter orientação sobre como definir a versão de compatibilidade do aplicativo, confira <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="b24a0-148">For guidance and examples of setting the app's compatibility version, see <xref:mvc/compatibility-version>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b24a0-149">A compilação de tempo de execução é habilitada usando o pacote `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="b24a0-149">Runtime compilation is enabled using the `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` package.</span></span> <span data-ttu-id="b24a0-150">Para habilitar a compilação de tempo de execução, os aplicativos precisam:</span><span class="sxs-lookup"><span data-stu-id="b24a0-150">To enable runtime compilation, apps must:</span></span>

* <span data-ttu-id="b24a0-151">Instalar o pacote do NuGet [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/).</span><span class="sxs-lookup"><span data-stu-id="b24a0-151">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
* <span data-ttu-id="b24a0-152">Atualizar o `ConfigureServices` do aplicativo para incluir uma chamada para `AddMvcRazorRuntimeCompilation`:</span><span class="sxs-lookup"><span data-stu-id="b24a0-152">Update the application's `ConfigureServices` to include a call to `AddMvcRazorRuntimeCompilation`:</span></span>

  ```csharp
  services
      .AddMvc()
      .AddMvcRazorRuntimeCompilation()
  ```

<span data-ttu-id="b24a0-153">Para que a compilação de tempo de execução funcione quando implantada, os aplicativos além disso precisam modificar seus arquivos de projeto para definir o `PreserveCompilationReferences` para `true`.</span><span class="sxs-lookup"><span data-stu-id="b24a0-153">For runtime compilation to work when deployed, apps must additionally modify their project files to set the `PreserveCompilationReferences` to `true`.</span></span>

[!code-xml[](view-compilation/sample/RuntimeCompilation.csproj?highlight=3)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b24a0-154">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b24a0-154">Additional resources</span></span>

::: moniker range="= aspnetcore-1.1"

* <xref:mvc/views/overview>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
