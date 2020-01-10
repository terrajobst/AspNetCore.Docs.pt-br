---
title: Compartilhar controladores, exibições, Razor Pages e muito mais com partes de aplicativo no ASP.NET Core
author: rick-anderson
description: Compartilhar controladores, exibir, Razor Pages e muito mais com partes de aplicativo no ASP.NET Core
ms.author: riande
ms.date: 11/11/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: a95c344410db0651b9f8f1c1eb7551029f084c25
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829069"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts"></a><span data-ttu-id="a56af-103">Compartilhar controladores, exibições, Razor Pages e muito mais com partes do aplicativo</span><span class="sxs-lookup"><span data-stu-id="a56af-103">Share controllers, views, Razor Pages and more with Application Parts</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a56af-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a56af-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a56af-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a56af-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a56af-106">Uma *parte do aplicativo* é uma abstração sobre os recursos de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a56af-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="a56af-107">As partes do aplicativo permitem ASP.NET Core descobrir controladores, exibir componentes, auxiliares de marcas, Razor Pages, fontes de compilação do Razor e muito mais.</span><span class="sxs-lookup"><span data-stu-id="a56af-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="a56af-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> é uma parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a56af-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> is an Application part.</span></span> <span data-ttu-id="a56af-109">`AssemblyPart` encapsula uma referência de assembly e expõe tipos e referências de compilação.</span><span class="sxs-lookup"><span data-stu-id="a56af-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="a56af-110">Os [provedores de recursos](#fp) trabalham com partes do aplicativo para preencher os recursos de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a56af-110">[Feature providers](#fp) work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="a56af-111">O principal caso de uso para partes do aplicativo é configurar um aplicativo para descobrir (ou evitar carregar) ASP.NET Core recursos de um assembly.</span><span class="sxs-lookup"><span data-stu-id="a56af-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="a56af-112">Por exemplo, talvez você queira compartilhar funcionalidade comum entre vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="a56af-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="a56af-113">Usando partes de aplicativo, você pode compartilhar um assembly (DLL) contendo controladores, exibições, Razor Pages, fontes de compilação do Razor, auxiliares de marca e muito mais com vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="a56af-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="a56af-114">O compartilhamento de um assembly é preferencial para duplicar o código em vários projetos.</span><span class="sxs-lookup"><span data-stu-id="a56af-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="a56af-115">Os aplicativos ASP.NET Core carregam recursos do <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="a56af-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="a56af-116">A classe <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> representa uma parte do aplicativo que é apoiada por um assembly.</span><span class="sxs-lookup"><span data-stu-id="a56af-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="a56af-117">Carregar recursos de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a56af-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="a56af-118">Use as classes <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> e <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> para descobrir e carregar recursos de ASP.NET Core (controladores, exibir componentes, etc.).</span><span class="sxs-lookup"><span data-stu-id="a56af-118">Use the <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> and <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="a56af-119">O <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> controla as partes do aplicativo e os provedores de recursos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="a56af-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="a56af-120">o `ApplicationPartManager` está configurado em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a56af-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="a56af-121">O código a seguir fornece uma abordagem alternativa para configurar `ApplicationPartManager` usando `AssemblyPart`:</span><span class="sxs-lookup"><span data-stu-id="a56af-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="a56af-122">Os dois exemplos de código anteriores carregam o `SharedController` de um assembly.</span><span class="sxs-lookup"><span data-stu-id="a56af-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="a56af-123">O `SharedController` não está no projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a56af-123">The `SharedController` is not in the app's project.</span></span> <span data-ttu-id="a56af-124">Consulte o download de exemplo da [solução WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="a56af-124">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="a56af-125">Incluir exibições</span><span class="sxs-lookup"><span data-stu-id="a56af-125">Include views</span></span>

<span data-ttu-id="a56af-126">Use uma [biblioteca de classes Razor](xref:razor-pages/ui-class) para incluir exibições no assembly.</span><span class="sxs-lookup"><span data-stu-id="a56af-126">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="a56af-127">Impedir o carregamento de recursos</span><span class="sxs-lookup"><span data-stu-id="a56af-127">Prevent loading resources</span></span>

<span data-ttu-id="a56af-128">Partes de aplicativo podem ser usadas para *evitar* o carregamento de recursos em um determinado assembly ou local.</span><span class="sxs-lookup"><span data-stu-id="a56af-128">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="a56af-129">Adicionar ou remover membros da coleção de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para ocultar ou tornar os recursos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="a56af-129">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="a56af-130">A ordem das entradas na coleção `ApplicationParts` não é importante.</span><span class="sxs-lookup"><span data-stu-id="a56af-130">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="a56af-131">Configure o `ApplicationPartManager` antes de usá-lo para configurar serviços no contêiner.</span><span class="sxs-lookup"><span data-stu-id="a56af-131">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="a56af-132">Por exemplo, configure o `ApplicationPartManager` antes de invocar `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="a56af-132">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="a56af-133">Chame `Remove` na coleção de `ApplicationParts` para remover um recurso.</span><span class="sxs-lookup"><span data-stu-id="a56af-133">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="a56af-134">O `ApplicationPartManager` inclui partes para:</span><span class="sxs-lookup"><span data-stu-id="a56af-134">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="a56af-135">O assembly do aplicativo e os assemblies dependentes.</span><span class="sxs-lookup"><span data-stu-id="a56af-135">The app's assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* <span data-ttu-id="a56af-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="a56af-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="a56af-137">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="a56af-137">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

<a name="fp"></a>

## <a name="feature-providers"></a><span data-ttu-id="a56af-138">Provedores de recursos</span><span class="sxs-lookup"><span data-stu-id="a56af-138">Feature providers</span></span>

<span data-ttu-id="a56af-139">Os provedores de recursos de aplicativos examinam partes do aplicativo e fornecem recursos para essas partes.</span><span class="sxs-lookup"><span data-stu-id="a56af-139">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="a56af-140">Há provedores de recursos internos para os seguintes recursos de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a56af-140">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* <span data-ttu-id="a56af-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span><span class="sxs-lookup"><span data-stu-id="a56af-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span></span>

<span data-ttu-id="a56af-142">Provedores de recursos herdam de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, em que `T` é o tipo do recurso.</span><span class="sxs-lookup"><span data-stu-id="a56af-142">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="a56af-143">Os provedores de recursos podem ser implementados para qualquer um dos tipos de recursos listados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="a56af-143">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="a56af-144">A ordem dos provedores de recursos no `ApplicationPartManager.FeatureProviders` pode afetar o comportamento do tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="a56af-144">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="a56af-145">Provedores adicionados posteriormente podem reagir a ações executadas por provedores adicionados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="a56af-145">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="a56af-146">Exibir recursos disponíveis</span><span class="sxs-lookup"><span data-stu-id="a56af-146">Display available features</span></span>

<span data-ttu-id="a56af-147">Os recursos disponíveis para um aplicativo podem ser enumerados solicitando uma `ApplicationPartManager` por meio da [injeção de dependência](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="a56af-147">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="a56af-148">O [exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa o código anterior para exibir os recursos do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="a56af-148">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="a56af-149">Descoberta em partes do aplicativo</span><span class="sxs-lookup"><span data-stu-id="a56af-149">Discovery in application parts</span></span>

<span data-ttu-id="a56af-150">Os erros HTTP 404 não são incomuns ao desenvolver com partes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a56af-150">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="a56af-151">Esses erros são geralmente causados por falta de um requisito essencial para o modo como as partes de aplicativos são descobertas.</span><span class="sxs-lookup"><span data-stu-id="a56af-151">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="a56af-152">Se seu aplicativo retornar um erro HTTP 404, verifique se os seguintes requisitos foram atendidos:</span><span class="sxs-lookup"><span data-stu-id="a56af-152">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="a56af-153">A configuração de `applicationName` precisa ser definida como o assembly raiz usado para descoberta.</span><span class="sxs-lookup"><span data-stu-id="a56af-153">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="a56af-154">O assembly raiz usado para descoberta normalmente é o assembly de ponto de entrada.</span><span class="sxs-lookup"><span data-stu-id="a56af-154">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="a56af-155">O assembly raiz precisa ter uma referência às partes usadas para descoberta.</span><span class="sxs-lookup"><span data-stu-id="a56af-155">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="a56af-156">A referência pode ser direta ou transitiva.</span><span class="sxs-lookup"><span data-stu-id="a56af-156">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="a56af-157">O assembly raiz precisa fazer referência ao SDK da Web.</span><span class="sxs-lookup"><span data-stu-id="a56af-157">The root assembly needs to reference the Web SDK.</span></span> <span data-ttu-id="a56af-158">A estrutura tem lógica que carimba os atributos no assembly raiz que são usados para descoberta.</span><span class="sxs-lookup"><span data-stu-id="a56af-158">The framework has logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a56af-159">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a56af-159">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a56af-160">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a56af-160">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a56af-161">Uma *parte do aplicativo* é uma abstração sobre os recursos de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a56af-161">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="a56af-162">As partes do aplicativo permitem ASP.NET Core descobrir controladores, exibir componentes, auxiliares de marcas, Razor Pages, fontes de compilação do Razor e muito mais.</span><span class="sxs-lookup"><span data-stu-id="a56af-162">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="a56af-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) é uma parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a56af-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="a56af-164">`AssemblyPart` encapsula uma referência de assembly e expõe tipos e referências de compilação.</span><span class="sxs-lookup"><span data-stu-id="a56af-164">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="a56af-165">Os *provedores de recursos* trabalham com partes do aplicativo para preencher os recursos de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a56af-165">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="a56af-166">O principal caso de uso para partes do aplicativo é configurar um aplicativo para descobrir (ou evitar carregar) ASP.NET Core recursos de um assembly.</span><span class="sxs-lookup"><span data-stu-id="a56af-166">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="a56af-167">Por exemplo, talvez você queira compartilhar funcionalidade comum entre vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="a56af-167">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="a56af-168">Usando partes de aplicativo, você pode compartilhar um assembly (DLL) contendo controladores, exibições, Razor Pages, fontes de compilação do Razor, auxiliares de marca e muito mais com vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="a56af-168">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="a56af-169">O compartilhamento de um assembly é preferencial para duplicar o código em vários projetos.</span><span class="sxs-lookup"><span data-stu-id="a56af-169">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="a56af-170">Os aplicativos ASP.NET Core carregam recursos do <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="a56af-170">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="a56af-171">A classe <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> representa uma parte do aplicativo que é apoiada por um assembly.</span><span class="sxs-lookup"><span data-stu-id="a56af-171">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="a56af-172">Carregar recursos de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a56af-172">Load ASP.NET Core features</span></span>

<span data-ttu-id="a56af-173">Use as classes `ApplicationPart` e `AssemblyPart` para descobrir e carregar recursos de ASP.NET Core (controladores, exibir componentes, etc.).</span><span class="sxs-lookup"><span data-stu-id="a56af-173">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="a56af-174">O <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> controla as partes do aplicativo e os provedores de recursos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="a56af-174">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="a56af-175">o `ApplicationPartManager` está configurado em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a56af-175">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="a56af-176">O código a seguir fornece uma abordagem alternativa para configurar `ApplicationPartManager` usando `AssemblyPart`:</span><span class="sxs-lookup"><span data-stu-id="a56af-176">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="a56af-177">Os dois exemplos de código anteriores carregam o `SharedController` de um assembly.</span><span class="sxs-lookup"><span data-stu-id="a56af-177">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="a56af-178">O `SharedController` não está no projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a56af-178">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="a56af-179">Consulte o download de exemplo da [solução WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="a56af-179">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="a56af-180">Incluir exibições</span><span class="sxs-lookup"><span data-stu-id="a56af-180">Include views</span></span>

<span data-ttu-id="a56af-181">Use uma [biblioteca de classes Razor](xref:razor-pages/ui-class) para incluir exibições no assembly.</span><span class="sxs-lookup"><span data-stu-id="a56af-181">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="a56af-182">Impedir o carregamento de recursos</span><span class="sxs-lookup"><span data-stu-id="a56af-182">Prevent loading resources</span></span>

<span data-ttu-id="a56af-183">Partes de aplicativo podem ser usadas para *evitar* o carregamento de recursos em um determinado assembly ou local.</span><span class="sxs-lookup"><span data-stu-id="a56af-183">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="a56af-184">Adicionar ou remover membros da coleção de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para ocultar ou tornar os recursos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="a56af-184">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="a56af-185">A ordem das entradas na coleção `ApplicationParts` não é importante.</span><span class="sxs-lookup"><span data-stu-id="a56af-185">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="a56af-186">Configure o `ApplicationPartManager` antes de usá-lo para configurar serviços no contêiner.</span><span class="sxs-lookup"><span data-stu-id="a56af-186">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="a56af-187">Por exemplo, configure o `ApplicationPartManager` antes de invocar `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="a56af-187">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="a56af-188">Chame `Remove` na coleção de `ApplicationParts` para remover um recurso.</span><span class="sxs-lookup"><span data-stu-id="a56af-188">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="a56af-189">O código a seguir usa <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para remover `MyDependentLibrary` do aplicativo: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="a56af-189">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="a56af-190">O `ApplicationPartManager` inclui partes para:</span><span class="sxs-lookup"><span data-stu-id="a56af-190">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="a56af-191">O assembly do aplicativo e os assemblies dependentes.</span><span class="sxs-lookup"><span data-stu-id="a56af-191">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="a56af-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="a56af-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="a56af-193">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="a56af-193">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="a56af-194">Provedores de recursos de aplicativos</span><span class="sxs-lookup"><span data-stu-id="a56af-194">Application feature providers</span></span>

<span data-ttu-id="a56af-195">Os provedores de recursos de aplicativos examinam partes do aplicativo e fornecem recursos para essas partes.</span><span class="sxs-lookup"><span data-stu-id="a56af-195">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="a56af-196">Há provedores de recursos internos para os seguintes recursos de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a56af-196">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="a56af-197">Controladores</span><span class="sxs-lookup"><span data-stu-id="a56af-197">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="a56af-198">Auxiliares de marcação</span><span class="sxs-lookup"><span data-stu-id="a56af-198">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="a56af-199">Componentes da exibição</span><span class="sxs-lookup"><span data-stu-id="a56af-199">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="a56af-200">Provedores de recursos herdam de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, em que `T` é o tipo do recurso.</span><span class="sxs-lookup"><span data-stu-id="a56af-200">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="a56af-201">Os provedores de recursos podem ser implementados para qualquer um dos tipos de recursos listados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="a56af-201">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="a56af-202">A ordem dos provedores de recursos no `ApplicationPartManager.FeatureProviders` pode afetar o comportamento do tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="a56af-202">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="a56af-203">Provedores adicionados posteriormente podem reagir a ações executadas por provedores adicionados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="a56af-203">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="a56af-204">Exibir recursos disponíveis</span><span class="sxs-lookup"><span data-stu-id="a56af-204">Display available features</span></span>

<span data-ttu-id="a56af-205">Os recursos disponíveis para um aplicativo podem ser enumerados solicitando uma `ApplicationPartManager` por meio da [injeção de dependência](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="a56af-205">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="a56af-206">O [exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa o código anterior para exibir os recursos do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="a56af-206">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="a56af-207">Descoberta em partes do aplicativo</span><span class="sxs-lookup"><span data-stu-id="a56af-207">Discovery in application parts</span></span>

<span data-ttu-id="a56af-208">Os erros HTTP 404 não são incomuns ao desenvolver com partes do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="a56af-208">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="a56af-209">Esses erros são geralmente causados por falta de um requisito essencial para o modo como as partes de aplicativos são descobertas.</span><span class="sxs-lookup"><span data-stu-id="a56af-209">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="a56af-210">Se seu aplicativo retornar um erro HTTP 404, verifique se os seguintes requisitos foram atendidos:</span><span class="sxs-lookup"><span data-stu-id="a56af-210">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="a56af-211">A configuração de `applicationName` precisa ser definida como o assembly raiz usado para descoberta.</span><span class="sxs-lookup"><span data-stu-id="a56af-211">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="a56af-212">O assembly raiz usado para descoberta normalmente é o assembly de ponto de entrada.</span><span class="sxs-lookup"><span data-stu-id="a56af-212">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="a56af-213">O assembly raiz precisa ter uma referência às partes usadas para descoberta.</span><span class="sxs-lookup"><span data-stu-id="a56af-213">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="a56af-214">A referência pode ser direta ou transitiva.</span><span class="sxs-lookup"><span data-stu-id="a56af-214">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="a56af-215">O assembly raiz precisa fazer referência ao SDK da Web.</span><span class="sxs-lookup"><span data-stu-id="a56af-215">The root assembly needs to reference the Web SDK.</span></span>
  * <span data-ttu-id="a56af-216">A estrutura de ASP.NET Core tem uma lógica de compilação personalizada que carimba atributos no assembly raiz que são usados para descoberta.</span><span class="sxs-lookup"><span data-stu-id="a56af-216">The ASP.NET Core framework has custom build logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end
