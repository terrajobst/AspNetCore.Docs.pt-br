---
title: Convenções de rota e aplicativo das Páginas do Razor no ASP.NET Core
author: guardrex
description: Descubra como as convenções do provedor de modelo de aplicativo e rota ajudam você a controlar o roteamento, a descoberta e o processamento de página.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: a0a1eda69da34d1865fd11ef464c3697bcd01eff
ms.sourcegitcommit: 810d5831169770ee240d03207d6671dabea2486e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72779213"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="04e86-103">Convenções de rota e aplicativo das Páginas do Razor no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="04e86-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

<span data-ttu-id="04e86-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="04e86-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="04e86-105">Saiba como usar a página [convenções do provedor de modelo de rota e aplicativo](xref:mvc/controllers/application-model#conventions) para controlar o roteamento, a descoberta e o processamento de páginas nos aplicativos Páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="04e86-105">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="04e86-106">Quando precisar configurar rotas de página personalizadas para páginas individuais, configure o roteamento para páginas com a [convenção AddPageRoute](#configure-a-page-route) descrita mais adiante neste tópico.</span><span class="sxs-lookup"><span data-stu-id="04e86-106">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="04e86-107">Para especificar uma rota de página, adicionar segmentos de rota ou adicionar parâmetros a uma rota, use a diretiva de `@page` da página.</span><span class="sxs-lookup"><span data-stu-id="04e86-107">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="04e86-108">Para obter mais informações, consulte [rotas personalizadas](xref:razor-pages/index#custom-routes).</span><span class="sxs-lookup"><span data-stu-id="04e86-108">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="04e86-109">Há palavras reservadas que não podem ser usadas como segmentos de rota ou nomes de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="04e86-109">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="04e86-110">Para obter mais informações, consulte [Roteamento: nomes de roteamento reservados](xref:fundamentals/routing#reserved-routing-names).</span><span class="sxs-lookup"><span data-stu-id="04e86-110">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="04e86-111">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="04e86-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="04e86-112">Cenário</span><span class="sxs-lookup"><span data-stu-id="04e86-112">Scenario</span></span> | <span data-ttu-id="04e86-113">A amostra explica...</span><span class="sxs-lookup"><span data-stu-id="04e86-113">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="04e86-114">Convenções de modelo</span><span class="sxs-lookup"><span data-stu-id="04e86-114">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="04e86-115">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="04e86-115">Conventions.Add</span></span><ul><li><span data-ttu-id="04e86-116">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="04e86-116">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="04e86-117">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="04e86-117">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="04e86-118">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="04e86-118">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="04e86-119">Adicione um cabeçalho e um modelo de rota às páginas de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="04e86-119">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="04e86-120">Convenções de ação da rota de página</span><span class="sxs-lookup"><span data-stu-id="04e86-120">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="04e86-121">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="04e86-121">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="04e86-122">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="04e86-122">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="04e86-123">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="04e86-123">AddPageRoute</span></span></li></ul> | <span data-ttu-id="04e86-124">Adicione um modelo de rota às páginas em uma pasta e a uma única página.</span><span class="sxs-lookup"><span data-stu-id="04e86-124">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="04e86-125">Convenções de ação do modelo de página</span><span class="sxs-lookup"><span data-stu-id="04e86-125">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="04e86-126">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="04e86-126">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="04e86-127">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="04e86-127">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="04e86-128">ConfigureFilter (classe de filtro, expressão lambda ou alocador de filtro)</span><span class="sxs-lookup"><span data-stu-id="04e86-128">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="04e86-129">Adicione um cabeçalho às páginas em uma pasta, adicione um cabeçalho a uma única página e configure um [alocador de filtro](xref:mvc/controllers/filters#ifilterfactory) para adicionar um cabeçalho às páginas de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="04e86-129">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="04e86-130">As convenções de Razor Pages são adicionadas e configuradas usando o método de extensão <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> para <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> na coleção de serviços na classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="04e86-130">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="04e86-131">Os exemplos de convenção a seguir estão descritos mais adiante neste tópico:</span><span class="sxs-lookup"><span data-stu-id="04e86-131">The following convention examples are explained later in this topic:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

::: moniker-end

## <a name="route-order"></a><span data-ttu-id="04e86-132">Ordem de rota</span><span class="sxs-lookup"><span data-stu-id="04e86-132">Route order</span></span>

<span data-ttu-id="04e86-133">Rotas especificam um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> para processamento (correspondência de rota).</span><span class="sxs-lookup"><span data-stu-id="04e86-133">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="04e86-134">Pedido</span><span class="sxs-lookup"><span data-stu-id="04e86-134">Order</span></span>            | <span data-ttu-id="04e86-135">Comportamento</span><span class="sxs-lookup"><span data-stu-id="04e86-135">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="04e86-136">-1</span><span class="sxs-lookup"><span data-stu-id="04e86-136">-1</span></span>               | <span data-ttu-id="04e86-137">A rota é processada antes que outras rotas sejam processadas.</span><span class="sxs-lookup"><span data-stu-id="04e86-137">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="04e86-138">0</span><span class="sxs-lookup"><span data-stu-id="04e86-138">0</span></span>                | <span data-ttu-id="04e86-139">A ordem não está especificada (valor padrão).</span><span class="sxs-lookup"><span data-stu-id="04e86-139">Order isn't specified (default value).</span></span> <span data-ttu-id="04e86-140">Não atribuir `Order` (`Order = null`) usa como padrão a rota `Order` para 0 (zero) para processamento.</span><span class="sxs-lookup"><span data-stu-id="04e86-140">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="04e86-141">1, 2, &hellip; n</span><span class="sxs-lookup"><span data-stu-id="04e86-141">1, 2, &hellip; n</span></span> | <span data-ttu-id="04e86-142">Especifica a ordem de processamento da rota.</span><span class="sxs-lookup"><span data-stu-id="04e86-142">Specifies the route processing order.</span></span> |

<span data-ttu-id="04e86-143">O processamento de rota é estabelecido por convenção:</span><span class="sxs-lookup"><span data-stu-id="04e86-143">Route processing is established by convention:</span></span>

* <span data-ttu-id="04e86-144">As rotas são processadas em ordem sequencial (-1, 0, 1, 2, &hellip; n).</span><span class="sxs-lookup"><span data-stu-id="04e86-144">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="04e86-145">Quando as rotas têm o mesmo `Order`, a rota mais específica é correspondida primeiro, seguida por rotas menos específicas.</span><span class="sxs-lookup"><span data-stu-id="04e86-145">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="04e86-146">Quando as rotas com o mesmo `Order` e o mesmo número de parâmetros correspondem a uma URL de solicitação, as rotas são processadas na ordem em que são adicionadas à <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span><span class="sxs-lookup"><span data-stu-id="04e86-146">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="04e86-147">Se possível, evite dependendo de uma ordem de processamento de rota estabelecida.</span><span class="sxs-lookup"><span data-stu-id="04e86-147">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="04e86-148">Em geral, o roteamento seleciona a rota correta com correspondência de URL.</span><span class="sxs-lookup"><span data-stu-id="04e86-148">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="04e86-149">Se você tiver que definir as propriedades de `Order` de rota para rotear solicitações corretamente, o esquema de roteamento do aplicativo provavelmente será confuso para clientes e frágeis para manter.</span><span class="sxs-lookup"><span data-stu-id="04e86-149">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="04e86-150">Busque para simplificar o esquema de roteamento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="04e86-150">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="04e86-151">O aplicativo de exemplo requer uma ordem de processamento de rota explícita para demonstrar vários cenários de roteamento usando um único aplicativo.</span><span class="sxs-lookup"><span data-stu-id="04e86-151">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="04e86-152">No entanto, você deve tentar evitar a prática de definir `Order` de rota em aplicativos de produção.</span><span class="sxs-lookup"><span data-stu-id="04e86-152">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="04e86-153">Roteamento do Razor Pages e do controlador do MVC compartilham uma implementação.</span><span class="sxs-lookup"><span data-stu-id="04e86-153">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="04e86-154">As informações sobre a ordem de rota nos tópicos do MVC estão disponíveis em [Roteamento para ações do controlador: ordenando rotas de atributo](xref:mvc/controllers/routing#ordering-attribute-routes).</span><span class="sxs-lookup"><span data-stu-id="04e86-154">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="04e86-155">Convenções de modelo</span><span class="sxs-lookup"><span data-stu-id="04e86-155">Model conventions</span></span>

<span data-ttu-id="04e86-156">Adicione um delegado para <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> adicionar [convenções de modelo](xref:mvc/controllers/application-model#conventions) que se aplicam a Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="04e86-156">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="04e86-157">Adicionar uma Convenção de modelo de rota a todas as páginas</span><span class="sxs-lookup"><span data-stu-id="04e86-157">Add a route model convention to all pages</span></span>

<span data-ttu-id="04e86-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> à coleção de instâncias de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que são aplicadas durante a construção do modelo de rota de página.</span><span class="sxs-lookup"><span data-stu-id="04e86-158">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="04e86-159">O aplicativo de exemplo adiciona um modelo de rota `{globalTemplate?}` a todas as páginas no aplicativo:</span><span class="sxs-lookup"><span data-stu-id="04e86-159">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="04e86-160">A propriedade <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> é definida como `1`.</span><span class="sxs-lookup"><span data-stu-id="04e86-160">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="04e86-161">Isso garante o seguinte comportamento de correspondência de rota no aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="04e86-161">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="04e86-162">Um modelo de rota para `TheContactPage/{text?}` é adicionado posteriormente no tópico.</span><span class="sxs-lookup"><span data-stu-id="04e86-162">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="04e86-163">A rota da página de contato tem uma ordem padrão de `null` (`Order = 0`), portanto, ela corresponde antes do modelo de rota `{globalTemplate?}`.</span><span class="sxs-lookup"><span data-stu-id="04e86-163">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="04e86-164">Um modelo de rota `{aboutTemplate?}` é adicionado posteriormente no tópico.</span><span class="sxs-lookup"><span data-stu-id="04e86-164">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="04e86-165">O modelo `{aboutTemplate?}` recebe uma `Order` de `2`.</span><span class="sxs-lookup"><span data-stu-id="04e86-165">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="04e86-166">Quando a página About é solicitada no `/About/RouteDataValue`, "RouteDataValue" é carregado no `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["aboutTemplate"]` (`Order = 2`) devido à configuração da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="04e86-166">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="04e86-167">Um modelo de rota `{otherPagesTemplate?}` é adicionado posteriormente no tópico.</span><span class="sxs-lookup"><span data-stu-id="04e86-167">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="04e86-168">O modelo `{otherPagesTemplate?}` recebe uma `Order` de `2`.</span><span class="sxs-lookup"><span data-stu-id="04e86-168">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="04e86-169">Quando qualquer página na pasta *pages/OtherPages* é solicitada com um parâmetro de rota (por exemplo, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" é carregado em `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) devido à definição da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="04e86-169">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="04e86-170">Sempre que possível, não defina o `Order`, o que resulta em `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="04e86-170">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="04e86-171">Confie no roteamento para selecionar a rota correta.</span><span class="sxs-lookup"><span data-stu-id="04e86-171">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="04e86-172">Razor Pages opções, como adicionar <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, são adicionadas quando o MVC é adicionado à coleção de serviços no `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="04e86-172">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="04e86-173">Para obter um exemplo, confira o [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span><span class="sxs-lookup"><span data-stu-id="04e86-173">For an example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="04e86-174">Solicite a página About da amostra em `localhost:5000/About/GlobalRouteValue` e inspecione o resultado:</span><span class="sxs-lookup"><span data-stu-id="04e86-174">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![A página About é solicitada com um segmento de rota GlobalRouteValue.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="04e86-177">Adicionar uma Convenção de modelo de aplicativo a todas as páginas</span><span class="sxs-lookup"><span data-stu-id="04e86-177">Add an app model convention to all pages</span></span>

<span data-ttu-id="04e86-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> à coleção de instâncias de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que são aplicadas durante a construção de modelo de aplicativo de página.</span><span class="sxs-lookup"><span data-stu-id="04e86-178">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="04e86-179">Para demonstrar isso e outras convenções mais adiante no tópico, o aplicativo de exemplo inclui uma classe `AddHeaderAttribute`.</span><span class="sxs-lookup"><span data-stu-id="04e86-179">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="04e86-180">O construtor de classe aceita uma cadeia de caracteres `name` e uma matriz de cadeia de caracteres `values`.</span><span class="sxs-lookup"><span data-stu-id="04e86-180">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="04e86-181">Esses valores são usados em seu método `OnResultExecuting` para definir um cabeçalho de resposta.</span><span class="sxs-lookup"><span data-stu-id="04e86-181">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="04e86-182">A classe completa é mostrada na seção [Convenções de ação do modelo de página](#page-model-action-conventions) mais adiante no tópico.</span><span class="sxs-lookup"><span data-stu-id="04e86-182">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="04e86-183">O aplicativo de exemplo usa a classe `AddHeaderAttribute` para adicionar um cabeçalho, `GlobalHeader`, a todas as páginas no aplicativo:</span><span class="sxs-lookup"><span data-stu-id="04e86-183">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="04e86-184">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="04e86-184">*Startup.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

<span data-ttu-id="04e86-185">Solicite a página About da amostra em `localhost:5000/About` e inspecione os cabeçalhos para exibir o resultado:</span><span class="sxs-lookup"><span data-stu-id="04e86-185">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Os cabeçalhos de resposta da página About mostram que o GlobalHeader foi adicionado.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="04e86-187">Adicionar uma Convenção de modelo de manipulador a todas as páginas</span><span class="sxs-lookup"><span data-stu-id="04e86-187">Add a handler model convention to all pages</span></span>

<span data-ttu-id="04e86-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> à coleção de instâncias de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> que são aplicadas durante a construção do modelo de manipulador de página.</span><span class="sxs-lookup"><span data-stu-id="04e86-188">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="04e86-189">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="04e86-189">*Startup.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

## <a name="page-route-action-conventions"></a><span data-ttu-id="04e86-190">Convenções de ação da rota de página</span><span class="sxs-lookup"><span data-stu-id="04e86-190">Page route action conventions</span></span>

<span data-ttu-id="04e86-191">O provedor de modelo de rota padrão que deriva de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invoca convenções que são projetadas para fornecer pontos de extensibilidade para configurar rotas de página.</span><span class="sxs-lookup"><span data-stu-id="04e86-191">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="04e86-192">Convenção de modelo de rota de pasta</span><span class="sxs-lookup"><span data-stu-id="04e86-192">Folder route model convention</span></span>

<span data-ttu-id="04e86-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> que invoca uma ação no <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> para todas as páginas na pasta especificada.</span><span class="sxs-lookup"><span data-stu-id="04e86-193">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="04e86-194">O aplicativo de exemplo usa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> para adicionar um modelo de rota `{otherPagesTemplate?}` às páginas da pasta *OtherPages*:</span><span class="sxs-lookup"><span data-stu-id="04e86-194">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

<span data-ttu-id="04e86-195">A propriedade <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> é definida como `2`.</span><span class="sxs-lookup"><span data-stu-id="04e86-195">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="04e86-196">Isso garante que o modelo para `{globalTemplate?}` (definido anteriormente no tópico como `1`) receba prioridade para a primeira posição de valor de dados de rota quando um único valor de rota for fornecido.</span><span class="sxs-lookup"><span data-stu-id="04e86-196">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="04e86-197">Se uma página na pasta *pages/OtherPages* for solicitada com um valor de parâmetro de rota (por exemplo, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" será carregado em `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) devido à definição da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="04e86-197">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="04e86-198">Sempre que possível, não defina o `Order`, o que resulta em `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="04e86-198">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="04e86-199">Confie no roteamento para selecionar a rota correta.</span><span class="sxs-lookup"><span data-stu-id="04e86-199">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="04e86-200">Solicite a página Page1 da amostra em `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` e inspecione o resultado:</span><span class="sxs-lookup"><span data-stu-id="04e86-200">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![A Page1 da pasta OtherPages é solicitada com um segmento de rota GlobalRouteValue e OtherPagesRouteValue.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="04e86-203">Convenção de modelo de rota de página</span><span class="sxs-lookup"><span data-stu-id="04e86-203">Page route model convention</span></span>

<span data-ttu-id="04e86-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> que invoca uma ação no <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> para a página com o nome especificado.</span><span class="sxs-lookup"><span data-stu-id="04e86-204">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="04e86-205">O aplicativo de exemplo usa `AddPageRouteModelConvention` para adicionar um modelo de rota `{aboutTemplate?}` à página About:</span><span class="sxs-lookup"><span data-stu-id="04e86-205">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

<span data-ttu-id="04e86-206">A propriedade <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> é definida como `2`.</span><span class="sxs-lookup"><span data-stu-id="04e86-206">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="04e86-207">Isso garante que o modelo para `{globalTemplate?}` (definido anteriormente no tópico como `1`) receba prioridade para a primeira posição de valor de dados de rota quando um único valor de rota for fornecido.</span><span class="sxs-lookup"><span data-stu-id="04e86-207">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="04e86-208">Se a página About for solicitada com um valor de parâmetro de rota em `/About/RouteDataValue`, "RouteDataValue" será carregado em `RouteData.Values["globalTemplate"]` (`Order = 1`) e não `RouteData.Values["aboutTemplate"]` (`Order = 2`) devido à definição da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="04e86-208">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="04e86-209">Sempre que possível, não defina o `Order`, o que resulta em `Order = 0`.</span><span class="sxs-lookup"><span data-stu-id="04e86-209">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="04e86-210">Confie no roteamento para selecionar a rota correta.</span><span class="sxs-lookup"><span data-stu-id="04e86-210">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="04e86-211">Solicite a página About da amostra em `localhost:5000/About/GlobalRouteValue/AboutRouteValue` e inspecione o resultado:</span><span class="sxs-lookup"><span data-stu-id="04e86-211">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![A página About é solicitada com segmentos de rota para GlobalRouteValue e AboutRouteValue.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="04e86-214">Usar um transformador de parâmetro para personalizar rotas de página</span><span class="sxs-lookup"><span data-stu-id="04e86-214">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="04e86-215">As rotas de página geradas por ASP.NET Core podem ser personalizadas usando um transformador de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="04e86-215">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="04e86-216">Um transformador de parâmetro implementa `IOutboundParameterTransformer` e transforma o valor dos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="04e86-216">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="04e86-217">Por exemplo, um transformador de parâmetro `SlugifyParameterTransformer` personalizado muda o valor de rota `SubscriptionManagement` para `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="04e86-217">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="04e86-218">A Convenção de modelo de rota de página `PageRouteTransformerConvention` aplica um transformador de parâmetro aos segmentos de nome de arquivo e pasta de rotas de página geradas automaticamente em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="04e86-218">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="04e86-219">Por exemplo, o arquivo de Razor Pages em */pages/SubscriptionManagement/ViewAll.cshtml* teria sua rota reescrita de `/SubscriptionManagement/ViewAll` para `/subscription-management/view-all`.</span><span class="sxs-lookup"><span data-stu-id="04e86-219">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="04e86-220">`PageRouteTransformerConvention` transforma apenas os segmentos gerados automaticamente de uma rota de página que vem da pasta Razor Pages e o nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="04e86-220">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="04e86-221">Ele não transforma segmentos de rota adicionados com a diretiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="04e86-221">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="04e86-222">A Convenção também não transforma as rotas adicionadas por <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span><span class="sxs-lookup"><span data-stu-id="04e86-222">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="04e86-223">O `PageRouteTransformerConvention` é registrado como uma opção no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="04e86-223">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end

## <a name="configure-a-page-route"></a><span data-ttu-id="04e86-224">Configurar uma rota de página</span><span class="sxs-lookup"><span data-stu-id="04e86-224">Configure a page route</span></span>

<span data-ttu-id="04e86-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> para configurar uma rota para uma página no caminho de página especificado.</span><span class="sxs-lookup"><span data-stu-id="04e86-225">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="04e86-226">Os links gerados para a página usam a rota especificada.</span><span class="sxs-lookup"><span data-stu-id="04e86-226">Generated links to the page use your specified route.</span></span> <span data-ttu-id="04e86-227">`AddPageRoute` usa `AddPageRouteModelConvention` para estabelecer a rota.</span><span class="sxs-lookup"><span data-stu-id="04e86-227">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="04e86-228">O aplicativo de exemplo cria uma rota para `/TheContactPage` para *Contact.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="04e86-228">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

<span data-ttu-id="04e86-229">A página Contact também pode ser acessada em `/Contact` por meio de sua rota padrão.</span><span class="sxs-lookup"><span data-stu-id="04e86-229">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="04e86-230">A rota personalizada do aplicativo de exemplo para a página Contact permite um segmento de rota `text` opcional (`{text?}`).</span><span class="sxs-lookup"><span data-stu-id="04e86-230">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="04e86-231">A página também inclui esse segmento opcional em sua diretiva `@page`, caso o visitante acesse a página em sua rota `/Contact`:</span><span class="sxs-lookup"><span data-stu-id="04e86-231">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

<span data-ttu-id="04e86-232">Observe que a URL gerada para o link **Contato** na página renderizada reflete a rota atualizada:</span><span class="sxs-lookup"><span data-stu-id="04e86-232">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![Link Contato do aplicativo de exemplo na barra de navegação](razor-pages-conventions/_static/contact-link.png)

![A inspeção do link Contato no HTML renderizado indica que o href é definido como '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="04e86-235">Visite a página Contact em sua rota comum, `/Contact`, ou na rota personalizada, `/TheContactPage`.</span><span class="sxs-lookup"><span data-stu-id="04e86-235">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="04e86-236">Se você fornecer um segmento de rota `text` adicional, a página mostrará o segmento codificado em HTML fornecido:</span><span class="sxs-lookup"><span data-stu-id="04e86-236">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![Exemplo do navegador Microsoft Edge de fornecimento de um segmento de rota 'text' opcional de 'TextValue' na URL.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="04e86-239">Convenções de ação do modelo de página</span><span class="sxs-lookup"><span data-stu-id="04e86-239">Page model action conventions</span></span>

<span data-ttu-id="04e86-240">O provedor de modelo de página padrão que implementa <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invoca convenções que são projetadas para fornecer pontos de extensibilidade para configurar modelos de página.</span><span class="sxs-lookup"><span data-stu-id="04e86-240">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="04e86-241">Essas convenções são úteis ao criar e modificar cenários de descoberta e processamento de página.</span><span class="sxs-lookup"><span data-stu-id="04e86-241">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="04e86-242">Para os exemplos nesta seção, o aplicativo de exemplo usa uma classe `AddHeaderAttribute`, que é uma <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, que aplica um cabeçalho de resposta:</span><span class="sxs-lookup"><span data-stu-id="04e86-242">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="04e86-243">Usando convenções, a amostra explica como aplicar o atributo a todas as páginas de uma pasta e a uma única página.</span><span class="sxs-lookup"><span data-stu-id="04e86-243">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="04e86-244">**Convenção de modelo de aplicativo de pasta**</span><span class="sxs-lookup"><span data-stu-id="04e86-244">**Folder app model convention**</span></span>

<span data-ttu-id="04e86-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> que invoca uma ação em <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instâncias para todas as páginas na pasta especificada.</span><span class="sxs-lookup"><span data-stu-id="04e86-245">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="04e86-246">A amostra explica o uso de `AddFolderApplicationModelConvention` adicionando um cabeçalho, `OtherPagesHeader`, às páginas dentro da pasta *OtherPages* do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="04e86-246">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

<span data-ttu-id="04e86-247">Solicite a página Page1 da amostra em `localhost:5000/OtherPages/Page1` e inspecione os cabeçalhos para exibir o resultado:</span><span class="sxs-lookup"><span data-stu-id="04e86-247">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![Os cabeçalhos de resposta da página OtherPages/Page1 mostram que o OtherPagesHeader foi adicionado.](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="04e86-249">**Convenção de modelo de aplicativo de página**</span><span class="sxs-lookup"><span data-stu-id="04e86-249">**Page app model convention**</span></span>

<span data-ttu-id="04e86-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> para criar e adicionar um <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> que invoca uma ação no <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> para a página com o nome especificado.</span><span class="sxs-lookup"><span data-stu-id="04e86-250">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="04e86-251">A amostra explica o uso de `AddPageApplicationModelConvention` adicionando um cabeçalho, `AboutHeader`, à página About:</span><span class="sxs-lookup"><span data-stu-id="04e86-251">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

<span data-ttu-id="04e86-252">Solicite a página About da amostra em `localhost:5000/About` e inspecione os cabeçalhos para exibir o resultado:</span><span class="sxs-lookup"><span data-stu-id="04e86-252">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Os cabeçalhos de resposta da página About mostram que o AboutHeader foi adicionado.](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="04e86-254">**Configurar um filtro**</span><span class="sxs-lookup"><span data-stu-id="04e86-254">**Configure a filter**</span></span>

<span data-ttu-id="04e86-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configura o filtro especificado a ser aplicado.</span><span class="sxs-lookup"><span data-stu-id="04e86-255"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="04e86-256">É possível implementar uma classe de filtro, mas o aplicativo de exemplo mostra como implementar um filtro em uma expressão lambda, que é implementado em segundo plano como um alocador que retorna um filtro:</span><span class="sxs-lookup"><span data-stu-id="04e86-256">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

<span data-ttu-id="04e86-257">O modelo de aplicativo de página é usado para verificar o caminho relativo para segmentos que levam à página Page2 na pasta *OtherPages*.</span><span class="sxs-lookup"><span data-stu-id="04e86-257">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="04e86-258">Se a condição é aprovada, um cabeçalho é adicionado.</span><span class="sxs-lookup"><span data-stu-id="04e86-258">If the condition passes, a header is added.</span></span> <span data-ttu-id="04e86-259">Caso contrário, o `EmptyFilter` é aplicado.</span><span class="sxs-lookup"><span data-stu-id="04e86-259">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="04e86-260">`EmptyFilter` é um [filtro de Ação](xref:mvc/controllers/filters#action-filters).</span><span class="sxs-lookup"><span data-stu-id="04e86-260">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="04e86-261">Como os filtros de ação são ignorados por Razor Pages, o `EmptyFilter` não tem nenhum efeito como se o caminho não contiver `OtherPages/Page2`.</span><span class="sxs-lookup"><span data-stu-id="04e86-261">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="04e86-262">Solicite a página Page2 da amostra em `localhost:5000/OtherPages/Page2` e inspecione os cabeçalhos para exibir o resultado:</span><span class="sxs-lookup"><span data-stu-id="04e86-262">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![O OtherPagesPage2Header é adicionado à resposta para Page2.](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="04e86-264">**Configurar um alocador de filtro**</span><span class="sxs-lookup"><span data-stu-id="04e86-264">**Configure a filter factory**</span></span>

<span data-ttu-id="04e86-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configura a fábrica especificada para aplicar [filtros](xref:mvc/controllers/filters) a todas as Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="04e86-265"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="04e86-266">O aplicativo de exemplo fornece um exemplo de como usar um [alocador de filtro](xref:mvc/controllers/filters#ifilterfactory) adicionando um cabeçalho, `FilterFactoryHeader`, com dois valores para as páginas do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="04e86-266">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

<span data-ttu-id="04e86-267">*AddHeaderWithFactory.cs*:</span><span class="sxs-lookup"><span data-stu-id="04e86-267">*AddHeaderWithFactory.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="04e86-268">Solicite a página About da amostra em `localhost:5000/About` e inspecione os cabeçalhos para exibir o resultado:</span><span class="sxs-lookup"><span data-stu-id="04e86-268">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![Os cabeçalhos de resposta da página About mostram que dois cabeçalhos FilterFactoryHeader foram adicionados.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="04e86-270">Filtros MVC e o filtro de Página (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="04e86-270">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="04e86-271">Os [filtros de Ação](xref:mvc/controllers/filters#action-filters) MVC são ignorados pelas Páginas do Razor, pois elas usam métodos de manipulador.</span><span class="sxs-lookup"><span data-stu-id="04e86-271">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="04e86-272">Outros tipos de filtros MVC estão disponíveis para uso: [Autorização](xref:mvc/controllers/filters#authorization-filters), [Exceção](xref:mvc/controllers/filters#exception-filters), [Recurso](xref:mvc/controllers/filters#resource-filters) e [Resultado](xref:mvc/controllers/filters#result-filters).</span><span class="sxs-lookup"><span data-stu-id="04e86-272">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="04e86-273">Para obter mais informações, consulte o tópico [Filtros](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="04e86-273">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="04e86-274">O filtro de página (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) é um filtro que se aplica a Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="04e86-274">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="04e86-275">Para obter mais informações, confira [Métodos de filtro para Páginas Razor](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="04e86-275">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="04e86-276">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="04e86-276">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
