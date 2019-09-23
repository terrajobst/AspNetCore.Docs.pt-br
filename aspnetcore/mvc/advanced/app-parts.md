---
title: Partes do aplicativo no ASP.NET Core
author: rick-anderson
description: Compartilhar controladores, exibir, Razor Pages e muito mais com partes de aplicativo no ASP.NET Core
ms.author: riande
ms.date: 05/14/2019
uid: mvc/extensibility/app-parts
ms.openlocfilehash: ad0372f25377115e6fc7c8ea42db75de56b3e6d2
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187005"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts-in-aspnet-core"></a><span data-ttu-id="b2ce5-103">Compartilhar controladores, exibições, Razor Pages e muito mais com partes de aplicativo no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b2ce5-103">Share controllers, views, Razor Pages and more with Application Parts in ASP.NET Core</span></span>
=======

<!-- DO NOT MAKE CHANGES BEFORE https://github.com/aspnet/AspNetCore.Docs/pull/12376 Merges -->

<span data-ttu-id="b2ce5-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b2ce5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b2ce5-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b2ce5-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b2ce5-106">Uma *parte do aplicativo* é uma abstração sobre os recursos de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="b2ce5-107">As partes do aplicativo permitem ASP.NET Core descobrir controladores, exibir componentes, auxiliares de marcas, Razor Pages, fontes de compilação do Razor e muito mais.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="b2ce5-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) é uma parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-108">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="b2ce5-109">`AssemblyPart`encapsula uma referência de assembly e expõe tipos e referências de compilação.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="b2ce5-110">Os *provedores de recursos* trabalham com partes do aplicativo para preencher os recursos de um aplicativo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-110">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="b2ce5-111">O principal caso de uso para partes do aplicativo é configurar um aplicativo para descobrir (ou evitar carregar) ASP.NET Core recursos de um assembly.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="b2ce5-112">Por exemplo, talvez você queira compartilhar funcionalidade comum entre vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="b2ce5-113">Usando partes de aplicativo, você pode compartilhar um assembly (DLL) contendo controladores, exibições, Razor Pages, fontes de compilação do Razor, auxiliares de marca e muito mais com vários aplicativos.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="b2ce5-114">O compartilhamento de um assembly é preferencial para duplicar o código em vários projetos.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="b2ce5-115">Os aplicativos ASP.NET Core carregam <xref:System.Web.WebPages.ApplicationPart>recursos do.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="b2ce5-116">A <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classe representa uma parte do aplicativo que é apoiada por um assembly.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="b2ce5-117">Carregar recursos de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b2ce5-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="b2ce5-118">Use as `ApplicationPart` classes `AssemblyPart` e para descobrir e carregar ASP.NET Core recursos (controladores, exibir componentes, etc.).</span><span class="sxs-lookup"><span data-stu-id="b2ce5-118">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="b2ce5-119">O <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> rastreia as partes do aplicativo e os provedores de recursos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="b2ce5-120">`ApplicationPartManager`está configurado em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b2ce5-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="b2ce5-121">O código a seguir fornece uma abordagem alternativa para `ApplicationPartManager` configurar `AssemblyPart`o usando:</span><span class="sxs-lookup"><span data-stu-id="b2ce5-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="b2ce5-122">Os dois exemplos de código anteriores carregam o `SharedController` de um assembly.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="b2ce5-123">O `SharedController` não está no projeto de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-123">The `SharedController` is not in the applications project.</span></span> <span data-ttu-id="b2ce5-124">Consulte o download de exemplo da [solução WebAppParts](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="b2ce5-124">See the [WebAppParts solution](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="b2ce5-125">Incluir exibições</span><span class="sxs-lookup"><span data-stu-id="b2ce5-125">Include views</span></span>

<span data-ttu-id="b2ce5-126">Para incluir exibições no assembly:</span><span class="sxs-lookup"><span data-stu-id="b2ce5-126">To include views in the assembly:</span></span>

* <span data-ttu-id="b2ce5-127">Adicione a seguinte marcação ao arquivo de projeto compartilhado:</span><span class="sxs-lookup"><span data-stu-id="b2ce5-127">Add the following markup to the shared project file:</span></span>

  ```csproj
    <ItemGroup>
      <EmbeddedResource Include = "Views\**\*.cshtml" />
    </ ItemGroup >
  ```

* <span data-ttu-id="b2ce5-128">Adicione o <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>ao:</span><span class="sxs-lookup"><span data-stu-id="b2ce5-128">Add the <xref:Microsoft.Extensions.FileProviders.EmbeddedFileProvider> to the <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine>:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/StartupViews.cs?name=snippet&highlight=3-7)]

### <a name="prevent-loading-resources"></a><span data-ttu-id="b2ce5-129">Impedir o carregamento de recursos</span><span class="sxs-lookup"><span data-stu-id="b2ce5-129">Prevent loading resources</span></span>

