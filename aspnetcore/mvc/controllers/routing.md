---
title: Roteamento para ações do controlador no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core MVC usa o middleware de roteamento para corresponder a URLs das solicitações de entrada e mapeá-las para ações.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: c1c0d978714718af1de0f627e50a54f66ed391ed
ms.sourcegitcommit: 4b166b49ec557a03f99f872dd069ca5e56faa524
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80362660"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="9ec63-103">Roteamento para ações do controlador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ec63-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="9ec63-104">Por [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5)e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9ec63-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9ec63-105">Os controladores de ASP.NET Core usam o [middleware](xref:fundamentals/middleware/index) de roteamento para corresponder as URLs de solicitações de entrada e mapeá-las para [ações](#action).</span><span class="sxs-lookup"><span data-stu-id="9ec63-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="9ec63-106">Modelos de rotas:</span><span class="sxs-lookup"><span data-stu-id="9ec63-106">Routes templates:</span></span>

* <span data-ttu-id="9ec63-107">São definidos em código ou atributos de inicialização.</span><span class="sxs-lookup"><span data-stu-id="9ec63-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="9ec63-108">Descreva como os caminhos de URL são correspondidos às [ações](#action).</span><span class="sxs-lookup"><span data-stu-id="9ec63-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="9ec63-109">São usados para gerar URLs para links.</span><span class="sxs-lookup"><span data-stu-id="9ec63-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="9ec63-110">Os links gerados são normalmente retornados em respostas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="9ec63-111">As ações são [roteadas por convenção](#cr) ou [roteadas por atributo](#ar).</span><span class="sxs-lookup"><span data-stu-id="9ec63-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="9ec63-112">Colocar uma rota no controlador ou na [ação](#action) a torna roteada por atributo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="9ec63-113">Para obter mais informações, consulte [Roteamento misto](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="9ec63-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="9ec63-114">Este documento:</span><span class="sxs-lookup"><span data-stu-id="9ec63-114">This document:</span></span>

* <span data-ttu-id="9ec63-115">Explica as interações entre o MVC e o roteamento:</span><span class="sxs-lookup"><span data-stu-id="9ec63-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="9ec63-116">Como os aplicativos MVC típicos fazem uso dos recursos de roteamento.</span><span class="sxs-lookup"><span data-stu-id="9ec63-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="9ec63-117">Aborda ambos:</span><span class="sxs-lookup"><span data-stu-id="9ec63-117">Covers both:</span></span>
    * <span data-ttu-id="9ec63-118">[Roteamento convencional](#cr) normalmente usado com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="9ec63-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="9ec63-119">*Roteamento de atributos* usado com APIs REST.</span><span class="sxs-lookup"><span data-stu-id="9ec63-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="9ec63-120">Se você estiver interessado principalmente no roteamento de APIs REST, vá para a seção [Roteamento de atributo para APIs REST](#ar) .</span><span class="sxs-lookup"><span data-stu-id="9ec63-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="9ec63-121">Consulte [Roteamento](xref:fundamentals/routing) para obter detalhes avançados de roteamento.</span><span class="sxs-lookup"><span data-stu-id="9ec63-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="9ec63-122">Refere-se ao sistema de roteamento padrão adicionado no ASP.NET Core 3,0, chamado roteamento de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="9ec63-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="9ec63-123">É possível usar controladores com a versão anterior do roteamento para fins de compatibilidade.</span><span class="sxs-lookup"><span data-stu-id="9ec63-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="9ec63-124">Consulte o [Guia de migração 2.2-3.0](xref:migration/22-to-30) para obter instruções.</span><span class="sxs-lookup"><span data-stu-id="9ec63-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="9ec63-125">Consulte a [versão 2,2 deste documento](xref:mvc/controllers/routing?view=aspnetcore-2.2) para obter material de referência sobre o sistema de roteamento herdado.</span><span class="sxs-lookup"><span data-stu-id="9ec63-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="9ec63-126">Configurar a rota convencional</span><span class="sxs-lookup"><span data-stu-id="9ec63-126">Set up conventional route</span></span>

<span data-ttu-id="9ec63-127">o `Startup.Configure` normalmente tem um código semelhante ao seguinte ao usar o [Roteamento convencional](#crd):</span><span class="sxs-lookup"><span data-stu-id="9ec63-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="9ec63-128">Dentro da chamada para <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> é usado para criar uma única rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="9ec63-129">A rota única é denominada `default` rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-129">The single route is named `default` route.</span></span> <span data-ttu-id="9ec63-130">A maioria dos aplicativos com controladores e exibições usa um modelo de rota semelhante ao `default` rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="9ec63-131">As APIs REST devem usar o [Roteamento de atributos](#ar).</span><span class="sxs-lookup"><span data-stu-id="9ec63-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="9ec63-132">O modelo de rota `"{controller=Home}/{action=Index}/{id?}"`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="9ec63-133">Corresponde a um caminho de URL como `/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="9ec63-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="9ec63-134">Extrai os valores de rota `{ controller = Products, action = Details, id = 5 }` pela geração de tokens do caminho.</span><span class="sxs-lookup"><span data-stu-id="9ec63-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="9ec63-135">A extração dos valores de rota resultará em uma correspondência se o aplicativo tiver um controlador chamado `ProductsController` e uma ação `Details`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

 <span data-ttu-id="9ec63-136">O método [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) está incluído no [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) e é usado para exibir informações de roteamento.</span><span class="sxs-lookup"><span data-stu-id="9ec63-136">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>

  * <span data-ttu-id="9ec63-137">`/Products/Details/5` modelo associa o valor de `id = 5` para definir o parâmetro `id` como `5`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-137">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="9ec63-138">Consulte [Associação de modelo](xref:mvc/models/model-binding) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="9ec63-138">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="9ec63-139">`{controller=Home}` define `Home` como o `controller`padrão.</span><span class="sxs-lookup"><span data-stu-id="9ec63-139">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="9ec63-140">`{action=Index}` define `Index` como o `action`padrão.</span><span class="sxs-lookup"><span data-stu-id="9ec63-140">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="9ec63-141">O caractere `?` no `{id?}` define `id` como opcional.</span><span class="sxs-lookup"><span data-stu-id="9ec63-141">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="9ec63-142">Parâmetros de rota opcionais e padrão não precisam estar presentes no caminho da URL para que haja uma correspondência.</span><span class="sxs-lookup"><span data-stu-id="9ec63-142">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="9ec63-143">Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-143">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="9ec63-144">Corresponde ao caminho da URL `/`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-144">Matches the URL path `/`.</span></span>
* <span data-ttu-id="9ec63-145">Produz os valores de rota `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-145">Produces the route values `{ controller = Home, action = Index }`.</span></span>
* <span data-ttu-id="9ec63-146">O método [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) está incluído no [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) e é usado para exibir informações de roteamento.</span><span class="sxs-lookup"><span data-stu-id="9ec63-146">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>

<span data-ttu-id="9ec63-147">Os valores para `controller` e `action` fazem uso dos valores padrão.</span><span class="sxs-lookup"><span data-stu-id="9ec63-147">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="9ec63-148">`id` não produz um valor, pois não há nenhum segmento correspondente no caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-148">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="9ec63-149">`/` só corresponde se existir uma `HomeController` e `Index` ação:</span><span class="sxs-lookup"><span data-stu-id="9ec63-149">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="9ec63-150">Usando a definição de controlador e o modelo de rota anteriores, a ação de `HomeController.Index` é executada para os seguintes caminhos de URL:</span><span class="sxs-lookup"><span data-stu-id="9ec63-150">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="9ec63-151">O caminho da URL `/` usa os controladores de `Home` padrão do modelo de rota e a ação de `Index`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-151">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="9ec63-152">O caminho da URL `/Home` usa a ação de `Index` padrão do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-152">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="9ec63-153">O método de conveniência <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span><span class="sxs-lookup"><span data-stu-id="9ec63-153">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="9ec63-154">Substituto</span><span class="sxs-lookup"><span data-stu-id="9ec63-154">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="9ec63-155">O roteamento é configurado usando o <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> e o middleware de <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span><span class="sxs-lookup"><span data-stu-id="9ec63-155">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="9ec63-156">Para usar controladores:</span><span class="sxs-lookup"><span data-stu-id="9ec63-156">To use controllers:</span></span>

* <span data-ttu-id="9ec63-157">Chame <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> dentro de `UseEndpoints` para mapear controladores [roteados de atributo](#ar) .</span><span class="sxs-lookup"><span data-stu-id="9ec63-157">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
* <span data-ttu-id="9ec63-158">Chame <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> ou <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>para mapear controladores [roteados de Convenção](#cr) .</span><span class="sxs-lookup"><span data-stu-id="9ec63-158">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="9ec63-159">Roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="9ec63-159">Conventional routing</span></span>

<span data-ttu-id="9ec63-160">O roteamento convencional é usado com controladores e exibições.</span><span class="sxs-lookup"><span data-stu-id="9ec63-160">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="9ec63-161">A rota `default`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-161">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="9ec63-162">é um exemplo de *roteamento convencional*.</span><span class="sxs-lookup"><span data-stu-id="9ec63-162">is an example of a *conventional routing*.</span></span> <span data-ttu-id="9ec63-163">Ele é chamado de *Roteamento convencional* porque estabelece uma *Convenção* para caminhos de URL:</span><span class="sxs-lookup"><span data-stu-id="9ec63-163">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="9ec63-164">O primeiro segmento de caminho, `{controller=Home}`, é mapeado para o nome do controlador.</span><span class="sxs-lookup"><span data-stu-id="9ec63-164">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="9ec63-165">O segundo segmento, `{action=Index}`, é mapeado para o nome da [ação](#action) .</span><span class="sxs-lookup"><span data-stu-id="9ec63-165">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="9ec63-166">O terceiro segmento, `{id?}` é usado para um `id`opcional.</span><span class="sxs-lookup"><span data-stu-id="9ec63-166">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="9ec63-167">O `?` no `{id?}` o torna opcional.</span><span class="sxs-lookup"><span data-stu-id="9ec63-167">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="9ec63-168">`id` é usado para mapear para uma entidade de modelo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-168">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="9ec63-169">Usando essa `default` rota, o caminho da URL:</span><span class="sxs-lookup"><span data-stu-id="9ec63-169">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="9ec63-170">`/Products/List` mapeia para a ação de `ProductsController.List`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-170">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="9ec63-171">`/Blog/Article/17` mapeia para `BlogController.Article` e, normalmente, o modelo associa o parâmetro `id` a 17.</span><span class="sxs-lookup"><span data-stu-id="9ec63-171">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="9ec63-172">Este mapeamento:</span><span class="sxs-lookup"><span data-stu-id="9ec63-172">This mapping:</span></span>

* <span data-ttu-id="9ec63-173">É baseado **somente**nos nomes de controlador e de [ação](#action) .</span><span class="sxs-lookup"><span data-stu-id="9ec63-173">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="9ec63-174">Não é baseado em namespaces, locais de arquivos de origem ou parâmetros de método.</span><span class="sxs-lookup"><span data-stu-id="9ec63-174">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="9ec63-175">O uso do roteamento convencional com a rota padrão permite a criação do aplicativo sem a necessidade de surgir um novo padrão de URL para cada ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-175">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="9ec63-176">Para um aplicativo com ações de estilo [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) , tendo consistência para as URLs entre controladores:</span><span class="sxs-lookup"><span data-stu-id="9ec63-176">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="9ec63-177">Ajuda a simplificar o código.</span><span class="sxs-lookup"><span data-stu-id="9ec63-177">Helps simplify the code.</span></span>
* <span data-ttu-id="9ec63-178">Torna a interface do usuário mais previsível.</span><span class="sxs-lookup"><span data-stu-id="9ec63-178">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="9ec63-179">O `id` no código anterior é definido como opcional pelo modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-179">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="9ec63-180">As ações podem ser executadas sem a ID opcional fornecida como parte da URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-180">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="9ec63-181">Em geral, quando`id` é omitido da URL:</span><span class="sxs-lookup"><span data-stu-id="9ec63-181">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="9ec63-182">`id` é definido como `0` por associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-182">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="9ec63-183">Nenhuma entidade encontrada no `id == 0`correspondente ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="9ec63-183">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="9ec63-184">O [Roteamento de atributos](#ar) fornece controle refinado para tornar a ID necessária para algumas ações e não para outras.</span><span class="sxs-lookup"><span data-stu-id="9ec63-184">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="9ec63-185">Por convenção, a documentação inclui parâmetros opcionais como `id` quando é provável que eles apareçam no uso correto.</span><span class="sxs-lookup"><span data-stu-id="9ec63-185">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="9ec63-186">A maioria dos aplicativos deve escolher um esquema de roteamento básico e descritivo para que as URLs sejam legíveis e significativas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-186">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="9ec63-187">A rota convencional padrão `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-187">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="9ec63-188">Dá suporte a um esquema de roteamento básico e descritivo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-188">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="9ec63-189">É um ponto de partida útil para aplicativos baseados em interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="9ec63-189">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="9ec63-190">É o único modelo de rota necessário para muitos aplicativos da interface do usuário da Web.</span><span class="sxs-lookup"><span data-stu-id="9ec63-190">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="9ec63-191">Para aplicativos de interface do usuário da Web maiores, outra rota usando [áreas](#areas) , se for sempre tudo o que for necessário.</span><span class="sxs-lookup"><span data-stu-id="9ec63-191">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="9ec63-192"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> e <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>:</span><span class="sxs-lookup"><span data-stu-id="9ec63-192"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="9ec63-193">Atribua automaticamente um valor de **pedido** aos seus pontos de extremidade com base na ordem em que são invocados.</span><span class="sxs-lookup"><span data-stu-id="9ec63-193">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="9ec63-194">Roteamento de ponto de extremidade no ASP.NET Core 3,0 e posterior:</span><span class="sxs-lookup"><span data-stu-id="9ec63-194">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="9ec63-195">Não tem um conceito de rotas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-195">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="9ec63-196">Não fornece garantias de ordenação para a execução de extensibilidade, todos os pontos de extremidade são processados de uma só vez.</span><span class="sxs-lookup"><span data-stu-id="9ec63-196">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="9ec63-197">Habilite o [Log](xref:fundamentals/logging/index) para ver como as implementações de roteamento internas, como <xref:Microsoft.AspNetCore.Routing.Route>, correspondem às solicitações.</span><span class="sxs-lookup"><span data-stu-id="9ec63-197">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="9ec63-198">O [Roteamento de atributos](#ar) é explicado mais adiante neste documento.</span><span class="sxs-lookup"><span data-stu-id="9ec63-198">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="9ec63-199">Várias rotas convencionais</span><span class="sxs-lookup"><span data-stu-id="9ec63-199">Multiple conventional routes</span></span>

<span data-ttu-id="9ec63-200">Várias [rotas convencionais](#cr) podem ser adicionadas dentro de `UseEndpoints` adicionando mais chamadas para <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> e <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span><span class="sxs-lookup"><span data-stu-id="9ec63-200">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="9ec63-201">Isso permite a definição de várias convenções ou a adição de rotas convencionais que são dedicadas a uma [ação](#action)específica, como:</span><span class="sxs-lookup"><span data-stu-id="9ec63-201">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="9ec63-202">A rota de `blog` no código anterior é uma **rota convencional dedicada**.</span><span class="sxs-lookup"><span data-stu-id="9ec63-202">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="9ec63-203">Ele é chamado de rota convencional dedicada porque:</span><span class="sxs-lookup"><span data-stu-id="9ec63-203">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="9ec63-204">Ele usa o [Roteamento convencional](#cr).</span><span class="sxs-lookup"><span data-stu-id="9ec63-204">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="9ec63-205">Ele é dedicado a uma [ação](#action)específica.</span><span class="sxs-lookup"><span data-stu-id="9ec63-205">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="9ec63-206">Como `controller` e `action` não aparecem no modelo de rota `"blog/{*article}"` como parâmetros:</span><span class="sxs-lookup"><span data-stu-id="9ec63-206">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="9ec63-207">Eles só podem ter os valores padrão `{ controller = "Blog", action = "Article" }`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-207">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="9ec63-208">Essa rota sempre é mapeada para a ação `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-208">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="9ec63-209">`/Blog`, `/Blog/Article`e `/Blog/{any-string}` são os únicos caminhos de URL que correspondem à rota do blog.</span><span class="sxs-lookup"><span data-stu-id="9ec63-209">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="9ec63-210">O exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="9ec63-210">The preceding example:</span></span>

* <span data-ttu-id="9ec63-211">`blog` rota tem uma prioridade mais alta para correspondências do que a rota de `default`, pois ela é adicionada primeiro.</span><span class="sxs-lookup"><span data-stu-id="9ec63-211">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="9ec63-212">É um exemplo de roteamento de estilo de separador, onde é comum ter um [nome de artigo](https://developer.mozilla.org/docs/Glossary/Slug) como parte da URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-212">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="9ec63-213">No ASP.NET Core 3,0 e posterior, o roteamento não:</span><span class="sxs-lookup"><span data-stu-id="9ec63-213">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="9ec63-214">Defina um conceito chamado *rota*.</span><span class="sxs-lookup"><span data-stu-id="9ec63-214">Define a concept called a *route*.</span></span> <span data-ttu-id="9ec63-215">`UseRouting` adiciona a correspondência de rota ao pipeline de middleware.</span><span class="sxs-lookup"><span data-stu-id="9ec63-215">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="9ec63-216">O middleware de `UseRouting` examina o conjunto de pontos de extremidade definidos no aplicativo e seleciona a melhor correspondência de ponto final com base na solicitação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-216">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="9ec63-217">Forneça garantias sobre a ordem de execução de extensibilidade, como <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> ou <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span><span class="sxs-lookup"><span data-stu-id="9ec63-217">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="9ec63-218">Consulte [Roteamento](xref:fundamentals/routing) para obter material de referência sobre roteamento.</span><span class="sxs-lookup"><span data-stu-id="9ec63-218">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="9ec63-219">Ordem de roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="9ec63-219">Conventional routing order</span></span>

<span data-ttu-id="9ec63-220">O roteamento convencional só corresponde a uma combinação de ação e controlador que são definidos pelo aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-220">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="9ec63-221">Isso se destina a simplificar os casos em que as rotas convencionais se sobrepõem.</span><span class="sxs-lookup"><span data-stu-id="9ec63-221">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="9ec63-222">Adicionar rotas usando <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>e <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> atribuir automaticamente um valor de pedido a seus pontos de extremidade com base na ordem em que são invocados.</span><span class="sxs-lookup"><span data-stu-id="9ec63-222">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="9ec63-223">As correspondências de uma rota que aparece anteriormente têm uma prioridade mais alta.</span><span class="sxs-lookup"><span data-stu-id="9ec63-223">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="9ec63-224">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="9ec63-224">Conventional routing is order-dependent.</span></span> <span data-ttu-id="9ec63-225">Em geral, as rotas com áreas devem ser posicionadas antes, pois são mais específicas do que as rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="9ec63-225">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="9ec63-226">[Rotas convencionais dedicadas](#dcr) com [parâmetros de rota](xref:fundamentals/routing#greedy)catch all como `{*article}` podem tornar uma rota muito grande, o que significa que ela corresponde às URLs que você pretende corresponder a outras rotas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-226">[Dedicated conventional routes](#dcr) with catch all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="9ec63-227">Coloque as rotas de ávido mais tarde na tabela de rotas para evitar correspondências de caminhada.</span><span class="sxs-lookup"><span data-stu-id="9ec63-227">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="9ec63-228">Resolvendo ações ambíguas</span><span class="sxs-lookup"><span data-stu-id="9ec63-228">Resolving ambiguous actions</span></span>

<span data-ttu-id="9ec63-229">Quando dois pontos de extremidade correspondem ao roteamento, o roteamento deve seguir um destes procedimentos:</span><span class="sxs-lookup"><span data-stu-id="9ec63-229">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="9ec63-230">Escolha o melhor candidato.</span><span class="sxs-lookup"><span data-stu-id="9ec63-230">Choose the best candidate.</span></span>
* <span data-ttu-id="9ec63-231">Gera uma exceção.</span><span class="sxs-lookup"><span data-stu-id="9ec63-231">Throw an exception.</span></span>

<span data-ttu-id="9ec63-232">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9ec63-232">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="9ec63-233">O controlador anterior define duas ações que correspondem a:</span><span class="sxs-lookup"><span data-stu-id="9ec63-233">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="9ec63-234">O caminho da URL `/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="9ec63-234">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="9ec63-235">`{ controller = Products33, action = Edit, id = 17 }`de dados de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-235">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="9ec63-236">Esse é um padrão típico para controladores MVC:</span><span class="sxs-lookup"><span data-stu-id="9ec63-236">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="9ec63-237">`Edit(int)` exibe um formulário para editar um produto.</span><span class="sxs-lookup"><span data-stu-id="9ec63-237">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="9ec63-238">`Edit(int, Product)` processa o formulário Postado.</span><span class="sxs-lookup"><span data-stu-id="9ec63-238">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="9ec63-239">Para resolver a rota correta:</span><span class="sxs-lookup"><span data-stu-id="9ec63-239">To resolve the correct route:</span></span>

* <span data-ttu-id="9ec63-240">`Edit(int, Product)` é selecionado quando a solicitação é um `POST`HTTP.</span><span class="sxs-lookup"><span data-stu-id="9ec63-240">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="9ec63-241">`Edit(int)` é selecionado quando o [verbo http](#verb) é qualquer outra coisa.</span><span class="sxs-lookup"><span data-stu-id="9ec63-241">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="9ec63-242">`Edit(int)` geralmente é chamado via `GET`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-242">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="9ec63-243">O <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, é fornecido ao roteamento para que ele possa escolher com base no método HTTP da solicitação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-243">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="9ec63-244">O `HttpPostAttribute` faz `Edit(int, Product)` uma correspondência melhor do que `Edit(int)`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-244">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="9ec63-245">É importante entender a função de atributos, como `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-245">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="9ec63-246">Atributos semelhantes são definidos para outros [verbos HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="9ec63-246">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="9ec63-247">No [Roteamento convencional](#cr), é comum que as ações usem o mesmo nome de ação quando fazem parte de um fluxo de trabalho de Mostrar formulário, enviar formulário.</span><span class="sxs-lookup"><span data-stu-id="9ec63-247">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="9ec63-248">Por exemplo, consulte [examinar os dois métodos de ação de edição](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="9ec63-248">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="9ec63-249">Se o roteamento não puder escolher um melhor candidato, um <xref:System.Reflection.AmbiguousMatchException> será lançado, listando os vários pontos de extremidade correspondentes.</span><span class="sxs-lookup"><span data-stu-id="9ec63-249">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="9ec63-250">Nomes de rota convencionais</span><span class="sxs-lookup"><span data-stu-id="9ec63-250">Conventional route names</span></span>

<span data-ttu-id="9ec63-251">As cadeias de caracteres `"blog"` e `"default"` nos exemplos a seguir são nomes de rotas convencionais:</span><span class="sxs-lookup"><span data-stu-id="9ec63-251">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="9ec63-252">Os nomes de rota fornecem um nome lógico à rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-252">The route names give the route a logical name.</span></span> <span data-ttu-id="9ec63-253">A rota nomeada pode ser usada para a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-253">The named route can be used for URL generation.</span></span> <span data-ttu-id="9ec63-254">O uso de uma rota nomeada simplifica a criação de URL quando a ordenação de rotas pode tornar a geração de URL complicada.</span><span class="sxs-lookup"><span data-stu-id="9ec63-254">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="9ec63-255">Os nomes de rota devem ser de todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-255">Route names must be unique application wide.</span></span>

<span data-ttu-id="9ec63-256">Nomes de rota:</span><span class="sxs-lookup"><span data-stu-id="9ec63-256">Route names:</span></span>

* <span data-ttu-id="9ec63-257">Não têm impacto sobre a correspondência de URL ou o tratamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="9ec63-257">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="9ec63-258">São usados somente para a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-258">Are used only for URL generation.</span></span>

<span data-ttu-id="9ec63-259">O conceito de nome de rota é representado no roteamento como [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="9ec63-259">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="9ec63-260">Os termos **nome da rota** e **nome do ponto de extremidade**:</span><span class="sxs-lookup"><span data-stu-id="9ec63-260">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="9ec63-261">São intercambiáveis.</span><span class="sxs-lookup"><span data-stu-id="9ec63-261">Are interchangeable.</span></span>
* <span data-ttu-id="9ec63-262">Qual delas é usada na documentação e no código depende da API que está sendo descrita.</span><span class="sxs-lookup"><span data-stu-id="9ec63-262">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="9ec63-263">Roteamento de atributos para APIs REST</span><span class="sxs-lookup"><span data-stu-id="9ec63-263">Attribute routing for REST APIs</span></span>

<span data-ttu-id="9ec63-264">As APIs REST devem usar o roteamento de atributos para modelar a funcionalidade do aplicativo como um conjunto de recursos em que as operações são representadas por [verbos HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="9ec63-264">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="9ec63-265">O roteamento de atributo usa um conjunto de atributos para mapear ações diretamente para modelos de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-265">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="9ec63-266">O código de `StartUp.Configure` a seguir é típico para uma API REST e é usado no próximo exemplo:</span><span class="sxs-lookup"><span data-stu-id="9ec63-266">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="9ec63-267">No código anterior, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> é chamado dentro de `UseEndpoints` para mapear controladores roteados de atributo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-267">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="9ec63-268">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9ec63-268">In the following example:</span></span>

* <span data-ttu-id="9ec63-269">O método de `Configure` anterior é usado.</span><span class="sxs-lookup"><span data-stu-id="9ec63-269">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="9ec63-270">`HomeController` corresponde a um conjunto de URLs semelhantes ao que a rota convencional padrão `{controller=Home}/{action=Index}/{id?}` corresponde.</span><span class="sxs-lookup"><span data-stu-id="9ec63-270">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="9ec63-271">A ação `HomeController.Index` é executada para qualquer um dos caminhos de URL `/`, `/Home`, `/Home/Index`ou `/Home/Index/3`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-271">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="9ec63-272">Este exemplo realça uma importante diferença de programação entre roteamento de atributo e [Roteamento convencional](#cr).</span><span class="sxs-lookup"><span data-stu-id="9ec63-272">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="9ec63-273">O roteamento de atributos requer mais entrada para especificar uma rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-273">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="9ec63-274">A rota padrão convencional lida com as rotas mais sucintamente.</span><span class="sxs-lookup"><span data-stu-id="9ec63-274">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="9ec63-275">No entanto, o roteamento de atributos permite e exige um controle preciso de quais modelos de rota se aplicam a cada [ação](#action).</span><span class="sxs-lookup"><span data-stu-id="9ec63-275">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="9ec63-276">Com o roteamento de atributos, o nome do controlador e os nomes de ação não desempenham **nenhuma** função na qual a ação é correspondida.</span><span class="sxs-lookup"><span data-stu-id="9ec63-276">With attribute routing, the controller name and action names play **no** role in which action is matched.</span></span> <span data-ttu-id="9ec63-277">O exemplo a seguir corresponde às mesmas URLs do exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="9ec63-277">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="9ec63-278">O código a seguir usa a substituição de token para `action` e `controller`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-278">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="9ec63-279">O código a seguir aplica-se `[Route("[controller]/[action]")]` ao controlador:</span><span class="sxs-lookup"><span data-stu-id="9ec63-279">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="9ec63-280">No código anterior, os modelos de método de `Index` devem preceder `/` ou `~/` aos modelos de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-280">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="9ec63-281">Modelos de rota aplicados a uma ação, que começam com `/` ou `~/`, não são combinados com modelos de rota aplicados ao controlador.</span><span class="sxs-lookup"><span data-stu-id="9ec63-281">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="9ec63-282">Consulte [precedência do modelo de rota](xref:fundamentals/routing#rtp) para obter informações sobre a seleção do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-282">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="9ec63-283">Nomes reservados de roteamento</span><span class="sxs-lookup"><span data-stu-id="9ec63-283">Reserved routing names</span></span>

<span data-ttu-id="9ec63-284">As seguintes palavras-chave são nomes de parâmetro de rota reservados ao usar controladores ou Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="9ec63-284">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="9ec63-285">O uso de `page` como um parâmetro de rota com roteamento de atributo é um erro comum.</span><span class="sxs-lookup"><span data-stu-id="9ec63-285">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="9ec63-286">Fazer isso resulta em comportamento inconsistente e confuso com a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-286">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="9ec63-287">Os nomes de parâmetro especiais são usados pela geração de URL para determinar se uma operação de geração de URL refere-se a uma página Razor ou a um controlador.</span><span class="sxs-lookup"><span data-stu-id="9ec63-287">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="9ec63-288">Modelos de verbo HTTP</span><span class="sxs-lookup"><span data-stu-id="9ec63-288">HTTP verb templates</span></span>

<span data-ttu-id="9ec63-289">ASP.NET Core tem os seguintes modelos de verbo HTTP:</span><span class="sxs-lookup"><span data-stu-id="9ec63-289">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="9ec63-290">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="9ec63-290">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="9ec63-291">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="9ec63-291">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="9ec63-292">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="9ec63-292">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="9ec63-293">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="9ec63-293">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="9ec63-294">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="9ec63-294">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="9ec63-295">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="9ec63-295">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="9ec63-296">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="9ec63-296">Route templates</span></span>

<span data-ttu-id="9ec63-297">ASP.NET Core tem os seguintes modelos de rota:</span><span class="sxs-lookup"><span data-stu-id="9ec63-297">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="9ec63-298">Todos os [modelos de verbo http](#verb) são modelos de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-298">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="9ec63-299">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="9ec63-299">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="9ec63-300">Roteamento de atributos com atributos de verbo http</span><span class="sxs-lookup"><span data-stu-id="9ec63-300">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="9ec63-301">Considere o seguinte controlador:</span><span class="sxs-lookup"><span data-stu-id="9ec63-301">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="9ec63-302">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="9ec63-302">In the preceding code:</span></span>

* <span data-ttu-id="9ec63-303">Cada ação contém o atributo `[HttpGet]`, que restringe a correspondência somente a solicitações HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="9ec63-303">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="9ec63-304">A ação de `GetProduct` inclui o modelo de `"{id}"`, portanto `id` é anexado ao modelo `"api/[controller]"` no controlador.</span><span class="sxs-lookup"><span data-stu-id="9ec63-304">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="9ec63-305">O modelo de métodos é `"api/[controller]/"{id}""`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-305">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="9ec63-306">Portanto, essa ação só corresponde às solicitações GET do formulário `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span><span class="sxs-lookup"><span data-stu-id="9ec63-306">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="9ec63-307">A ação `GetIntProduct` contém o modelo de `"int/{id:int}")`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-307">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="9ec63-308">A parte `:int` do modelo restringe os valores de rota `id` para cadeias de caracteres que podem ser convertidas em um número inteiro.</span><span class="sxs-lookup"><span data-stu-id="9ec63-308">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="9ec63-309">Uma solicitação GET para `/api/test2/int/abc`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-309">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="9ec63-310">Não corresponde a esta ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-310">Doesn't match this action.</span></span>
  * <span data-ttu-id="9ec63-311">Retorna um erro [404 não encontrado](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .</span><span class="sxs-lookup"><span data-stu-id="9ec63-311">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="9ec63-312">A ação de `GetInt2Product` contém `{id}` no modelo, mas não restringe `id` a valores que podem ser convertidos em um número inteiro.</span><span class="sxs-lookup"><span data-stu-id="9ec63-312">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="9ec63-313">Uma solicitação GET para `/api/test2/int2/abc`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-313">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="9ec63-314">Corresponde a essa rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-314">Matches this route.</span></span>
  * <span data-ttu-id="9ec63-315">A associação de modelo falha ao converter `abc` em um inteiro.</span><span class="sxs-lookup"><span data-stu-id="9ec63-315">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="9ec63-316">O parâmetro `id` do método é inteiro.</span><span class="sxs-lookup"><span data-stu-id="9ec63-316">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="9ec63-317">Retorna uma [solicitação inadequada 400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) porque a associação de modelo falhou ao converter`abc` em um inteiro.</span><span class="sxs-lookup"><span data-stu-id="9ec63-317">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="9ec63-318">O roteamento de atributos pode usar atributos de <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> como <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>e <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span><span class="sxs-lookup"><span data-stu-id="9ec63-318">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="9ec63-319">Todos os atributos de [verbo http](#verb) aceitam um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-319">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="9ec63-320">O exemplo a seguir mostra duas ações que correspondem ao mesmo modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="9ec63-320">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="9ec63-321">Usando o caminho da URL `/products3`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-321">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="9ec63-322">A ação `MyProductsController.ListProducts` é executada quando o [verbo http](#verb) é `GET`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-322">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="9ec63-323">A ação `MyProductsController.CreateProduct` é executada quando o [verbo http](#verb) é `POST`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-323">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="9ec63-324">Ao criar uma API REST, é raro que você precise usar `[Route(...)]` em um método de ação, pois a ação aceita todos os métodos HTTP.</span><span class="sxs-lookup"><span data-stu-id="9ec63-324">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="9ec63-325">É melhor usar o [atributo de verbo http](#verb) mais específico para ser preciso em relação ao que sua API dá suporte.</span><span class="sxs-lookup"><span data-stu-id="9ec63-325">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="9ec63-326">Espera-se que clientes de APIs REST saibam quais caminhos e verbos HTTP são mapeados para operações lógicas específicas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-326">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="9ec63-327">As APIs REST devem usar o roteamento de atributos para modelar a funcionalidade do aplicativo como um conjunto de recursos em que as operações são representadas por verbos HTTP.</span><span class="sxs-lookup"><span data-stu-id="9ec63-327">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="9ec63-328">Isso significa que muitas operações, por exemplo, GET e POST no mesmo recurso lógico usam a mesma URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-328">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="9ec63-329">O roteamento de atributo fornece um nível de controle necessário para projetar cuidadosamente o layout de ponto de extremidade público de uma API.</span><span class="sxs-lookup"><span data-stu-id="9ec63-329">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="9ec63-330">Como uma rota de atributo se aplica a uma ação específica, é fácil fazer com que parâmetros sejam obrigatórios como parte da definição do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-330">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="9ec63-331">No exemplo a seguir, `id` é necessário como parte do caminho da URL:</span><span class="sxs-lookup"><span data-stu-id="9ec63-331">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="9ec63-332">A ação `Products2ApiController.GetProduct(int)`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-332">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="9ec63-333">É executado com caminho de URL como `/products2/3`</span><span class="sxs-lookup"><span data-stu-id="9ec63-333">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="9ec63-334">Não é executado com o caminho da URL `/products2`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-334">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="9ec63-335">O atributo [[consume]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permite que uma ação limite os tipos de conteúdo de solicitação com suporte.</span><span class="sxs-lookup"><span data-stu-id="9ec63-335">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="9ec63-336">Para obter mais informações, consulte [definir tipos de conteúdo de solicitação com suporte com o atributo consumes](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="9ec63-336">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="9ec63-337">Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição completa de modelos de rota e as opções relacionadas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-337">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="9ec63-338">Para obter mais informações sobre `[ApiController]`, consulte [atributo ApiController](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="9ec63-338">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="9ec63-339">Nome da rota</span><span class="sxs-lookup"><span data-stu-id="9ec63-339">Route name</span></span>

<span data-ttu-id="9ec63-340">O código a seguir define um nome de rota de `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-340">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="9ec63-341">Nomes de rota podem ser usados para gerar uma URL com base em uma rota específica.</span><span class="sxs-lookup"><span data-stu-id="9ec63-341">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="9ec63-342">Nomes de rota:</span><span class="sxs-lookup"><span data-stu-id="9ec63-342">Route names:</span></span>

* <span data-ttu-id="9ec63-343">Não têm impacto sobre o comportamento de correspondência de URL do roteamento.</span><span class="sxs-lookup"><span data-stu-id="9ec63-343">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="9ec63-344">São usados somente para a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-344">Are only used for URL generation.</span></span>

<span data-ttu-id="9ec63-345">Nomes de rotas devem ser exclusivos no nível do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-345">Route names must be unique application-wide.</span></span>

<span data-ttu-id="9ec63-346">Compare o código anterior com a rota padrão convencional, que define o parâmetro `id` como opcional (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="9ec63-346">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="9ec63-347">A capacidade de especificar com precisão as APIs tem vantagens, como permitir que `/products` e `/products/5` sejam expedidas para ações diferentes.</span><span class="sxs-lookup"><span data-stu-id="9ec63-347">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="9ec63-348">Combinando rotas de atributo</span><span class="sxs-lookup"><span data-stu-id="9ec63-348">Combining attribute routes</span></span>

<span data-ttu-id="9ec63-349">Para tornar o roteamento de atributo menos repetitivo, os atributos de rota no controlador são combinados com atributos de rota nas ações individuais.</span><span class="sxs-lookup"><span data-stu-id="9ec63-349">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="9ec63-350">Modelos de rota definidos no controlador precedem modelos de rota nas ações.</span><span class="sxs-lookup"><span data-stu-id="9ec63-350">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="9ec63-351">Colocar um atributo de rota no controlador faz com que  **todas** as ações no controlador usem o roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-351">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="9ec63-352">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="9ec63-352">In the preceding example:</span></span>

* <span data-ttu-id="9ec63-353">O caminho da URL `/products` pode corresponder `ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="9ec63-353">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="9ec63-354">O caminho da URL `/products/5` pode corresponder `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-354">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="9ec63-355">Ambas as ações correspondem somente a `GET` HTTP porque elas são marcadas com o atributo `[HttpGet]`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-355">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="9ec63-356">Modelos de rota aplicados a uma ação, que começam com `/` ou `~/`, não são combinados com modelos de rota aplicados ao controlador.</span><span class="sxs-lookup"><span data-stu-id="9ec63-356">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="9ec63-357">O exemplo a seguir corresponde a um conjunto de caminhos de URL semelhante à rota padrão.</span><span class="sxs-lookup"><span data-stu-id="9ec63-357">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="9ec63-358">A tabela a seguir explica os atributos de `[Route]` no código anterior:</span><span class="sxs-lookup"><span data-stu-id="9ec63-358">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="9ec63-359">Atributo</span><span class="sxs-lookup"><span data-stu-id="9ec63-359">Attribute</span></span>               | <span data-ttu-id="9ec63-360">Combina com `[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="9ec63-360">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="9ec63-361">Define o modelo de rota</span><span class="sxs-lookup"><span data-stu-id="9ec63-361">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="9ec63-362">Sim</span><span class="sxs-lookup"><span data-stu-id="9ec63-362">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="9ec63-363">Sim</span><span class="sxs-lookup"><span data-stu-id="9ec63-363">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="9ec63-364">**Não**</span><span class="sxs-lookup"><span data-stu-id="9ec63-364">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="9ec63-365">Sim</span><span class="sxs-lookup"><span data-stu-id="9ec63-365">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="9ec63-366">Ordem de rota de atributo</span><span class="sxs-lookup"><span data-stu-id="9ec63-366">Attribute route order</span></span>

<span data-ttu-id="9ec63-367">O roteamento cria uma árvore e corresponde a todos os pontos de extremidade simultaneamente:</span><span class="sxs-lookup"><span data-stu-id="9ec63-367">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="9ec63-368">As entradas de rota se comportam como se colocadas em uma ordenação ideal.</span><span class="sxs-lookup"><span data-stu-id="9ec63-368">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="9ec63-369">As rotas mais específicas têm a chance de serem executadas antes das rotas mais gerais.</span><span class="sxs-lookup"><span data-stu-id="9ec63-369">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="9ec63-370">Por exemplo, uma rota de atributo como `blog/search/{topic}` é mais específica do que uma rota de atributo como `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-370">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="9ec63-371">A rota de `blog/search/{topic}` tem prioridade mais alta, por padrão, porque é mais específica.</span><span class="sxs-lookup"><span data-stu-id="9ec63-371">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="9ec63-372">Usando o [Roteamento convencional](#cr), o desenvolvedor é responsável por colocar rotas na ordem desejada.</span><span class="sxs-lookup"><span data-stu-id="9ec63-372">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="9ec63-373">As rotas de atributo podem configurar um pedido usando a propriedade <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order>.</span><span class="sxs-lookup"><span data-stu-id="9ec63-373">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="9ec63-374">Todos os [atributos de rota](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) fornecidos pelo framework incluem `Order`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-374">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="9ec63-375">As rotas são processadas segundo uma classificação crescente da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-375">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="9ec63-376">A ordem padrão é `0`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-376">The default order is `0`.</span></span> <span data-ttu-id="9ec63-377">Definir uma rota usando `Order = -1` é executado antes das rotas que não definem um pedido.</span><span class="sxs-lookup"><span data-stu-id="9ec63-377">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="9ec63-378">Definir uma rota usando `Order = 1` é executado após a ordenação de rota padrão.</span><span class="sxs-lookup"><span data-stu-id="9ec63-378">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="9ec63-379">**Evite** , dependendo do `Order`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-379">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="9ec63-380">Se o espaço de URL de um aplicativo exigir valores de ordem explícitos para rotear corretamente, isso provavelmente será confuso para os clientes também.</span><span class="sxs-lookup"><span data-stu-id="9ec63-380">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="9ec63-381">Em geral, o roteamento de atributos seleciona a rota correta com correspondência de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-381">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="9ec63-382">Se a ordem padrão usada para a geração de URL não estiver funcionando, usar um nome de rota como uma substituição é geralmente mais simples do que aplicar a propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-382">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="9ec63-383">Considere os dois controladores a seguir que definem a rota correspondente `/home`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-383">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="9ec63-384">Solicitar `/home` com o código anterior gera uma exceção semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="9ec63-384">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="9ec63-385">Adicionar `Order` a um dos atributos de rota resolve a ambiguidade:</span><span class="sxs-lookup"><span data-stu-id="9ec63-385">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="9ec63-386">Com o código anterior, `/home` executa o ponto de extremidade `HomeController.Index`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-386">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="9ec63-387">Para obter o `MyDemoController.MyIndex`, solicite `/home/MyIndex`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-387">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="9ec63-388">**Observação**:</span><span class="sxs-lookup"><span data-stu-id="9ec63-388">**Note**:</span></span>

* <span data-ttu-id="9ec63-389">O código anterior é um exemplo de design de roteamento insatisfatório.</span><span class="sxs-lookup"><span data-stu-id="9ec63-389">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="9ec63-390">Ele foi usado para ilustrar a propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-390">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="9ec63-391">A propriedade `Order` resolve apenas a ambiguidade, o modelo não pode ser correspondido.</span><span class="sxs-lookup"><span data-stu-id="9ec63-391">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="9ec63-392">Seria melhor remover o modelo de `[Route("Home")]`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-392">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="9ec63-393">Consulte [Razor Pages rota e convenções de aplicativo: ordem de rota](xref:razor-pages/razor-pages-conventions#route-order) para obter informações sobre a ordem de rota com Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="9ec63-393">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="9ec63-394">Em alguns casos, um erro HTTP 500 é retornado com rotas ambíguas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-394">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="9ec63-395">Use o [registro em log](xref:fundamentals/logging/index) para ver quais pontos de extremidade causaram a `AmbiguousMatchException`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-395">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="9ec63-396">Substituição de token em modelos de rota [controlador], [ação], [área]</span><span class="sxs-lookup"><span data-stu-id="9ec63-396">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="9ec63-397">Para sua conveniência, as rotas de atributo dão suporte à substituição de token para parâmetros de rota reservados ao colocar um token em um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="9ec63-397">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="9ec63-398">Chaves: `[]`</span><span class="sxs-lookup"><span data-stu-id="9ec63-398">Square braces: `[]`</span></span>
* <span data-ttu-id="9ec63-399">Chaves: `{}`</span><span class="sxs-lookup"><span data-stu-id="9ec63-399">Curly braces: `{}`</span></span>

<span data-ttu-id="9ec63-400">Os tokens `[action]`, `[area]`e `[controller]` são substituídos pelos valores do nome da ação, do nome da área e do nome do controlador da ação em que a rota é definida:</span><span class="sxs-lookup"><span data-stu-id="9ec63-400">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="9ec63-401">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="9ec63-401">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="9ec63-402">Corresponde a `/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="9ec63-402">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="9ec63-403">Corresponde a `/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="9ec63-403">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="9ec63-404">A substituição de token ocorre como a última etapa da criação das rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-404">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="9ec63-405">O exemplo anterior se comporta da mesma forma que o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="9ec63-405">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="9ec63-406">Rotas de atributo também podem ser combinadas com herança.</span><span class="sxs-lookup"><span data-stu-id="9ec63-406">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="9ec63-407">Isso é poderoso combinado com a substituição de token.</span><span class="sxs-lookup"><span data-stu-id="9ec63-407">This is powerful combined with token replacement.</span></span> <span data-ttu-id="9ec63-408">A substituição de token também se aplica a nomes de rota definidos por rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-408">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="9ec63-409">`[Route("[controller]/[action]", Name="[controller]_[action]")]`gera um nome de rota exclusivo para cada ação:</span><span class="sxs-lookup"><span data-stu-id="9ec63-409">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="9ec63-410">A substituição de token também se aplica a nomes de rota definidos por rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-410">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="9ec63-411">gera um nome de rota exclusivo para cada ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-411">generates a unique route name for each action.</span></span>

<span data-ttu-id="9ec63-412">Para corresponder ao delimitador de substituição de token literal `[` ou `]`, faça seu escape repetindo o caractere (`[[` ou `]]`).</span><span class="sxs-lookup"><span data-stu-id="9ec63-412">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="9ec63-413">Usar um transformador de parâmetro para personalizar a substituição de token</span><span class="sxs-lookup"><span data-stu-id="9ec63-413">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="9ec63-414">A substituição do token pode ser personalizada usando um transformador de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="9ec63-414">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="9ec63-415">Um transformador de parâmetro implementa <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> e transforma o valor dos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="9ec63-415">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="9ec63-416">Por exemplo, um transformador de parâmetro de `SlugifyParameterTransformer` personalizado altera o `SubscriptionManagement` valor de rota para `subscription-management`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-416">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="9ec63-417">O <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> é uma convenção de modelo de aplicativo que:</span><span class="sxs-lookup"><span data-stu-id="9ec63-417">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="9ec63-418">Aplica um transformador de parâmetro a todas as rotas de atributo em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-418">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="9ec63-419">Personaliza os valores de token de rota de atributo conforme eles são substituídos.</span><span class="sxs-lookup"><span data-stu-id="9ec63-419">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="9ec63-420">O método `ListAll` anterior corresponde a `/subscription-management/list-all`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-420">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="9ec63-421">O `RouteTokenTransformerConvention` está registrado como uma opção em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-421">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="9ec63-422">Consulte [MDN Web docs no](https://developer.mozilla.org/docs/Glossary/Slug) separador para obter a definição do separador.</span><span class="sxs-lookup"><span data-stu-id="9ec63-422">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="9ec63-423">Várias rotas de atributos</span><span class="sxs-lookup"><span data-stu-id="9ec63-423">Multiple attribute routes</span></span>

<span data-ttu-id="9ec63-424">O roteamento de atributo dá suporte à definição de várias rotas que atingem a mesma ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-424">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="9ec63-425">O uso mais comum disso é imitar o comportamento da rota convencional padrão, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9ec63-425">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="9ec63-426">Colocar vários atributos de rota no controlador significa que cada um combina com cada um dos atributos de rota nos métodos de ação:</span><span class="sxs-lookup"><span data-stu-id="9ec63-426">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="9ec63-427">Todas as restrições de rota de [verbo http](#verb) implementam `IActionConstraint`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-427">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="9ec63-428">Quando vários atributos de rota que implementam <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> são colocados em uma ação:</span><span class="sxs-lookup"><span data-stu-id="9ec63-428">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="9ec63-429">Cada restrição de ação combina com o modelo de rota aplicado ao controlador.</span><span class="sxs-lookup"><span data-stu-id="9ec63-429">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="9ec63-430">O uso de várias rotas em ações pode parecer útil e eficiente, é melhor manter o espaço de URL básico e bem definido do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-430">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="9ec63-431">Use várias rotas em ações **somente** quando necessário, por exemplo, para dar suporte a clientes existentes.</span><span class="sxs-lookup"><span data-stu-id="9ec63-431">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="9ec63-432">Especificando parâmetros opcionais, valores padrão e restrições da rota de atributo</span><span class="sxs-lookup"><span data-stu-id="9ec63-432">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="9ec63-433">Rotas de atributo dão suporte à mesma sintaxe embutida que as rotas convencionais para especificar parâmetros opcionais, valores padrão e restrições.</span><span class="sxs-lookup"><span data-stu-id="9ec63-433">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="9ec63-434">No código anterior, `[HttpPost("product/{id:int}")]` aplica uma restrição de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-434">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="9ec63-435">A ação `ProductsController.ShowProduct` é correspondida somente por caminhos de URL como `/product/3`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-435">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="9ec63-436">A parte do modelo de rota `{id:int}` restringe esse segmento a apenas inteiros.</span><span class="sxs-lookup"><span data-stu-id="9ec63-436">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="9ec63-437">Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-437">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="9ec63-438">Atributos de rota personalizados usando IRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="9ec63-438">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="9ec63-439">Todos os [atributos de rota](#rt) implementam <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span><span class="sxs-lookup"><span data-stu-id="9ec63-439">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="9ec63-440">O tempo de execução de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9ec63-440">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="9ec63-441">Procura atributos em classes de controlador e métodos de ação quando o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="9ec63-441">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="9ec63-442">Usa os atributos que implementam `IRouteTemplateProvider` para criar o conjunto inicial de rotas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-442">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="9ec63-443">Implemente `IRouteTemplateProvider` para definir atributos de rota personalizados.</span><span class="sxs-lookup"><span data-stu-id="9ec63-443">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="9ec63-444">Cada `IRouteTemplateProvider` permite definir uma única rota com um nome, uma ordem e um modelo de rota personalizado:</span><span class="sxs-lookup"><span data-stu-id="9ec63-444">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="9ec63-445">O método `Get` anterior retorna `Order = 2, Template = api/MyTestApi`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-445">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="9ec63-446">Usar o modelo de aplicativo para personalizar as rotas de atributo</span><span class="sxs-lookup"><span data-stu-id="9ec63-446">Use application model to customize attribute routes</span></span>

<span data-ttu-id="9ec63-447">O modelo de aplicativo:</span><span class="sxs-lookup"><span data-stu-id="9ec63-447">The application model:</span></span>

* <span data-ttu-id="9ec63-448">É um modelo de objeto criado na inicialização.</span><span class="sxs-lookup"><span data-stu-id="9ec63-448">Is an object model created at startup.</span></span>
* <span data-ttu-id="9ec63-449">Contém todos os metadados usados pelo ASP.NET Core para rotear e executar as ações em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-449">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="9ec63-450">O modelo de aplicativo inclui todos os dados coletados dos atributos de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-450">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="9ec63-451">Os dados dos atributos de rota são fornecidos pela implementação de `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-451">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="9ec63-452">Regras</span><span class="sxs-lookup"><span data-stu-id="9ec63-452">Conventions:</span></span>

* <span data-ttu-id="9ec63-453">Pode ser escrito para modificar o modelo de aplicativo para personalizar a forma como o roteamento se comporta.</span><span class="sxs-lookup"><span data-stu-id="9ec63-453">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="9ec63-454">São lidos na inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-454">Are read at app startup.</span></span>

<span data-ttu-id="9ec63-455">Esta seção mostra um exemplo básico de personalização de roteamento usando o modelo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-455">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="9ec63-456">O código a seguir torna as rotas aproximadamente alinhadas com a estrutura de pastas do projeto.</span><span class="sxs-lookup"><span data-stu-id="9ec63-456">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="9ec63-457">O código a seguir impede que a Convenção de `namespace` seja aplicada a controladores que são roteados por atributo:</span><span class="sxs-lookup"><span data-stu-id="9ec63-457">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="9ec63-458">Por exemplo, o controlador a seguir não usa `NamespaceRoutingConvention`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-458">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="9ec63-459">O método `NamespaceRoutingConvention.Apply`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-459">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="9ec63-460">Não fará nada se o controlador for um atributo roteado.</span><span class="sxs-lookup"><span data-stu-id="9ec63-460">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="9ec63-461">Define o modelo de controladores com base na `namespace`, com a `namespace` de base removida.</span><span class="sxs-lookup"><span data-stu-id="9ec63-461">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="9ec63-462">O `NamespaceRoutingConvention` pode ser aplicado no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-462">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="9ec63-463">Por exemplo, considere o seguinte controlador:</span><span class="sxs-lookup"><span data-stu-id="9ec63-463">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="9ec63-464">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="9ec63-464">In the preceding code:</span></span>

* <span data-ttu-id="9ec63-465">O `namespace` base é `My.Application`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-465">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="9ec63-466">O nome completo do controlador anterior é `My.Application.Admin.Controllers.UsersController`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-466">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="9ec63-467">O `NamespaceRoutingConvention` define o modelo de controladores como `Admin/Controllers/Users/[action]/{id?`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-467">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="9ec63-468">O `NamespaceRoutingConvention` também pode ser aplicado como um atributo em um controlador:</span><span class="sxs-lookup"><span data-stu-id="9ec63-468">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="9ec63-469">Roteamento misto: roteamento de atributo versus roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="9ec63-469">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="9ec63-470">ASP.NET Core aplicativos podem misturar o uso de roteamento convencional e roteamento de atributos.</span><span class="sxs-lookup"><span data-stu-id="9ec63-470">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="9ec63-471">É comum usar rotas convencionais para controladores que servem páginas HTML para navegadores e usar o roteamento de atributo para controladores que servem APIs REST.</span><span class="sxs-lookup"><span data-stu-id="9ec63-471">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="9ec63-472">As ações são roteadas convencionalmente ou segundo os atributos.</span><span class="sxs-lookup"><span data-stu-id="9ec63-472">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="9ec63-473">Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-473">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="9ec63-474">Ações que definem rotas de atributo não podem ser acessadas por meio das rotas convencionais e vice-versa.</span><span class="sxs-lookup"><span data-stu-id="9ec63-474">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="9ec63-475">**Qualquer** atributo de rota no controlador faz com que **todas as** ações no atributo do controlador sejam roteadas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-475">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="9ec63-476">Roteamento de atributos e roteamento convencional usam o mesmo mecanismo de roteamento.</span><span class="sxs-lookup"><span data-stu-id="9ec63-476">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="9ec63-477">Geração de URL e valores de ambiente</span><span class="sxs-lookup"><span data-stu-id="9ec63-477">URL Generation and ambient values</span></span>

<span data-ttu-id="9ec63-478">Os aplicativos podem usar os recursos de geração de URL de roteamento para gerar links de URL para ações.</span><span class="sxs-lookup"><span data-stu-id="9ec63-478">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="9ec63-479">A geração de URLs elimina URLs de codificação, tornando o código mais robusto e passível de manutenção.</span><span class="sxs-lookup"><span data-stu-id="9ec63-479">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="9ec63-480">Esta seção se concentra nos recursos de geração de URL fornecidos pelo MVC e aborda apenas noções básicas de como funciona a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-480">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="9ec63-481">Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição detalhada da geração de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-481">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="9ec63-482">A interface <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> é o elemento subjacente da infraestrutura entre o MVC e o roteamento para a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-482">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="9ec63-483">Uma instância de `IUrlHelper` está disponível por meio da propriedade `Url` em controladores, exibições e componentes de exibição.</span><span class="sxs-lookup"><span data-stu-id="9ec63-483">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="9ec63-484">No exemplo a seguir, a interface `IUrlHelper` é usada por meio da propriedade `Controller.Url` para gerar uma URL para outra ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-484">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="9ec63-485">Se o aplicativo estiver usando a rota convencional padrão, o valor da variável `url` será a cadeia de caracteres do caminho da URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-485">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="9ec63-486">Esse caminho de URL é criado pelo roteamento combinando:</span><span class="sxs-lookup"><span data-stu-id="9ec63-486">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="9ec63-487">Os valores de rota da solicitação atual, que são chamados de **valores de ambiente**.</span><span class="sxs-lookup"><span data-stu-id="9ec63-487">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="9ec63-488">Os valores passados para `Url.Action` e substituindo esses valores no modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="9ec63-488">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="9ec63-489">Cada parâmetro de rota no modelo de rota tem seu valor substituído por nomes correspondentes com os valores e os valores de ambiente.</span><span class="sxs-lookup"><span data-stu-id="9ec63-489">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="9ec63-490">Um parâmetro de rota que não tem um valor pode:</span><span class="sxs-lookup"><span data-stu-id="9ec63-490">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="9ec63-491">Use um valor padrão se ele tiver um.</span><span class="sxs-lookup"><span data-stu-id="9ec63-491">Use a default value if it has one.</span></span>
* <span data-ttu-id="9ec63-492">Será ignorado se for opcional.</span><span class="sxs-lookup"><span data-stu-id="9ec63-492">Be skipped if it's optional.</span></span> <span data-ttu-id="9ec63-493">Por exemplo, o `id` do modelo de rota `{controller}/{action}/{id?}`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-493">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="9ec63-494">A geração de URL falhará se qualquer parâmetro de rota necessário não tiver um valor correspondente.</span><span class="sxs-lookup"><span data-stu-id="9ec63-494">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="9ec63-495">Se a geração de URL falhar para uma rota, a rota seguinte será tentada até que todas as rotas tenham sido tentadas ou que uma correspondência seja encontrada.</span><span class="sxs-lookup"><span data-stu-id="9ec63-495">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="9ec63-496">O exemplo anterior de `Url.Action` pressupõe [Roteamento convencional](#cr).</span><span class="sxs-lookup"><span data-stu-id="9ec63-496">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="9ec63-497">A geração de URL funciona de forma semelhante ao [Roteamento de atributos](#ar), embora os conceitos sejam diferentes.</span><span class="sxs-lookup"><span data-stu-id="9ec63-497">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="9ec63-498">Com roteamento convencional:</span><span class="sxs-lookup"><span data-stu-id="9ec63-498">With conventional routing:</span></span>

* <span data-ttu-id="9ec63-499">Os valores de rota são usados para expandir um modelo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-499">The route values are used to expand a template.</span></span>
* <span data-ttu-id="9ec63-500">Os valores de rota para `controller` e `action` geralmente aparecem nesse modelo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-500">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="9ec63-501">Isso funciona porque as URLs correspondidas pelo roteamento aderem a uma convenção.</span><span class="sxs-lookup"><span data-stu-id="9ec63-501">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="9ec63-502">O exemplo a seguir usa roteamento de atributo:</span><span class="sxs-lookup"><span data-stu-id="9ec63-502">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="9ec63-503">A ação de `Source` no código anterior gera `custom/url/to/destination`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-503">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="9ec63-504"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> foi adicionado no ASP.NET Core 3,0 como uma alternativa ao `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-504"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="9ec63-505">o `LinkGenerator` oferece uma funcionalidade semelhante, mas mais flexível.</span><span class="sxs-lookup"><span data-stu-id="9ec63-505">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="9ec63-506">Cada um dos outros métodos em `IUrlHelper` também tem uma família correspondente de métodos em `LinkGenerator`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-506">Each other the methods on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="9ec63-507">Gerando URLs pelo nome da ação</span><span class="sxs-lookup"><span data-stu-id="9ec63-507">Generating URLs by action name</span></span>

<span data-ttu-id="9ec63-508">A [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)e todas as sobrecargas relacionadas são projetadas para gerar o ponto de extremidade de destino especificando um nome de controlador e um nome de ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-508">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="9ec63-509">Ao usar `Url.Action`, os valores de rota atuais para `controller` e `action` são fornecidos pelo tempo de execução:</span><span class="sxs-lookup"><span data-stu-id="9ec63-509">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="9ec63-510">O valor de `controller` e `action` fazem parte de [valores de ambiente](#ambient) e valores.</span><span class="sxs-lookup"><span data-stu-id="9ec63-510">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="9ec63-511">O método `Url.Action` sempre usa os valores atuais de `action` e `controller` e gera um caminho de URL que roteia a ação atual.</span><span class="sxs-lookup"><span data-stu-id="9ec63-511">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="9ec63-512">O roteamento tenta usar os valores em valores de ambiente para preencher as informações que não foram fornecidas durante a geração de uma URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-512">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="9ec63-513">Considere uma rota como `{a}/{b}/{c}/{d}` com valores de ambiente `{ a = Alice, b = Bob, c = Carol, d = David }`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-513">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="9ec63-514">O roteamento tem informações suficientes para gerar uma URL sem valores adicionais.</span><span class="sxs-lookup"><span data-stu-id="9ec63-514">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="9ec63-515">O roteamento tem informações suficientes porque todos os parâmetros de rota têm um valor.</span><span class="sxs-lookup"><span data-stu-id="9ec63-515">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="9ec63-516">Se o valor `{ d = Donovan }` for adicionado:</span><span class="sxs-lookup"><span data-stu-id="9ec63-516">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="9ec63-517">O valor `{ d = David }` é ignorado.</span><span class="sxs-lookup"><span data-stu-id="9ec63-517">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="9ec63-518">O caminho de URL gerado é `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-518">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="9ec63-519">**Aviso**: os caminhos de URL são hierárquicos.</span><span class="sxs-lookup"><span data-stu-id="9ec63-519">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="9ec63-520">No exemplo anterior, se o valor `{ c = Cheryl }` for adicionado:</span><span class="sxs-lookup"><span data-stu-id="9ec63-520">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="9ec63-521">Ambos os valores `{ c = Carol, d = David }` são ignorados.</span><span class="sxs-lookup"><span data-stu-id="9ec63-521">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="9ec63-522">Não há mais um valor para `d` e a geração de URL falha.</span><span class="sxs-lookup"><span data-stu-id="9ec63-522">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="9ec63-523">Os valores desejados de `c` e `d` devem ser especificados para gerar uma URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-523">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="9ec63-524">Talvez você espere atingir esse problema com o `{controller}/{action}/{id?}`de rota padrão.</span><span class="sxs-lookup"><span data-stu-id="9ec63-524">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="9ec63-525">Esse problema é raro na prática porque `Url.Action` sempre especifica explicitamente um `controller` e `action` valor.</span><span class="sxs-lookup"><span data-stu-id="9ec63-525">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="9ec63-526">Várias sobrecargas de [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) usam um objeto de valores de rota para fornecer valores para parâmetros de rota diferentes de `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-526">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="9ec63-527">O objeto de valores de rota é usado frequentemente com `id`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-527">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="9ec63-528">Por exemplo, `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-528">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="9ec63-529">O objeto de valores de rota:</span><span class="sxs-lookup"><span data-stu-id="9ec63-529">The route values object:</span></span>

* <span data-ttu-id="9ec63-530">Por convenção geralmente é um objeto de tipo anônimo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-530">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="9ec63-531">Pode ser um `IDictionary<>` ou um [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="9ec63-531">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="9ec63-532">Qualquer valor de rota adicional que não corresponder aos parâmetros de rota será colocado na cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="9ec63-532">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="9ec63-533">O código anterior gera `/Products/Buy/17?color=red`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-533">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="9ec63-534">O código a seguir gera uma URL absoluta:</span><span class="sxs-lookup"><span data-stu-id="9ec63-534">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="9ec63-535">Para criar uma URL absoluta, use um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="9ec63-535">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="9ec63-536">Uma sobrecarga que aceita uma `protocol`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-536">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="9ec63-537">Por exemplo, o código anterior.</span><span class="sxs-lookup"><span data-stu-id="9ec63-537">For example, the preceding code.</span></span>
* <span data-ttu-id="9ec63-538">[LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), que gera URIs absolutos por padrão.</span><span class="sxs-lookup"><span data-stu-id="9ec63-538">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="9ec63-539">Gerar URLs por rota</span><span class="sxs-lookup"><span data-stu-id="9ec63-539">Generate URLs by route</span></span>

<span data-ttu-id="9ec63-540">O código anterior demonstrou a geração de uma URL passando o nome do controlador e da ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-540">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="9ec63-541">`IUrlHelper` também fornece a família de métodos [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) .</span><span class="sxs-lookup"><span data-stu-id="9ec63-541">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="9ec63-542">Esses métodos são semelhantes a [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), mas não copiam os valores atuais de `action` e `controller` para os valores de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-542">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="9ec63-543">O uso mais comum de `Url.RouteUrl`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-543">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="9ec63-544">Especifica um nome de rota para gerar a URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-544">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="9ec63-545">Geralmente, não especifica um controlador ou um nome de ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-545">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="9ec63-546">O seguinte arquivo Razor gera um link HTML para o `Destination_Route`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-546">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="9ec63-547">Gerar URLs em HTML e Razor</span><span class="sxs-lookup"><span data-stu-id="9ec63-547">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="9ec63-548"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> fornece os métodos de <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) e [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) para gerar elementos `<form>` e `<a>` respectivamente.</span><span class="sxs-lookup"><span data-stu-id="9ec63-548"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="9ec63-549">Esses métodos usam o método [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) para gerar uma URL e aceitam argumentos semelhantes.</span><span class="sxs-lookup"><span data-stu-id="9ec63-549">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="9ec63-550">O complementos `Url.RouteUrl` para `HtmlHelper` são `Html.BeginRouteForm` e `Html.RouteLink`, que têm uma funcionalidade semelhante.</span><span class="sxs-lookup"><span data-stu-id="9ec63-550">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="9ec63-551">TagHelpers geram URLs por meio do TagHelper `form` e do TagHelper `<a>`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-551">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="9ec63-552">Ambos usam `IUrlHelper` para sua implementação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-552">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="9ec63-553">Consulte [auxiliares de marca em formulários](xref:mvc/views/working-with-forms) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="9ec63-553">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="9ec63-554">Nos modos de exibição, o `IUrlHelper` está disponível por meio da propriedade `Url` para qualquer geração de URL ad hoc não abordada acima.</span><span class="sxs-lookup"><span data-stu-id="9ec63-554">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="9ec63-555">Geração de URL nos resultados da ação</span><span class="sxs-lookup"><span data-stu-id="9ec63-555">URL generation in Action Results</span></span>

<span data-ttu-id="9ec63-556">Os exemplos anteriores mostraram o uso de `IUrlHelper` em um controlador.</span><span class="sxs-lookup"><span data-stu-id="9ec63-556">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="9ec63-557">O uso mais comum em um controlador é gerar uma URL como parte de um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-557">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="9ec63-558">As classes base <xref:Microsoft.AspNetCore.Mvc.ControllerBase> e <xref:Microsoft.AspNetCore.Mvc.Controller> fornecem métodos de conveniência para resultados de ação que fazem referência a outra ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-558">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="9ec63-559">Um uso típico é redirecionar depois de aceitar a entrada do usuário:</span><span class="sxs-lookup"><span data-stu-id="9ec63-559">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="9ec63-560">A ação resulta em métodos de fábrica, como <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> e <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> seguir um padrão semelhante aos métodos no `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-560">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="9ec63-561">Caso especial para rotas convencionais dedicadas</span><span class="sxs-lookup"><span data-stu-id="9ec63-561">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="9ec63-562">O [Roteamento convencional](#cr) pode usar um tipo especial de definição de rota chamada de [rota convencional dedicada](#dcr).</span><span class="sxs-lookup"><span data-stu-id="9ec63-562">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="9ec63-563">No exemplo a seguir, a rota chamada `blog` é uma rota convencional dedicada:</span><span class="sxs-lookup"><span data-stu-id="9ec63-563">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="9ec63-564">Usando as definições de rota anteriores, `Url.Action("Index", "Home")` gera o caminho da URL `/` usando a rota `default`, mas por quê?</span><span class="sxs-lookup"><span data-stu-id="9ec63-564">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="9ec63-565">Você poderia imaginar que os valores de rota `{ controller = Home, action = Index }` seriam suficientes para gerar uma URL usando `blog` e o resultado seria `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-565">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="9ec63-566">As [rotas convencionais dedicadas](#dcr) contam com um comportamento especial de valores padrão que não têm um parâmetro de rota correspondente que impede que a rota seja muito [ávido](xref:fundamentals/routing#greedy) com a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-566">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="9ec63-567">Nesse caso, os valores padrão são `{ controller = Blog, action = Article }` e nem `controller` ou `action` aparece como um parâmetro de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-567">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="9ec63-568">Quando o roteamento executa a geração de URL, os valores fornecidos devem corresponder aos valores padrão.</span><span class="sxs-lookup"><span data-stu-id="9ec63-568">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="9ec63-569">A geração de URL usando `blog` falha porque os valores `{ controller = Home, action = Index }` não correspondem `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-569">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="9ec63-570">O roteamento, então, faz o fallback para tentar `default`, que é bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="9ec63-570">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="9ec63-571">Áreas</span><span class="sxs-lookup"><span data-stu-id="9ec63-571">Areas</span></span>

<span data-ttu-id="9ec63-572">As [áreas](xref:mvc/controllers/areas) são um recurso do MVC usado para organizar a funcionalidade relacionada em um grupo como separado:</span><span class="sxs-lookup"><span data-stu-id="9ec63-572">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="9ec63-573">Namespace de roteamento para ações do controlador.</span><span class="sxs-lookup"><span data-stu-id="9ec63-573">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="9ec63-574">Estrutura de pastas para exibições.</span><span class="sxs-lookup"><span data-stu-id="9ec63-574">Folder structure for views.</span></span>

<span data-ttu-id="9ec63-575">O uso de áreas permite que um aplicativo tenha vários controladores com o mesmo nome, desde que eles tenham áreas diferentes.</span><span class="sxs-lookup"><span data-stu-id="9ec63-575">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="9ec63-576">O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area` a `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-576">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="9ec63-577">Esta seção discute como o roteamento interage com áreas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-577">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="9ec63-578">Consulte [áreas](xref:mvc/controllers/areas) para obter detalhes sobre como as áreas são usadas com exibições.</span><span class="sxs-lookup"><span data-stu-id="9ec63-578">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="9ec63-579">O exemplo a seguir configura o MVC para usar a rota convencional padrão e uma rota de `area` para um `area` chamado `Blog`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-579">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="9ec63-580">No código anterior, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> é chamado para criar o `"blog_route"`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-580">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="9ec63-581">O segundo parâmetro, `"Blog"`, é o nome da área.</span><span class="sxs-lookup"><span data-stu-id="9ec63-581">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="9ec63-582">Ao fazer a correspondência de um caminho de URL como `/Manage/Users/AddUser`, a rota de `"blog_route"` gera os valores de rota `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-582">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="9ec63-583">O valor de rota `area` é produzido por um valor padrão para `area`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-583">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="9ec63-584">A rota criada por `MapAreaControllerRoute` é equivalente à seguinte:</span><span class="sxs-lookup"><span data-stu-id="9ec63-584">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="9ec63-585">`MapAreaControllerRoute` cria uma rota usando um valor padrão e a restrição para `area` usando o nome da área fornecido, nesse caso, `Blog`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-585">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="9ec63-586">O valor padrão garante que a rota sempre produza `{ area = Blog, ... }`, a restrição requer o valor `{ area = Blog, ... }` para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-586">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="9ec63-587">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="9ec63-587">Conventional routing is order-dependent.</span></span> <span data-ttu-id="9ec63-588">Em geral, as rotas com áreas devem ser posicionadas antes, pois são mais específicas do que as rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="9ec63-588">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="9ec63-589">Usando o exemplo anterior, os valores de rota `{ area = Blog, controller = Users, action = AddUser }` correspondem à seguinte ação:</span><span class="sxs-lookup"><span data-stu-id="9ec63-589">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="9ec63-590">O atributo [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) é o que denota um controlador como parte de uma área.</span><span class="sxs-lookup"><span data-stu-id="9ec63-590">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="9ec63-591">Esse controlador está na área de `Blog`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-591">This controller is in the `Blog` area.</span></span> <span data-ttu-id="9ec63-592">Os controladores sem um atributo `[Area]` não são membros de nenhuma área e **não** correspondem quando o valor de rota `area` é fornecido pelo roteamento.</span><span class="sxs-lookup"><span data-stu-id="9ec63-592">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="9ec63-593">No exemplo a seguir, somente o primeiro controlador listado pode corresponder aos valores de rota `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-593">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="9ec63-594">O namespace de cada controlador é mostrado aqui para fins de integridade.</span><span class="sxs-lookup"><span data-stu-id="9ec63-594">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="9ec63-595">Se os controladores anteriores usarem o mesmo namespace, um erro do compilador será gerado.</span><span class="sxs-lookup"><span data-stu-id="9ec63-595">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="9ec63-596">Namespaces de classe não têm efeito sobre o roteamento do MVC.</span><span class="sxs-lookup"><span data-stu-id="9ec63-596">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="9ec63-597">Os primeiros dois controladores são membros de áreas e correspondem somente quando seus respectivos nomes de área são fornecidos pelo valor de rota `area`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-597">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="9ec63-598">O terceiro controlador não é um membro de nenhuma área e só pode corresponder quando nenhum valor para `area` for fornecido pelo roteamento.</span><span class="sxs-lookup"><span data-stu-id="9ec63-598">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="9ec63-599">Em termos de não corresponder a *nenhum valor*, a ausência do valor de `area` é equivalente ao valor de `area` ser nulo ou uma cadeia de caracteres vazia.</span><span class="sxs-lookup"><span data-stu-id="9ec63-599">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="9ec63-600">Ao executar uma ação dentro de uma área, o valor de rota para `area` está disponível como um [valor de ambiente](#ambient) para o roteamento a ser usado para a geração de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-600">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="9ec63-601">Isso significa que, por padrão, as áreas atuam como se fossem *autoadesivas* para a geração de URL, como demonstrado no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="9ec63-601">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="9ec63-602">O código a seguir gera uma URL para `/Zebra/Users/AddUser`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-602">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="9ec63-603">Definição de ação</span><span class="sxs-lookup"><span data-stu-id="9ec63-603">Action definition</span></span>

<span data-ttu-id="9ec63-604">Os métodos públicos em um controlador, exceto aqueles com o atributo [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , são ações.</span><span class="sxs-lookup"><span data-stu-id="9ec63-604">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="9ec63-605">Código de exemplo</span><span class="sxs-lookup"><span data-stu-id="9ec63-605">Sample code</span></span>

 * <span data-ttu-id="9ec63-606">O método [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) está incluído no [download de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) e é usado para exibir informações de roteamento.</span><span class="sxs-lookup"><span data-stu-id="9ec63-606">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="9ec63-607">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9ec63-607">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9ec63-608">O ASP.NET Core MVC usa o [middleware](xref:fundamentals/middleware/index) de Roteamento para fazer as correspondências das URLs de solicitações de entrada e mapeá-las para ações.</span><span class="sxs-lookup"><span data-stu-id="9ec63-608">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="9ec63-609">As rotas são definidas em atributos ou no código de inicialização.</span><span class="sxs-lookup"><span data-stu-id="9ec63-609">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="9ec63-610">Elas descrevem como deve ser feita a correspondência entre caminhos de URL e ações.</span><span class="sxs-lookup"><span data-stu-id="9ec63-610">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="9ec63-611">As rotas também são usadas para gerar URLs (para links) enviados em resposta.</span><span class="sxs-lookup"><span data-stu-id="9ec63-611">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="9ec63-612">As ações são roteadas convencionalmente ou segundo os atributos.</span><span class="sxs-lookup"><span data-stu-id="9ec63-612">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="9ec63-613">Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-613">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="9ec63-614">Para obter mais informações, consulte [Roteamento misto](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="9ec63-614">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="9ec63-615">Este documento explicará as interações entre o MVC e o roteamento e como aplicativos MVC comuns usam recursos de roteamento.</span><span class="sxs-lookup"><span data-stu-id="9ec63-615">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="9ec63-616">Consulte [Roteamento](xref:fundamentals/routing) para obter detalhes sobre o roteamento avançado.</span><span class="sxs-lookup"><span data-stu-id="9ec63-616">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="9ec63-617">Configurando o middleware de Roteamento</span><span class="sxs-lookup"><span data-stu-id="9ec63-617">Setting up Routing Middleware</span></span>

<span data-ttu-id="9ec63-618">No método *Configurar*, você poderá ver código semelhante a:</span><span class="sxs-lookup"><span data-stu-id="9ec63-618">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="9ec63-619">Dentro da chamada para `UseMvc`, `MapRoute` é usado para criar uma única rota, que chamaremos de rota `default`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-619">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="9ec63-620">A maioria dos aplicativos MVC usa uma rota com um modelo semelhante à rota `default`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-620">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="9ec63-621">O modelo de rota `"{controller=Home}/{action=Index}/{id?}"` pode corresponder a um caminho de URL como `/Products/Details/5` e extrai os valores de rota `{ controller = Products, action = Details, id = 5 }` gerando tokens para o caminho.</span><span class="sxs-lookup"><span data-stu-id="9ec63-621">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="9ec63-622">O MVC tentará localizar um controlador chamado `ProductsController` e executar a ação `Details`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-622">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="9ec63-623">Observe que, neste exemplo, o model binding usaria o valor de `id = 5` para definir o parâmetro `id` como `5` ao invocar essa ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-623">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="9ec63-624">Consulte [Model binding](../models/model-binding.md) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="9ec63-624">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="9ec63-625">Usando a rota `default`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-625">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="9ec63-626">O modelo da rota:</span><span class="sxs-lookup"><span data-stu-id="9ec63-626">The route template:</span></span>

* <span data-ttu-id="9ec63-627">`{controller=Home}` define `Home` como o `controller` padrão</span><span class="sxs-lookup"><span data-stu-id="9ec63-627">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="9ec63-628">`{action=Index}` define `Index` como o `action` padrão</span><span class="sxs-lookup"><span data-stu-id="9ec63-628">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="9ec63-629">`{id?}` define `id` como opcional</span><span class="sxs-lookup"><span data-stu-id="9ec63-629">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="9ec63-630">Parâmetros de rota opcionais e padrão não precisam estar presentes no caminho da URL para que haja uma correspondência.</span><span class="sxs-lookup"><span data-stu-id="9ec63-630">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="9ec63-631">Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-631">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="9ec63-632">`"{controller=Home}/{action=Index}/{id?}"` pode corresponder ao caminho da URL `/` e produzirá os valores de rota `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-632">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="9ec63-633">Os valores de `controller` e `action` usam os valores padrão, `id` não produz um valor, uma vez que não há nenhum segmento correspondente no caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-633">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="9ec63-634">O MVC usaria esses valores de rota para selecionar a ação `HomeController` e `Index`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-634">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="9ec63-635">Usando essa definição de controlador e modelo de rota, a ação `HomeController.Index` seria executada para qualquer um dos caminhos de URL a seguir:</span><span class="sxs-lookup"><span data-stu-id="9ec63-635">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="9ec63-636">O método de conveniência `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-636">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="9ec63-637">Pode ser usado para substituir:</span><span class="sxs-lookup"><span data-stu-id="9ec63-637">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="9ec63-638">`UseMvc` e `UseMvcWithDefaultRoute` adicionam uma instância de `RouterMiddleware` ao pipeline de middleware.</span><span class="sxs-lookup"><span data-stu-id="9ec63-638">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="9ec63-639">O MVC não interage diretamente com o middleware e usa o roteamento para tratar das solicitações.</span><span class="sxs-lookup"><span data-stu-id="9ec63-639">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="9ec63-640">O MVC é conectado às rotas por meio de uma instância de `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-640">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="9ec63-641">O código dentro de `UseMvc` é semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="9ec63-641">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="9ec63-642">`UseMvc` não define diretamente nenhuma rota, ele adiciona um espaço reservado à coleção de rotas para a rota `attribute`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-642">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="9ec63-643">A sobrecarga `UseMvc(Action<IRouteBuilder>)` permite adicionar suas próprias rotas e também dá suporte ao roteamento de atributos.</span><span class="sxs-lookup"><span data-stu-id="9ec63-643">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="9ec63-644">`UseMvc` e todas as suas variações adicionam um espaço reservado para o atributo roteamento de atributo de rota está sempre disponível, independentemente de como você configura `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-644">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="9ec63-645">`UseMvcWithDefaultRoute` define uma rota padrão e dá suporte ao roteamento de atributos.</span><span class="sxs-lookup"><span data-stu-id="9ec63-645">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="9ec63-646">A seção [Roteamento de atributos](#attribute-routing-ref-label) inclui mais detalhes sobre o roteamento de atributos.</span><span class="sxs-lookup"><span data-stu-id="9ec63-646">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="9ec63-647">Roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="9ec63-647">Conventional routing</span></span>

<span data-ttu-id="9ec63-648">A rota `default`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-648">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="9ec63-649">O código anterior é um exemplo de roteamento convencional.</span><span class="sxs-lookup"><span data-stu-id="9ec63-649">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="9ec63-650">Esse estilo é chamado de roteamento convencional porque ele estabelece uma *Convenção* para caminhos de URL:</span><span class="sxs-lookup"><span data-stu-id="9ec63-650">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="9ec63-651">O primeiro segmento de caminho mapeia para o nome do controlador.</span><span class="sxs-lookup"><span data-stu-id="9ec63-651">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="9ec63-652">O segundo mapeia para o nome da ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-652">The second maps to the action name.</span></span>
* <span data-ttu-id="9ec63-653">O terceiro segmento é usado para um `id`opcional.</span><span class="sxs-lookup"><span data-stu-id="9ec63-653">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="9ec63-654">`id` mapeia para uma entidade de modelo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-654">`id` maps to a model entity.</span></span>

<span data-ttu-id="9ec63-655">Usando essa rota `default`, o caminho da URL `/Products/List` é mapeado para a ação `ProductsController.List` e `/Blog/Article/17` é mapeado para `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-655">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="9ec63-656">Esse mapeamento é baseado **somente** nos nomes do controlador e da ação e não é baseado em namespaces, locais de arquivos de origem ou parâmetros de método.</span><span class="sxs-lookup"><span data-stu-id="9ec63-656">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="9ec63-657">Usar o roteamento convencional com a rota padrão permite compilar o aplicativo rapidamente sem precisar criar um novo padrão de URL para cada ação que você definir.</span><span class="sxs-lookup"><span data-stu-id="9ec63-657">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="9ec63-658">Para um aplicativo com ações de estilo CRUD, ter consistência para as URLs em seus controladores pode ajudar a simplificar seu código e a tornar sua interface do usuário mais previsível.</span><span class="sxs-lookup"><span data-stu-id="9ec63-658">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="9ec63-659">O `id` é definido como opcional pelo modelo de rota, o que significa que suas ações podem ser executadas sem a ID fornecida como parte da URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-659">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="9ec63-660">Normalmente, o que acontecerá se `id` for omitido da URL é que ele será definido como `0` pelo model binding e, dessa forma, não será encontrada no banco de dados nenhuma entidade correspondente a `id == 0`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-660">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="9ec63-661">O roteamento de atributos pode lhe proporcionar controle refinado para tornar a ID obrigatória para algumas ações e não para outras.</span><span class="sxs-lookup"><span data-stu-id="9ec63-661">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="9ec63-662">Por convenção, a documentação incluirá parâmetros opcionais, como `id`, quando for provável que eles apareçam no uso correto.</span><span class="sxs-lookup"><span data-stu-id="9ec63-662">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="9ec63-663">Várias rotas</span><span class="sxs-lookup"><span data-stu-id="9ec63-663">Multiple routes</span></span>

<span data-ttu-id="9ec63-664">É possível adicionar várias rotas dentro de `UseMvc` adicionando mais chamadas para `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-664">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="9ec63-665">Fazer isso permite que você defina várias convenções ou que adicione rotas convencionais dedicadas a uma ação específica, como:</span><span class="sxs-lookup"><span data-stu-id="9ec63-665">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="9ec63-666">A rota `blog` aqui é uma *rota convencional dedicada*, o que significa que ela usa o sistema de roteamento convencional, mas é dedicada a uma ação específica.</span><span class="sxs-lookup"><span data-stu-id="9ec63-666">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="9ec63-667">Como `controller` e `action` não aparecem no modelo de rota como parâmetros, eles só podem ter os valores padrão e, portanto, essa rota sempre será mapeada para a ação `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-667">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="9ec63-668">As rotas na coleção de rotas são ordenadas e serão processadas na ordem em que forem adicionadas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-668">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="9ec63-669">Portanto, neste exemplo, a rota `blog` será tentada antes da rota `default`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-669">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="9ec63-670">*Rotas convencionais dedicadas* geralmente usam parâmetros de rota **catch-all** como `{*article}` para capturar a parte restante do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-670">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="9ec63-671">Isso pode fazer com que uma rota fique "muito ambiciosa", ou seja, que faça a correspondência com URLs que deveriam ser correspondidas com outras rotas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-671">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="9ec63-672">Coloque as rotas "ambiciosas" mais adiante na tabela de rotas para solucionar esse problema.</span><span class="sxs-lookup"><span data-stu-id="9ec63-672">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="9ec63-673">Fallback</span><span class="sxs-lookup"><span data-stu-id="9ec63-673">Fallback</span></span>

<span data-ttu-id="9ec63-674">Como parte do processamento de solicitações, o MVC verificará se o valores das rotas podem ser usados para encontrar um controlador e uma ação em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-674">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="9ec63-675">Se os valores das rotas não corresponderem a uma ação, a rota não será considerada correspondente e a próxima rota será tentada.</span><span class="sxs-lookup"><span data-stu-id="9ec63-675">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="9ec63-676">Isso é chamado de *fallback* e sua finalidade é simplificar casos em que rotas convencionais se sobrepõem.</span><span class="sxs-lookup"><span data-stu-id="9ec63-676">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="9ec63-677">Desambiguação de ações</span><span class="sxs-lookup"><span data-stu-id="9ec63-677">Disambiguating actions</span></span>

<span data-ttu-id="9ec63-678">Quando duas ações correspondem por meio do roteamento, o MVC precisa resolver a ambiguidade para escolher a "melhor" candidata ou lançar uma exceção.</span><span class="sxs-lookup"><span data-stu-id="9ec63-678">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="9ec63-679">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="9ec63-679">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="9ec63-680">Esse controlador define duas ações que fariam a correspondência entre caminho da URL `/Products/Edit/17` e a os dados da rota `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-680">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="9ec63-681">Este é um padrão comum para controladores MVC em que `Edit(int)` mostra um formulário para editar um produto e `Edit(int, Product)` processa o formulário postado.</span><span class="sxs-lookup"><span data-stu-id="9ec63-681">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="9ec63-682">Para que isso seja possível, o MVC precisa escolher `Edit(int, Product)` quando a solicitação é um `POST` HTTP e `Edit(int)` quando o verbo HTTP é qualquer outra coisa.</span><span class="sxs-lookup"><span data-stu-id="9ec63-682">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="9ec63-683">O `HttpPostAttribute` (`[HttpPost]`) é uma implementação de `IActionConstraint` que só permite que a ação seja selecionada quando o verbo HTTP é `POST`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-683">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="9ec63-684">A presença de um `IActionConstraint` faz do `Edit(int, Product)` uma "melhor" correspondência do que `Edit(int)`, portanto, `Edit(int, Product)` será tentado primeiro.</span><span class="sxs-lookup"><span data-stu-id="9ec63-684">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="9ec63-685">Você só precisará gravar implementações personalizadas de `IActionConstraint` em cenários especializados, mas é importante compreender a função de atributos como `HttpPostAttribute` – atributos semelhantes são definidos para outros verbos HTTP.</span><span class="sxs-lookup"><span data-stu-id="9ec63-685">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="9ec63-686">No roteamento convencional, é comum que ações usem o mesmo nome de ação quando fazem parte de um fluxo de trabalho de `show form -> submit form`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-686">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="9ec63-687">A conveniência desse padrão ficará mais aparente após você revisar a seção [Noções básicas sobre IActionConstraint](#understanding-iactionconstraint).</span><span class="sxs-lookup"><span data-stu-id="9ec63-687">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="9ec63-688">Se várias rotas corresponderem e o MVC não puder encontrar uma rota "melhor", ele gerará um `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-688">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="9ec63-689">Nomes de rotas</span><span class="sxs-lookup"><span data-stu-id="9ec63-689">Route names</span></span>

<span data-ttu-id="9ec63-690">As cadeias de caracteres `"blog"` e `"default"` nos exemplos a seguir são nomes de rotas:</span><span class="sxs-lookup"><span data-stu-id="9ec63-690">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="9ec63-691">Os nomes de rotas dão a uma rota um nome lógico, de modo que a rota nomeada possa ser usada para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-691">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="9ec63-692">Isso simplifica muito a criação de URLs quando a ordenação de rotas poderia complicá-la.</span><span class="sxs-lookup"><span data-stu-id="9ec63-692">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="9ec63-693">Nomes de rotas devem ser exclusivos no nível do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-693">Route names must be unique application-wide.</span></span>

<span data-ttu-id="9ec63-694">Os nomes de rotas não têm impacto sobre a correspondência de URLs ou o tratamento de solicitações; eles são usados apenas para a geração de URLs.</span><span class="sxs-lookup"><span data-stu-id="9ec63-694">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="9ec63-695">[Roteamento](xref:fundamentals/routing) tem informações mais detalhadas sobre geração de URLs, incluindo a geração de URLs em auxiliares específicos do MVC.</span><span class="sxs-lookup"><span data-stu-id="9ec63-695">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="9ec63-696">Roteamento de atributo</span><span class="sxs-lookup"><span data-stu-id="9ec63-696">Attribute routing</span></span>

<span data-ttu-id="9ec63-697">O roteamento de atributo usa um conjunto de atributos para mapear ações diretamente para modelos de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-697">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="9ec63-698">No exemplo a seguir, `app.UseMvc();` é usado no método `Configure` e nenhuma rota é passada.</span><span class="sxs-lookup"><span data-stu-id="9ec63-698">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="9ec63-699">O `HomeController` corresponderá a um conjunto de URLs semelhantes ao que a rota padrão `{controller=Home}/{action=Index}/{id?}` corresponderia:</span><span class="sxs-lookup"><span data-stu-id="9ec63-699">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="9ec63-700">A ação `HomeController.Index()` será executada para qualquer um dos caminhos de URL `/`, `/Home` ou `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-700">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="9ec63-701">Este exemplo destaca uma diferença importante de programação entre o roteamento de atributo e o roteamento convencional.</span><span class="sxs-lookup"><span data-stu-id="9ec63-701">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="9ec63-702">O roteamento de atributo requer mais entradas para especificar uma rota; a rota padrão convencional manipula as rotas de forma mais sucinta.</span><span class="sxs-lookup"><span data-stu-id="9ec63-702">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="9ec63-703">No entanto, o roteamento de atributo permite (e exige) o controle preciso de quais modelos de rota se aplicam a cada ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-703">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="9ec63-704">Com o roteamento de atributo, o nome do controlador e os nomes de ação não desempenham **nenhuma** função quanto a qual ação é selecionada.</span><span class="sxs-lookup"><span data-stu-id="9ec63-704">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="9ec63-705">Este exemplo corresponderá as mesmas URLs que o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="9ec63-705">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="9ec63-706">Os modelos de rota acima não definem parâmetros de rota para `action`, `area` e `controller`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-706">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="9ec63-707">Na verdade, esses parâmetros de rota não são permitidos em rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-707">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="9ec63-708">Uma vez que o modelo de rota já está associado a uma ação, não faria sentido analisar o nome da ação da URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-708">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="9ec63-709">Roteamento de atributo com atributos Http[Verb]</span><span class="sxs-lookup"><span data-stu-id="9ec63-709">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="9ec63-710">O roteamento de atributo também pode usar atributos `Http[Verb]`, como `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-710">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="9ec63-711">Todos esses atributos podem aceitar um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-711">All of these attributes can accept a route template.</span></span> <span data-ttu-id="9ec63-712">Este exemplo mostra duas ações que correspondem ao mesmo modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="9ec63-712">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="9ec63-713">Para um caminho de URL como `/products`, a ação `ProductsApi.ListProducts` será executada quando o verbo HTTP for `GET` e `ProductsApi.CreateProduct` será executado quando o verbo HTTP for `POST`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-713">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="9ec63-714">Primeiro, o roteamento de atributo faz a correspondência da URL com o conjunto de modelos de rota definidos por atributos de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-714">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="9ec63-715">Quando um modelo de rota for correspondente, restrições de `IActionConstraint` serão aplicadas para determinar quais ações podem ser executadas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-715">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="9ec63-716">Ao criar uma API REST, é raro que você queira usar `[Route(...)]` em um método de ação, pois a ação aceitará todos os métodos HTTP.</span><span class="sxs-lookup"><span data-stu-id="9ec63-716">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="9ec63-717">É melhor usar o `Http*Verb*Attributes` mais específico para ser preciso quanto ao que tem suporte de sua API.</span><span class="sxs-lookup"><span data-stu-id="9ec63-717">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="9ec63-718">Espera-se que clientes de APIs REST saibam quais caminhos e verbos HTTP são mapeados para operações lógicas específicas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-718">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="9ec63-719">Como uma rota de atributo se aplica a uma ação específica, é fácil fazer com que parâmetros sejam obrigatórios como parte da definição do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-719">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="9ec63-720">Neste exemplo, `id` é obrigatório como parte do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-720">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="9ec63-721">A ação `ProductsApi.GetProduct(int)` será executada para um caminho de URL como `/products/3`, mas não para um caminho de URL como `/products`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-721">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="9ec63-722">Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição completa de modelos de rota e as opções relacionadas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-722">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="9ec63-723">Nome da rota</span><span class="sxs-lookup"><span data-stu-id="9ec63-723">Route Name</span></span>

<span data-ttu-id="9ec63-724">O código a seguir define um *nome da rota* como `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-724">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="9ec63-725">Nomes de rota podem ser usados para gerar uma URL com base em uma rota específica.</span><span class="sxs-lookup"><span data-stu-id="9ec63-725">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="9ec63-726">Nomes de rota não têm impacto sobre o comportamento de correspondência da URL e são usados somente para geração de URLs.</span><span class="sxs-lookup"><span data-stu-id="9ec63-726">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="9ec63-727">Nomes de rotas devem ser exclusivos no nível do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-727">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="9ec63-728">Compare isso com a *rota padrão* convencional, que define o parâmetro `id` como opcional (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="9ec63-728">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="9ec63-729">Essa capacidade de especificar APIs de forma específica tem vantagens, como permitir que `/products` e `/products/5` sejam expedidos para ações diferentes.</span><span class="sxs-lookup"><span data-stu-id="9ec63-729">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="9ec63-730">Combinando rotas</span><span class="sxs-lookup"><span data-stu-id="9ec63-730">Combining routes</span></span>

<span data-ttu-id="9ec63-731">Para tornar o roteamento de atributo menos repetitivo, os atributos de rota no controlador são combinados com atributos de rota nas ações individuais.</span><span class="sxs-lookup"><span data-stu-id="9ec63-731">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="9ec63-732">Modelos de rota definidos no controlador precedem modelos de rota nas ações.</span><span class="sxs-lookup"><span data-stu-id="9ec63-732">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="9ec63-733">Colocar um atributo de rota no controlador faz com que  **todas** as ações no controlador usem o roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-733">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="9ec63-734">Neste exemplo, o caminho de URL `/products` pode corresponder a `ProductsApi.ListProducts` e o caminho de URL `/products/5` pode corresponder a `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-734">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="9ec63-735">Ambas as ações correspondem somente a `GET` HTTP porque elas são marcadas com o `HttpGetAttribute`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-735">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="9ec63-736">Modelos de rota aplicados a uma ação, que começam com `/` ou `~/`, não são combinados com modelos de rota aplicados ao controlador.</span><span class="sxs-lookup"><span data-stu-id="9ec63-736">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="9ec63-737">Este exemplo corresponde a um conjunto de caminhos de URL semelhante à *rota padrão*.</span><span class="sxs-lookup"><span data-stu-id="9ec63-737">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="9ec63-738">Ordenando rotas de atributos</span><span class="sxs-lookup"><span data-stu-id="9ec63-738">Ordering attribute routes</span></span>

<span data-ttu-id="9ec63-739">Ao contrário das rotas convencionais, que são executadas em uma ordem definida, o roteamento de atributo cria uma árvore e corresponde a todas as rotas simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="9ec63-739">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="9ec63-740">O comportamento é como se as entradas de rota fossem colocadas em uma ordem ideal; as rotas mais específicas têm uma chance de ser executadas antes das rotas mais gerais.</span><span class="sxs-lookup"><span data-stu-id="9ec63-740">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="9ec63-741">Por exemplo, uma rota como `blog/search/{topic}` é mais específica que uma rota como `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-741">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="9ec63-742">Em termos da lógica, a rota `blog/search/{topic}` é "executada" primeiro, por padrão, porque essa é a única ordem que faz sentido.</span><span class="sxs-lookup"><span data-stu-id="9ec63-742">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="9ec63-743">Usando o roteamento convencional, o desenvolvedor é responsável por colocar as rotas na ordem desejada.</span><span class="sxs-lookup"><span data-stu-id="9ec63-743">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="9ec63-744">Rotas de atributos podem configurar uma ordem, usando a propriedade `Order` de todos os atributos de rota fornecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="9ec63-744">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="9ec63-745">As rotas são processadas segundo uma classificação crescente da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-745">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="9ec63-746">A ordem padrão é `0`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-746">The default order is `0`.</span></span> <span data-ttu-id="9ec63-747">Uma rota definida usando `Order = -1` será executada antes de rotas que não definem uma ordem.</span><span class="sxs-lookup"><span data-stu-id="9ec63-747">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="9ec63-748">Uma rota definida usando `Order = 1` será executada após a ordem das rotas padrão.</span><span class="sxs-lookup"><span data-stu-id="9ec63-748">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="9ec63-749">Evite depender de `Order`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-749">Avoid depending on `Order`.</span></span> <span data-ttu-id="9ec63-750">Se o seu espaço de URL exigir valores de ordem explícita para fazer o roteamento corretamente, provavelmente ele também será confuso para os clientes.</span><span class="sxs-lookup"><span data-stu-id="9ec63-750">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="9ec63-751">De modo geral, o roteamento de atributos selecionará a rota correta com a correspondência de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-751">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="9ec63-752">Se a ordem padrão usada para a geração de URL não estiver funcionando, usar o nome da rota como uma substituição geralmente será mais simples do que aplicar a propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-752">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="9ec63-753">Roteamento do Razor Pages e do controlador do MVC compartilham uma implementação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-753">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="9ec63-754">Informações sobre a ordem de rota nos tópicos do Razor Pages estão disponíveis em [Convenções de rota e aplicativo do Razor Pages: ordem de rota](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="9ec63-754">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="9ec63-755">Substituição de token em modelos de rota ([controlador] [ação] [área])</span><span class="sxs-lookup"><span data-stu-id="9ec63-755">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="9ec63-756">Para conveniência, as rotas de atributo dão suporte à *substituição de token* colocando um token entre chaves quadradas (`[`, `]`).</span><span class="sxs-lookup"><span data-stu-id="9ec63-756">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="9ec63-757">Os tokens `[action]`, `[area]` e `[controller]` são substituídos pelos valores do nome da ação, do nome da área e do nome do controlador da ação em que a rota é definida.</span><span class="sxs-lookup"><span data-stu-id="9ec63-757">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="9ec63-758">No exemplo a seguir, as ações correspondem a caminhos de URL conforme descrito nos comentários:</span><span class="sxs-lookup"><span data-stu-id="9ec63-758">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="9ec63-759">A substituição de token ocorre como a última etapa da criação das rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-759">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="9ec63-760">O exemplo acima se comportará da mesma forma que o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="9ec63-760">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="9ec63-761">Rotas de atributo também podem ser combinadas com herança.</span><span class="sxs-lookup"><span data-stu-id="9ec63-761">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="9ec63-762">Isso é especialmente eficiente em combinação com a substituição de token.</span><span class="sxs-lookup"><span data-stu-id="9ec63-762">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="9ec63-763">A substituição de token também se aplica a nomes de rota definidos por rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-763">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="9ec63-764">`[Route("[controller]/[action]", Name="[controller]_[action]")]` gera um nome de rota exclusivo para cada ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-764">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="9ec63-765">Para corresponder ao delimitador de substituição de token literal `[` ou `]`, faça seu escape repetindo o caractere (`[[` ou `]]`).</span><span class="sxs-lookup"><span data-stu-id="9ec63-765">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="9ec63-766">Usar um transformador de parâmetro para personalizar a substituição de token</span><span class="sxs-lookup"><span data-stu-id="9ec63-766">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="9ec63-767">A substituição do token pode ser personalizada usando um transformador de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="9ec63-767">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="9ec63-768">Um transformador de parâmetro implementa `IOutboundParameterTransformer` e transforma o valor dos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="9ec63-768">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="9ec63-769">Por exemplo, um transformador de parâmetro `SlugifyParameterTransformer` personalizado muda o valor de rota `SubscriptionManagement` para `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-769">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="9ec63-770">O `RouteTokenTransformerConvention` é uma convenção de modelo de aplicativo que:</span><span class="sxs-lookup"><span data-stu-id="9ec63-770">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="9ec63-771">Aplica um transformador de parâmetro a todas as rotas de atributo em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-771">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="9ec63-772">Personaliza os valores de token de rota de atributo conforme eles são substituídos.</span><span class="sxs-lookup"><span data-stu-id="9ec63-772">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="9ec63-773">O `RouteTokenTransformerConvention` está registrado como uma opção em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-773">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
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


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="9ec63-774">Várias rotas</span><span class="sxs-lookup"><span data-stu-id="9ec63-774">Multiple Routes</span></span>

<span data-ttu-id="9ec63-775">O roteamento de atributo dá suporte à definição de várias rotas que atingem a mesma ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-775">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="9ec63-776">O uso mais comum desse recurso é para simular o comportamento da *rota convencional padrão*, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="9ec63-776">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="9ec63-777">Colocar vários atributos de rota no controlador significa que cada um deles será combinado com cada um dos atributos de rota nos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-777">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="9ec63-778">Quando vários atributos de rota (que implementam `IActionConstraint`) são colocados em uma ação, cada restrição da ação combina com o modelo de rota do atributo que a definiu.</span><span class="sxs-lookup"><span data-stu-id="9ec63-778">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="9ec63-779">Embora o uso de várias rotas em ações possa parecer eficaz, é melhor manter o espaço de URL de seu aplicativo simples e bem definido.</span><span class="sxs-lookup"><span data-stu-id="9ec63-779">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="9ec63-780">Use várias rotas em ações somente quando for necessário; por exemplo, para dar suporte a clientes existentes.</span><span class="sxs-lookup"><span data-stu-id="9ec63-780">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="9ec63-781">Especificando parâmetros opcionais, valores padrão e restrições da rota de atributo</span><span class="sxs-lookup"><span data-stu-id="9ec63-781">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="9ec63-782">Rotas de atributo dão suporte à mesma sintaxe embutida que as rotas convencionais para especificar parâmetros opcionais, valores padrão e restrições.</span><span class="sxs-lookup"><span data-stu-id="9ec63-782">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="9ec63-783">Consulte [Referência de modelo de rota](xref:fundamentals/routing#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-783">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="9ec63-784">Atributos de rota personalizados usando `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="9ec63-784">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="9ec63-785">Todos os atributos de rota fornecidos na estrutura ( `[Route(...)]`, `[HttpGet(...)]` etc.) implementam a interface `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-785">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="9ec63-786">O MVC procura atributos em classes de controlador e métodos de ação quando o aplicativo é iniciado e usa aqueles que implementam `IRouteTemplateProvider` para criar o conjunto inicial de rotas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-786">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="9ec63-787">Você pode implementar `IRouteTemplateProvider` para definir seus próprios atributos de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-787">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="9ec63-788">Cada `IRouteTemplateProvider` permite definir uma única rota com um nome, uma ordem e um modelo de rota personalizado:</span><span class="sxs-lookup"><span data-stu-id="9ec63-788">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="9ec63-789">O atributo do exemplo acima configura automaticamente o `Template` como `"api/[controller]"` quando `[MyApiController]` é aplicado.</span><span class="sxs-lookup"><span data-stu-id="9ec63-789">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="9ec63-790">Usando o Modelo de Aplicativo para personalizar rotas de atributo</span><span class="sxs-lookup"><span data-stu-id="9ec63-790">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="9ec63-791">O *modelo de aplicativo* é um modelo de objeto criado durante a inicialização com todos os metadados usados pelo MVC para rotear e executar suas ações.</span><span class="sxs-lookup"><span data-stu-id="9ec63-791">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="9ec63-792">O *modelo de aplicativo* inclui todos os dados reunidos dos atributos de rota (por meio de `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="9ec63-792">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="9ec63-793">Você pode escrever *convenções* para modificar o modelo do aplicativo no momento da inicialização para personalizar o comportamento do roteamento.</span><span class="sxs-lookup"><span data-stu-id="9ec63-793">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="9ec63-794">Esta seção mostra um exemplo simples de personalização de roteamento usando o modelo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-794">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="9ec63-795">Roteamento misto: roteamento de atributo versus roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="9ec63-795">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="9ec63-796">Aplicativos MVC podem combinar o uso do roteamento convencional e do roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-796">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="9ec63-797">É comum usar rotas convencionais para controladores que servem páginas HTML para navegadores e usar o roteamento de atributo para controladores que servem APIs REST.</span><span class="sxs-lookup"><span data-stu-id="9ec63-797">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="9ec63-798">As ações são roteadas convencionalmente ou segundo os atributos.</span><span class="sxs-lookup"><span data-stu-id="9ec63-798">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="9ec63-799">Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-799">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="9ec63-800">Ações que definem rotas de atributo não podem ser acessadas por meio das rotas convencionais e vice-versa.</span><span class="sxs-lookup"><span data-stu-id="9ec63-800">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="9ec63-801">**Qualquer** atributo de rota no controlador faz com que todas as ações no atributo de controlador sejam roteadas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-801">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="9ec63-802">O que diferencia os dois tipos de sistemas de roteamento é o processo aplicado após uma URL corresponder a um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-802">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="9ec63-803">No roteamento convencional, os valores de rota da correspondência são usados para escolher a ação e o controlador em uma tabela de pesquisa com todas as ações roteadas convencionais.</span><span class="sxs-lookup"><span data-stu-id="9ec63-803">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="9ec63-804">No roteamento de atributo, cada modelo já está associado a uma ação e nenhuma pesquisa adicional é necessária.</span><span class="sxs-lookup"><span data-stu-id="9ec63-804">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="9ec63-805">Segmentos complexos</span><span class="sxs-lookup"><span data-stu-id="9ec63-805">Complex segments</span></span>

<span data-ttu-id="9ec63-806">Segmentos complexos (por exemplo, `[Route("/dog{token}cat")]`), são processados combinando literais da direita para a esquerda de uma maneira não Greedy.</span><span class="sxs-lookup"><span data-stu-id="9ec63-806">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="9ec63-807">Veja [o código-fonte](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) para obter uma descrição.</span><span class="sxs-lookup"><span data-stu-id="9ec63-807">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="9ec63-808">Para obter mais informações, confira [esta edição](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="9ec63-808">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="9ec63-809">Geração de URL</span><span class="sxs-lookup"><span data-stu-id="9ec63-809">URL Generation</span></span>

<span data-ttu-id="9ec63-810">Aplicativos MVC podem usar os recursos de geração de URL do roteamento para gerar links de URL para ações.</span><span class="sxs-lookup"><span data-stu-id="9ec63-810">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="9ec63-811">Gerar URLs elimina a necessidade de codificar URLs, tornando seu código mais robusto e sustentável.</span><span class="sxs-lookup"><span data-stu-id="9ec63-811">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="9ec63-812">Esta seção tem como foco os recursos de geração de URL fornecidos pelo MVC e só aborda as noções básicas de como a geração de URL funciona.</span><span class="sxs-lookup"><span data-stu-id="9ec63-812">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="9ec63-813">Consulte [Roteamento](xref:fundamentals/routing) para obter uma descrição detalhada da geração de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-813">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="9ec63-814">A interface `IUrlHelper` é a parte subjacente da infraestrutura entre o MVC e o roteamento para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-814">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="9ec63-815">Você encontrará uma instância de `IUrlHelper` disponível por meio da propriedade `Url` em controladores, exibições e componentes de exibição.</span><span class="sxs-lookup"><span data-stu-id="9ec63-815">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="9ec63-816">Neste exemplo, a interface `IUrlHelper` é usada por meio a propriedade `Controller.Url` para gerar uma URL para outra ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-816">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="9ec63-817">Se o aplicativo estiver usando a rota convencional padrão, o valor da variável `url` será a cadeia de caracteres do caminho de URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-817">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="9ec63-818">Esse caminho de URL é criado pelo roteamento combinando os valores de rota da solicitação atual (valores de ambiente) com os valores passados para `Url.Action` e substituindo esses valores no modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="9ec63-818">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="9ec63-819">Cada parâmetro de rota no modelo de rota tem seu valor substituído por nomes correspondentes com os valores e os valores de ambiente.</span><span class="sxs-lookup"><span data-stu-id="9ec63-819">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="9ec63-820">Um parâmetro de rota que não tem um valor pode usar um valor padrão se houver um ou pode ser ignorado se for opcional (como no caso de `id` neste exemplo).</span><span class="sxs-lookup"><span data-stu-id="9ec63-820">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="9ec63-821">A geração de URL falhará se qualquer parâmetro de rota obrigatório não tiver um valor correspondente.</span><span class="sxs-lookup"><span data-stu-id="9ec63-821">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="9ec63-822">Se a geração de URL falhar para uma rota, a rota seguinte será tentada até que todas as rotas tenham sido tentadas ou que uma correspondência seja encontrada.</span><span class="sxs-lookup"><span data-stu-id="9ec63-822">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="9ec63-823">O exemplo de `Url.Action` acima pressupõe que o roteamento seja convencional, mas a geração de URL funciona de forma semelhante com o roteamento de atributo, embora os conceitos sejam diferentes.</span><span class="sxs-lookup"><span data-stu-id="9ec63-823">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="9ec63-824">Com o roteamento convencional, os valores de rota são usados para expandir um modelo e os valores de rota para `controller` e `action` normalmente são exibidos no modelo – isso funciona porque as URLs correspondidas pelo roteamento aderem a uma *convenção*.</span><span class="sxs-lookup"><span data-stu-id="9ec63-824">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="9ec63-825">No roteamento de atributo, os valores de rota para `controller` e `action` não podem ser exibidos no modelo; em vez disso, eles são usados para pesquisar o modelo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="9ec63-825">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="9ec63-826">Este exemplo usa o roteamento de atributo:</span><span class="sxs-lookup"><span data-stu-id="9ec63-826">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="9ec63-827">O MVC cria uma tabela de pesquisa de todas as ações de atributo roteadas e faz a correspondência dos valores de `controller` e `action` para selecionar o modelo de rota a ser usado para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-827">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="9ec63-828">Na amostra acima, `custom/url/to/destination` é gerado.</span><span class="sxs-lookup"><span data-stu-id="9ec63-828">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="9ec63-829">Gerando URLs pelo nome da ação</span><span class="sxs-lookup"><span data-stu-id="9ec63-829">Generating URLs by action name</span></span>

<span data-ttu-id="9ec63-830">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="9ec63-830">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="9ec63-831">`Action`) e todas as sobrecargas relacionadas são baseadas na ideia de que você deseja especificar ao que está vinculando, especificando um nome do controlador e um nome da ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-831">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="9ec63-832">Ao usar `Url.Action`, os valores de rota atuais para `controller` e `action` são especificados para você-o valor de `controller` e `action` são parte dos *valores de ambiente* **e** *valores*.</span><span class="sxs-lookup"><span data-stu-id="9ec63-832">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="9ec63-833">O método `Url.Action` sempre usa os valores atuais de `action` e `controller` e gera um caminho de URL que roteia para a ação atual.</span><span class="sxs-lookup"><span data-stu-id="9ec63-833">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="9ec63-834">O roteamento tenta usar os valores em valores de ambiente para preencher informações que você não forneceu ao gerar uma URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-834">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="9ec63-835">Usando uma rota como `{a}/{b}/{c}/{d}` e valores de ambiente `{ a = Alice, b = Bob, c = Carol, d = David }`, o roteamento tem informações suficientes para gerar uma URL sem valores adicionais – uma vez que todos os parâmetros de rota têm um valor.</span><span class="sxs-lookup"><span data-stu-id="9ec63-835">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="9ec63-836">Se você tiver adicionado o valor `{ d = Donovan }`, o valor `{ d = David }` será ignorado e o caminho de URL gerado será `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-836">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="9ec63-837">Caminhos de URL são hierárquicos.</span><span class="sxs-lookup"><span data-stu-id="9ec63-837">URL paths are hierarchical.</span></span> <span data-ttu-id="9ec63-838">No exemplo acima, se você tiver adicionado o valor `{ c = Cheryl }`, ambos os valores `{ c = Carol, d = David }` serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="9ec63-838">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="9ec63-839">Nesse caso, não teremos mais um valor para `d` e a geração de URL falhará.</span><span class="sxs-lookup"><span data-stu-id="9ec63-839">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="9ec63-840">Você precisaria especificar o valor desejado de `c` e `d`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-840">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="9ec63-841">Você pode esperar se deparar com esse problema com a rota padrão (`{controller}/{action}/{id?}`) – mas raramente encontrará esse comportamento na prática, pois `Url.Action` sempre especificará explicitamente um valor de `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-841">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="9ec63-842">Sobrecargas maiores de `Url.Action` também usam um objeto adicional de *valores de rota* para fornecer valores para parâmetros de rota diferentes de `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-842">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="9ec63-843">É mais comum ver isso com `id` como `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-843">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="9ec63-844">Por convenção, o objeto de *valores de rota* geralmente é um objeto de tipo anônimo, mas também pode ser um `IDictionary<>` ou um *objeto .NET simples*.</span><span class="sxs-lookup"><span data-stu-id="9ec63-844">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="9ec63-845">Qualquer valor de rota adicional que não corresponder aos parâmetros de rota será colocado na cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="9ec63-845">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="9ec63-846">Para criar uma URL absoluta, use uma sobrecarga que aceita um `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="9ec63-846">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="9ec63-847">Gerando URLs pela rota</span><span class="sxs-lookup"><span data-stu-id="9ec63-847">Generating URLs by route</span></span>

<span data-ttu-id="9ec63-848">O código acima demonstrou a geração de uma URL passando o nome do controlador e da ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-848">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="9ec63-849">`IUrlHelper` também fornece a família de métodos `Url.RouteUrl`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-849">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="9ec63-850">Esses métodos são semelhantes a `Url.Action`, mas não copiam os valores atuais de `action` e `controller` para os valores de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-850">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="9ec63-851">O uso mais comum é especificar um nome de rota para usar uma rota específica para gerar a URL, geralmente *sem* especificar um nome de controlador ou de ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-851">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="9ec63-852">Gerar URLs em HTML</span><span class="sxs-lookup"><span data-stu-id="9ec63-852">Generating URLs in HTML</span></span>

<span data-ttu-id="9ec63-853">`IHtmlHelper` fornece o métodos `HtmlHelper` e `Html.BeginForm` de `Html.ActionLink` para gerar elementos `<form>` e `<a>` respectivamente.</span><span class="sxs-lookup"><span data-stu-id="9ec63-853">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="9ec63-854">Esses métodos usam o método `Url.Action` para gerar uma URL e aceitam argumentos semelhantes.</span><span class="sxs-lookup"><span data-stu-id="9ec63-854">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="9ec63-855">O complementos `Url.RouteUrl` para `HtmlHelper` são `Html.BeginRouteForm` e `Html.RouteLink`, que têm uma funcionalidade semelhante.</span><span class="sxs-lookup"><span data-stu-id="9ec63-855">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="9ec63-856">TagHelpers geram URLs por meio do TagHelper `form` e do TagHelper `<a>`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-856">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="9ec63-857">Ambos usam `IUrlHelper` para sua implementação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-857">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="9ec63-858">Consulte [Trabalhando com Formulários](../views/working-with-forms.md) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="9ec63-858">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="9ec63-859">Nos modos de exibição, o `IUrlHelper` está disponível por meio da propriedade `Url` para qualquer geração de URL ad hoc não abordada acima.</span><span class="sxs-lookup"><span data-stu-id="9ec63-859">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="9ec63-860">Gerando URLS nos resultados da ação</span><span class="sxs-lookup"><span data-stu-id="9ec63-860">Generating URLS in Action Results</span></span>

<span data-ttu-id="9ec63-861">Os exemplos acima mostraram o uso de `IUrlHelper` em um controlador, enquanto o uso mais comum em um controlador é gerar uma URL como parte do resultado de uma ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-861">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="9ec63-862">As classes base `ControllerBase` e `Controller` fornecem métodos de conveniência para resultados de ação que fazem referência a outra ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-862">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="9ec63-863">Um uso típico é para redirecionar após aceitar a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="9ec63-863">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="9ec63-864">Os métodos de fábrica dos resultados da ação seguem um padrão semelhante aos métodos em `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-864">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="9ec63-865">Caso especial para rotas convencionais dedicadas</span><span class="sxs-lookup"><span data-stu-id="9ec63-865">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="9ec63-866">O roteamento convencional pode usar um tipo especial de definição de rota chamado *rota convencional dedicada*.</span><span class="sxs-lookup"><span data-stu-id="9ec63-866">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="9ec63-867">No exemplo a seguir, a rota chamada `blog` é uma rota convencional dedicada.</span><span class="sxs-lookup"><span data-stu-id="9ec63-867">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="9ec63-868">Usando essas definições de rota, `Url.Action("Index", "Home")` gerará o caminho de URL `/` com a rota `default`, mas por quê?</span><span class="sxs-lookup"><span data-stu-id="9ec63-868">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="9ec63-869">Você poderia imaginar que os valores de rota `{ controller = Home, action = Index }` seriam suficientes para gerar uma URL usando `blog` e o resultado seria `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-869">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="9ec63-870">Rotas convencionais dedicadas dependem de um comportamento especial de valores padrão que não têm um parâmetro de rota correspondente que impeça que a rota seja "muito ambiciosa" com a geração de URLs.</span><span class="sxs-lookup"><span data-stu-id="9ec63-870">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="9ec63-871">Nesse caso, os valores padrão são `{ controller = Blog, action = Article }` e nem `controller` ou `action` aparece como um parâmetro de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-871">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="9ec63-872">Quando o roteamento executa a geração de URL, os valores fornecidos devem corresponder aos valores padrão.</span><span class="sxs-lookup"><span data-stu-id="9ec63-872">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="9ec63-873">A geração de URL usando `blog` falhará porque os valores de `{ controller = Home, action = Index }` não correspondem a `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-873">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="9ec63-874">O roteamento, então, faz o fallback para tentar `default`, que é bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="9ec63-874">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="9ec63-875">Áreas</span><span class="sxs-lookup"><span data-stu-id="9ec63-875">Areas</span></span>

<span data-ttu-id="9ec63-876">[Áreas](areas.md) são um recurso do MVC usado para organizar funcionalidades relacionadas em um grupo como um namespace de roteamento (para ações do controlador) e estrutura de pasta (para exibições) separada.</span><span class="sxs-lookup"><span data-stu-id="9ec63-876">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="9ec63-877">O uso de áreas permite que um aplicativo tenha vários controladores com o mesmo nome, desde que tenham *áreas* diferentes.</span><span class="sxs-lookup"><span data-stu-id="9ec63-877">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="9ec63-878">O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area` a `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-878">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="9ec63-879">Esta seção aborda como o roteamento interage com as áreas. Consulte [Áreas](areas.md) para obter detalhes sobre como as áreas são usadas com exibições.</span><span class="sxs-lookup"><span data-stu-id="9ec63-879">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="9ec63-880">O exemplo a seguir configura o MVC para usar a rota convencional padrão e uma *rota de área* para uma área chamada `Blog`:</span><span class="sxs-lookup"><span data-stu-id="9ec63-880">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="9ec63-881">Ao fazer a correspondência de um caminho de URL como `/Manage/Users/AddUser`, a primeira rota produzirá os valores de rota `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-881">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="9ec63-882">O valor de rota `area` é produzido por um valor padrão para `area`. De fato, a rota criada por `MapAreaRoute` é equivalente à seguinte:</span><span class="sxs-lookup"><span data-stu-id="9ec63-882">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="9ec63-883">`MapAreaRoute` cria uma rota usando um valor padrão e a restrição para `area` usando o nome da área fornecido, nesse caso, `Blog`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-883">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="9ec63-884">O valor padrão garante que a rota sempre produza `{ area = Blog, ... }`, a restrição requer o valor `{ area = Blog, ... }` para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-884">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="9ec63-885">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="9ec63-885">Conventional routing is order-dependent.</span></span> <span data-ttu-id="9ec63-886">De modo geral, rotas com áreas devem ser colocadas mais no início na tabela de rotas, uma vez que são mais específicas que rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="9ec63-886">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="9ec63-887">Usando o exemplo acima, os valores de rota corresponderiam à ação a seguir:</span><span class="sxs-lookup"><span data-stu-id="9ec63-887">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="9ec63-888">O `AreaAttribute` é o que indica que um controlador faz parte de uma área; dizemos que esse controlador está na área `Blog`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-888">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="9ec63-889">Controladores sem um atributo `[Area]` não são membros de nenhuma área e **não** corresponderão quando o valor de rota `area` for fornecido pelo roteamento.</span><span class="sxs-lookup"><span data-stu-id="9ec63-889">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="9ec63-890">No exemplo a seguir, somente o primeiro controlador listado pode corresponder aos valores de rota `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-890">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="9ec63-891">O namespace de cada controlador é mostrado aqui para fins de integridade – caso contrário, os controladores teriam um conflito de nomenclatura e gerariam um erro do compilador.</span><span class="sxs-lookup"><span data-stu-id="9ec63-891">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="9ec63-892">Namespaces de classe não têm efeito sobre o roteamento do MVC.</span><span class="sxs-lookup"><span data-stu-id="9ec63-892">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="9ec63-893">Os primeiros dois controladores são membros de áreas e correspondem somente quando seus respectivos nomes de área são fornecidos pelo valor de rota `area`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-893">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="9ec63-894">O terceiro controlador não é um membro de nenhuma área e só pode corresponder quando nenhum valor para `area` for fornecido pelo roteamento.</span><span class="sxs-lookup"><span data-stu-id="9ec63-894">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="9ec63-895">Em termos de não corresponder a *nenhum valor*, a ausência do valor de `area` é equivalente ao valor de `area` ser nulo ou uma cadeia de caracteres vazia.</span><span class="sxs-lookup"><span data-stu-id="9ec63-895">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="9ec63-896">Ao executar uma ação dentro de uma área, o valor de rota para `area` estará disponível como um *valor de ambiente* para o roteamento usar para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-896">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="9ec63-897">Isso significa que, por padrão, as áreas atuam como se fossem *autoadesivas* para a geração de URL, como demonstrado no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="9ec63-897">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="9ec63-898">Entendendo IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="9ec63-898">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="9ec63-899">Esta seção é uma análise aprofundada dos elementos internos da estrutura e de como o MVC escolhe uma ação para ser executada.</span><span class="sxs-lookup"><span data-stu-id="9ec63-899">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="9ec63-900">Um aplicativo típico não precisará de um `IActionConstraint` personalizado</span><span class="sxs-lookup"><span data-stu-id="9ec63-900">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="9ec63-901">Provavelmente, você já usou `IActionConstraint` mesmo que não esteja familiarizado com a interface.</span><span class="sxs-lookup"><span data-stu-id="9ec63-901">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="9ec63-902">O atributo `[HttpGet]` e atributos `[Http-VERB]` semelhantes implementam `IActionConstraint` para limitar a execução de um método de ação.</span><span class="sxs-lookup"><span data-stu-id="9ec63-902">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="9ec63-903">Presumindo a rota convencional padrão, o caminho de URL `/Products/Edit` produziria os valores `{ controller = Products, action = Edit }`, que corresponderiam a **ambas** as ações mostradas aqui.</span><span class="sxs-lookup"><span data-stu-id="9ec63-903">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="9ec63-904">Na terminologia `IActionConstraint`, diríamos que essas duas ações são consideradas candidatas – uma vez que ambas correspondem aos dados da rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-904">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="9ec63-905">Quando for executado, `HttpGetAttribute` indicará que *Edit()* corresponde a *GET* e não corresponde a nenhum outro verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="9ec63-905">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="9ec63-906">A ação `Edit(...)` não tem restrições definidas e, portanto, corresponderá a qualquer verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="9ec63-906">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="9ec63-907">Sendo assim, supondo um `POST`, `Edit(...)` será correspondente.</span><span class="sxs-lookup"><span data-stu-id="9ec63-907">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="9ec63-908">Mas, para um `GET`, ambas as ações ainda podem corresponder – no entanto, uma ação com um `IActionConstraint` sempre é considerada *melhor* que uma ação sem.</span><span class="sxs-lookup"><span data-stu-id="9ec63-908">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="9ec63-909">Assim, como `Edit()` tem `[HttpGet]`, ela é considerada mais específica e será selecionada se as duas ações puderem corresponder.</span><span class="sxs-lookup"><span data-stu-id="9ec63-909">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="9ec63-910">Conceitualmente, `IActionConstraint` é uma forma de *sobrecarga*, mas em vez de uma sobrecarga de métodos com o mesmo nome, trata-se da sobrecarga entre ações que correspondem à mesma URL.</span><span class="sxs-lookup"><span data-stu-id="9ec63-910">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="9ec63-911">O roteamento de atributo também usa `IActionConstraint` e pode fazer com que ações de controladores diferentes sejam consideradas candidatas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-911">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="9ec63-912">Implementando IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="9ec63-912">Implementing IActionConstraint</span></span>

<span data-ttu-id="9ec63-913">A maneira mais simples de implementar um `IActionConstraint` é criar uma classe derivada de `System.Attribute` e colocá-la em suas ações e controladores.</span><span class="sxs-lookup"><span data-stu-id="9ec63-913">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="9ec63-914">O MVC descobrirá automaticamente qualquer `IActionConstraint` que for aplicado como atributo.</span><span class="sxs-lookup"><span data-stu-id="9ec63-914">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="9ec63-915">Você pode usar o modelo de aplicativo para aplicar restrições e, provavelmente, essa é a abordagem mais flexível, pois permite a você faça uma metaprogramação de como elas são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-915">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="9ec63-916">No exemplo a seguir, uma restrição escolhe uma ação com base em um *código de país* dos dados de rota.</span><span class="sxs-lookup"><span data-stu-id="9ec63-916">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="9ec63-917">O [exemplo completo no GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="9ec63-917">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="9ec63-918">Você é responsável por implementar o método `Accept` e por escolher uma "ordem" na qual a restrição deve ser executada.</span><span class="sxs-lookup"><span data-stu-id="9ec63-918">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="9ec63-919">Nesse caso, o método `Accept` retorna `true` para indicar que a ação é correspondente quando o valor de rota `country` é correspondente.</span><span class="sxs-lookup"><span data-stu-id="9ec63-919">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="9ec63-920">Isso é diferente de um `RouteValueAttribute`, pois permite o fallback para uma ação não atribuída.</span><span class="sxs-lookup"><span data-stu-id="9ec63-920">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="9ec63-921">O exemplo mostra que se você definir uma ação `en-US`, um código de país como `fr-FR` fará o fallback para um controlador mais genérico que não tem `[CountrySpecific(...)]` aplicado.</span><span class="sxs-lookup"><span data-stu-id="9ec63-921">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="9ec63-922">A propriedade `Order` decide de qual *estágio* a restrição faz parte.</span><span class="sxs-lookup"><span data-stu-id="9ec63-922">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="9ec63-923">Restrições de ação são executadas em grupos com base no `Order`.</span><span class="sxs-lookup"><span data-stu-id="9ec63-923">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="9ec63-924">Por exemplo, todos atributos de método HTTP fornecidos pela estrutura usam o mesmo valor de `Order` para que sejam executados no mesmo estágio.</span><span class="sxs-lookup"><span data-stu-id="9ec63-924">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="9ec63-925">Você pode ter tantos estágios quantos forem necessários para implementar suas políticas desejadas.</span><span class="sxs-lookup"><span data-stu-id="9ec63-925">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="9ec63-926">Para decidir o valor para `Order`, considere se sua restrição deve ou não deve ser aplicada antes de métodos HTTP.</span><span class="sxs-lookup"><span data-stu-id="9ec63-926">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="9ec63-927">Números inferiores são executados primeiro.</span><span class="sxs-lookup"><span data-stu-id="9ec63-927">Lower numbers run first.</span></span>

::: moniker-end
