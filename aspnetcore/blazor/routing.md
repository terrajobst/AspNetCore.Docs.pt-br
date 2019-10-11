---
title: Roteamento de ASP.NET Core mais
author: guardrex
description: Saiba como rotear solicitações em aplicativos e sobre o componente NavLink.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2019
uid: blazor/routing
ms.openlocfilehash: 8f48112237e6dd3fed88404c53b8d7d9137ef6ff
ms.sourcegitcommit: 0b8a7571bf7acf85bf16118acb2435001cbe4b5d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72236537"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="627b7-103">Roteamento de ASP.NET Core mais</span><span class="sxs-lookup"><span data-stu-id="627b7-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="627b7-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="627b7-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="627b7-105">Saiba como rotear solicitações e como usar o componente `NavLink` para criar links de navegação em aplicativos mais Incrivelmenteos.</span><span class="sxs-lookup"><span data-stu-id="627b7-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="627b7-106">Integração de roteamento de ponto de extremidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="627b7-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="627b7-107">O servidor mais incrivelmente é integrado ao [Roteamento de ponto de extremidade ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="627b7-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="627b7-108">Um aplicativo ASP.NET Core está configurado para aceitar conexões de entrada para componentes interativos com `MapBlazorHub` no `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="627b7-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="627b7-109">A configuração mais típica é rotear todas as solicitações para uma página Razor, que atua como o host para a parte do lado do servidor do aplicativo de servidor mais grande.</span><span class="sxs-lookup"><span data-stu-id="627b7-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="627b7-110">Por convenção, a página *host* geralmente é chamada de *_Host. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="627b7-110">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="627b7-111">A rota especificada no arquivo de host é chamada de *rota de fallback* porque opera com uma prioridade baixa na correspondência de rota.</span><span class="sxs-lookup"><span data-stu-id="627b7-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="627b7-112">A rota de fallback é considerada quando outras rotas não correspondem.</span><span class="sxs-lookup"><span data-stu-id="627b7-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="627b7-113">Isso permite que o aplicativo use outros controladores e páginas sem interferir no aplicativo de servidor mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="627b7-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="627b7-114">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="627b7-114">Route templates</span></span>

<span data-ttu-id="627b7-115">O componente `Router` permite o roteamento para cada componente com uma rota especificada.</span><span class="sxs-lookup"><span data-stu-id="627b7-115">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="627b7-116">O componente `Router` aparece no arquivo *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="627b7-116">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="627b7-117">Quando um arquivo *. Razor* com uma diretiva `@page` é compilado, a classe gerada recebe um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="627b7-117">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="627b7-118">Em tempo de execução, o componente `RouteView`:</span><span class="sxs-lookup"><span data-stu-id="627b7-118">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="627b7-119">Recebe o `RouteData` do `Router` junto com os parâmetros desejados.</span><span class="sxs-lookup"><span data-stu-id="627b7-119">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="627b7-120">Renderiza o componente especificado com seu layout (ou um layout padrão opcional) usando os parâmetros especificados.</span><span class="sxs-lookup"><span data-stu-id="627b7-120">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="627b7-121">Opcionalmente, você pode especificar um parâmetro `DefaultLayout` com uma classe de layout a ser usada para componentes que não especificam um layout.</span><span class="sxs-lookup"><span data-stu-id="627b7-121">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="627b7-122">Os modelos de mais alto padrão especificam o componente `MainLayout`.</span><span class="sxs-lookup"><span data-stu-id="627b7-122">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="627b7-123">*MainLayout. Razor* está na pasta *compartilhada* do projeto de modelo.</span><span class="sxs-lookup"><span data-stu-id="627b7-123">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="627b7-124">Para obter mais informações sobre layouts, consulte <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="627b7-124">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="627b7-125">Vários modelos de rota podem ser aplicados a um componente.</span><span class="sxs-lookup"><span data-stu-id="627b7-125">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="627b7-126">O componente a seguir responde às solicitações de `/BlazorRoute` e `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="627b7-126">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="627b7-127">Para que as URLs sejam resolvidas corretamente, o aplicativo deve incluir uma marca `<base>` em seu arquivo *wwwroot/index.html* (Webassembly de alta disponibilidade) ou arquivo *pages/_Host. cshtml* (servidor de alto-susigner) com o caminho base do aplicativo especificado no atributo `href` (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="627b7-127">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="627b7-128">Para obter mais informações, consulte <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="627b7-128">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="627b7-129">Fornecer conteúdo personalizado quando o conteúdo não for encontrado</span><span class="sxs-lookup"><span data-stu-id="627b7-129">Provide custom content when content isn't found</span></span>

<span data-ttu-id="627b7-130">O componente `Router` permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.</span><span class="sxs-lookup"><span data-stu-id="627b7-130">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="627b7-131">No arquivo *app. Razor* , defina conteúdo personalizado no parâmetro de modelo `NotFound` do componente `Router`:</span><span class="sxs-lookup"><span data-stu-id="627b7-131">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="627b7-132">O conteúdo das marcas `<NotFound>` pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="627b7-132">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="627b7-133">Para aplicar um layout padrão ao conteúdo `NotFound`, consulte <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="627b7-133">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="627b7-134">Rotear para componentes de vários assemblies</span><span class="sxs-lookup"><span data-stu-id="627b7-134">Route to components from multiple assemblies</span></span>

<span data-ttu-id="627b7-135">Use o parâmetro `AdditionalAssemblies` para especificar assemblies adicionais para o componente `Router` a ser considerado ao procurar componentes roteáveis.</span><span class="sxs-lookup"><span data-stu-id="627b7-135">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="627b7-136">Os assemblies especificados são considerados além do @no__t assembly especificado pelo-0.</span><span class="sxs-lookup"><span data-stu-id="627b7-136">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="627b7-137">No exemplo a seguir, `Component1` é um componente roteável definido em uma biblioteca de classes referenciada.</span><span class="sxs-lookup"><span data-stu-id="627b7-137">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="627b7-138">O exemplo a seguir `AdditionalAssemblies` resulta no suporte de roteamento para `Component1`:</span><span class="sxs-lookup"><span data-stu-id="627b7-138">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```cshtml
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }>
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="627b7-139">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="627b7-139">Route parameters</span></span>

