---
title: Partes do aplicativo no ASP.NET Core
author: rick-anderson
description: Compartilhar controladores, exibir, Razor Pages e muito mais com partes de aplicativo no ASP.NET Core
ms.author: riande
ms.date: 05/14/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 4b4c8c554a7045a180b56cf9998ab1a8496cde1b
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207356"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a><span data-ttu-id="8c69b-103">Compartilhar controladores, exibições, Razor Pages e muito mais com partes de aplicativo no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c69b-103">Share controllers, views, Razor Pages and more with Application Parts in ASP.NET Core</span></span>

<span data-ttu-id="8c69b-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8c69b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8c69b-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8c69b-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8c69b-106">Uma *parte do aplicativo* é uma abstração sobre os recursos de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8c69b-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="8c69b-107">As partes do aplicativo permitem ASP.NET Core descobrir controladores, exibir componentes, auxiliares de marcas, Razor Pages, fontes de compilação do Razor e muito mais.</span><span class="sxs-lookup"><span data-stu-id="8c69b-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="8c69b-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) é uma parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8c69b-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="8c69b-109">`AssemblyPart`encapsula uma referência de assembly e expõe tipos e referências de compilação.</span><span class="sxs-lookup"><span data-stu-id="8c69b-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="8c69b-110">Os *provedores de recursos* trabalham com partes do aplicativo para preencher os recursos de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c69b-110">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="8c69b-111">O principal caso de uso para partes do aplicativo é configurar um aplicativo para descobrir (ou evitar carregar) ASP.NET Core recursos de um assembly.</span><span class="sxs-lookup"><span data-stu-id="8c69b-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="8c69b-112">Por exemplo, talvez você queira compartilhar funcionalidade comum entre vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="8c69b-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="8c69b-113">Usando partes de aplicativo, você pode compartilhar um assembly (DLL) contendo controladores, exibições, Razor Pages, fontes de compilação do Razor, auxiliares de marca e muito mais com vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="8c69b-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="8c69b-114">O compartilhamento de um assembly é preferencial para duplicar o código em vários projetos.</span><span class="sxs-lookup"><span data-stu-id="8c69b-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="8c69b-115">Os aplicativos ASP.NET Core carregam <xref:System.Web.WebPages.ApplicationPart>recursos do.</span><span class="sxs-lookup"><span data-stu-id="8c69b-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="8c69b-116">A <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classe representa uma parte do aplicativo que é apoiada por um assembly.</span><span class="sxs-lookup"><span data-stu-id="8c69b-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="8c69b-117">Carregar recursos de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c69b-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="8c69b-118">Use as `ApplicationPart` classes `AssemblyPart` e para descobrir e carregar ASP.NET Core recursos (controladores, exibir componentes, etc.).</span><span class="sxs-lookup"><span data-stu-id="8c69b-118">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="8c69b-119">O <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> rastreia as partes do aplicativo e os provedores de recursos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="8c69b-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="8c69b-120">`ApplicationPartManager`está configurado em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8c69b-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="8c69b-121">O código a seguir fornece uma abordagem alternativa para `ApplicationPartManager` configurar `AssemblyPart`o usando:</span><span class="sxs-lookup"><span data-stu-id="8c69b-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="8c69b-122">Os dois exemplos de código anteriores carregam o `SharedController` de um assembly.</span><span class="sxs-lookup"><span data-stu-id="8c69b-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="8c69b-123">O `SharedController` não está no projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8c69b-123">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="8c69b-124">Consulte o download de exemplo da [solução WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="8c69b-124">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="8c69b-125">Incluir exibições</span><span class="sxs-lookup"><span data-stu-id="8c69b-125">Include views</span></span>

<span data-ttu-id="8c69b-126">Para incluir exibições no assembly:</span><span class="sxs-lookup"><span data-stu-id="8c69b-126">To include views in the assembly:</span></span>

* <span data-ttu-id="8c69b-127">Adicione a seguinte marcação ao arquivo de projeto compartilhado:</span><span class="sxs-lookup"><span data-stu-id="8c69b-127">Add the following markup to the shared project file:</span></span>

  ```csproj
  <ItemGroup>
      <EmbeddedResource Include="Views\**\*.cshtml" />
  </ItemGroup>
  ```

* <span data-ttu-id="8c69b-128">Adicione o <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>ao:</span><span class="sxs-lookup"><span data-stu-id="8c69b-128">Add the <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> to the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span></span>

  [!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a><span data-ttu-id="8c69b-129">Impedir o carregamento de recursos</span><span class="sxs-lookup"><span data-stu-id="8c69b-129">Prevent loading resources</span></span>

<span data-ttu-id="8c69b-130">Partes de aplicativo podem ser usadas para *evitar* o carregamento de recursos em um determinado assembly ou local.</span><span class="sxs-lookup"><span data-stu-id="8c69b-130">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="8c69b-131">Adicionar ou remover membros da <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> coleção para ocultar ou tornar os recursos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="8c69b-131">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="8c69b-132">A ordem das entradas na coleção `ApplicationParts` não é importante.</span><span class="sxs-lookup"><span data-stu-id="8c69b-132">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="8c69b-133">Configure o `ApplicationPartManager` antes de usá-lo para configurar serviços no contêiner.</span><span class="sxs-lookup"><span data-stu-id="8c69b-133">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="8c69b-134">Por exemplo, configure o `ApplicationPartManager` antes de `AddControllersAsServices`chamar.</span><span class="sxs-lookup"><span data-stu-id="8c69b-134">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="8c69b-135">`Remove` Chame`ApplicationParts` na coleção para remover um recurso.</span><span class="sxs-lookup"><span data-stu-id="8c69b-135">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="8c69b-136">O código a seguir <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> usa para `MyDependentLibrary` remover do aplicativo:[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="8c69b-136">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="8c69b-137">O `ApplicationPartManager` inclui partes para:</span><span class="sxs-lookup"><span data-stu-id="8c69b-137">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="8c69b-138">O assembly do aplicativo e os assemblies dependentes.</span><span class="sxs-lookup"><span data-stu-id="8c69b-138">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="8c69b-139">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="8c69b-139">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="8c69b-140">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="8c69b-140">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="8c69b-141">Provedores de recursos de aplicativos</span><span class="sxs-lookup"><span data-stu-id="8c69b-141">Application feature providers</span></span>

<span data-ttu-id="8c69b-142">Os provedores de recursos de aplicativos examinam partes do aplicativo e fornecem recursos para essas partes.</span><span class="sxs-lookup"><span data-stu-id="8c69b-142">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="8c69b-143">Há provedores de recursos internos para os seguintes recursos de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="8c69b-143">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="8c69b-144">Controladores</span><span class="sxs-lookup"><span data-stu-id="8c69b-144">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="8c69b-145">Auxiliares de marcação</span><span class="sxs-lookup"><span data-stu-id="8c69b-145">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="8c69b-146">Componentes da exibição</span><span class="sxs-lookup"><span data-stu-id="8c69b-146">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="8c69b-147">Provedores de recursos herdam de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, em que `T` é o tipo do recurso.</span><span class="sxs-lookup"><span data-stu-id="8c69b-147">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="8c69b-148">Os provedores de recursos podem ser implementados para qualquer um dos tipos de recursos listados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="8c69b-148">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="8c69b-149">A ordem dos provedores de recursos no `ApplicationPartManager.FeatureProviders` pode afetar o comportamento do tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="8c69b-149">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="8c69b-150">Provedores adicionados posteriormente podem reagir a ações executadas por provedores adicionados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="8c69b-150">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="generic-controller-feature"></a><span data-ttu-id="8c69b-151">Recurso de controlador genérico</span><span class="sxs-lookup"><span data-stu-id="8c69b-151">Generic controller feature</span></span>

<span data-ttu-id="8c69b-152">ASP.NET Core ignora [controladores genéricos](/dotnet/csharp/programming-guide/generics/generic-classes).</span><span class="sxs-lookup"><span data-stu-id="8c69b-152">ASP.NET Core ignores [generic controllers](/dotnet/csharp/programming-guide/generics/generic-classes).</span></span> <span data-ttu-id="8c69b-153">Um controlador genérico tem um parâmetro de tipo (por exemplo `MyController<T>`,).</span><span class="sxs-lookup"><span data-stu-id="8c69b-153">A generic controller has a type parameter (for example, `MyController<T>`).</span></span> <span data-ttu-id="8c69b-154">O exemplo a seguir adiciona instâncias de controlador genérico para uma lista especificada de tipos:</span><span class="sxs-lookup"><span data-stu-id="8c69b-154">The following sample adds generic controller instances for a specified list of types:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

<span data-ttu-id="8c69b-155">Os tipos são definidos em `EntityTypes.Types`:</span><span class="sxs-lookup"><span data-stu-id="8c69b-155">The types are defined in `EntityTypes.Types`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

<span data-ttu-id="8c69b-156">O provedor de recursos é adicionado em `Startup`:</span><span class="sxs-lookup"><span data-stu-id="8c69b-156">The feature provider is added in `Startup`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

<span data-ttu-id="8c69b-157">Os nomes de controlador genéricos usados para roteamento estão no formato *GenericController ' 1 [Widget]* em vez de *widget*.</span><span class="sxs-lookup"><span data-stu-id="8c69b-157">Generic controller names used for routing are of the form *GenericController\`1[Widget]* rather than *Widget*.</span></span> <span data-ttu-id="8c69b-158">O atributo a seguir modifica o nome para corresponder ao tipo genérico usado pelo controlador:</span><span class="sxs-lookup"><span data-stu-id="8c69b-158">The following attribute modifies the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="8c69b-159">A classe `GenericController`:</span><span class="sxs-lookup"><span data-stu-id="8c69b-159">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

<span data-ttu-id="8c69b-160">Por exemplo, solicitar uma URL de `https://localhost:5001/Sprocket` resulta na seguinte resposta:</span><span class="sxs-lookup"><span data-stu-id="8c69b-160">For example, requesting a URL of `https://localhost:5001/Sprocket` results in the following response:</span></span>

```text
Hello from a generic Sprocket controller.
```

### <a name="display-available-features"></a><span data-ttu-id="8c69b-161">Exibir recursos disponíveis</span><span class="sxs-lookup"><span data-stu-id="8c69b-161">Display available features</span></span>

<span data-ttu-id="8c69b-162">Os recursos disponíveis para um aplicativo podem ser enumerados solicitando uma `ApplicationPartManager` [injeção de dependência](../../fundamentals/dependency-injection.md)por meio do:</span><span class="sxs-lookup"><span data-stu-id="8c69b-162">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="8c69b-163">O [exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa o código anterior para exibir os recursos do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="8c69b-163">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

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
