---
title: Partes do aplicativo no ASP.NET Core
author: rick-anderson
description: Compartilhar controladores, exibir, Razor Pages e muito mais com partes de aplicativo no ASP.NET Core
ms.author: riande
ms.date: 11/7/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: ff6afa1852a3ee97fc4dbbae970dd746ec92f74c
ms.sourcegitcommit: 67116718dc33a7a01696d41af38590fdbb58e014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799460"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a><span data-ttu-id="43c9d-103">Compartilhar controladores, exibições, Razor Pages e muito mais com partes de aplicativo no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="43c9d-103">Share controllers, views, Razor Pages and more with Application Parts in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="43c9d-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="43c9d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="43c9d-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="43c9d-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="43c9d-106">Uma *parte do aplicativo* é uma abstração sobre os recursos de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43c9d-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="43c9d-107">As partes do aplicativo permitem ASP.NET Core descobrir controladores, exibir componentes, auxiliares de marcas, Razor Pages, fontes de compilação do Razor e muito mais.</span><span class="sxs-lookup"><span data-stu-id="43c9d-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="43c9d-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) é uma parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43c9d-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="43c9d-109">`AssemblyPart` encapsula uma referência de assembly e expõe tipos e referências de compilação.</span><span class="sxs-lookup"><span data-stu-id="43c9d-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="43c9d-110">Os *provedores de recursos* trabalham com partes do aplicativo para preencher os recursos de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43c9d-110">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="43c9d-111">O principal caso de uso para partes do aplicativo é configurar um aplicativo para descobrir (ou evitar carregar) ASP.NET Core recursos de um assembly.</span><span class="sxs-lookup"><span data-stu-id="43c9d-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="43c9d-112">Por exemplo, talvez você queira compartilhar funcionalidade comum entre vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="43c9d-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="43c9d-113">Usando partes de aplicativo, você pode compartilhar um assembly (DLL) contendo controladores, exibições, Razor Pages, fontes de compilação do Razor, auxiliares de marca e muito mais com vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="43c9d-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="43c9d-114">O compartilhamento de um assembly é preferencial para duplicar o código em vários projetos.</span><span class="sxs-lookup"><span data-stu-id="43c9d-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="43c9d-115">Os aplicativos ASP.NET Core carregam recursos do <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="43c9d-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="43c9d-116">A classe <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> representa uma parte do aplicativo que é apoiada por um assembly.</span><span class="sxs-lookup"><span data-stu-id="43c9d-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="43c9d-117">Carregar recursos de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="43c9d-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="43c9d-118">Use as classes `ApplicationPart` e `AssemblyPart` para descobrir e carregar recursos de ASP.NET Core (controladores, exibir componentes, etc.).</span><span class="sxs-lookup"><span data-stu-id="43c9d-118">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="43c9d-119">O <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> controla as partes do aplicativo e os provedores de recursos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="43c9d-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="43c9d-120">o `ApplicationPartManager` está configurado em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="43c9d-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="43c9d-121">O código a seguir fornece uma abordagem alternativa para configurar `ApplicationPartManager` usando `AssemblyPart`:</span><span class="sxs-lookup"><span data-stu-id="43c9d-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="43c9d-122">Os dois exemplos de código anteriores carregam o `SharedController` de um assembly.</span><span class="sxs-lookup"><span data-stu-id="43c9d-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="43c9d-123">O `SharedController` não está no projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43c9d-123">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="43c9d-124">Consulte o download de exemplo da [solução WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="43c9d-124">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="43c9d-125">Incluir exibições</span><span class="sxs-lookup"><span data-stu-id="43c9d-125">Include views</span></span>

<span data-ttu-id="43c9d-126">Para incluir exibições no assembly:</span><span class="sxs-lookup"><span data-stu-id="43c9d-126">To include views in the assembly:</span></span>

* <span data-ttu-id="43c9d-127">Adicione a seguinte marcação ao arquivo de projeto compartilhado:</span><span class="sxs-lookup"><span data-stu-id="43c9d-127">Add the following markup to the shared project file:</span></span>

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <span data-ttu-id="43c9d-128">Adicione o <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> ao <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span><span class="sxs-lookup"><span data-stu-id="43c9d-128">Add the <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> to the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span></span>

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a><span data-ttu-id="43c9d-129">Impedir o carregamento de recursos</span><span class="sxs-lookup"><span data-stu-id="43c9d-129">Prevent loading resources</span></span>