<span data-ttu-id="627b7-140">O roteador usa parâmetros de rota para popular os parâmetros de componente correspondentes com o mesmo nome (não diferencia maiúsculas de minúsculas):</span><span class="sxs-lookup"><span data-stu-id="627b7-140">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="627b7-141">Os parâmetros opcionais não têm suporte para aplicativos mais no ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="627b7-141">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0.</span></span> <span data-ttu-id="627b7-142">Duas diretivas `@page` são aplicadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="627b7-142">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="627b7-143">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="627b7-143">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="627b7-144">A segunda diretiva `@page` usa o parâmetro de rota `{text}` e atribui o valor à propriedade `Text`.</span><span class="sxs-lookup"><span data-stu-id="627b7-144">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="627b7-145">Restrições de rota</span><span class="sxs-lookup"><span data-stu-id="627b7-145">Route constraints</span></span>

<span data-ttu-id="627b7-146">Uma restrição de rota impõe a correspondência de tipo em um segmento de rota para um componente.</span><span class="sxs-lookup"><span data-stu-id="627b7-146">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="627b7-147">No exemplo a seguir, a rota para o componente `Users` só corresponde se:</span><span class="sxs-lookup"><span data-stu-id="627b7-147">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="627b7-148">Um segmento de rota `Id` está presente na URL da solicitação.</span><span class="sxs-lookup"><span data-stu-id="627b7-148">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="627b7-149">O segmento `Id` é um inteiro (`int`).</span><span class="sxs-lookup"><span data-stu-id="627b7-149">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="627b7-150">As restrições de rota mostradas na tabela a seguir estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="627b7-150">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="627b7-151">Para as restrições de rota que correspondem à cultura invariável, consulte o aviso abaixo da tabela para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="627b7-151">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="627b7-152">Restrição</span><span class="sxs-lookup"><span data-stu-id="627b7-152">Constraint</span></span> | <span data-ttu-id="627b7-153">Exemplo</span><span class="sxs-lookup"><span data-stu-id="627b7-153">Example</span></span>           | <span data-ttu-id="627b7-154">Correspondências de exemplo</span><span class="sxs-lookup"><span data-stu-id="627b7-154">Example Matches</span></span>                                                                  | <span data-ttu-id="627b7-155">Constante</span><span class="sxs-lookup"><span data-stu-id="627b7-155">Invariant</span></span><br><span data-ttu-id="627b7-156">cultura</span><span class="sxs-lookup"><span data-stu-id="627b7-156">culture</span></span><br><span data-ttu-id="627b7-157">correspondência</span><span class="sxs-lookup"><span data-stu-id="627b7-157">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="627b7-158">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="627b7-158">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="627b7-159">Não</span><span class="sxs-lookup"><span data-stu-id="627b7-159">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="627b7-160">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="627b7-160">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="627b7-161">Sim</span><span class="sxs-lookup"><span data-stu-id="627b7-161">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="627b7-162">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="627b7-162">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="627b7-163">Sim</span><span class="sxs-lookup"><span data-stu-id="627b7-163">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="627b7-164">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="627b7-164">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="627b7-165">Sim</span><span class="sxs-lookup"><span data-stu-id="627b7-165">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="627b7-166">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="627b7-166">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="627b7-167">Sim</span><span class="sxs-lookup"><span data-stu-id="627b7-167">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="627b7-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="627b7-168">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="627b7-169">Não</span><span class="sxs-lookup"><span data-stu-id="627b7-169">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="627b7-170">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="627b7-170">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="627b7-171">Sim</span><span class="sxs-lookup"><span data-stu-id="627b7-171">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="627b7-172">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="627b7-172">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="627b7-173">Sim</span><span class="sxs-lookup"><span data-stu-id="627b7-173">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="627b7-174">As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou `DateTime`) sempre usam a cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="627b7-174">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="627b7-175">Essas restrições consideram que a URL não é localizável.</span><span class="sxs-lookup"><span data-stu-id="627b7-175">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="627b7-176">Roteamento com URLs que contêm pontos</span><span class="sxs-lookup"><span data-stu-id="627b7-176">Routing with URLs that contain dots</span></span>

