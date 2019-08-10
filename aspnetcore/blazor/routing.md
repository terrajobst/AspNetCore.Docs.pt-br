---
title: Roteamento de ASP.NET Core mais
author: guardrex
description: Saiba como rotear solicitações em aplicativos e sobre o componente NavLink.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/routing
ms.openlocfilehash: 70cae6b3a21fe3537d6841a6716398a5fc45db62
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68948306"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="d72f4-103">Roteamento de ASP.NET Core mais</span><span class="sxs-lookup"><span data-stu-id="d72f4-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="d72f4-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d72f4-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d72f4-105">Saiba como rotear solicitações e como usar o `NavLink` componente para criar links de navegação em aplicativos mais incrivelmenteos.</span><span class="sxs-lookup"><span data-stu-id="d72f4-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="d72f4-106">Integração de roteamento de ponto de extremidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d72f4-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="d72f4-107">O lado do servidor mais incrivelmente é integrado ao [Roteamento de ponto de extremidade ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="d72f4-107">Blazor server-side is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="d72f4-108">Um aplicativo ASP.NET Core está configurado para aceitar conexões de entrada para componentes interativos com `MapBlazorHub` o no: `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="d72f4-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="d72f4-109">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="d72f4-109">Route templates</span></span>

<span data-ttu-id="d72f4-110">O `Router` componente habilita o roteamento e um modelo de rota é fornecido para cada componente acessível.</span><span class="sxs-lookup"><span data-stu-id="d72f4-110">The `Router` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="d72f4-111">O `Router` componente aparece no arquivo *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="d72f4-111">The `Router` component appears in the *App.razor* file:</span></span>

<span data-ttu-id="d72f4-112">Em um aplicativo do lado do servidor mais incrivelmente:</span><span class="sxs-lookup"><span data-stu-id="d72f4-112">In a Blazor server-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly" />
```

<span data-ttu-id="d72f4-113">Em um aplicativo do lado do cliente mais incrivelmente:</span><span class="sxs-lookup"><span data-stu-id="d72f4-113">In a Blazor client-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Program).Assembly" />
```

<span data-ttu-id="d72f4-114">Quando um arquivo *. Razor* com uma `@page` diretiva é compilado, a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> classe gerada é fornecida especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="d72f4-114">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="d72f4-115">Em tempo de execução, o roteador procura classes de componentes `RouteAttribute` com um e renderiza o componente com um modelo de rota que corresponde à URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="d72f4-115">At runtime, the router looks for component classes with a `RouteAttribute` and renders the component with a route template that matches the requested URL.</span></span>