<span data-ttu-id="b2ce5-130">Partes de aplicativo podem ser usadas para *evitar* o carregamento de recursos em um determinado assembly ou local.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-130">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="b2ce5-131">Adicionar ou remover membros da <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> coleção para ocultar ou tornar os recursos disponíveis.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-131">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="b2ce5-132">A ordem das entradas na coleção `ApplicationParts` não é importante.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-132">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="b2ce5-133">Configure o `ApplicationPartManager` antes de usá-lo para configurar serviços no contêiner.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-133">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="b2ce5-134">Por exemplo, configure o `ApplicationPartManager` antes de `AddControllersAsServices`chamar.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-134">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="b2ce5-135">`Remove` Chame`ApplicationParts` na coleção para remover um recurso.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-135">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="b2ce5-136">O código a seguir <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> usa para `MyDependentLibrary` remover do aplicativo:[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="b2ce5-136">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="b2ce5-137">O `ApplicationPartManager` inclui partes para:</span><span class="sxs-lookup"><span data-stu-id="b2ce5-137">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="b2ce5-138">O assembly de aplicativos e os assemblies dependentes.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-138">The apps assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.TagHelpers`
* <span data-ttu-id="b2ce5-139">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-139">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="b2ce5-140">Provedores de recursos de aplicativos</span><span class="sxs-lookup"><span data-stu-id="b2ce5-140">Application feature providers</span></span>

<span data-ttu-id="b2ce5-141">Os provedores de recursos de aplicativos examinam partes do aplicativo e fornecem recursos para essas partes.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-141">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="b2ce5-142">Há provedores de recursos internos para os seguintes recursos de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b2ce5-142">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="b2ce5-143">Controladores</span><span class="sxs-lookup"><span data-stu-id="b2ce5-143">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="b2ce5-144">Auxiliares de marcação</span><span class="sxs-lookup"><span data-stu-id="b2ce5-144">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="b2ce5-145">Componentes da exibição</span><span class="sxs-lookup"><span data-stu-id="b2ce5-145">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="b2ce5-146">Provedores de recursos herdam de <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, em que `T` é o tipo do recurso.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-146">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="b2ce5-147">Os provedores de recursos podem ser implementados para qualquer um dos tipos de recursos listados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-147">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="b2ce5-148">A ordem dos provedores de recursos no `ApplicationPartManager.FeatureProviders` pode afetar o comportamento do tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-148">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="b2ce5-149">Provedores adicionados posteriormente podem reagir a ações executadas por provedores adicionados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-149">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="generic-controller-feature"></a><span data-ttu-id="b2ce5-150">Recurso de controlador genérico</span><span class="sxs-lookup"><span data-stu-id="b2ce5-150">Generic controller feature</span></span>

<span data-ttu-id="b2ce5-151">ASP.NET Core ignora [controladores genéricos](/dotnet/csharp/programming-guide/generics/generic-classes).</span><span class="sxs-lookup"><span data-stu-id="b2ce5-151">ASP.NET Core ignores [generic controllers](/dotnet/csharp/programming-guide/generics/generic-classes).</span></span> <span data-ttu-id="b2ce5-152">Um controlador genérico tem um parâmetro de tipo (por exemplo `MyController<T>`,).</span><span class="sxs-lookup"><span data-stu-id="b2ce5-152">A generic controller has a type parameter (for example, `MyController<T>`).</span></span> <span data-ttu-id="b2ce5-153">O exemplo a seguir adiciona instâncias de controlador genérico para uma lista especificada de tipos.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-153">The following sample adds generic controller instances for a specified list of types.</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerFeatureProvider.cs?name=snippet)]

<span data-ttu-id="b2ce5-154">Os tipos são definidos em `EntityTypes.Types`:</span><span class="sxs-lookup"><span data-stu-id="b2ce5-154">The types are defined in `EntityTypes.Types`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Models/EntityTypes.cs?name=snippet)]

<span data-ttu-id="b2ce5-155">O provedor de recursos é adicionado em `Startup`:</span><span class="sxs-lookup"><span data-stu-id="b2ce5-155">The feature provider is added in `Startup`:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Startup.cs?name=snippet)]

<span data-ttu-id="b2ce5-156">Os nomes de controlador genéricos usados para roteamento estão no formato *GenericController ' 1 [Widget]* em vez de *widget*.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-156">Generic controller names used for routing are of the form *GenericController\`1[Widget]* rather than *Widget*.</span></span> <span data-ttu-id="b2ce5-157">O atributo a seguir modifica o nome para corresponder ao tipo genérico usado pelo controlador:</span><span class="sxs-lookup"><span data-stu-id="b2ce5-157">The following attribute modifies the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="b2ce5-158">A classe `GenericController`:</span><span class="sxs-lookup"><span data-stu-id="b2ce5-158">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/GenericController.cs)]

### <a name="display-available-features"></a><span data-ttu-id="b2ce5-159">Exibir recursos disponíveis</span><span class="sxs-lookup"><span data-stu-id="b2ce5-159">Display available features</span></span>

<span data-ttu-id="b2ce5-160">Os recursos disponíveis para um aplicativo podem ser enumerados pelo solicitando uma `ApplicationPartManager` [injeção de dependência](../../fundamentals/dependency-injection.md)por meio de:</span><span class="sxs-lookup"><span data-stu-id="b2ce5-160">The features available to an app can be enumerated by by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="b2ce5-161">O [exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa o código anterior para exibir os recursos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b2ce5-161">The [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features.</span></span>