<span data-ttu-id="627b7-177">Em aplicativos de servidor mais incrivelmente, a rota padrão em *_Host. cshtml* é `/` (`@page "/"`).</span><span class="sxs-lookup"><span data-stu-id="627b7-177">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="627b7-178">Uma URL de solicitação que contém um ponto (`.`) não é correspondida pela rota padrão porque a URL parece solicitar um arquivo.</span><span class="sxs-lookup"><span data-stu-id="627b7-178">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="627b7-179">Um aplicativo mais alto retorna uma resposta *404-não encontrada* para um arquivo estático que não existe.</span><span class="sxs-lookup"><span data-stu-id="627b7-179">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="627b7-180">Para usar rotas que contenham um ponto, configure *_Host. cshtml* com o seguinte modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="627b7-180">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="627b7-181">O modelo `"/{**path}"` inclui:</span><span class="sxs-lookup"><span data-stu-id="627b7-181">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="627b7-182">Sintaxe *catch-all* de asterisco duplo (`**`) para capturar o caminho entre vários limites de pasta sem barras invertidas de codificação (`/`).</span><span class="sxs-lookup"><span data-stu-id="627b7-182">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="627b7-183">Um nome de parâmetro de rota `path`.</span><span class="sxs-lookup"><span data-stu-id="627b7-183">A `path` route parameter name.</span></span>

<span data-ttu-id="627b7-184">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="627b7-184">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="627b7-185">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="627b7-185">NavLink component</span></span>