<span data-ttu-id="d72f4-116">Vários modelos de rota podem ser aplicados a um componente.</span><span class="sxs-lookup"><span data-stu-id="d72f4-116">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="d72f4-117">O componente a seguir responde a solicitações `/BlazorRoute` para `/DifferentBlazorRoute`o e o:</span><span class="sxs-lookup"><span data-stu-id="d72f4-117">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="d72f4-118">Para gerar rotas corretamente, o aplicativo deve incluir uma `<base>` marca em seu arquivo *wwwroot/index.html* (mais de um lado do cliente) ou no arquivo *pages/_Host. cshtml* (no lado do servidor) com o caminho base do `href` aplicativo especificado no atributo ( `<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="d72f4-118">To generate routes properly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor client-side) or *Pages/_Host.cshtml* file (Blazor server-side) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="d72f4-119">Para obter mais informações, consulte <xref:host-and-deploy/blazor/client-side#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="d72f4-119">For more information, see <xref:host-and-deploy/blazor/client-side#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="d72f4-120">Fornecer conteúdo personalizado quando o conteúdo não for encontrado</span><span class="sxs-lookup"><span data-stu-id="d72f4-120">Provide custom content when content isn't found</span></span>

<span data-ttu-id="d72f4-121">O `Router` componente permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.</span><span class="sxs-lookup"><span data-stu-id="d72f4-121">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="d72f4-122">No arquivo *app. Razor* , defina conteúdo personalizado no `<NotFoundContent>` elemento do `Router` componente:</span><span class="sxs-lookup"><span data-stu-id="d72f4-122">In the *App.razor* file, set custom content in the `<NotFoundContent>` element of the `Router` component:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <NotFoundContent>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFoundContent>
</Router>
```

<span data-ttu-id="d72f4-123">O conteúdo de `<NotFoundContent>` pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="d72f4-123">The content of `<NotFoundContent>` can include arbitrary items, such as other interactive components.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="d72f4-124">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="d72f4-124">Route parameters</span></span>

<span data-ttu-id="d72f4-125">O roteador usa parâmetros de rota para popular os parâmetros de componente correspondentes com o mesmo nome (não diferencia maiúsculas de minúsculas):</span><span class="sxs-lookup"><span data-stu-id="d72f4-125">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="d72f4-126">Os parâmetros opcionais não têm suporte para aplicativos mais na versão prévia do ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="d72f4-126">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="d72f4-127">Duas `@page` diretivas são aplicadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="d72f4-127">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="d72f4-128">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="d72f4-128">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="d72f4-129">A segunda `@page` diretiva usa o `{text}` parâmetro de rota e atribui o valor à `Text` propriedade.</span><span class="sxs-lookup"><span data-stu-id="d72f4-129">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="d72f4-130">Restrições de rota</span><span class="sxs-lookup"><span data-stu-id="d72f4-130">Route constraints</span></span>

<span data-ttu-id="d72f4-131">Uma restrição de rota impõe a correspondência de tipo em um segmento de rota para um componente.</span><span class="sxs-lookup"><span data-stu-id="d72f4-131">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="d72f4-132">No exemplo a seguir, a rota para o `Users` componente só corresponde se:</span><span class="sxs-lookup"><span data-stu-id="d72f4-132">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="d72f4-133">Um `Id` segmento de rota está presente na URL da solicitação.</span><span class="sxs-lookup"><span data-stu-id="d72f4-133">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="d72f4-134">O `Id` segmento é um inteiro (`int`).</span><span class="sxs-lookup"><span data-stu-id="d72f4-134">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="d72f4-135">As restrições de rota mostradas na tabela a seguir estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="d72f4-135">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="d72f4-136">Para as restrições de rota que correspondem à cultura invariável, consulte o aviso abaixo da tabela para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="d72f4-136">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="d72f4-137">Restrição</span><span class="sxs-lookup"><span data-stu-id="d72f4-137">Constraint</span></span> | <span data-ttu-id="d72f4-138">Exemplo</span><span class="sxs-lookup"><span data-stu-id="d72f4-138">Example</span></span>           | <span data-ttu-id="d72f4-139">Correspondências de exemplo</span><span class="sxs-lookup"><span data-stu-id="d72f4-139">Example Matches</span></span>                                                                  | <span data-ttu-id="d72f4-140">Constante</span><span class="sxs-lookup"><span data-stu-id="d72f4-140">Invariant</span></span><br><span data-ttu-id="d72f4-141">cultura</span><span class="sxs-lookup"><span data-stu-id="d72f4-141">culture</span></span><br><span data-ttu-id="d72f4-142">correspondência</span><span class="sxs-lookup"><span data-stu-id="d72f4-142">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="d72f4-143">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="d72f4-143">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="d72f4-144">Não</span><span class="sxs-lookup"><span data-stu-id="d72f4-144">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="d72f4-145">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="d72f4-145">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="d72f4-146">Sim</span><span class="sxs-lookup"><span data-stu-id="d72f4-146">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="d72f4-147">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="d72f4-147">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="d72f4-148">Sim</span><span class="sxs-lookup"><span data-stu-id="d72f4-148">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="d72f4-149">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="d72f4-149">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="d72f4-150">Sim</span><span class="sxs-lookup"><span data-stu-id="d72f4-150">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="d72f4-151">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="d72f4-151">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="d72f4-152">Sim</span><span class="sxs-lookup"><span data-stu-id="d72f4-152">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="d72f4-153">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="d72f4-153">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="d72f4-154">Não</span><span class="sxs-lookup"><span data-stu-id="d72f4-154">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="d72f4-155">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="d72f4-155">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="d72f4-156">Sim</span><span class="sxs-lookup"><span data-stu-id="d72f4-156">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="d72f4-157">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="d72f4-157">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="d72f4-158">Sim</span><span class="sxs-lookup"><span data-stu-id="d72f4-158">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="d72f4-159">As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou `DateTime`) sempre usam a cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="d72f4-159">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="d72f4-160">Essas restrições consideram que a URL não é localizável.</span><span class="sxs-lookup"><span data-stu-id="d72f4-160">These constraints assume that the URL is non-localizable.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="d72f4-161">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="d72f4-161">NavLink component</span></span>

