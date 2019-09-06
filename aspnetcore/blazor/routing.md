---
title: Roteamento de ASP.NET Core mais
author: guardrex
description: Saiba como rotear solicitações em aplicativos e sobre o componente NavLink.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/23/2019
uid: blazor/routing
ms.openlocfilehash: ae3d7ab01185dd6f2e8e0f59b78c2e693fe464b0
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310339"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="a7c38-103">Roteamento de ASP.NET Core mais</span><span class="sxs-lookup"><span data-stu-id="a7c38-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="a7c38-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a7c38-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a7c38-105">Saiba como rotear solicitações e como usar o `NavLink` componente para criar links de navegação em aplicativos mais incrivelmenteos.</span><span class="sxs-lookup"><span data-stu-id="a7c38-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="a7c38-106">Integração de roteamento de ponto de extremidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7c38-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="a7c38-107">O lado do servidor mais incrivelmente é integrado ao [Roteamento de ponto de extremidade ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="a7c38-107">Blazor server-side is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="a7c38-108">Um aplicativo ASP.NET Core está configurado para aceitar conexões de entrada para componentes interativos com `MapBlazorHub` o no: `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="a7c38-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="a7c38-109">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="a7c38-109">Route templates</span></span>

<span data-ttu-id="a7c38-110">O `Router` componente habilita o roteamento e um modelo de rota é fornecido para cada componente acessível.</span><span class="sxs-lookup"><span data-stu-id="a7c38-110">The `Router` component enables routing, and a route template is provided to each accessible component.</span></span> <span data-ttu-id="a7c38-111">O `Router` componente aparece no arquivo *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="a7c38-111">The `Router` component appears in the *App.razor* file:</span></span>

<span data-ttu-id="a7c38-112">Em um aplicativo mais alto do lado do servidor ou do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="a7c38-112">In a Blazor server-side or client-side app:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="a7c38-113">Quando um arquivo *. Razor* com uma `@page` diretiva é compilado, a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> classe gerada é fornecida especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="a7c38-113">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="a7c38-114">Em tempo de execução, o roteador procura classes de componentes `RouteAttribute` com um e renderiza o componente com um modelo de rota que corresponde à URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="a7c38-114">At runtime, the router looks for component classes with a `RouteAttribute` and renders the component with a route template that matches the requested URL.</span></span>