<span data-ttu-id="43c9d-130">Partes de aplicativo podem ser usadas para *evitar* o carregamento de recursos em um determinado assembly ou local.</span><span class="sxs-lookup"><span data-stu-id="43c9d-130">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="43c9d-131">Adicionar ou remover membros da coleção de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para ocultar ou tornar os recursos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="43c9d-131">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="43c9d-132">A ordem das entradas na coleção `ApplicationParts` não é importante.</span><span class="sxs-lookup"><span data-stu-id="43c9d-132">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="43c9d-133">Configure o `ApplicationPartManager` antes de usá-lo para configurar serviços no contêiner.</span><span class="sxs-lookup"><span data-stu-id="43c9d-133">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="43c9d-134">Por exemplo, configure o `ApplicationPartManager` antes de invocar `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="43c9d-134">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="43c9d-135">Chame `Remove` na coleção de `ApplicationParts` para remover um recurso.</span><span class="sxs-lookup"><span data-stu-id="43c9d-135">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="43c9d-136">O código a seguir usa <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para remover `MyDependentLibrary` do aplicativo: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="43c9d-136">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="43c9d-137">O `ApplicationPartManager` inclui partes para:</span><span class="sxs-lookup"><span data-stu-id="43c9d-137">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="43c9d-138">O assembly do aplicativo e os assemblies dependentes.</span><span class="sxs-lookup"><span data-stu-id="43c9d-138">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="43c9d-139">`Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="43c9d-139">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="43c9d-140">`Microsoft.AspNetCore.Mvc.Razor`</span><span class="sxs-lookup"><span data-stu-id="43c9d-140">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="43c9d-141">Provedores de recursos de aplicativos</span><span class="sxs-lookup"><span data-stu-id="43c9d-141">Application feature providers</span></span>

<span data-ttu-id="43c9d-142">Os provedores de recursos de aplicativos examinam partes do aplicativo e fornecem recursos para essas partes.</span><span class="sxs-lookup"><span data-stu-id="43c9d-142">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="43c9d-143">Há provedores de recursos internos para os seguintes recursos de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="43c9d-143">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="43c9d-144">Controladores</span><span class="sxs-lookup"><span data-stu-id="43c9d-144">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="43c9d-145">Auxiliares de Marcas</span><span class="sxs-lookup"><span data-stu-id="43c9d-145">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="43c9d-146">Componentes da exibição</span><span class="sxs-lookup"><span data-stu-id="43c9d-146">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="43c9d-147">Provedores de recursos herdam de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, em que `T` é o tipo do recurso.</span><span class="sxs-lookup"><span data-stu-id="43c9d-147">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="43c9d-148">Os provedores de recursos podem ser implementados para qualquer um dos tipos de recursos listados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="43c9d-148">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="43c9d-149">A ordem dos provedores de recursos no `ApplicationPartManager.FeatureProviders` pode afetar o comportamento do tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="43c9d-149">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="43c9d-150">Provedores adicionados posteriormente podem reagir a ações executadas por provedores adicionados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="43c9d-150">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="generic-controller-feature"></a><span data-ttu-id="43c9d-151">Recurso de controlador genérico</span><span class="sxs-lookup"><span data-stu-id="43c9d-151">Generic controller feature</span></span>

<span data-ttu-id="43c9d-152">ASP.NET Core ignora [controladores genéricos](/dotnet/csharp/programming-guide/generics/generic-classes).</span><span class="sxs-lookup"><span data-stu-id="43c9d-152">ASP.NET Core ignores [generic controllers](/dotnet/csharp/programming-guide/generics/generic-classes).</span></span> <span data-ttu-id="43c9d-153">Um controlador genérico tem um parâmetro de tipo (por exemplo, `MyController<T>`).</span><span class="sxs-lookup"><span data-stu-id="43c9d-153">A generic controller has a type parameter (for example, `MyController<T>`).</span></span> <span data-ttu-id="43c9d-154">O exemplo a seguir adiciona instâncias de controlador genérico para uma lista especificada de tipos:</span><span class="sxs-lookup"><span data-stu-id="43c9d-154">The following sample adds generic controller instances for a specified list of types:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

