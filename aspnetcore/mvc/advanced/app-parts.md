---
title: Partes do aplicativo no ASP.NET Core
author: ardalis
description: Saiba como usar as partes do aplicativo, que são abstrações sobre os recursos de um aplicativo, para descobrir ou evitar o carregamento de recursos de um assembly.
ms.author: riande
ms.date: 01/04/2017
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 67bd40adef4cdb0bd781f70114d3954cd9a8ed09
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320089"
---
# <a name="application-parts-in-aspnet-core"></a><span data-ttu-id="bc374-103">Partes do aplicativo no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bc374-103">Application Parts in ASP.NET Core</span></span>

<span data-ttu-id="bc374-104">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bc374-104">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="bc374-105">Uma *Parte do aplicativo* é uma abstração dos recursos de um aplicativo, da qual recursos de MVC como controladores, componentes de exibição ou auxiliares de marca podem ser descobertos.</span><span class="sxs-lookup"><span data-stu-id="bc374-105">An *Application Part* is an abstraction over the resources of an application, from which MVC features like controllers, view components, or tag helpers may be discovered.</span></span> <span data-ttu-id="bc374-106">Um exemplo de uma parte do aplicativo é um AssemblyPart, que encapsula uma referência de assembly e expõe tipos e referências de compilação.</span><span class="sxs-lookup"><span data-stu-id="bc374-106">One example of an application part is an AssemblyPart, which encapsulates an assembly reference and exposes types and compilation references.</span></span> <span data-ttu-id="bc374-107">*Provedores de recursos* funcionam com as partes do aplicativo para preencher os recursos de um aplicativo do ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="bc374-107">*Feature providers* work with application parts to populate the features of an ASP.NET Core MVC app.</span></span> <span data-ttu-id="bc374-108">O caso de uso principal das partes do aplicativo é permitir que você configure seu aplicativo para descobrir (ou evitar o carregamento) recursos de MVC de um assembly.</span><span class="sxs-lookup"><span data-stu-id="bc374-108">The main use case for application parts is to allow you to configure your app to discover (or avoid loading) MVC features from an assembly.</span></span>

## <a name="introducing-application-parts"></a><span data-ttu-id="bc374-109">Introdução às partes do aplicativo</span><span class="sxs-lookup"><span data-stu-id="bc374-109">Introducing Application Parts</span></span>

