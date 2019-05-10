---
title: Roteamento para ações do controlador no ASP.NET Core
author: rick-anderson
description: Saiba como o ASP.NET Core MVC usa o middleware de roteamento para corresponder a URLs das solicitações de entrada e mapeá-las para ações.
ms.author: riande
ms.date: 01/24/2019
uid: mvc/controllers/routing
ms.openlocfilehash: b4d5cd3add3fda6b70873eb5cce1dcee651f9185
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087514"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="73c55-103">Roteamento para ações do controlador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73c55-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="73c55-104">Por [Ryan Nowak](https://github.com/rynowak) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="73c55-104">By [Ryan Nowak](https://github.com/rynowak) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="73c55-105">O ASP.NET Core MVC usa o [middleware](xref:fundamentals/middleware/index) de Roteamento para fazer as correspondências das URLs de solicitações de entrada e mapeá-las para ações.</span><span class="sxs-lookup"><span data-stu-id="73c55-105">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="73c55-106">As rotas são definidas em atributos ou no código de inicialização.</span><span class="sxs-lookup"><span data-stu-id="73c55-106">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="73c55-107">Elas descrevem como deve ser feita a correspondência entre caminhos de URL e ações.</span><span class="sxs-lookup"><span data-stu-id="73c55-107">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="73c55-108">As rotas também são usadas para gerar URLs (para links) enviados em resposta.</span><span class="sxs-lookup"><span data-stu-id="73c55-108">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="73c55-109">As ações são roteadas convencionalmente ou segundo os atributos.</span><span class="sxs-lookup"><span data-stu-id="73c55-109">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="73c55-110">Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo.</span><span class="sxs-lookup"><span data-stu-id="73c55-110">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="73c55-111">Para obter mais informações, consulte [Roteamento misto](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="73c55-111">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="73c55-112">Este documento explicará as interações entre o MVC e o roteamento e como aplicativos MVC comuns usam recursos de roteamento.</span><span class="sxs-lookup"><span data-stu-id="73c55-112">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="73c55-113">Consulte [Roteamento](xref:fundamentals/routing) para obter detalhes sobre o roteamento avançado.</span><span class="sxs-lookup"><span data-stu-id="73c55-113">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="73c55-114">Configurando o middleware de Roteamento</span><span class="sxs-lookup"><span data-stu-id="73c55-114">Setting up Routing Middleware</span></span>

<span data-ttu-id="73c55-115">No método *Configurar*, você poderá ver código semelhante a:</span><span class="sxs-lookup"><span data-stu-id="73c55-115">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="73c55-116">Dentro da chamada para `UseMvc`, `MapRoute` é usado para criar uma única rota, que chamaremos de rota `default`.</span><span class="sxs-lookup"><span data-stu-id="73c55-116">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="73c55-117">A maioria dos aplicativos MVC usa uma rota com um modelo semelhante à rota `default`.</span><span class="sxs-lookup"><span data-stu-id="73c55-117">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="73c55-118">O modelo de rota `"{controller=Home}/{action=Index}/{id?}"` pode corresponder a um caminho de URL como `/Products/Details/5` e extrai os valores de rota `{ controller = Products, action = Details, id = 5 }` gerando tokens para o caminho.</span><span class="sxs-lookup"><span data-stu-id="73c55-118">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="73c55-119">O MVC tentará localizar um controlador chamado `ProductsController` e executar a ação `Details`:</span><span class="sxs-lookup"><span data-stu-id="73c55-119">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="73c55-120">Observe que, neste exemplo, o model binding usaria o valor de `id = 5` para definir o parâmetro `id` como `5` ao invocar essa ação.</span><span class="sxs-lookup"><span data-stu-id="73c55-120">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="73c55-121">Consulte [Model binding](../models/model-binding.md) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="73c55-121">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="73c55-122">Usando a rota `default`:</span><span class="sxs-lookup"><span data-stu-id="73c55-122">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="73c55-123">O modelo da rota:</span><span class="sxs-lookup"><span data-stu-id="73c55-123">The route template:</span></span>