<span data-ttu-id="a7c38-115">Vários modelos de rota podem ser aplicados a um componente.</span><span class="sxs-lookup"><span data-stu-id="a7c38-115">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="a7c38-116">O componente a seguir responde a solicitações `/BlazorRoute` para `/DifferentBlazorRoute`o e o:</span><span class="sxs-lookup"><span data-stu-id="a7c38-116">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="a7c38-117">Para que as URLs sejam resolvidas corretamente, o aplicativo `<base>` deve incluir uma marca em seu arquivo *wwwroot/index.html* (mais de lado do cliente) ou no arquivo *pages/_Host. cshtml* (no lado do servidor) com o caminho base do `href` aplicativo especificado no atributo ( `<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="a7c38-117">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor client-side) or *Pages/_Host.cshtml* file (Blazor server-side) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="a7c38-118">Para obter mais informações, consulte <xref:host-and-deploy/blazor/client-side#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="a7c38-118">For more information, see <xref:host-and-deploy/blazor/client-side#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="a7c38-119">Fornecer conteúdo personalizado quando o conteúdo não for encontrado</span><span class="sxs-lookup"><span data-stu-id="a7c38-119">Provide custom content when content isn't found</span></span>

<span data-ttu-id="a7c38-120">O `Router` componente permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.</span><span class="sxs-lookup"><span data-stu-id="a7c38-120">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="a7c38-121">No arquivo *app. Razor* , defina conteúdo personalizado no `<NotFound>` parâmetro de `Router` modelo do componente:</span><span class="sxs-lookup"><span data-stu-id="a7c38-121">In the *App.razor* file, set custom content in the `<NotFound>` template parameter of the `Router` component:</span></span>

```cshtml
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="a7c38-122">O conteúdo de `<NotFound>` pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="a7c38-122">The content of `<NotFound>` can include arbitrary items, such as other interactive components.</span></span>

## <a name="route-parameters"></a><span data-ttu-id="a7c38-123">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="a7c38-123">Route parameters</span></span>

<span data-ttu-id="a7c38-124">O roteador usa parâmetros de rota para popular os parâmetros de componente correspondentes com o mesmo nome (não diferencia maiúsculas de minúsculas):</span><span class="sxs-lookup"><span data-stu-id="a7c38-124">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="a7c38-125">Os parâmetros opcionais não têm suporte para aplicativos mais na versão prévia do ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="a7c38-125">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="a7c38-126">Duas `@page` diretivas são aplicadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="a7c38-126">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="a7c38-127">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="a7c38-127">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="a7c38-128">A segunda `@page` diretiva usa o `{text}` parâmetro de rota e atribui o valor à `Text` propriedade.</span><span class="sxs-lookup"><span data-stu-id="a7c38-128">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="a7c38-129">Restrições de rota</span><span class="sxs-lookup"><span data-stu-id="a7c38-129">Route constraints</span></span>

<span data-ttu-id="a7c38-130">Uma restrição de rota impõe a correspondência de tipo em um segmento de rota para um componente.</span><span class="sxs-lookup"><span data-stu-id="a7c38-130">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="a7c38-131">No exemplo a seguir, a rota para o `Users` componente só corresponde se:</span><span class="sxs-lookup"><span data-stu-id="a7c38-131">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="a7c38-132">Um `Id` segmento de rota está presente na URL da solicitação.</span><span class="sxs-lookup"><span data-stu-id="a7c38-132">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="a7c38-133">O `Id` segmento é um inteiro (`int`).</span><span class="sxs-lookup"><span data-stu-id="a7c38-133">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="a7c38-134">As restrições de rota mostradas na tabela a seguir estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="a7c38-134">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="a7c38-135">Para as restrições de rota que correspondem à cultura invariável, consulte o aviso abaixo da tabela para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="a7c38-135">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="a7c38-136">Restrição</span><span class="sxs-lookup"><span data-stu-id="a7c38-136">Constraint</span></span> | <span data-ttu-id="a7c38-137">Exemplo</span><span class="sxs-lookup"><span data-stu-id="a7c38-137">Example</span></span>           | <span data-ttu-id="a7c38-138">Correspondências de exemplo</span><span class="sxs-lookup"><span data-stu-id="a7c38-138">Example Matches</span></span>                                                                  | <span data-ttu-id="a7c38-139">Constante</span><span class="sxs-lookup"><span data-stu-id="a7c38-139">Invariant</span></span><br><span data-ttu-id="a7c38-140">cultura</span><span class="sxs-lookup"><span data-stu-id="a7c38-140">culture</span></span><br><span data-ttu-id="a7c38-141">correspondência</span><span class="sxs-lookup"><span data-stu-id="a7c38-141">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="a7c38-142">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="a7c38-142">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="a7c38-143">Não</span><span class="sxs-lookup"><span data-stu-id="a7c38-143">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="a7c38-144">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="a7c38-144">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="a7c38-145">Sim</span><span class="sxs-lookup"><span data-stu-id="a7c38-145">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="a7c38-146">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="a7c38-146">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="a7c38-147">Sim</span><span class="sxs-lookup"><span data-stu-id="a7c38-147">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="a7c38-148">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="a7c38-148">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="a7c38-149">Sim</span><span class="sxs-lookup"><span data-stu-id="a7c38-149">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="a7c38-150">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="a7c38-150">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="a7c38-151">Sim</span><span class="sxs-lookup"><span data-stu-id="a7c38-151">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="a7c38-152">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="a7c38-152">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="a7c38-153">Não</span><span class="sxs-lookup"><span data-stu-id="a7c38-153">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="a7c38-154">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="a7c38-154">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="a7c38-155">Sim</span><span class="sxs-lookup"><span data-stu-id="a7c38-155">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="a7c38-156">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="a7c38-156">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="a7c38-157">Sim</span><span class="sxs-lookup"><span data-stu-id="a7c38-157">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="a7c38-158">As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou `DateTime`) sempre usam a cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="a7c38-158">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="a7c38-159">Essas restrições consideram que a URL não é localizável.</span><span class="sxs-lookup"><span data-stu-id="a7c38-159">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="a7c38-160">Roteamento com URLs que contêm pontos</span><span class="sxs-lookup"><span data-stu-id="a7c38-160">Routing with URLs that contain dots</span></span>

<span data-ttu-id="a7c38-161">No mais alto aplicativo do lado do servidor, a rota padrão em *_Host. cshtml* `/` é`@page "/"`().</span><span class="sxs-lookup"><span data-stu-id="a7c38-161">In Blazor server-side apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="a7c38-162">Uma URL de solicitação que contém um ponto`.`() não é correspondida pela rota padrão porque a URL parece solicitar um arquivo.</span><span class="sxs-lookup"><span data-stu-id="a7c38-162">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="a7c38-163">Um aplicativo mais alto retorna uma resposta *404-não encontrada* para um arquivo estático que não existe.</span><span class="sxs-lookup"><span data-stu-id="a7c38-163">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="a7c38-164">Para usar rotas que contenham um ponto, configure *_Host. cshtml* com o seguinte modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="a7c38-164">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="a7c38-165">O `"/{**path}"` modelo inclui:</span><span class="sxs-lookup"><span data-stu-id="a7c38-165">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="a7c38-166">Sintaxe *catch-all* de asterisco duplo (`**`) para capturar o caminho entre vários limites de pasta sem barras invertidas`/`de codificação ().</span><span class="sxs-lookup"><span data-stu-id="a7c38-166">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="a7c38-167">Um `path` nome de parâmetro de rota.</span><span class="sxs-lookup"><span data-stu-id="a7c38-167">A `path` route parameter name.</span></span>

<span data-ttu-id="a7c38-168">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="a7c38-168">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="a7c38-169">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="a7c38-169">NavLink component</span></span>