<span data-ttu-id="bc374-110">Aplicativos de MVC carregam seus recursos das [partes do aplicativo](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span><span class="sxs-lookup"><span data-stu-id="bc374-110">MVC apps load their features from [application parts](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span></span> <span data-ttu-id="bc374-111">Em particular, a classe [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) representa uma parte de aplicativo que é apoiada por um assembly.</span><span class="sxs-lookup"><span data-stu-id="bc374-111">In particular, the [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) class represents an application part that's backed by an assembly.</span></span> <span data-ttu-id="bc374-112">Você pode usar essas classes para descobrir e carregar recursos de MVC, como controladores, componentes de exibição, auxiliares de marca e fontes de compilação do Razor.</span><span class="sxs-lookup"><span data-stu-id="bc374-112">You can use these classes to discover and load MVC features, such as controllers, view components, tag helpers, and razor compilation sources.</span></span> <span data-ttu-id="bc374-113">O [ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) é responsável por controlar as partes do aplicativo e os provedores de recursos disponíveis para o aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="bc374-113">The [ApplicationPartManager](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) is responsible for tracking the application parts and feature providers available to the MVC app.</span></span> <span data-ttu-id="bc374-114">Você pode interagir com o `ApplicationPartManager` em `Startup` quando configura o MVC:</span><span class="sxs-lookup"><span data-stu-id="bc374-114">You can interact with the `ApplicationPartManager` in `Startup` when you configure MVC:</span></span>

```csharp
// create an assembly part from a class's assembly
var assembly = typeof(Startup).GetTypeInfo().Assembly;
services.AddMvc()
    .AddApplicationPart(assembly);

// OR
var assembly = typeof(Startup).GetTypeInfo().Assembly;
var part = new AssemblyPart(assembly);
services.AddMvc()
    .ConfigureApplicationPartManager(apm => apm.ApplicationParts.Add(part));
```

<span data-ttu-id="bc374-115">Por padrão, o MVC pesquisa a árvore de dependência e localiza controladores (mesmo em outros assemblies).</span><span class="sxs-lookup"><span data-stu-id="bc374-115">By default MVC will search the dependency tree and find controllers (even in other assemblies).</span></span> <span data-ttu-id="bc374-116">Para carregar um assembly arbitrário (por exemplo, de um plug-in que não é referenciado em tempo de compilação), você pode usar uma parte do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bc374-116">To load an arbitrary assembly (for instance, from a plugin that isn't referenced at compile time), you can use an application part.</span></span>

<span data-ttu-id="bc374-117">Você pode usar as partes do aplicativo para *evitar* pesquisar controladores em um determinado assembly ou local.</span><span class="sxs-lookup"><span data-stu-id="bc374-117">You can use application parts to *avoid* looking for controllers in a particular assembly or location.</span></span> <span data-ttu-id="bc374-118">Você pode controlar quais partes (ou assemblies) ficam disponíveis para o aplicativo modificando a coleção `ApplicationParts` do `ApplicationPartManager`.</span><span class="sxs-lookup"><span data-stu-id="bc374-118">You can control which parts (or assemblies) are available to the app by modifying the `ApplicationParts` collection of the `ApplicationPartManager`.</span></span> <span data-ttu-id="bc374-119">A ordem das entradas na coleção `ApplicationParts` não é importante.</span><span class="sxs-lookup"><span data-stu-id="bc374-119">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="bc374-120">É importante configurar totalmente o `ApplicationPartManager` antes de usá-lo para configurar serviços no contêiner.</span><span class="sxs-lookup"><span data-stu-id="bc374-120">It's important to fully configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="bc374-121">Por exemplo, você deve configurar totalmente o `ApplicationPartManager` antes de invocar `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="bc374-121">For example, you should fully configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="bc374-122">Se isso não for feito, os controladores em partes do aplicativo adicionadas depois da chamada de método não serão afetados (não serão registrados como serviços), o que poderá resultar em um comportamento incorreto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bc374-122">Failing to do so, will mean that controllers in application parts added after that method call won't be affected (won't get registered as services) which might result in incorrect behavior of your application.</span></span>

<span data-ttu-id="bc374-123">Se tiver um assembly que contém controladores que você não deseja usar, remova-o do `ApplicationPartManager`:</span><span class="sxs-lookup"><span data-stu-id="bc374-123">If you have an assembly that contains controllers you don't want to be used, remove it from the `ApplicationPartManager`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm =>
    {
        var dependentLibrary = apm.ApplicationParts
            .FirstOrDefault(part => part.Name == "DependentLibrary");

        if (dependentLibrary != null)
        {
           apm.ApplicationParts.Remove(dependentLibrary);
        }
    })
```

<span data-ttu-id="bc374-124">Além do assembly de seu projeto e de seus assemblies dependentes, o `ApplicationPartManager` incluirá partes para `Microsoft.AspNetCore.Mvc.TagHelpers` e `Microsoft.AspNetCore.Mvc.Razor` por padrão.</span><span class="sxs-lookup"><span data-stu-id="bc374-124">In addition to your project's assembly and its dependent assemblies, the `ApplicationPartManager` will include parts for `Microsoft.AspNetCore.Mvc.TagHelpers` and `Microsoft.AspNetCore.Mvc.Razor` by default.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="bc374-125">Provedores de recursos do aplicativo</span><span class="sxs-lookup"><span data-stu-id="bc374-125">Application Feature Providers</span></span>

<span data-ttu-id="bc374-126">Os Provedores de recursos do aplicativo examinam as partes do aplicativo e fornece recursos para essas partes.</span><span class="sxs-lookup"><span data-stu-id="bc374-126">Application Feature Providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="bc374-127">Há provedores de recursos internos para os seguintes recursos de MVC:</span><span class="sxs-lookup"><span data-stu-id="bc374-127">There are built-in feature providers for the following MVC features:</span></span>

* [<span data-ttu-id="bc374-128">Controladores</span><span class="sxs-lookup"><span data-stu-id="bc374-128">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="bc374-129">Referência de metadados</span><span class="sxs-lookup"><span data-stu-id="bc374-129">Metadata Reference</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [<span data-ttu-id="bc374-130">Auxiliares de marcação</span><span class="sxs-lookup"><span data-stu-id="bc374-130">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="bc374-131">Componentes da exibição</span><span class="sxs-lookup"><span data-stu-id="bc374-131">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="bc374-132">Provedores de recursos herdam de `IApplicationFeatureProvider<T>`, em que `T` é o tipo do recurso.</span><span class="sxs-lookup"><span data-stu-id="bc374-132">Feature providers inherit from `IApplicationFeatureProvider<T>`, where `T` is the type of the feature.</span></span> <span data-ttu-id="bc374-133">Você pode implementar seus próprios provedores de recursos para qualquer um dos tipos de recurso do MVC listados acima.</span><span class="sxs-lookup"><span data-stu-id="bc374-133">You can implement your own feature providers for any of MVC's feature types listed above.</span></span> <span data-ttu-id="bc374-134">A ordem dos provedores de recursos na coleção `ApplicationPartManager.FeatureProviders` pode ser importante, pois provedores posteriores podem reagir às ações tomadas por provedores anteriores.</span><span class="sxs-lookup"><span data-stu-id="bc374-134">The order of feature providers in the `ApplicationPartManager.FeatureProviders` collection can be important, since later providers can react to actions taken by previous providers.</span></span>

### <a name="sample-generic-controller-feature"></a><span data-ttu-id="bc374-135">Amostra: Recurso de controlador genérico</span><span class="sxs-lookup"><span data-stu-id="bc374-135">Sample: Generic controller feature</span></span>

<span data-ttu-id="bc374-136">Por padrão, o ASP.NET Core MVC ignora controladores genéricos (por exemplo, `SomeController<T>`).</span><span class="sxs-lookup"><span data-stu-id="bc374-136">By default, ASP.NET Core MVC ignores generic controllers (for example, `SomeController<T>`).</span></span> <span data-ttu-id="bc374-137">Este exemplo usa um provedor de recursos de controlador que é executado depois do provedor padrão e adiciona instâncias de controlador genérico a uma lista de tipos especificados (definidos em `EntityTypes.Types`):</span><span class="sxs-lookup"><span data-stu-id="bc374-137">This sample uses a controller feature provider that runs after the default provider and adds generic controller instances for a specified list of types (defined in `EntityTypes.Types`):</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]

<span data-ttu-id="bc374-138">Os tipos de entidade:</span><span class="sxs-lookup"><span data-stu-id="bc374-138">The entity types:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]

<span data-ttu-id="bc374-139">O provedor de recursos é adicionado em `Startup`:</span><span class="sxs-lookup"><span data-stu-id="bc374-139">The feature provider is added in `Startup`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(apm => 
        apm.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

<span data-ttu-id="bc374-140">Por padrão, os nomes do controlador genérico usados para roteamento seriam no formato *GenericController'1 [Widget]* em vez de *Widget*.</span><span class="sxs-lookup"><span data-stu-id="bc374-140">By default, the generic controller names used for routing would be of the form *GenericController\`1[Widget]* instead of *Widget*.</span></span> <span data-ttu-id="bc374-141">O atributo a seguir é usado para modificar o nome para que ele corresponda ao tipo genérico usado pelo controlador:</span><span class="sxs-lookup"><span data-stu-id="bc374-141">The following attribute is used to modify the name to correspond to the generic type used by the controller:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]

<span data-ttu-id="bc374-142">A classe `GenericController`:</span><span class="sxs-lookup"><span data-stu-id="bc374-142">The `GenericController` class:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]

<span data-ttu-id="bc374-143">O resultado, quando uma rota correspondente é solicitada:</span><span class="sxs-lookup"><span data-stu-id="bc374-143">The result, when a matching route is requested:</span></span>

![O exemplo de saída do aplicativo de exemplo lê, 'Olá de um controlador Sprocket genérico.'](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a><span data-ttu-id="bc374-145">Amostra: Exibir recursos disponíveis</span><span class="sxs-lookup"><span data-stu-id="bc374-145">Sample: Display available features</span></span>

<span data-ttu-id="bc374-146">Você pode iterar nos recursos preenchidos disponíveis para seu aplicativo solicitando um `ApplicationPartManager` por meio da [injeção de dependência](../../fundamentals/dependency-injection.md) e usando-o para preencher as instâncias dos recursos apropriados:</span><span class="sxs-lookup"><span data-stu-id="bc374-146">You can iterate through the populated features available to your app by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md) and using it to populate instances of the appropriate features:</span></span>

[!code-csharp[](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="bc374-147">Saída de exemplo:</span><span class="sxs-lookup"><span data-stu-id="bc374-147">Example output:</span></span>

![Saída de exemplo do aplicativo de exemplo](app-parts/_static/available-features.png)
