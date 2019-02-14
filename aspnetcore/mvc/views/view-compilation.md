---
title: Compilação e pré-compilação do arquivo do Razor no ASP.NET Core
author: rick-anderson
description: Saiba mais sobre os benefícios de pré-compilação arquivos do Razor e como realizar pré-compilação desses arquivos em um aplicativo do ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: c4e8f722fdf3d3f64807cc35ff9f349af7f32abd
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248180"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="0dfc7-103">Compilação de arquivo do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0dfc7-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="0dfc7-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0dfc7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="0dfc7-105">Um arquivo do Razor é compilado em tempo de execução, quando o modo de exibição do MVC associado é chamado.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-105">A Razor file is compiled at runtime, when the associated MVC view is invoked.</span></span> <span data-ttu-id="0dfc7-106">Não há suporte para a publicação de arquivos do Razor de tempo de build.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-106">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="0dfc7-107">Como opção, os arquivos do Razor podem ser compilados durante a publicação e implantados com o aplicativo &mdash; usando a ferramenta de pré-compilação.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-107">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="0dfc7-108">Um arquivo do Razor é compilado em tempo de execução, quando o modo de exibição do MVC ou da Página do Razor associada é chamado.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-108">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="0dfc7-109">Não há suporte para a publicação de arquivos do Razor de tempo de build.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-109">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="0dfc7-110">Como opção, os arquivos do Razor podem ser compilados durante a publicação e implantados com o aplicativo &mdash; usando a ferramenta de pré-compilação.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-110">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0dfc7-111">Um arquivo do Razor é compilado em tempo de execução, quando o modo de exibição do MVC ou da Página do Razor associada é chamado.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-111">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="0dfc7-112">Os arquivos do Razor são compilados em tempo de build e de publicação usando o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="0dfc7-112">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

::: moniker-end

## <a name="precompilation-considerations"></a><span data-ttu-id="0dfc7-113">Considerações sobre a pré-compilação</span><span class="sxs-lookup"><span data-stu-id="0dfc7-113">Precompilation considerations</span></span>

<span data-ttu-id="0dfc7-114">Estes são os efeitos colaterais da pré-compilação de arquivos do Razor:</span><span class="sxs-lookup"><span data-stu-id="0dfc7-114">The following are side effects of precompiling Razor files:</span></span>

* <span data-ttu-id="0dfc7-115">Pacote publicado menor</span><span class="sxs-lookup"><span data-stu-id="0dfc7-115">A smaller published bundle</span></span>
* <span data-ttu-id="0dfc7-116">Tempo de inicialização mais rápido</span><span class="sxs-lookup"><span data-stu-id="0dfc7-116">A faster startup time</span></span>
* <span data-ttu-id="0dfc7-117">Não é possível editar arquivos do Razor&mdash;o conteúdo associado está ausente do pacote publicado.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-117">You can't edit Razor files&mdash;the associated content is absent from the published bundle.</span></span>

## <a name="deploy-precompiled-files"></a><span data-ttu-id="0dfc7-118">Implantar arquivos pré-compilados</span><span class="sxs-lookup"><span data-stu-id="0dfc7-118">Deploy precompiled files</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0dfc7-119">A compilação em tempo de build e de publicação de arquivos do Razor está habilitada por padrão pelo SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-119">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="0dfc7-120">Há suporte para edição de arquivos do Razor depois que eles são atualizados em tempo de build.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-120">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="0dfc7-121">Por padrão, nenhum arquivo *.cshtml* é implantado com o aplicativo; somente a *Views.dll* compilada.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-121">By default, only the compiled *Views.dll* and no *.cshtml* files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0dfc7-122">A ferramenta de pré-compilação será removida no ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-122">The precompilation tool will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="0dfc7-123">É recomendado migrar para o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="0dfc7-123">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="0dfc7-124">O SDK do Razor é eficaz somente quando não há propriedades específicas de pré-compilação definidas no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-124">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="0dfc7-125">Por exemplo, definir a propriedade `MvcRazorCompileOnPublish` do arquivo *.csproj* como `true` desabilita o SDK do Razor.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-125">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="0dfc7-126">Se o projeto for direcionado ao .NET Framework, instale o pacote NuGet [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/):</span><span class="sxs-lookup"><span data-stu-id="0dfc7-126">If your project targets .NET Framework, install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package:</span></span>

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