<span data-ttu-id="627b7-186">Use um componente `NavLink` no lugar de elementos de hiperlink HTML (`<a>`) ao criar links de navegação.</span><span class="sxs-lookup"><span data-stu-id="627b7-186">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="627b7-187">Um componente `NavLink` se comporta como um elemento `<a>`, exceto que alterna uma classe CSS `active` com base em se seu `href` corresponde à URL atual.</span><span class="sxs-lookup"><span data-stu-id="627b7-187">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="627b7-188">A classe `active` ajuda um usuário a entender qual página é a página ativa entre os links de navegação exibidos.</span><span class="sxs-lookup"><span data-stu-id="627b7-188">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="627b7-189">O componente `NavMenu` a seguir cria uma barra de navegação de [inicialização](https://getbootstrap.com/docs/) que demonstra como usar componentes `NavLink`:</span><span class="sxs-lookup"><span data-stu-id="627b7-189">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="627b7-190">Há duas opções `NavLinkMatch` que você pode atribuir ao atributo `Match` do elemento `<NavLink>`:</span><span class="sxs-lookup"><span data-stu-id="627b7-190">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="627b7-191">`NavLinkMatch.All` &ndash; o `NavLink` está ativo quando corresponde à URL atual inteira.</span><span class="sxs-lookup"><span data-stu-id="627b7-191">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="627b7-192">`NavLinkMatch.Prefix` (*padrão*) &ndash; o `NavLink` está ativo quando ele corresponde a qualquer prefixo da URL atual.</span><span class="sxs-lookup"><span data-stu-id="627b7-192">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="627b7-193">No exemplo anterior, a @no__t inicial-0 `href=""` corresponde à URL inicial e recebe apenas a classe CSS `active` na URL do caminho base padrão do aplicativo (por exemplo, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="627b7-193">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="627b7-194">O segundo `NavLink` recebe a classe `active` quando o usuário visita qualquer URL com um prefixo `MyComponent` (por exemplo, `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="627b7-194">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="627b7-195">Atributos de componente `NavLink` adicionais são passados para a marca de âncora renderizada.</span><span class="sxs-lookup"><span data-stu-id="627b7-195">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="627b7-196">No exemplo a seguir, o componente `NavLink` inclui o atributo `target`:</span><span class="sxs-lookup"><span data-stu-id="627b7-196">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="627b7-197">A seguinte marcação HTML é renderizada:</span><span class="sxs-lookup"><span data-stu-id="627b7-197">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="627b7-198">Auxiliares de URI e estado de navegação</span><span class="sxs-lookup"><span data-stu-id="627b7-198">URI and navigation state helpers</span></span>

<span data-ttu-id="627b7-199">Use `Microsoft.AspNetCore.Components.NavigationManager` para trabalhar com URIs e navegação no C# código.</span><span class="sxs-lookup"><span data-stu-id="627b7-199">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="627b7-200">`NavigationManager` fornece o evento e os métodos mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="627b7-200">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="627b7-201">Membro</span><span class="sxs-lookup"><span data-stu-id="627b7-201">Member</span></span> | <span data-ttu-id="627b7-202">Descrição</span><span class="sxs-lookup"><span data-stu-id="627b7-202">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="627b7-203">Obtém o URI absoluto atual.</span><span class="sxs-lookup"><span data-stu-id="627b7-203">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="627b7-204">Obtém o URI de base (com uma barra à direita) que pode ser anexado a caminhos de URI relativos para produzir um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="627b7-204">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="627b7-205">Normalmente, `BaseUri` corresponde ao atributo `href` no elemento `<base>` do documento em *wwwroot/index.html* (Webassembly de mais Altova) ou *pages/_Host. cshtml* (servidor de mais Altova).</span><span class="sxs-lookup"><span data-stu-id="627b7-205">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| `NavigateTo` | <span data-ttu-id="627b7-206">Navega para o URI especificado.</span><span class="sxs-lookup"><span data-stu-id="627b7-206">Navigates to the specified URI.</span></span> <span data-ttu-id="627b7-207">Se `forceLoad` for `true`:</span><span class="sxs-lookup"><span data-stu-id="627b7-207">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="627b7-208">O roteamento do lado do cliente é ignorado.</span><span class="sxs-lookup"><span data-stu-id="627b7-208">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="627b7-209">O navegador é forçado a carregar a nova página do servidor, seja ou não o URI normalmente manipulado pelo roteador do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="627b7-209">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="627b7-210">Um evento que é acionado quando o local de navegação é alterado.</span><span class="sxs-lookup"><span data-stu-id="627b7-210">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="627b7-211">Converte um URI relativo em um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="627b7-211">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="627b7-212">Dado um URI de base (por exemplo, um URI retornado anteriormente por `GetBaseUri`), converte um URI absoluto em um URI relativo ao prefixo de URI de base.</span><span class="sxs-lookup"><span data-stu-id="627b7-212">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="627b7-213">O componente a seguir navega para o componente `Counter` do aplicativo quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="627b7-213">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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