<span data-ttu-id="43c9d-155">Os tipos são definidos no `EntityTypes.Types`:</span><span class="sxs-lookup"><span data-stu-id="43c9d-155">The types are defined in `EntityTypes.Types`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

<span data-ttu-id="43c9d-156">O provedor de recursos é adicionado em `Startup`:</span><span class="sxs-lookup"><span data-stu-id="43c9d-156">The feature provider is added in `Startup`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

<span data-ttu-id="43c9d-157">Os nomes de controlador genéricos usados para roteamento estão no formato *GenericController ' 1 [Widget]* em vez de *widget*.</span><span class="sxs-lookup"><span data-stu-id="43c9d-157">Generic controller names used for routing are of the form *GenericController\`1[Widget]* rather than *Widget*.</span></span> <span data-ttu-id="43c9d-158">O atributo a seguir modifica o nome para corresponder ao tipo genérico usado pelo controlador:</span><span class="sxs-lookup"><span data-stu-id="43c9d-158">The following attribute modifies the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="43c9d-159">A classe `GenericController`:</span><span class="sxs-lookup"><span data-stu-id="43c9d-159">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

<span data-ttu-id="43c9d-160">Por exemplo, solicitar uma URL de `https://localhost:5001/Sprocket` resulta na seguinte resposta:</span><span class="sxs-lookup"><span data-stu-id="43c9d-160">For example, requesting a URL of `https://localhost:5001/Sprocket` results in the following response:</span></span>

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a><span data-ttu-id="43c9d-161">Exibir recursos disponíveis</span><span class="sxs-lookup"><span data-stu-id="43c9d-161">Display available features</span></span>