* <span data-ttu-id="73c55-124">`{controller=Home}` define `Home` como o `controller` padrão</span><span class="sxs-lookup"><span data-stu-id="73c55-124">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="73c55-125">`{action=Index}` define `Index` como o `action` padrão</span><span class="sxs-lookup"><span data-stu-id="73c55-125">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="73c55-126">`{id?}` define `id` como opcional</span><span class="sxs-lookup"><span data-stu-id="73c55-126">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="73c55-127">Parâmetros de rota opcionais e padrão não precisam estar presentes no caminho da URL para que haja uma correspondência.</span><span class="sxs-lookup"><span data-stu-id="73c55-127">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="73c55-128">Consulte [Referência de modelo de rota](../../fundamentals/routing.md#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="73c55-128">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="73c55-129">`"{controller=Home}/{action=Index}/{id?}"` pode corresponder ao caminho da URL `/` e produzirá os valores de rota `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="73c55-129">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="73c55-130">Os valores de `controller` e `action` usam os valores padrão, `id` não produz um valor, uma vez que não há nenhum segmento correspondente no caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="73c55-130">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="73c55-131">O MVC usaria esses valores de rota para selecionar a ação `HomeController` e `Index`:</span><span class="sxs-lookup"><span data-stu-id="73c55-131">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="73c55-132">Usando essa definição de controlador e modelo de rota, a ação `HomeController.Index` seria executada para qualquer um dos caminhos de URL a seguir:</span><span class="sxs-lookup"><span data-stu-id="73c55-132">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="73c55-133">O método de conveniência `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="73c55-133">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="73c55-134">Pode ser usado para substituir:</span><span class="sxs-lookup"><span data-stu-id="73c55-134">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="73c55-135">`UseMvc` e `UseMvcWithDefaultRoute` adicionam uma instância de `RouterMiddleware` ao pipeline de middleware.</span><span class="sxs-lookup"><span data-stu-id="73c55-135">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="73c55-136">O MVC não interage diretamente com o middleware e usa o roteamento para tratar das solicitações.</span><span class="sxs-lookup"><span data-stu-id="73c55-136">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="73c55-137">O MVC é conectado às rotas por meio de uma instância de `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="73c55-137">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="73c55-138">O código dentro de `UseMvc` é semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="73c55-138">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="73c55-139">`UseMvc` não define diretamente nenhuma rota, ele adiciona um espaço reservado à coleção de rotas para a rota `attribute`.</span><span class="sxs-lookup"><span data-stu-id="73c55-139">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="73c55-140">A sobrecarga `UseMvc(Action<IRouteBuilder>)` permite adicionar suas próprias rotas e também dá suporte ao roteamento de atributos.</span><span class="sxs-lookup"><span data-stu-id="73c55-140">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="73c55-141">`UseMvc` e todas as suas variações adicionam um espaço reservado à rota do atributo – o roteamento de atributos sempre está disponível, independentemente de como você configura `UseMvc`.</span><span class="sxs-lookup"><span data-stu-id="73c55-141">`UseMvc` and all of its variations adds a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="73c55-142">`UseMvcWithDefaultRoute` define uma rota padrão e dá suporte ao roteamento de atributos.</span><span class="sxs-lookup"><span data-stu-id="73c55-142">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="73c55-143">A seção [Roteamento de atributos](#attribute-routing-ref-label) inclui mais detalhes sobre o roteamento de atributos.</span><span class="sxs-lookup"><span data-stu-id="73c55-143">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="73c55-144">Roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="73c55-144">Conventional routing</span></span>

<span data-ttu-id="73c55-145">A rota `default`:</span><span class="sxs-lookup"><span data-stu-id="73c55-145">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="73c55-146">é um exemplo de *roteamento convencional*.</span><span class="sxs-lookup"><span data-stu-id="73c55-146">is an example of a *conventional routing*.</span></span> <span data-ttu-id="73c55-147">Nós chamamos esse estilo de *roteamento convencional* porque ele estabelece uma *convenção* para caminhos de URL:</span><span class="sxs-lookup"><span data-stu-id="73c55-147">We call this style *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="73c55-148">o primeiro segmento do caminho é mapeado para o nome do controlador,</span><span class="sxs-lookup"><span data-stu-id="73c55-148">the first path segment maps to the controller name</span></span>

* <span data-ttu-id="73c55-149">o segundo é mapeado para o nome da ação,</span><span class="sxs-lookup"><span data-stu-id="73c55-149">the second maps to the action name.</span></span>

* <span data-ttu-id="73c55-150">o terceiro segmento é usado para uma `id` opcional usado para mapear para uma entidade de modelo.</span><span class="sxs-lookup"><span data-stu-id="73c55-150">the third segment is used for an optional `id` used to map to a model entity</span></span>

<span data-ttu-id="73c55-151">Usando essa rota `default`, o caminho da URL `/Products/List` é mapeado para a ação `ProductsController.List` e `/Blog/Article/17` é mapeado para `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="73c55-151">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="73c55-152">Esse mapeamento é baseado **somente** nos nomes do controlador e da ação e não é baseado em namespaces, locais de arquivos de origem ou parâmetros de método.</span><span class="sxs-lookup"><span data-stu-id="73c55-152">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="73c55-153">Usar o roteamento convencional com a rota padrão permite compilar o aplicativo rapidamente sem precisar criar um novo padrão de URL para cada ação que você definir.</span><span class="sxs-lookup"><span data-stu-id="73c55-153">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="73c55-154">Para um aplicativo com ações de estilo CRUD, ter consistência para as URLs em seus controladores pode ajudar a simplificar seu código e a tornar sua interface do usuário mais previsível.</span><span class="sxs-lookup"><span data-stu-id="73c55-154">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="73c55-155">O `id` é definido como opcional pelo modelo de rota, o que significa que suas ações podem ser executadas sem a ID fornecida como parte da URL.</span><span class="sxs-lookup"><span data-stu-id="73c55-155">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="73c55-156">Normalmente, o que acontecerá se `id` for omitido da URL é que ele será definido como `0` pelo model binding e, dessa forma, não será encontrada no banco de dados nenhuma entidade correspondente a `id == 0`.</span><span class="sxs-lookup"><span data-stu-id="73c55-156">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="73c55-157">O roteamento de atributos pode lhe proporcionar controle refinado para tornar a ID obrigatória para algumas ações e não para outras.</span><span class="sxs-lookup"><span data-stu-id="73c55-157">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="73c55-158">Por convenção, a documentação incluirá parâmetros opcionais, como `id`, quando for provável que eles apareçam no uso correto.</span><span class="sxs-lookup"><span data-stu-id="73c55-158">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="73c55-159">Várias rotas</span><span class="sxs-lookup"><span data-stu-id="73c55-159">Multiple routes</span></span>

<span data-ttu-id="73c55-160">É possível adicionar várias rotas dentro de `UseMvc` adicionando mais chamadas para `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="73c55-160">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="73c55-161">Fazer isso permite que você defina várias convenções ou que adicione rotas convencionais dedicadas a uma ação específica, como:</span><span class="sxs-lookup"><span data-stu-id="73c55-161">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="73c55-162">A rota `blog` aqui é uma *rota convencional dedicada*, o que significa que ela usa o sistema de roteamento convencional, mas é dedicada a uma ação específica.</span><span class="sxs-lookup"><span data-stu-id="73c55-162">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="73c55-163">Como `controller` e `action` não aparecem no modelo de rota como parâmetros, eles só podem ter os valores padrão e, portanto, essa rota sempre será mapeada para a ação `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="73c55-163">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="73c55-164">As rotas na coleção de rotas são ordenadas e serão processadas na ordem em que forem adicionadas.</span><span class="sxs-lookup"><span data-stu-id="73c55-164">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="73c55-165">Portanto, neste exemplo, a rota `blog` será tentada antes da rota `default`.</span><span class="sxs-lookup"><span data-stu-id="73c55-165">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="73c55-166">*Rotas convencionais dedicadas* geralmente usam parâmetros de rota que capturam tudo, como `{*article}`, para capturar a parte restante do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="73c55-166">*Dedicated conventional routes* often use catch-all route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="73c55-167">Isso pode fazer com que uma rota fique "muito ambiciosa", ou seja, que faça a correspondência com URLs que deveriam ser correspondidas com outras rotas.</span><span class="sxs-lookup"><span data-stu-id="73c55-167">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="73c55-168">Coloque as rotas "ambiciosas" mais adiante na tabela de rotas para solucionar esse problema.</span><span class="sxs-lookup"><span data-stu-id="73c55-168">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="73c55-169">Fallback</span><span class="sxs-lookup"><span data-stu-id="73c55-169">Fallback</span></span>

<span data-ttu-id="73c55-170">Como parte do processamento de solicitações, o MVC verificará se o valores das rotas podem ser usados para encontrar um controlador e uma ação em seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="73c55-170">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="73c55-171">Se os valores das rotas não corresponderem a uma ação, a rota não será considerada correspondente e a próxima rota será tentada.</span><span class="sxs-lookup"><span data-stu-id="73c55-171">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="73c55-172">Isso é chamado de *fallback* e sua finalidade é simplificar casos em que rotas convencionais se sobrepõem.</span><span class="sxs-lookup"><span data-stu-id="73c55-172">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="73c55-173">Desambiguação de ações</span><span class="sxs-lookup"><span data-stu-id="73c55-173">Disambiguating actions</span></span>

<span data-ttu-id="73c55-174">Quando duas ações correspondem por meio do roteamento, o MVC precisa resolver a ambiguidade para escolher a "melhor" candidata ou lançar uma exceção.</span><span class="sxs-lookup"><span data-stu-id="73c55-174">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="73c55-175">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="73c55-175">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="73c55-176">Esse controlador define duas ações que fariam a correspondência entre caminho da URL `/Products/Edit/17` e a os dados da rota `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="73c55-176">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="73c55-177">Este é um padrão comum para controladores MVC em que `Edit(int)` mostra um formulário para editar um produto e `Edit(int, Product)` processa o formulário postado.</span><span class="sxs-lookup"><span data-stu-id="73c55-177">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="73c55-178">Para que isso seja possível, o MVC precisa escolher `Edit(int, Product)` quando a solicitação é um `POST` HTTP e `Edit(int)` quando o verbo HTTP é qualquer outra coisa.</span><span class="sxs-lookup"><span data-stu-id="73c55-178">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="73c55-179">O `HttpPostAttribute` (`[HttpPost]`) é uma implementação de `IActionConstraint` que só permite que a ação seja selecionada quando o verbo HTTP é `POST`.</span><span class="sxs-lookup"><span data-stu-id="73c55-179">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="73c55-180">A presença de um `IActionConstraint` faz do `Edit(int, Product)` uma "melhor" correspondência do que `Edit(int)`, portanto, `Edit(int, Product)` será tentado primeiro.</span><span class="sxs-lookup"><span data-stu-id="73c55-180">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="73c55-181">Você só precisará gravar implementações personalizadas de `IActionConstraint` em cenários especializados, mas é importante compreender a função de atributos como `HttpPostAttribute` – atributos semelhantes são definidos para outros verbos HTTP.</span><span class="sxs-lookup"><span data-stu-id="73c55-181">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="73c55-182">No roteamento convencional, é comum que ações usem o mesmo nome de ação quando fazem parte de um fluxo de trabalho de `show form -> submit form`.</span><span class="sxs-lookup"><span data-stu-id="73c55-182">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="73c55-183">A conveniência desse padrão ficará mais aparente após você revisar a seção [Noções básicas sobre IActionConstraint](#understanding-iactionconstraint).</span><span class="sxs-lookup"><span data-stu-id="73c55-183">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="73c55-184">Se várias rotas corresponderem e o MVC não puder encontrar uma rota "melhor", ele gerará um `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="73c55-184">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="73c55-185">Nomes de rotas</span><span class="sxs-lookup"><span data-stu-id="73c55-185">Route names</span></span>

<span data-ttu-id="73c55-186">As cadeias de caracteres `"blog"` e `"default"` nos exemplos a seguir são nomes de rotas:</span><span class="sxs-lookup"><span data-stu-id="73c55-186">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="73c55-187">Os nomes de rotas dão a uma rota um nome lógico, de modo que a rota nomeada possa ser usada para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="73c55-187">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="73c55-188">Isso simplifica muito a criação de URLs quando a ordenação de rotas poderia complicá-la.</span><span class="sxs-lookup"><span data-stu-id="73c55-188">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="73c55-189">Nomes de rotas devem ser exclusivos no nível do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="73c55-189">Route names must be unique application-wide.</span></span>

<span data-ttu-id="73c55-190">Os nomes de rotas não têm impacto sobre a correspondência de URLs ou o tratamento de solicitações; eles são usados apenas para a geração de URLs.</span><span class="sxs-lookup"><span data-stu-id="73c55-190">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="73c55-191">[Roteamento](xref:fundamentals/routing) tem informações mais detalhadas sobre geração de URLs, incluindo a geração de URLs em auxiliares específicos do MVC.</span><span class="sxs-lookup"><span data-stu-id="73c55-191">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="73c55-192">Roteamento de atributo</span><span class="sxs-lookup"><span data-stu-id="73c55-192">Attribute routing</span></span>

<span data-ttu-id="73c55-193">O roteamento de atributo usa um conjunto de atributos para mapear ações diretamente para modelos de rota.</span><span class="sxs-lookup"><span data-stu-id="73c55-193">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="73c55-194">No exemplo a seguir, `app.UseMvc();` é usado no método `Configure` e nenhuma rota é passada.</span><span class="sxs-lookup"><span data-stu-id="73c55-194">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="73c55-195">O `HomeController` corresponderá a um conjunto de URLs semelhantes ao que a rota padrão `{controller=Home}/{action=Index}/{id?}` corresponderia:</span><span class="sxs-lookup"><span data-stu-id="73c55-195">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="73c55-196">A ação `HomeController.Index()` será executada para qualquer um dos caminhos de URL `/`, `/Home` ou `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="73c55-196">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="73c55-197">Este exemplo destaca uma diferença importante de programação entre o roteamento de atributo e o roteamento convencional.</span><span class="sxs-lookup"><span data-stu-id="73c55-197">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="73c55-198">O roteamento de atributo requer mais entradas para especificar uma rota; a rota padrão convencional manipula as rotas de forma mais sucinta.</span><span class="sxs-lookup"><span data-stu-id="73c55-198">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="73c55-199">No entanto, o roteamento de atributo permite (e exige) o controle preciso de quais modelos de rota se aplicam a cada ação.</span><span class="sxs-lookup"><span data-stu-id="73c55-199">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="73c55-200">Com o roteamento de atributo, o nome do controlador e os nomes de ação não desempenham **nenhuma** função quanto a qual ação é selecionada.</span><span class="sxs-lookup"><span data-stu-id="73c55-200">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="73c55-201">Este exemplo corresponderá as mesmas URLs que o exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="73c55-201">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="73c55-202">Os modelos de rota acima não definem parâmetros de rota para `action`, `area` e `controller`.</span><span class="sxs-lookup"><span data-stu-id="73c55-202">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="73c55-203">Na verdade, esses parâmetros de rota não são permitidos em rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="73c55-203">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="73c55-204">Uma vez que o modelo de rota já está associado a uma ação, não faria sentido analisar o nome da ação da URL.</span><span class="sxs-lookup"><span data-stu-id="73c55-204">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="73c55-205">Roteamento de atributo com atributos Http[Verb]</span><span class="sxs-lookup"><span data-stu-id="73c55-205">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="73c55-206">O roteamento de atributo também pode usar atributos `Http[Verb]`, como `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="73c55-206">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="73c55-207">Todos esses atributos podem aceitar um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="73c55-207">All of these attributes can accept a route template.</span></span> <span data-ttu-id="73c55-208">Este exemplo mostra duas ações que correspondem ao mesmo modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="73c55-208">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="73c55-209">Para um caminho de URL como `/products`, a ação `ProductsApi.ListProducts` será executada quando o verbo HTTP for `GET` e `ProductsApi.CreateProduct` será executado quando o verbo HTTP for `POST`.</span><span class="sxs-lookup"><span data-stu-id="73c55-209">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="73c55-210">Primeiro, o roteamento de atributo faz a correspondência da URL com o conjunto de modelos de rota definidos por atributos de rota.</span><span class="sxs-lookup"><span data-stu-id="73c55-210">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="73c55-211">Quando um modelo de rota for correspondente, restrições de `IActionConstraint` serão aplicadas para determinar quais ações podem ser executadas.</span><span class="sxs-lookup"><span data-stu-id="73c55-211">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="73c55-212">Ao compilar uma API REST, é raro que você queira usar `[Route(...)]` em um método de ação.</span><span class="sxs-lookup"><span data-stu-id="73c55-212">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method.</span></span> <span data-ttu-id="73c55-213">É melhor usar o `Http*Verb*Attributes` mais específico para ser preciso quanto ao que tem suporte de sua API.</span><span class="sxs-lookup"><span data-stu-id="73c55-213">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="73c55-214">Espera-se que clientes de APIs REST saibam quais caminhos e verbos HTTP são mapeados para operações lógicas específicas.</span><span class="sxs-lookup"><span data-stu-id="73c55-214">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="73c55-215">Como uma rota de atributo se aplica a uma ação específica, é fácil fazer com que parâmetros sejam obrigatórios como parte da definição do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="73c55-215">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="73c55-216">Neste exemplo, `id` é obrigatório como parte do caminho da URL.</span><span class="sxs-lookup"><span data-stu-id="73c55-216">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="73c55-217">A ação `ProductsApi.GetProduct(int)` será executada para um caminho de URL como `/products/3`, mas não para um caminho de URL como `/products`.</span><span class="sxs-lookup"><span data-stu-id="73c55-217">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="73c55-218">Consulte [Roteamento](../../fundamentals/routing.md) para obter uma descrição completa de modelos de rota e as opções relacionadas.</span><span class="sxs-lookup"><span data-stu-id="73c55-218">See [Routing](../../fundamentals/routing.md) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="73c55-219">Nome da rota</span><span class="sxs-lookup"><span data-stu-id="73c55-219">Route Name</span></span>

<span data-ttu-id="73c55-220">O código a seguir define um *nome da rota* como `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="73c55-220">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="73c55-221">Nomes de rota podem ser usados para gerar uma URL com base em uma rota específica.</span><span class="sxs-lookup"><span data-stu-id="73c55-221">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="73c55-222">Nomes de rota não têm impacto sobre o comportamento de correspondência da URL e são usados somente para geração de URLs.</span><span class="sxs-lookup"><span data-stu-id="73c55-222">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="73c55-223">Nomes de rotas devem ser exclusivos no nível do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="73c55-223">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="73c55-224">Compare isso com a *rota padrão* convencional, que define o parâmetro `id` como opcional (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="73c55-224">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="73c55-225">Essa capacidade de especificar APIs de forma específica tem vantagens, como permitir que `/products` e `/products/5` sejam expedidos para ações diferentes.</span><span class="sxs-lookup"><span data-stu-id="73c55-225">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="73c55-226">Combinando rotas</span><span class="sxs-lookup"><span data-stu-id="73c55-226">Combining routes</span></span>

<span data-ttu-id="73c55-227">Para tornar o roteamento de atributo menos repetitivo, os atributos de rota no controlador são combinados com atributos de rota nas ações individuais.</span><span class="sxs-lookup"><span data-stu-id="73c55-227">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="73c55-228">Modelos de rota definidos no controlador precedem modelos de rota nas ações. </span><span class="sxs-lookup"><span data-stu-id="73c55-228">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="73c55-229">Colocar um atributo de rota no controlador foz com que **todas** as ações no controlador usem o roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="73c55-229">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="73c55-230">Neste exemplo, o caminho de URL `/products` pode corresponder a `ProductsApi.ListProducts` e o caminho de URL `/products/5` pode corresponder a `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="73c55-230">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="73c55-231">Essas duas ações são correspondentes somente ao `GET` HTTP porque são decoradas com o `HttpGetAttribute`.</span><span class="sxs-lookup"><span data-stu-id="73c55-231">Both of these actions only match HTTP `GET` because they're decorated with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="73c55-232">Modelos de rota aplicados a uma ação, que começam com `/` ou `~/`, não são combinados com modelos de rota aplicados ao controlador.</span><span class="sxs-lookup"><span data-stu-id="73c55-232">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="73c55-233">Este exemplo corresponde a um conjunto de caminhos de URL semelhante à *rota padrão*.</span><span class="sxs-lookup"><span data-stu-id="73c55-233">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="73c55-234">Ordenando rotas de atributos</span><span class="sxs-lookup"><span data-stu-id="73c55-234">Ordering attribute routes</span></span>

<span data-ttu-id="73c55-235">Diferente de rotas convencionais, que são executadas em uma ordem definida, o roteamento de atributo cria uma árvore e faz a correspondência de todas as rotas simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="73c55-235">In contrast to conventional routes which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="73c55-236">O comportamento é como se as entradas de rota fossem colocadas em uma ordem ideal; as rotas mais específicas têm uma chance de ser executadas antes das rotas mais gerais.</span><span class="sxs-lookup"><span data-stu-id="73c55-236">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="73c55-237">Por exemplo, uma rota como `blog/search/{topic}` é mais específica que uma rota como `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="73c55-237">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="73c55-238">Em termos da lógica, a rota `blog/search/{topic}` é "executada" primeiro, por padrão, porque essa é a única ordem que faz sentido.</span><span class="sxs-lookup"><span data-stu-id="73c55-238">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="73c55-239">Usando o roteamento convencional, o desenvolvedor é responsável por colocar as rotas na ordem desejada.</span><span class="sxs-lookup"><span data-stu-id="73c55-239">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="73c55-240">Rotas de atributos podem configurar uma ordem, usando a propriedade `Order` de todos os atributos de rota fornecidos pela estrutura.</span><span class="sxs-lookup"><span data-stu-id="73c55-240">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="73c55-241">As rotas são processadas segundo uma classificação crescente da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="73c55-241">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="73c55-242">A ordem padrão é `0`.</span><span class="sxs-lookup"><span data-stu-id="73c55-242">The default order is `0`.</span></span> <span data-ttu-id="73c55-243">Uma rota definida usando `Order = -1` será executada antes de rotas que não definem uma ordem.</span><span class="sxs-lookup"><span data-stu-id="73c55-243">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="73c55-244">Uma rota definida usando `Order = 1` será executada após a ordem das rotas padrão.</span><span class="sxs-lookup"><span data-stu-id="73c55-244">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="73c55-245">Evite depender de `Order`.</span><span class="sxs-lookup"><span data-stu-id="73c55-245">Avoid depending on `Order`.</span></span> <span data-ttu-id="73c55-246">Se o seu espaço de URL exigir valores de ordem explícita para fazer o roteamento corretamente, provavelmente ele também será confuso para os clientes.</span><span class="sxs-lookup"><span data-stu-id="73c55-246">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="73c55-247">De modo geral, o roteamento de atributos selecionará a rota correta com a correspondência de URL.</span><span class="sxs-lookup"><span data-stu-id="73c55-247">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="73c55-248">Se a ordem padrão usada para a geração de URL não estiver funcionando, usar o nome da rota como uma substituição geralmente será mais simples do que aplicar a propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="73c55-248">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="73c55-249">Roteamento do Razor Pages e do controlador do MVC compartilham uma implementação.</span><span class="sxs-lookup"><span data-stu-id="73c55-249">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="73c55-250">Para saber mais sobre Ordem de Rota, confira os tópicos do Razor Pages disponíveis em [Convenções de rota e aplicativo do Razor Pages: Ordem de Rota](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="73c55-250">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="73c55-251">Substituição de token em modelos de rota ([controlador] [ação] [área])</span><span class="sxs-lookup"><span data-stu-id="73c55-251">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="73c55-252">Para conveniência, as rotas de atributo dão suporte à *substituição de token* colocando um token entre chaves quadradas (`[`, `]`).</span><span class="sxs-lookup"><span data-stu-id="73c55-252">For convenience, attribute routes support *token replacement* by enclosing a token in square-braces (`[`, `]`).</span></span> <span data-ttu-id="73c55-253">Os tokens `[action]`, `[area]` e `[controller]` são substituídos pelos valores do nome da ação, do nome da área e do nome do controlador da ação em que a rota é definida.</span><span class="sxs-lookup"><span data-stu-id="73c55-253">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="73c55-254">No exemplo a seguir, as ações correspondem a caminhos de URL conforme descrito nos comentários:</span><span class="sxs-lookup"><span data-stu-id="73c55-254">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/sample/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="73c55-255">A substituição de token ocorre como a última etapa da criação das rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="73c55-255">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="73c55-256">O exemplo acima se comportará da mesma forma que o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="73c55-256">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/sample/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="73c55-257">Rotas de atributo também podem ser combinadas com herança.</span><span class="sxs-lookup"><span data-stu-id="73c55-257">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="73c55-258">Isso é especialmente eficiente em combinação com a substituição de token.</span><span class="sxs-lookup"><span data-stu-id="73c55-258">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="73c55-259">A substituição de token também se aplica a nomes de rota definidos por rotas de atributo.</span><span class="sxs-lookup"><span data-stu-id="73c55-259">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="73c55-260">`[Route("[controller]/[action]", Name="[controller]_[action]")]` gera um nome de rota exclusivo para cada ação.</span><span class="sxs-lookup"><span data-stu-id="73c55-260">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="73c55-261">Para corresponder ao delimitador de substituição de token literal `[` ou `]`, faça seu escape repetindo o caractere (`[[` ou `]]`).</span><span class="sxs-lookup"><span data-stu-id="73c55-261">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker range=">= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="73c55-262">Usar um transformador de parâmetro para personalizar a substituição de token</span><span class="sxs-lookup"><span data-stu-id="73c55-262">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="73c55-263">A substituição do token pode ser personalizada usando um transformador de parâmetro.</span><span class="sxs-lookup"><span data-stu-id="73c55-263">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="73c55-264">Um transformador de parâmetro implementa `IOutboundParameterTransformer` e transforma o valor dos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="73c55-264">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="73c55-265">Por exemplo, um transformador de parâmetro `SlugifyParameterTransformer` personalizado muda o valor de rota `SubscriptionManagement` para `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="73c55-265">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="73c55-266">O `RouteTokenTransformerConvention` é uma convenção de modelo de aplicativo que:</span><span class="sxs-lookup"><span data-stu-id="73c55-266">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="73c55-267">Aplica um transformador de parâmetro a todas as rotas de atributo em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="73c55-267">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="73c55-268">Personaliza os valores de token de rota de atributo conforme eles são substituídos.</span><span class="sxs-lookup"><span data-stu-id="73c55-268">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="73c55-269">O `RouteTokenTransformerConvention` está registrado como uma opção em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="73c55-269">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="73c55-270">Várias rotas</span><span class="sxs-lookup"><span data-stu-id="73c55-270">Multiple Routes</span></span>

<span data-ttu-id="73c55-271">O roteamento de atributo dá suporte à definição de várias rotas que atingem a mesma ação.</span><span class="sxs-lookup"><span data-stu-id="73c55-271">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="73c55-272">O uso mais comum desse recurso é para simular o comportamento da *rota convencional padrão*, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="73c55-272">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="73c55-273">Colocar vários atributos de rota no controlador significa que cada um deles será combinado com cada um dos atributos de rota nos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="73c55-273">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="73c55-274">Quando vários atributos de rota (que implementam `IActionConstraint`) são colocados em uma ação, cada restrição da ação combina com o modelo de rota do atributo que a definiu.</span><span class="sxs-lookup"><span data-stu-id="73c55-274">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="73c55-275">Embora o uso de várias rotas em ações possa parecer eficaz, é melhor manter o espaço de URL de seu aplicativo simples e bem definido.</span><span class="sxs-lookup"><span data-stu-id="73c55-275">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="73c55-276">Use várias rotas em ações somente quando for necessário; por exemplo, para dar suporte a clientes existentes.</span><span class="sxs-lookup"><span data-stu-id="73c55-276">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="73c55-277">Especificando parâmetros opcionais, valores padrão e restrições da rota de atributo</span><span class="sxs-lookup"><span data-stu-id="73c55-277">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="73c55-278">Rotas de atributo dão suporte à mesma sintaxe embutida que as rotas convencionais para especificar parâmetros opcionais, valores padrão e restrições.</span><span class="sxs-lookup"><span data-stu-id="73c55-278">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="73c55-279">Consulte [Referência de modelo de rota](../../fundamentals/routing.md#route-template-reference) para obter uma descrição detalhada da sintaxe do modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="73c55-279">See [Route Template Reference](../../fundamentals/routing.md#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="73c55-280">Atributos de rota personalizados usando `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="73c55-280">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="73c55-281">Todos os atributos de rota fornecidos na estrutura ( `[Route(...)]`, `[HttpGet(...)]` etc.) implementam a interface `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="73c55-281">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="73c55-282">O MVC procura atributos em classes de controlador e métodos de ação quando o aplicativo é iniciado e usa aqueles que implementam `IRouteTemplateProvider` para criar o conjunto inicial de rotas.</span><span class="sxs-lookup"><span data-stu-id="73c55-282">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="73c55-283">Você pode implementar `IRouteTemplateProvider` para definir seus próprios atributos de rota.</span><span class="sxs-lookup"><span data-stu-id="73c55-283">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="73c55-284">Cada `IRouteTemplateProvider` permite definir uma única rota com um nome, uma ordem e um modelo de rota personalizado:</span><span class="sxs-lookup"><span data-stu-id="73c55-284">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="73c55-285">O atributo do exemplo acima configura automaticamente o `Template` como `"api/[controller]"` quando `[MyApiController]` é aplicado.</span><span class="sxs-lookup"><span data-stu-id="73c55-285">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="73c55-286">Usando o Modelo de Aplicativo para personalizar rotas de atributo</span><span class="sxs-lookup"><span data-stu-id="73c55-286">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="73c55-287">O *modelo de aplicativo* é um modelo de objeto criado durante a inicialização com todos os metadados usados pelo MVC para rotear e executar suas ações.</span><span class="sxs-lookup"><span data-stu-id="73c55-287">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="73c55-288">O *modelo de aplicativo* inclui todos os dados reunidos dos atributos de rota (por meio de `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="73c55-288">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="73c55-289">Você pode escrever *convenções* para modificar o modelo do aplicativo no momento da inicialização para personalizar o comportamento do roteamento.</span><span class="sxs-lookup"><span data-stu-id="73c55-289">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="73c55-290">Esta seção mostra um exemplo simples de personalização de roteamento usando o modelo de aplicativo.</span><span class="sxs-lookup"><span data-stu-id="73c55-290">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/sample/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="73c55-291">Roteamento misto: roteamento de atributo versus roteamento convencional</span><span class="sxs-lookup"><span data-stu-id="73c55-291">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="73c55-292">Aplicativos MVC podem combinar o uso do roteamento convencional e do roteamento de atributo.</span><span class="sxs-lookup"><span data-stu-id="73c55-292">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="73c55-293">É comum usar rotas convencionais para controladores que servem páginas HTML para navegadores e usar o roteamento de atributo para controladores que servem APIs REST.</span><span class="sxs-lookup"><span data-stu-id="73c55-293">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="73c55-294">As ações são roteadas convencionalmente ou segundo os atributos.</span><span class="sxs-lookup"><span data-stu-id="73c55-294">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="73c55-295">Colocar uma rota no controlador ou na ação faz com que ela seja roteada segundo o atributo.</span><span class="sxs-lookup"><span data-stu-id="73c55-295">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="73c55-296">Ações que definem rotas de atributo não podem ser acessadas por meio das rotas convencionais e vice-versa.</span><span class="sxs-lookup"><span data-stu-id="73c55-296">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="73c55-297">**Qualquer** atributo de rota no controlador faz com que todas as ações no atributo de controlador sejam roteadas.</span><span class="sxs-lookup"><span data-stu-id="73c55-297">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="73c55-298">O que diferencia os dois tipos de sistemas de roteamento é o processo aplicado após uma URL corresponder a um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="73c55-298">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="73c55-299">No roteamento convencional, os valores de rota da correspondência são usados para escolher a ação e o controlador em uma tabela de pesquisa com todas as ações roteadas convencionais.</span><span class="sxs-lookup"><span data-stu-id="73c55-299">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="73c55-300">No roteamento de atributo, cada modelo já está associado a uma ação e nenhuma pesquisa adicional é necessária.</span><span class="sxs-lookup"><span data-stu-id="73c55-300">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="73c55-301">Segmentos complexos</span><span class="sxs-lookup"><span data-stu-id="73c55-301">Complex segments</span></span>

<span data-ttu-id="73c55-302">Segmentos complexos (por exemplo, `[Route("/dog{token}cat")]`), são processados combinando literais da direita para a esquerda de uma maneira não Greedy.</span><span class="sxs-lookup"><span data-stu-id="73c55-302">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="73c55-303">Veja [o código-fonte](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) para obter uma descrição.</span><span class="sxs-lookup"><span data-stu-id="73c55-303">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="73c55-304">Para obter mais informações, confira [esta edição](https://github.com/aspnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="73c55-304">For more information, see [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="73c55-305">Geração de URL</span><span class="sxs-lookup"><span data-stu-id="73c55-305">URL Generation</span></span>

<span data-ttu-id="73c55-306">Aplicativos MVC podem usar os recursos de geração de URL do roteamento para gerar links de URL para ações.</span><span class="sxs-lookup"><span data-stu-id="73c55-306">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="73c55-307">Gerar URLs elimina a necessidade de codificar URLs, tornando seu código mais robusto e sustentável.</span><span class="sxs-lookup"><span data-stu-id="73c55-307">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="73c55-308">Esta seção tem como foco os recursos de geração de URL fornecidos pelo MVC e só aborda as noções básicas de como a geração de URL funciona.</span><span class="sxs-lookup"><span data-stu-id="73c55-308">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="73c55-309">Consulte [Roteamento](../../fundamentals/routing.md) para obter uma descrição detalhada da geração de URL.</span><span class="sxs-lookup"><span data-stu-id="73c55-309">See [Routing](../../fundamentals/routing.md) for a detailed description of URL generation.</span></span>

<span data-ttu-id="73c55-310">A interface `IUrlHelper` é a parte subjacente da infraestrutura entre o MVC e o roteamento para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="73c55-310">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="73c55-311">Você encontrará uma instância de `IUrlHelper` disponível por meio da propriedade `Url` em controladores, exibições e componentes de exibição.</span><span class="sxs-lookup"><span data-stu-id="73c55-311">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="73c55-312">Neste exemplo, a interface `IUrlHelper` é usada por meio a propriedade `Controller.Url` para gerar uma URL para outra ação.</span><span class="sxs-lookup"><span data-stu-id="73c55-312">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="73c55-313">Se o aplicativo estiver usando a rota convencional padrão, o valor da variável `url` será a cadeia de caracteres do caminho de URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="73c55-313">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="73c55-314">Esse caminho de URL é criado pelo roteamento combinando os valores de rota da solicitação atual (valores de ambiente) com os valores passados para `Url.Action` e substituindo esses valores no modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="73c55-314">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="73c55-315">Cada parâmetro de rota no modelo de rota tem seu valor substituído por nomes correspondentes com os valores e os valores de ambiente.</span><span class="sxs-lookup"><span data-stu-id="73c55-315">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="73c55-316">Um parâmetro de rota que não tem um valor pode usar um valor padrão se houver um ou pode ser ignorado se for opcional (como no caso de `id` neste exemplo).</span><span class="sxs-lookup"><span data-stu-id="73c55-316">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="73c55-317">A geração de URL falhará se qualquer parâmetro de rota obrigatório não tiver um valor correspondente.</span><span class="sxs-lookup"><span data-stu-id="73c55-317">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="73c55-318">Se a geração de URL falhar para uma rota, a rota seguinte será tentada até que todas as rotas tenham sido tentadas ou que uma correspondência seja encontrada.</span><span class="sxs-lookup"><span data-stu-id="73c55-318">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="73c55-319">O exemplo de `Url.Action` acima pressupõe que o roteamento seja convencional, mas a geração de URL funciona de forma semelhante com o roteamento de atributo, embora os conceitos sejam diferentes.</span><span class="sxs-lookup"><span data-stu-id="73c55-319">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="73c55-320">Com o roteamento convencional, os valores de rota são usados para expandir um modelo e os valores de rota para `controller` e `action` normalmente são exibidos no modelo – isso funciona porque as URLs correspondidas pelo roteamento aderem a uma *convenção*.</span><span class="sxs-lookup"><span data-stu-id="73c55-320">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="73c55-321">No roteamento de atributo, os valores de rota para `controller` e `action` não podem ser exibidos no modelo; em vez disso, eles são usados para pesquisar o modelo a ser usado.</span><span class="sxs-lookup"><span data-stu-id="73c55-321">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="73c55-322">Este exemplo usa o roteamento de atributo:</span><span class="sxs-lookup"><span data-stu-id="73c55-322">This example uses attribute routing:</span></span>

[!code-csharp[](routing/sample/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="73c55-323">O MVC cria uma tabela de pesquisa de todas as ações de atributo roteadas e faz a correspondência dos valores de `controller` e `action` para selecionar o modelo de rota a ser usado para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="73c55-323">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="73c55-324">Na amostra acima, `custom/url/to/destination` é gerado.</span><span class="sxs-lookup"><span data-stu-id="73c55-324">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="73c55-325">Gerando URLs pelo nome da ação</span><span class="sxs-lookup"><span data-stu-id="73c55-325">Generating URLs by action name</span></span>

<span data-ttu-id="73c55-326">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="73c55-326">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="73c55-327">`Action`) e todas as sobrecargas relacionadas são baseadas na ideia de que você deseja especificar ao que está vinculando, especificando um nome do controlador e um nome da ação.</span><span class="sxs-lookup"><span data-stu-id="73c55-327">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="73c55-328">Ao usar `Url.Action`, os valores de rota atuais para `controller` e `action` são especificados para você – o valor de `controller` e `action` fazem parte de *valores de ambiente* **e** de *valores*.</span><span class="sxs-lookup"><span data-stu-id="73c55-328">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="73c55-329">O método `Url.Action` sempre usa os valores atuais de `action` e `controller` e gera um caminho de URL que roteia para a ação atual.</span><span class="sxs-lookup"><span data-stu-id="73c55-329">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="73c55-330">O roteamento tenta usar os valores em valores de ambiente para preencher informações que você não forneceu ao gerar uma URL.</span><span class="sxs-lookup"><span data-stu-id="73c55-330">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="73c55-331">Usando uma rota como `{a}/{b}/{c}/{d}` e valores de ambiente `{ a = Alice, b = Bob, c = Carol, d = David }`, o roteamento tem informações suficientes para gerar uma URL sem valores adicionais – uma vez que todos os parâmetros de rota têm um valor.</span><span class="sxs-lookup"><span data-stu-id="73c55-331">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="73c55-332">Se você tiver adicionado o valor `{ d = Donovan }`, o valor `{ d = David }` será ignorado e o caminho de URL gerado será `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="73c55-332">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="73c55-333">Caminhos de URL são hierárquicos.</span><span class="sxs-lookup"><span data-stu-id="73c55-333">URL paths are hierarchical.</span></span> <span data-ttu-id="73c55-334">No exemplo acima, se você tiver adicionado o valor `{ c = Cheryl }`, ambos os valores `{ c = Carol, d = David }` serão ignorados.</span><span class="sxs-lookup"><span data-stu-id="73c55-334">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="73c55-335">Nesse caso, não teremos mais um valor para `d` e a geração de URL falhará.</span><span class="sxs-lookup"><span data-stu-id="73c55-335">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="73c55-336">Você precisaria especificar o valor desejado de `c` e `d`.</span><span class="sxs-lookup"><span data-stu-id="73c55-336">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="73c55-337">Você pode esperar se deparar com esse problema com a rota padrão (`{controller}/{action}/{id?}`) – mas raramente encontrará esse comportamento na prática, pois `Url.Action` sempre especificará explicitamente um valor de `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="73c55-337">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="73c55-338">Sobrecargas maiores de `Url.Action` também usam um objeto adicional de *valores de rota* para fornecer valores para parâmetros de rota diferentes de `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="73c55-338">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="73c55-339">É mais comum ver isso com `id` como `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="73c55-339">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="73c55-340">Por convenção, o objeto de *valores de rota* geralmente é um objeto de tipo anônimo, mas também pode ser um `IDictionary<>` ou um *objeto .NET simples*.</span><span class="sxs-lookup"><span data-stu-id="73c55-340">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="73c55-341">Qualquer valor de rota adicional que não corresponder aos parâmetros de rota será colocado na cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="73c55-341">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/sample/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="73c55-342">Para criar uma URL absoluta, use uma sobrecarga que aceita um `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="73c55-342">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="73c55-343">Gerando URLs pela rota</span><span class="sxs-lookup"><span data-stu-id="73c55-343">Generating URLs by route</span></span>

<span data-ttu-id="73c55-344">O código acima demonstrou a geração de uma URL passando o nome do controlador e da ação.</span><span class="sxs-lookup"><span data-stu-id="73c55-344">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="73c55-345">`IUrlHelper` também fornece a família de métodos `Url.RouteUrl`.</span><span class="sxs-lookup"><span data-stu-id="73c55-345">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="73c55-346">Esses métodos são semelhantes a `Url.Action`, mas não copiam os valores atuais de `action` e `controller` para os valores de rota.</span><span class="sxs-lookup"><span data-stu-id="73c55-346">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="73c55-347">O uso mais comum é especificar um nome de rota para usar uma rota específica para gerar a URL, geralmente *sem* especificar um nome de controlador ou de ação.</span><span class="sxs-lookup"><span data-stu-id="73c55-347">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/sample/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="73c55-348">Gerar URLs em HTML</span><span class="sxs-lookup"><span data-stu-id="73c55-348">Generating URLs in HTML</span></span>

<span data-ttu-id="73c55-349">`IHtmlHelper` fornece o métodos `Html.BeginForm` e `Html.ActionLink` de `HtmlHelper` para gerar elementos `<form>` e `<a>` respectivamente.</span><span class="sxs-lookup"><span data-stu-id="73c55-349">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="73c55-350">Esses métodos usam o método `Url.Action` para gerar uma URL e aceitam argumentos semelhantes.</span><span class="sxs-lookup"><span data-stu-id="73c55-350">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="73c55-351">O complementos `Url.RouteUrl` para `HtmlHelper` são `Html.BeginRouteForm` e `Html.RouteLink`, que têm uma funcionalidade semelhante.</span><span class="sxs-lookup"><span data-stu-id="73c55-351">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="73c55-352">TagHelpers geram URLs por meio do TagHelper `form` e do TagHelper `<a>`.</span><span class="sxs-lookup"><span data-stu-id="73c55-352">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="73c55-353">Ambos usam `IUrlHelper` para sua implementação.</span><span class="sxs-lookup"><span data-stu-id="73c55-353">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="73c55-354">Consulte [Trabalhando com Formulários](../views/working-with-forms.md) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="73c55-354">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="73c55-355">Nos modos de exibição, o `IUrlHelper` está disponível por meio da propriedade `Url` para qualquer geração de URL ad hoc não abordada acima.</span><span class="sxs-lookup"><span data-stu-id="73c55-355">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="73c55-356">Gerando URLS nos resultados da ação</span><span class="sxs-lookup"><span data-stu-id="73c55-356">Generating URLS in Action Results</span></span>

<span data-ttu-id="73c55-357">Os exemplos acima mostraram o uso de `IUrlHelper` em um controlador, enquanto o uso mais comum em um controlador é gerar uma URL como parte do resultado de uma ação.</span><span class="sxs-lookup"><span data-stu-id="73c55-357">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="73c55-358">As classes base `ControllerBase` e `Controller` fornecem métodos de conveniência para resultados de ação que fazem referência a outra ação.</span><span class="sxs-lookup"><span data-stu-id="73c55-358">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="73c55-359">Um uso típico é para redirecionar após aceitar a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="73c55-359">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="73c55-360">Os métodos de fábrica dos resultados da ação seguem um padrão semelhante aos métodos em `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="73c55-360">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="73c55-361">Caso especial para rotas convencionais dedicadas</span><span class="sxs-lookup"><span data-stu-id="73c55-361">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="73c55-362">O roteamento convencional pode usar um tipo especial de definição de rota chamado *rota convencional dedicada*.</span><span class="sxs-lookup"><span data-stu-id="73c55-362">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="73c55-363">No exemplo a seguir, a rota chamada `blog` é uma rota convencional dedicada.</span><span class="sxs-lookup"><span data-stu-id="73c55-363">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="73c55-364">Usando essas definições de rota, `Url.Action("Index", "Home")` gerará o caminho de URL `/` com a rota `default`, mas por quê?</span><span class="sxs-lookup"><span data-stu-id="73c55-364">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="73c55-365">Você poderia imaginar que os valores de rota `{ controller = Home, action = Index }` seriam suficientes para gerar uma URL usando `blog` e o resultado seria `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="73c55-365">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="73c55-366">Rotas convencionais dedicadas dependem de um comportamento especial de valores padrão que não têm um parâmetro de rota correspondente que impeça que a rota seja "muito ambiciosa" com a geração de URLs.</span><span class="sxs-lookup"><span data-stu-id="73c55-366">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="73c55-367">Nesse caso, os valores padrão são `{ controller = Blog, action = Article }` e nem `controller` ou `action` aparece como um parâmetro de rota.</span><span class="sxs-lookup"><span data-stu-id="73c55-367">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="73c55-368">Quando o roteamento executa a geração de URL, os valores fornecidos devem corresponder aos valores padrão.</span><span class="sxs-lookup"><span data-stu-id="73c55-368">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="73c55-369">A geração de URL usando `blog` falhará porque os valores de `{ controller = Home, action = Index }` não correspondem a `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="73c55-369">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="73c55-370">O roteamento, então, faz o fallback para tentar `default`, que é bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="73c55-370">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="73c55-371">Áreas</span><span class="sxs-lookup"><span data-stu-id="73c55-371">Areas</span></span>

<span data-ttu-id="73c55-372">[Áreas](areas.md) são um recurso do MVC usado para organizar funcionalidades relacionadas em um grupo como um namespace de roteamento (para ações do controlador) e estrutura de pasta (para exibições) separada.</span><span class="sxs-lookup"><span data-stu-id="73c55-372">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="73c55-373">O uso de áreas permite que um aplicativo tenha vários controladores com o mesmo nome, desde que tenham *áreas* diferentes.</span><span class="sxs-lookup"><span data-stu-id="73c55-373">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="73c55-374">O uso de áreas cria uma hierarquia para fins de roteamento, adicionando outro parâmetro de rota, `area` a `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="73c55-374">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="73c55-375">Esta seção aborda como o roteamento interage com as áreas. Consulte [Áreas](areas.md) para obter detalhes sobre como as áreas são usadas com exibições.</span><span class="sxs-lookup"><span data-stu-id="73c55-375">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="73c55-376">O exemplo a seguir configura o MVC para usar a rota convencional padrão e uma *rota de área* para uma área chamada `Blog`:</span><span class="sxs-lookup"><span data-stu-id="73c55-376">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="73c55-377">Ao fazer a correspondência de um caminho de URL como `/Manage/Users/AddUser`, a primeira rota produzirá os valores de rota `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="73c55-377">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="73c55-378">O valor de rota `area` é produzido por um valor padrão para `area`. De fato, a rota criada por `MapAreaRoute` é equivalente à seguinte:</span><span class="sxs-lookup"><span data-stu-id="73c55-378">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="73c55-379">`MapAreaRoute` cria uma rota usando um valor padrão e a restrição para `area` usando o nome da área fornecido, nesse caso, `Blog`.</span><span class="sxs-lookup"><span data-stu-id="73c55-379">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="73c55-380">O valor padrão garante que a rota sempre produza `{ area = Blog, ... }`, a restrição requer o valor `{ area = Blog, ... }` para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="73c55-380">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="73c55-381">O roteamento convencional é dependente da ordem.</span><span class="sxs-lookup"><span data-stu-id="73c55-381">Conventional routing is order-dependent.</span></span> <span data-ttu-id="73c55-382">De modo geral, rotas com áreas devem ser colocadas mais no início na tabela de rotas, uma vez que são mais específicas que rotas sem uma área.</span><span class="sxs-lookup"><span data-stu-id="73c55-382">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="73c55-383">Usando o exemplo acima, os valores de rota corresponderiam à ação a seguir:</span><span class="sxs-lookup"><span data-stu-id="73c55-383">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="73c55-384">O `AreaAttribute` é o que indica que um controlador faz parte de uma área; dizemos que esse controlador está na área `Blog`.</span><span class="sxs-lookup"><span data-stu-id="73c55-384">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="73c55-385">Controladores sem um atributo `[Area]` não são membros de nenhuma área e **não** corresponderão quando o valor de rota `area` for fornecido pelo roteamento.</span><span class="sxs-lookup"><span data-stu-id="73c55-385">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="73c55-386">No exemplo a seguir, somente o primeiro controlador listado pode corresponder aos valores de rota `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="73c55-386">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/sample/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/sample/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="73c55-387">O namespace de cada controlador é mostrado aqui para fins de integridade – caso contrário, os controladores teriam um conflito de nomenclatura e gerariam um erro do compilador.</span><span class="sxs-lookup"><span data-stu-id="73c55-387">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="73c55-388">Namespaces de classe não têm efeito sobre o roteamento do MVC.</span><span class="sxs-lookup"><span data-stu-id="73c55-388">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="73c55-389">Os primeiros dois controladores são membros de áreas e correspondem somente quando seus respectivos nomes de área são fornecidos pelo valor de rota `area`.</span><span class="sxs-lookup"><span data-stu-id="73c55-389">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="73c55-390">O terceiro controlador não é um membro de nenhuma área e só pode corresponder quando nenhum valor para `area` for fornecido pelo roteamento.</span><span class="sxs-lookup"><span data-stu-id="73c55-390">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="73c55-391">Em termos de não corresponder a *nenhum valor*, a ausência do valor de `area` é equivalente ao valor de `area` ser nulo ou uma cadeia de caracteres vazia.</span><span class="sxs-lookup"><span data-stu-id="73c55-391">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="73c55-392">Ao executar uma ação dentro de uma área, o valor de rota para `area` estará disponível como um *valor de ambiente* para o roteamento usar para geração de URL.</span><span class="sxs-lookup"><span data-stu-id="73c55-392">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="73c55-393">Isso significa que, por padrão, as áreas atuam como se fossem *autoadesivas* para a geração de URL, como demonstrado no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="73c55-393">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/sample/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/sample/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="73c55-394">Entendendo IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="73c55-394">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="73c55-395">Esta seção é uma análise aprofundada dos elementos internos da estrutura e de como o MVC escolhe uma ação para ser executada.</span><span class="sxs-lookup"><span data-stu-id="73c55-395">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="73c55-396">Um aplicativo típico não precisará de um `IActionConstraint` personalizado</span><span class="sxs-lookup"><span data-stu-id="73c55-396">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="73c55-397">Provavelmente, você já usou `IActionConstraint` mesmo que não esteja familiarizado com a interface.</span><span class="sxs-lookup"><span data-stu-id="73c55-397">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="73c55-398">O atributo `[HttpGet]` e atributos `[Http-VERB]` semelhantes implementam `IActionConstraint` para limitar a execução de um método de ação.</span><span class="sxs-lookup"><span data-stu-id="73c55-398">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="73c55-399">Presumindo a rota convencional padrão, o caminho de URL `/Products/Edit` produziria os valores `{ controller = Products, action = Edit }`, que corresponderiam a **ambas** as ações mostradas aqui.</span><span class="sxs-lookup"><span data-stu-id="73c55-399">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="73c55-400">Na terminologia `IActionConstraint`, diríamos que essas duas ações são consideradas candidatas – uma vez que ambas correspondem aos dados da rota.</span><span class="sxs-lookup"><span data-stu-id="73c55-400">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="73c55-401">Quando for executado, `HttpGetAttribute` indicará que *Edit()* corresponde a *GET* e não corresponde a nenhum outro verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="73c55-401">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="73c55-402">A ação `Edit(...)` não tem restrições definidas e, portanto, corresponderá a qualquer verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="73c55-402">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="73c55-403">Sendo assim, supondo um `POST`, `Edit(...)` será correspondente.</span><span class="sxs-lookup"><span data-stu-id="73c55-403">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="73c55-404">Mas, para um `GET`, ambas as ações ainda podem corresponder – no entanto, uma ação com um `IActionConstraint` sempre é considerada *melhor* que uma ação sem.</span><span class="sxs-lookup"><span data-stu-id="73c55-404">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="73c55-405">Assim, como `Edit()` tem `[HttpGet]`, ela é considerada mais específica e será selecionada se as duas ações puderem corresponder.</span><span class="sxs-lookup"><span data-stu-id="73c55-405">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="73c55-406">Conceitualmente, `IActionConstraint` é uma forma de *sobrecarga*, mas em vez de uma sobrecarga de métodos com o mesmo nome, trata-se da sobrecarga entre ações que correspondem à mesma URL.</span><span class="sxs-lookup"><span data-stu-id="73c55-406">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="73c55-407">O roteamento de atributo também usa `IActionConstraint` e pode fazer com que ações de controladores diferentes sejam consideradas candidatas.</span><span class="sxs-lookup"><span data-stu-id="73c55-407">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="73c55-408">Implementando IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="73c55-408">Implementing IActionConstraint</span></span>

<span data-ttu-id="73c55-409">A maneira mais simples de implementar um `IActionConstraint` é criar uma classe derivada de `System.Attribute` e colocá-la em suas ações e controladores.</span><span class="sxs-lookup"><span data-stu-id="73c55-409">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="73c55-410">O MVC descobrirá automaticamente qualquer `IActionConstraint` que for aplicado como atributo.</span><span class="sxs-lookup"><span data-stu-id="73c55-410">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="73c55-411">Você pode usar o modelo de aplicativo para aplicar restrições e, provavelmente, essa é a abordagem mais flexível, pois permite a você faça uma metaprogramação de como elas são aplicadas.</span><span class="sxs-lookup"><span data-stu-id="73c55-411">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="73c55-412">No exemplo a seguir, uma restrição escolhe uma ação com base em um *código de país* dos dados de rota.</span><span class="sxs-lookup"><span data-stu-id="73c55-412">In the following example a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="73c55-413">O [exemplo completo no GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="73c55-413">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="73c55-414">Você é responsável por implementar o método `Accept` e por escolher uma "ordem" na qual a restrição deve ser executada.</span><span class="sxs-lookup"><span data-stu-id="73c55-414">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="73c55-415">Nesse caso, o método `Accept` retorna `true` para indicar que a ação é correspondente quando o valor de rota `country` é correspondente.</span><span class="sxs-lookup"><span data-stu-id="73c55-415">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="73c55-416">Isso é diferente de um `RouteValueAttribute`, pois permite o fallback para uma ação não atribuída.</span><span class="sxs-lookup"><span data-stu-id="73c55-416">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="73c55-417">O exemplo mostra que se você definir uma ação `en-US`, um código de país como `fr-FR` fará o fallback para um controlador mais genérico que não tem `[CountrySpecific(...)]` aplicado.</span><span class="sxs-lookup"><span data-stu-id="73c55-417">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="73c55-418">A propriedade `Order` decide de qual *estágio* a restrição faz parte.</span><span class="sxs-lookup"><span data-stu-id="73c55-418">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="73c55-419">Restrições de ação são executadas em grupos com base no `Order`.</span><span class="sxs-lookup"><span data-stu-id="73c55-419">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="73c55-420">Por exemplo, todos atributos de método HTTP fornecidos pela estrutura usam o mesmo valor de `Order` para que sejam executados no mesmo estágio.</span><span class="sxs-lookup"><span data-stu-id="73c55-420">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="73c55-421">Você pode ter tantos estágios quantos forem necessários para implementar suas políticas desejadas.</span><span class="sxs-lookup"><span data-stu-id="73c55-421">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="73c55-422">Para decidir o valor para `Order`, considere se sua restrição deve ou não deve ser aplicada antes de métodos HTTP.</span><span class="sxs-lookup"><span data-stu-id="73c55-422">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="73c55-423">Números inferiores são executados primeiro.</span><span class="sxs-lookup"><span data-stu-id="73c55-423">Lower numbers run first.</span></span>