<span data-ttu-id="d72f4-162">Use um `NavLink` componente no lugar de elementos de hiperlink HTML`<a>`() ao criar links de navegação.</span><span class="sxs-lookup"><span data-stu-id="d72f4-162">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="d72f4-163">Um `NavLink` componente se comporta como um `<a>` elemento, exceto que alterna uma `active` classe CSS com base no fato de sua `href` correspondência com a URL atual.</span><span class="sxs-lookup"><span data-stu-id="d72f4-163">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="d72f4-164">A `active` classe ajuda um usuário a entender qual página é a página ativa entre os links de navegação exibidos.</span><span class="sxs-lookup"><span data-stu-id="d72f4-164">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="d72f4-165">O componente `NavMenu` a seguir cria uma barra de navegação de [inicialização](https://getbootstrap.com/docs/) que demonstra `NavLink` como usar componentes:</span><span class="sxs-lookup"><span data-stu-id="d72f4-165">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="d72f4-166">Há duas `NavLinkMatch` opções que você pode atribuir `Match` ao atributo do `<NavLink>` elemento:</span><span class="sxs-lookup"><span data-stu-id="d72f4-166">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="d72f4-167">`NavLinkMatch.All`&ndash; O`NavLink` está ativo quando corresponde à URL atual inteira.</span><span class="sxs-lookup"><span data-stu-id="d72f4-167">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="d72f4-168">`NavLinkMatch.Prefix`(*padrão*) &ndash; O`NavLink` está ativo quando ele corresponde a qualquer prefixo da URL atual.</span><span class="sxs-lookup"><span data-stu-id="d72f4-168">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="d72f4-169">No exemplo anterior, a página inicial `NavLink` `href=""` corresponde à URL inicial e recebe apenas a `active` classe CSS na URL do caminho base padrão do aplicativo (por exemplo, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="d72f4-169">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="d72f4-170">O segundo `NavLink` recebe a `active` classe quando o usuário visita qualquer URL com um `MyComponent` prefixo (por exemplo, `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="d72f4-170">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="d72f4-171">Auxiliares de URI e estado de navegação</span><span class="sxs-lookup"><span data-stu-id="d72f4-171">URI and navigation state helpers</span></span>

<span data-ttu-id="d72f4-172">Use `Microsoft.AspNetCore.Components.IUriHelper` para trabalhar com URIs e navegação no C# código.</span><span class="sxs-lookup"><span data-stu-id="d72f4-172">Use `Microsoft.AspNetCore.Components.IUriHelper` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="d72f4-173">`IUriHelper`fornece o evento e os métodos mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="d72f4-173">`IUriHelper` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="d72f4-174">Membro</span><span class="sxs-lookup"><span data-stu-id="d72f4-174">Member</span></span> | <span data-ttu-id="d72f4-175">Descrição</span><span class="sxs-lookup"><span data-stu-id="d72f4-175">Description</span></span> |
| ------ | ----------- |
| `GetAbsoluteUri` | <span data-ttu-id="d72f4-176">Obtém o URI absoluto atual.</span><span class="sxs-lookup"><span data-stu-id="d72f4-176">Gets the current absolute URI.</span></span> |
| `GetBaseUri` | <span data-ttu-id="d72f4-177">Obtém o URI de base (com uma barra à direita) que pode ser anexado a caminhos de URI relativos para produzir um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="d72f4-177">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="d72f4-178">Normalmente, `GetBaseUri` corresponde `href` ao atributo no elemento `<base>` do documento em *wwwroot/index.html* (mais de um lado do cliente) ou *pages/_Host. cshtml* (no lado do servidor).</span><span class="sxs-lookup"><span data-stu-id="d72f4-178">Typically, `GetBaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor client-side) or *Pages/_Host.cshtml* (Blazor server-side).</span></span> |
| `NavigateTo` | <span data-ttu-id="d72f4-179">Navega para o URI especificado.</span><span class="sxs-lookup"><span data-stu-id="d72f4-179">Navigates to the specified URI.</span></span> <span data-ttu-id="d72f4-180">Se `forceLoad` for `true`:</span><span class="sxs-lookup"><span data-stu-id="d72f4-180">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="d72f4-181">O roteamento do lado do cliente é ignorado.</span><span class="sxs-lookup"><span data-stu-id="d72f4-181">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="d72f4-182">O navegador é forçado a carregar a nova página do servidor, seja ou não o URI normalmente manipulado pelo roteador do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="d72f4-182">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `OnLocationChanged` | <span data-ttu-id="d72f4-183">Um evento que é acionado quando o local de navegação é alterado.</span><span class="sxs-lookup"><span data-stu-id="d72f4-183">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="d72f4-184">Converte um URI relativo em um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="d72f4-184">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="d72f4-185">Dado um URI de base (por exemplo, um URI retornado anteriormente `GetBaseUri`pelo), converte um URI absoluto em um URI relativo ao prefixo de URI de base.</span><span class="sxs-lookup"><span data-stu-id="d72f4-185">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="d72f4-186">O componente a seguir navega para o componente do `Counter` aplicativo quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="d72f4-186">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```cshtml
@page "/navigate"
@using Microsoft.AspNetCore.Components
@inject IUriHelper UriHelper

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        UriHelper.NavigateTo("counter");
    }
}
```