<span data-ttu-id="a7c38-170">Use um `NavLink` componente no lugar de elementos de hiperlink HTML`<a>`() ao criar links de navegação.</span><span class="sxs-lookup"><span data-stu-id="a7c38-170">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="a7c38-171">Um `NavLink` componente se comporta como um `<a>` elemento, exceto que alterna uma `active` classe CSS com base no fato de sua `href` correspondência com a URL atual.</span><span class="sxs-lookup"><span data-stu-id="a7c38-171">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="a7c38-172">A `active` classe ajuda um usuário a entender qual página é a página ativa entre os links de navegação exibidos.</span><span class="sxs-lookup"><span data-stu-id="a7c38-172">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="a7c38-173">O componente `NavMenu` a seguir cria uma barra de navegação de [inicialização](https://getbootstrap.com/docs/) que demonstra `NavLink` como usar componentes:</span><span class="sxs-lookup"><span data-stu-id="a7c38-173">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="a7c38-174">Há duas `NavLinkMatch` opções que você pode atribuir `Match` ao atributo do `<NavLink>` elemento:</span><span class="sxs-lookup"><span data-stu-id="a7c38-174">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="a7c38-175">`NavLinkMatch.All`&ndash; O`NavLink` está ativo quando corresponde à URL atual inteira.</span><span class="sxs-lookup"><span data-stu-id="a7c38-175">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="a7c38-176">`NavLinkMatch.Prefix`(*padrão*) &ndash; O`NavLink` está ativo quando ele corresponde a qualquer prefixo da URL atual.</span><span class="sxs-lookup"><span data-stu-id="a7c38-176">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="a7c38-177">No exemplo anterior, a página inicial `NavLink` `href=""` corresponde à URL inicial e recebe apenas a `active` classe CSS na URL do caminho base padrão do aplicativo (por exemplo, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="a7c38-177">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="a7c38-178">O segundo `NavLink` recebe a `active` classe quando o usuário visita qualquer URL com um `MyComponent` prefixo (por exemplo, `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="a7c38-178">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="a7c38-179">Atributos `NavLink` de componente adicionais são passados para a marca de âncora renderizada.</span><span class="sxs-lookup"><span data-stu-id="a7c38-179">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="a7c38-180">No exemplo a seguir, o `NavLink` componente inclui o `target` atributo:</span><span class="sxs-lookup"><span data-stu-id="a7c38-180">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="a7c38-181">A seguinte marcação HTML é renderizada:</span><span class="sxs-lookup"><span data-stu-id="a7c38-181">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="a7c38-182">Auxiliares de URI e estado de navegação</span><span class="sxs-lookup"><span data-stu-id="a7c38-182">URI and navigation state helpers</span></span>

<span data-ttu-id="a7c38-183">Use `Microsoft.AspNetCore.Components.NavigationManager` para trabalhar com URIs e navegação no C# código.</span><span class="sxs-lookup"><span data-stu-id="a7c38-183">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="a7c38-184">`NavigationManager`fornece o evento e os métodos mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="a7c38-184">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="a7c38-185">Membro</span><span class="sxs-lookup"><span data-stu-id="a7c38-185">Member</span></span> | <span data-ttu-id="a7c38-186">Descrição</span><span class="sxs-lookup"><span data-stu-id="a7c38-186">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="a7c38-187">Obtém o URI absoluto atual.</span><span class="sxs-lookup"><span data-stu-id="a7c38-187">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="a7c38-188">Obtém o URI de base (com uma barra à direita) que pode ser anexado a caminhos de URI relativos para produzir um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="a7c38-188">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="a7c38-189">Normalmente, `BaseUri` corresponde `href` ao atributo no elemento `<base>` do documento em *wwwroot/index.html* (mais de um lado do cliente) ou *pages/_Host. cshtml* (no lado do servidor).</span><span class="sxs-lookup"><span data-stu-id="a7c38-189">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor client-side) or *Pages/_Host.cshtml* (Blazor server-side).</span></span> |
| `NavigateTo` | <span data-ttu-id="a7c38-190">Navega para o URI especificado.</span><span class="sxs-lookup"><span data-stu-id="a7c38-190">Navigates to the specified URI.</span></span> <span data-ttu-id="a7c38-191">Se `forceLoad` for `true`:</span><span class="sxs-lookup"><span data-stu-id="a7c38-191">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="a7c38-192">O roteamento do lado do cliente é ignorado.</span><span class="sxs-lookup"><span data-stu-id="a7c38-192">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="a7c38-193">O navegador é forçado a carregar a nova página do servidor, seja ou não o URI normalmente manipulado pelo roteador do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="a7c38-193">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="a7c38-194">Um evento que é acionado quando o local de navegação é alterado.</span><span class="sxs-lookup"><span data-stu-id="a7c38-194">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="a7c38-195">Converte um URI relativo em um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="a7c38-195">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="a7c38-196">Dado um URI de base (por exemplo, um URI retornado anteriormente `GetBaseUri`pelo), converte um URI absoluto em um URI relativo ao prefixo de URI de base.</span><span class="sxs-lookup"><span data-stu-id="a7c38-196">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="a7c38-197">O componente a seguir navega para o componente do `Counter` aplicativo quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="a7c38-197">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```cshtml
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

