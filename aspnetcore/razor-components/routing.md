---
title: Componentes de roteamento do Razor
author: guardrex
description: Saiba como rotear solicitações em aplicativos e sobre o componente NavLink.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/routing
ms.openlocfilehash: ef82fa7e0d571979a43fd8ce712bf4f22c06f9a7
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59515371"
---
# <a name="razor-components-routing"></a><span data-ttu-id="d2424-103">Componentes de roteamento do Razor</span><span class="sxs-lookup"><span data-stu-id="d2424-103">Razor Components routing</span></span>

<span data-ttu-id="d2424-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d2424-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d2424-105">Saiba como rotear solicitações em aplicativos e sobre o componente NavLink.</span><span class="sxs-lookup"><span data-stu-id="d2424-105">Learn how to route requests in apps and about the NavLink component.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="d2424-106">Integração de roteamento de ponto de extremidade do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d2424-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="d2424-107">Componentes do Razor são integradas ao [roteamento do ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="d2424-107">Razor Components are integrated into [ASP.NET Core routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="d2424-108">Um aplicativo ASP.NET Core está configurado para aceitar conexões de entrada para componentes interativos do Razor com `MapComponentHub<TComponent>` em `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="d2424-108">An ASP.NET Core app is configured to accept incoming connections for interactive Razor Components with `MapComponentHub<TComponent>` in `Startup.Configure`.</span></span> `MapComponentHub` <span data-ttu-id="d2424-109">Especifica que o componente raiz `App` deve ser renderizada dentro de um elemento DOM correspondente no seletor de `app`:</span><span class="sxs-lookup"><span data-stu-id="d2424-109">specifies that the root component `App` should be rendered within a DOM element matching the selector `app`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapComponentHub<App>("app");
});
```

## <a name="route-templates"></a><span data-ttu-id="d2424-110">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="d2424-110">Route templates</span></span>

<span data-ttu-id="d2424-111">O `<Router>` habilita o componente de roteamento e um modelo de rota é fornecido para cada componente acessível.</span><span class="sxs-lookup"><span data-stu-id="d2424-111">The `<Router>` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="d2424-112">O `<Router>` componente aparece na *Components/App.razor* arquivo:</span><span class="sxs-lookup"><span data-stu-id="d2424-112">The `<Router>` component appears in the *Components/App.razor* file:</span></span>

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

<span data-ttu-id="d2424-113">Quando um *.razor* ou *. cshtml* arquivo com um `@page` diretiva é compilada, a classe gerada recebe um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="d2424-113">When a *.razor* or *.cshtml* file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="d2424-114">Em tempo de execução, o roteador procura por classes de componente com um `RouteAttribute` e renderiza a qualquer componente tem um modelo de rota que corresponde à URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="d2424-114">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="d2424-115">Vários modelos de rota podem ser aplicados a um componente.</span><span class="sxs-lookup"><span data-stu-id="d2424-115">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="d2424-116">O componente a seguir responde a solicitações para `/BlazorRoute` e `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="d2424-116">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?name=snippet_BlazorRoute)]

`<Router>` <span data-ttu-id="d2424-117">dá suporte à configuração de um componente de fallback para a renderização quando uma rota solicitada não é resolvido.</span><span class="sxs-lookup"><span data-stu-id="d2424-117">supports setting a fallback component for rendering when a requested route isn't resolved.</span></span> <span data-ttu-id="d2424-118">Habilitar esse cenário consentir, definindo o `FallbackComponent` parâmetro para o tipo da classe de componente de fallback.</span><span class="sxs-lookup"><span data-stu-id="d2424-118">Enable this opt-in scenario by setting the `FallbackComponent` parameter to the type of the fallback component class.</span></span>

<span data-ttu-id="d2424-119">O exemplo a seguir define um componente definido no *Pages/MyFallbackRazorComponent.cshtml* como o componente de fallback para um `<Router>`:</span><span class="sxs-lookup"><span data-stu-id="d2424-119">The following example sets a component defined in *Pages/MyFallbackRazorComponent.cshtml* as the fallback component for a `<Router>`:</span></span>

```cshtml
<Router ... FallbackComponent="typeof(Pages.MyFallbackRazorComponent)" />
```

> [!IMPORTANT]
> <span data-ttu-id="d2424-120">Para gerar rotas corretamente, o aplicativo deve incluir um `<base>` marcar no seu *wwwroot/index.html* arquivo com o caminho base do aplicativo especificado no `href` atributo (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="d2424-120">To generate routes properly, the app must include a `<base>` tag in its *wwwroot/index.html* file with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="d2424-121">Para obter mais informações, consulte <xref:host-and-deploy/razor-components-blazor/blazor#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="d2424-121">For more information, see <xref:host-and-deploy/razor-components-blazor/blazor#app-base-path>.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="d2424-122">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="d2424-122">Route parameters</span></span>

<span data-ttu-id="d2424-123">O roteador usar parâmetros de rota para preencher os parâmetros correspondentes do componente com o mesmo nome (diferencia maiusculas de minúsculas):</span><span class="sxs-lookup"><span data-stu-id="d2424-123">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="d2424-124">Parâmetros opcionais não têm suporte ainda, portanto, dois `@page` diretivas são aplicadas no exemplo acima.</span><span class="sxs-lookup"><span data-stu-id="d2424-124">Optional parameters aren't supported yet, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="d2424-125">A primeira permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d2424-125">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="d2424-126">A segunda `@page` diretiva leva a `{text}` parâmetro de rota e atribui o valor para o `Text` propriedade.</span><span class="sxs-lookup"><span data-stu-id="d2424-126">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="d2424-127">Restrições de rota</span><span class="sxs-lookup"><span data-stu-id="d2424-127">Route constraints</span></span>

<span data-ttu-id="d2424-128">Uma restrição de rota impõe o tipo correspondente em um segmento de rota para um componente.</span><span class="sxs-lookup"><span data-stu-id="d2424-128">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="d2424-129">No exemplo a seguir, a rota para o componente de usuários corresponde apenas se:</span><span class="sxs-lookup"><span data-stu-id="d2424-129">In the following example, the route to the Users component only matches if:</span></span>

* <span data-ttu-id="d2424-130">Um `Id` segmento de rota está presente na URL de solicitação.</span><span class="sxs-lookup"><span data-stu-id="d2424-130">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="d2424-131">O `Id` segmento é um inteiro (`int`).</span><span class="sxs-lookup"><span data-stu-id="d2424-131">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.cshtml?highlight=1)]

<span data-ttu-id="d2424-132">As restrições da rota mostradas na tabela a seguir estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="d2424-132">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="d2424-133">Para as restrições da rota que coincidirem com a cultura invariável, consulte o aviso abaixo da tabela para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d2424-133">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="d2424-134">Restrição</span><span class="sxs-lookup"><span data-stu-id="d2424-134">Constraint</span></span> | <span data-ttu-id="d2424-135">Exemplo</span><span class="sxs-lookup"><span data-stu-id="d2424-135">Example</span></span>           | <span data-ttu-id="d2424-136">Correspondências de exemplo</span><span class="sxs-lookup"><span data-stu-id="d2424-136">Example Matches</span></span>                                                                  | <span data-ttu-id="d2424-137">Constante</span><span class="sxs-lookup"><span data-stu-id="d2424-137">Invariant</span></span><br><span data-ttu-id="d2424-138">cultura</span><span class="sxs-lookup"><span data-stu-id="d2424-138">culture</span></span><br><span data-ttu-id="d2424-139">correspondência</span><span class="sxs-lookup"><span data-stu-id="d2424-139">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`<span data-ttu-id="d2424-140">,</span><span class="sxs-lookup"><span data-stu-id="d2424-140">,</span></span> `FALSE`                                                                  | <span data-ttu-id="d2424-141">Não</span><span class="sxs-lookup"><span data-stu-id="d2424-141">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`<span data-ttu-id="d2424-142">,</span><span class="sxs-lookup"><span data-stu-id="d2424-142">,</span></span> `2016-12-31 7:32pm`                                                | <span data-ttu-id="d2424-143">Sim</span><span class="sxs-lookup"><span data-stu-id="d2424-143">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | `49.99`<span data-ttu-id="d2424-144">,</span><span class="sxs-lookup"><span data-stu-id="d2424-144">,</span></span> `-1,000.01`                                                             | <span data-ttu-id="d2424-145">Sim</span><span class="sxs-lookup"><span data-stu-id="d2424-145">Yes</span></span>                              |
| `double`   | `{weight:double}` | `1.234`<span data-ttu-id="d2424-146">,</span><span class="sxs-lookup"><span data-stu-id="d2424-146">,</span></span> `-1,001.01e8`                                                           | <span data-ttu-id="d2424-147">Sim</span><span class="sxs-lookup"><span data-stu-id="d2424-147">Yes</span></span>                              |
| `float`    | `{weight:float}`  | `1.234`<span data-ttu-id="d2424-148">,</span><span class="sxs-lookup"><span data-stu-id="d2424-148">,</span></span> `-1,001.01e8`                                                           | <span data-ttu-id="d2424-149">Sim</span><span class="sxs-lookup"><span data-stu-id="d2424-149">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`<span data-ttu-id="d2424-150">,</span><span class="sxs-lookup"><span data-stu-id="d2424-150">,</span></span> `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | <span data-ttu-id="d2424-151">Não</span><span class="sxs-lookup"><span data-stu-id="d2424-151">No</span></span>                               |
| `int`      | `{id:int}`        | `123456789`<span data-ttu-id="d2424-152">,</span><span class="sxs-lookup"><span data-stu-id="d2424-152">,</span></span> `-123456789`                                                        | <span data-ttu-id="d2424-153">Sim</span><span class="sxs-lookup"><span data-stu-id="d2424-153">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | `123456789`<span data-ttu-id="d2424-154">,</span><span class="sxs-lookup"><span data-stu-id="d2424-154">,</span></span> `-123456789`                                                        | <span data-ttu-id="d2424-155">Sim</span><span class="sxs-lookup"><span data-stu-id="d2424-155">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="d2424-156">As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou `DateTime`) sempre usam a cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="d2424-156">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="d2424-157">Essas restrições consideram que a URL não é localizável.</span><span class="sxs-lookup"><span data-stu-id="d2424-157">These constraints assume that the URL is non-localizable.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="d2424-158">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="d2424-158">NavLink component</span></span>

<span data-ttu-id="d2424-159">Usar um componente NavLink no lugar de HTML `<a>` elementos durante a criação de links de navegação.</span><span class="sxs-lookup"><span data-stu-id="d2424-159">Use a NavLink component in place of HTML `<a>` elements when creating navigation links.</span></span> <span data-ttu-id="d2424-160">Um componente NavLink se comporta como uma `<a>` elemento, exceto que ele é alternado uma `active` classe CSS com base na possibilidade seu `href` corresponde à URL atual.</span><span class="sxs-lookup"><span data-stu-id="d2424-160">A NavLink component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="d2424-161">O `active` classe ajuda um usuário a entender qual página é a página ativa entre os links de navegação exibidos.</span><span class="sxs-lookup"><span data-stu-id="d2424-161">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="d2424-162">O componente de NavMenu a seguir cria uma [Bootstrap](https://getbootstrap.com/docs/) barra de navegação que demonstra como usar componentes NavLink:</span><span class="sxs-lookup"><span data-stu-id="d2424-162">The following NavMenu component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use NavLink components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Shared/NavMenu.cshtml?name=snippet_NavLinks&highlight=4-6,9-11)]

<span data-ttu-id="d2424-163">Há dois `NavLinkMatch` opções:</span><span class="sxs-lookup"><span data-stu-id="d2424-163">There are two `NavLinkMatch` options:</span></span>

* `NavLinkMatch.All` <span data-ttu-id="d2424-164">&ndash; Especifica que o NavLink deve estar ativo quando ele corresponder a toda a URL atual.</span><span class="sxs-lookup"><span data-stu-id="d2424-164">&ndash; Specifies that the NavLink should be active when it matches the entire current URL.</span></span>
* `NavLinkMatch.Prefix` <span data-ttu-id="d2424-165">&ndash; Especifica que o NavLink deve estar ativo quando há correspondência com qualquer prefixo da URL atual.</span><span class="sxs-lookup"><span data-stu-id="d2424-165">&ndash; Specifies that the NavLink should be active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="d2424-166">No exemplo anterior, NavLink Home (`href=""`) corresponde a todas as URLs e sempre recebe o `active` classe CSS.</span><span class="sxs-lookup"><span data-stu-id="d2424-166">In the preceding example, the Home NavLink (`href=""`) matches all URLs and always receives the `active` CSS class.</span></span> <span data-ttu-id="d2424-167">O segundo NavLink recebe apenas a `active` classe quando o usuário visitar o componente de rota Blazor (`href="BlazorRoute"`).</span><span class="sxs-lookup"><span data-stu-id="d2424-167">The second NavLink only receives the `active` class when the user visits the Blazor Route component (`href="BlazorRoute"`).</span></span>