<span data-ttu-id="43c9d-162">Os recursos disponíveis para um aplicativo podem ser enumerados solicitando uma `ApplicationPartManager` por meio da [injeção de dependência](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="43c9d-162">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="43c9d-163">O [exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa o código anterior para exibir os recursos do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="43c9d-163">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

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

::: moniker-end

::: moniker range="<= aspnetcore-3.0"

<span data-ttu-id="43c9d-164">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="43c9d-164">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="43c9d-165">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="43c9d-165">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="43c9d-166">Uma *parte do aplicativo* é uma abstração sobre os recursos de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43c9d-166">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="43c9d-167">As partes do aplicativo permitem ASP.NET Core descobrir controladores, exibir componentes, auxiliares de marcas, Razor Pages, fontes de compilação do Razor e muito mais.</span><span class="sxs-lookup"><span data-stu-id="43c9d-167">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="43c9d-168">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) é uma parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43c9d-168">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="43c9d-169">`AssemblyPart` encapsula uma referência de assembly e expõe tipos e referências de compilação.</span><span class="sxs-lookup"><span data-stu-id="43c9d-169">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="43c9d-170">Os *provedores de recursos* trabalham com partes do aplicativo para preencher os recursos de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="43c9d-170">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="43c9d-171">O principal caso de uso para partes do aplicativo é configurar um aplicativo para descobrir (ou evitar carregar) ASP.NET Core recursos de um assembly.</span><span class="sxs-lookup"><span data-stu-id="43c9d-171">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="43c9d-172">Por exemplo, talvez você queira compartilhar funcionalidade comum entre vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="43c9d-172">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="43c9d-173">Usando partes de aplicativo, você pode compartilhar um assembly (DLL) contendo controladores, exibições, Razor Pages, fontes de compilação do Razor, auxiliares de marca e muito mais com vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="43c9d-173">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="43c9d-174">O compartilhamento de um assembly é preferencial para duplicar o código em vários projetos.</span><span class="sxs-lookup"><span data-stu-id="43c9d-174">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="43c9d-175">Os aplicativos ASP.NET Core carregam recursos do <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="43c9d-175">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="43c9d-176">A classe <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> representa uma parte do aplicativo que é apoiada por um assembly.</span><span class="sxs-lookup"><span data-stu-id="43c9d-176">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="43c9d-177">Carregar recursos de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="43c9d-177">Load ASP.NET Core features</span></span>

<span data-ttu-id="43c9d-178">Use as classes `ApplicationPart` e `AssemblyPart` para descobrir e carregar recursos de ASP.NET Core (controladores, exibir componentes, etc.).</span><span class="sxs-lookup"><span data-stu-id="43c9d-178">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="43c9d-179">O <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> controla as partes do aplicativo e os provedores de recursos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="43c9d-179">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="43c9d-180">o `ApplicationPartManager` está configurado em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="43c9d-180">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="43c9d-181">O código a seguir fornece uma abordagem alternativa para configurar `ApplicationPartManager` usando `AssemblyPart`:</span><span class="sxs-lookup"><span data-stu-id="43c9d-181">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="43c9d-182">Os dois exemplos de código anteriores carregam o `SharedController` de um assembly.</span><span class="sxs-lookup"><span data-stu-id="43c9d-182">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="43c9d-183">O `SharedController` não está no projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="43c9d-183">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="43c9d-184">Consulte o download de exemplo da [solução WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="43c9d-184">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="43c9d-185">Incluir exibições</span><span class="sxs-lookup"><span data-stu-id="43c9d-185">Include views</span></span>

<span data-ttu-id="43c9d-186">Para incluir exibições no assembly:</span><span class="sxs-lookup"><span data-stu-id="43c9d-186">To include views in the assembly:</span></span>

* <span data-ttu-id="43c9d-187">Adicione a seguinte marcação ao arquivo de projeto compartilhado:</span><span class="sxs-lookup"><span data-stu-id="43c9d-187">Add the following markup to the shared project file:</span></span>

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <span data-ttu-id="43c9d-188">Adicione o <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> ao <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span><span class="sxs-lookup"><span data-stu-id="43c9d-188">Add the <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> to the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span></span>

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a><span data-ttu-id="43c9d-189">Impedir o carregamento de recursos</span><span class="sxs-lookup"><span data-stu-id="43c9d-189">Prevent loading resources</span></span>

<span data-ttu-id="43c9d-190">Partes de aplicativo podem ser usadas para *evitar* o carregamento de recursos em um determinado assembly ou local.</span><span class="sxs-lookup"><span data-stu-id="43c9d-190">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="43c9d-191">Adicionar ou remover membros da coleção de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para ocultar ou tornar os recursos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="43c9d-191">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="43c9d-192">A ordem das entradas na coleção `ApplicationParts` não é importante.</span><span class="sxs-lookup"><span data-stu-id="43c9d-192">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="43c9d-193">Configure o `ApplicationPartManager` antes de usá-lo para configurar serviços no contêiner.</span><span class="sxs-lookup"><span data-stu-id="43c9d-193">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="43c9d-194">Por exemplo, configure o `ApplicationPartManager` antes de invocar `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="43c9d-194">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="43c9d-195">Chame `Remove` na coleção de `ApplicationParts` para remover um recurso.</span><span class="sxs-lookup"><span data-stu-id="43c9d-195">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="43c9d-196">O código a seguir usa <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> para remover `MyDependentLibrary` do aplicativo: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="43c9d-196">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="43c9d-197">O `ApplicationPartManager` inclui partes para:</span><span class="sxs-lookup"><span data-stu-id="43c9d-197">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="43c9d-198">O assembly do aplicativo e os assemblies dependentes.</span><span class="sxs-lookup"><span data-stu-id="43c9d-198">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="43c9d-199">`Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="43c9d-199">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="43c9d-200">`Microsoft.AspNetCore.Mvc.Razor`</span><span class="sxs-lookup"><span data-stu-id="43c9d-200">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="43c9d-201">Provedores de recursos de aplicativos</span><span class="sxs-lookup"><span data-stu-id="43c9d-201">Application feature providers</span></span>

<span data-ttu-id="43c9d-202">Os provedores de recursos de aplicativos examinam partes do aplicativo e fornecem recursos para essas partes.</span><span class="sxs-lookup"><span data-stu-id="43c9d-202">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="43c9d-203">Há provedores de recursos internos para os seguintes recursos de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="43c9d-203">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="43c9d-204">Controladores</span><span class="sxs-lookup"><span data-stu-id="43c9d-204">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="43c9d-205">Auxiliares de Marcas</span><span class="sxs-lookup"><span data-stu-id="43c9d-205">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="43c9d-206">Componentes da exibição</span><span class="sxs-lookup"><span data-stu-id="43c9d-206">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="43c9d-207">Provedores de recursos herdam de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, em que `T` é o tipo do recurso.</span><span class="sxs-lookup"><span data-stu-id="43c9d-207">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="43c9d-208">Os provedores de recursos podem ser implementados para qualquer um dos tipos de recursos listados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="43c9d-208">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="43c9d-209">A ordem dos provedores de recursos no `ApplicationPartManager.FeatureProviders` pode afetar o comportamento do tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="43c9d-209">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="43c9d-210">Provedores adicionados posteriormente podem reagir a ações executadas por provedores adicionados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="43c9d-210">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="generic-controller-feature"></a><span data-ttu-id="43c9d-211">Recurso de controlador genérico</span><span class="sxs-lookup"><span data-stu-id="43c9d-211">Generic controller feature</span></span>

<span data-ttu-id="43c9d-212">ASP.NET Core ignora [controladores genéricos](/dotnet/csharp/programming-guide/generics/generic-classes).</span><span class="sxs-lookup"><span data-stu-id="43c9d-212">ASP.NET Core ignores [generic controllers](/dotnet/csharp/programming-guide/generics/generic-classes).</span></span> <span data-ttu-id="43c9d-213">Um controlador genérico tem um parâmetro de tipo (por exemplo, `MyController<T>`).</span><span class="sxs-lookup"><span data-stu-id="43c9d-213">A generic controller has a type parameter (for example, `MyController<T>`).</span></span> <span data-ttu-id="43c9d-214">O exemplo a seguir adiciona instâncias de controlador genérico para uma lista especificada de tipos:</span><span class="sxs-lookup"><span data-stu-id="43c9d-214">The following sample adds generic controller instances for a specified list of types:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

<span data-ttu-id="43c9d-215">Os tipos são definidos no `EntityTypes.Types`:</span><span class="sxs-lookup"><span data-stu-id="43c9d-215">The types are defined in `EntityTypes.Types`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

<span data-ttu-id="43c9d-216">O provedor de recursos é adicionado em `Startup`:</span><span class="sxs-lookup"><span data-stu-id="43c9d-216">The feature provider is added in `Startup`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

<span data-ttu-id="43c9d-217">Os nomes de controlador genéricos usados para roteamento estão no formato *GenericController ' 1 [Widget]* em vez de *widget*.</span><span class="sxs-lookup"><span data-stu-id="43c9d-217">Generic controller names used for routing are of the form *GenericController\`1[Widget]* rather than *Widget*.</span></span> <span data-ttu-id="43c9d-218">O atributo a seguir modifica o nome para corresponder ao tipo genérico usado pelo controlador:</span><span class="sxs-lookup"><span data-stu-id="43c9d-218">The following attribute modifies the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="43c9d-219">A classe `GenericController`:</span><span class="sxs-lookup"><span data-stu-id="43c9d-219">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

<span data-ttu-id="43c9d-220">Por exemplo, solicitar uma URL de `https://localhost:5001/Sprocket` resulta na seguinte resposta:</span><span class="sxs-lookup"><span data-stu-id="43c9d-220">For example, requesting a URL of `https://localhost:5001/Sprocket` results in the following response:</span></span>

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a><span data-ttu-id="43c9d-221">Exibir recursos disponíveis</span><span class="sxs-lookup"><span data-stu-id="43c9d-221">Display available features</span></span>

<span data-ttu-id="43c9d-222">Os recursos disponíveis para um aplicativo podem ser enumerados solicitando uma `ApplicationPartManager` por meio da [injeção de dependência](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="43c9d-222">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="43c9d-223">O [exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa o código anterior para exibir os recursos do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="43c9d-223">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

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

::: moniker-end