<span data-ttu-id="0dfc7-127">Se o projeto for direcionado ao .NET Core, nenhuma alteração será necessária.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-127">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="0dfc7-128">Os modelos de projeto do ASP.NET Core 2.x definem implicitamente a propriedade `MvcRazorCompileOnPublish` como `true` por padrão.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-128">The ASP.NET Core 2.x project templates implicitly set the `MvcRazorCompileOnPublish` property to `true` by default.</span></span> <span data-ttu-id="0dfc7-129">Consequentemente, esse elemento pode ser removido com segurança do arquivo *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-129">Consequently, this element can be safely removed from the *.csproj* file.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0dfc7-130">A ferramenta de pré-compilação será removida no ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-130">The precompilation tool will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="0dfc7-131">É recomendado migrar para o [SDK do Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="0dfc7-131">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="0dfc7-132">A pré-compilação do arquivo do Razor não está disponível durante a execução de uma [SCD (implantação autossuficiente)](/dotnet/core/deploying/#self-contained-deployments-scd) no ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-132">Razor file precompilation is unavailable when performing a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="0dfc7-133">Defina a propriedade `MvcRazorCompileOnPublish` como `true` e instale o pacote NuGet [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/).</span><span class="sxs-lookup"><span data-stu-id="0dfc7-133">Set the `MvcRazorCompileOnPublish` property to `true`, and install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package.</span></span> <span data-ttu-id="0dfc7-134">A seguinte amostra *.csproj* realça essas configurações:</span><span class="sxs-lookup"><span data-stu-id="0dfc7-134">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="0dfc7-135">Prepare o aplicativo para uma [implantação dependente de estrutura](/dotnet/core/deploying/#framework-dependent-deployments-fdd) com o [comando de publicação da CLI do .NET Core](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="0dfc7-135">Prepare the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd) with the [.NET Core CLI publish command](/dotnet/core/tools/dotnet-publish).</span></span> <span data-ttu-id="0dfc7-136">Por exemplo, execute o seguinte comando na raiz do projeto:</span><span class="sxs-lookup"><span data-stu-id="0dfc7-136">For example, execute the following command at the project root:</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="0dfc7-137">Um arquivo *<nome_do_projeto>.PrecompiledViews.dll*, que contém os arquivos do Razor compilados, é produzido quando a pré-compilação é bem-sucedida.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-137">A *<project_name>.PrecompiledViews.dll* file, containing the compiled Razor files, is produced when precompilation succeeds.</span></span> <span data-ttu-id="0dfc7-138">Por exemplo, a captura de tela abaixo mostra o conteúdo de *Index.cshtml* dentro de *WebApplication1.PrecompiledViews.dll*:</span><span class="sxs-lookup"><span data-stu-id="0dfc7-138">For example, the screenshot below depicts the contents of *Index.cshtml* within *WebApplication1.PrecompiledViews.dll*:</span></span>

![Exibições do Razor dentro da DLL](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="recompile-razor-files-on-change"></a><span data-ttu-id="0dfc7-140">Recompilar arquivos do Razor em alteração</span><span class="sxs-lookup"><span data-stu-id="0dfc7-140">Recompile Razor files on change</span></span>

<span data-ttu-id="0dfc7-141">O <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> obtém ou define um valor que determina se os arquivos do Razor (Exibições Razor e Razor Pages) são recompilados e atualizados, quando alterados em disco.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-141">The <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> gets or sets a value that determines if Razor files (Razor views and Razor Pages) are recompiled and updated if files change on disk.</span></span>

<span data-ttu-id="0dfc7-142">Quando definido como `true`, [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) busca alterações nos arquivos do Razor, em instâncias configuradas de <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-142">When set to `true`, [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) watches for changes to Razor files in configured <xref:Microsoft.Extensions.FileProviders.IFileProvider> instances.</span></span>

<span data-ttu-id="0dfc7-143">O valor padrão é `true` para:</span><span class="sxs-lookup"><span data-stu-id="0dfc7-143">The default value is `true` for:</span></span>

* <span data-ttu-id="0dfc7-144">ASP.NET Core 2.1 ou aplicativos anteriores.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-144">ASP.NET Core 2.1 or earlier apps.</span></span>
* <span data-ttu-id="0dfc7-145">ASP.NET Core 2.2 ou aplicativos posteriores no ambiente de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-145">ASP.NET Core 2.2 or later apps in the Development environment.</span></span>

<span data-ttu-id="0dfc7-146"><xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> está associado a uma opção de compatibilidade e pode fornecer um comportamento diferente, dependendo da versão de compatibilidade configurada do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-146"><xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> is associated with a compatibility switch and can provide different behavior depending on the configured compatibility version for the app.</span></span> <span data-ttu-id="0dfc7-147">A configuração do aplicativo definindo <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> tem precedência sobre o valor indicado pela versão de compatibilidade do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-147">Configuring the app by setting <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> takes precedence over the value implied by the app's compatibility version.</span></span>

<span data-ttu-id="0dfc7-148">Se a versão de compatibilidade do aplicativo for definida como <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> ou anterior, <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> será definida como `true`, a menos que seja explicitamente configurada.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-148">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> or earlier, <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> is set to `true` unless explicitly configured.</span></span>

<span data-ttu-id="0dfc7-149">Se a versão de compatibilidade do aplicativo for definida como <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> ou posterior, <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> será definida como `false`, a menos que é o ambiente seja de desenvolvimento ou o valor seja explicitamente configurado.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-149">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> or later, <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> is set to `false` unless the environment is Development or the value is explicitly configured.</span></span>

<span data-ttu-id="0dfc7-150">Para ver exemplos e obter orientação sobre como definir a versão de compatibilidade do aplicativo, confira <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="0dfc7-150">For guidance and examples of setting the app's compatibility version, see <xref:mvc/compatibility-version>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0dfc7-151">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0dfc7-151">Additional resources</span></span>

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
