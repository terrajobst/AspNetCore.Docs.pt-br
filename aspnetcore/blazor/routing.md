---
title: Roteamento de ASP.NET Core mais
author: guardrex
description: Saiba como rotear solicitações em aplicativos e sobre o componente NavLink.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/routing
ms.openlocfilehash: 1c61eedf7dbf0bbc8796eaa11360783b9d7aba6c
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70963869"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="2055b-103">Roteamento de ASP.NET Core mais</span><span class="sxs-lookup"><span data-stu-id="2055b-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="2055b-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2055b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2055b-105">Saiba como rotear solicitações e como usar o `NavLink` componente para criar links de navegação em aplicativos mais incrivelmenteos.</span><span class="sxs-lookup"><span data-stu-id="2055b-105">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="2055b-106">Integração de roteamento de ponto de extremidade ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2055b-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="2055b-107">O servidor mais incrivelmente é integrado ao [Roteamento de ponto de extremidade ASP.NET Core](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="2055b-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="2055b-108">Um aplicativo ASP.NET Core está configurado para aceitar conexões de entrada para componentes interativos com `MapBlazorHub` o no: `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="2055b-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a><span data-ttu-id="2055b-109">Modelos de rota</span><span class="sxs-lookup"><span data-stu-id="2055b-109">Route templates</span></span>

<span data-ttu-id="2055b-110">O `Router` componente permite o roteamento para cada componente com uma rota especificada.</span><span class="sxs-lookup"><span data-stu-id="2055b-110">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="2055b-111">O `Router` componente aparece no arquivo *app. Razor* :</span><span class="sxs-lookup"><span data-stu-id="2055b-111">The `Router` component appears in the *App.razor* file:</span></span>

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

<span data-ttu-id="2055b-112">Quando um arquivo *. Razor* com uma `@page` diretiva é compilado, a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> classe gerada é fornecida especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="2055b-112">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="2055b-113">Em tempo de execução `RouteView` , o componente:</span><span class="sxs-lookup"><span data-stu-id="2055b-113">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="2055b-114">Recebe o `RouteData` `Router` do junto com os parâmetros desejados.</span><span class="sxs-lookup"><span data-stu-id="2055b-114">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="2055b-115">Renderiza o componente especificado com seu layout (ou um layout padrão opcional) usando os parâmetros especificados.</span><span class="sxs-lookup"><span data-stu-id="2055b-115">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="2055b-116">Opcionalmente, você pode especificar `DefaultLayout` um parâmetro com uma classe de layout a ser usada para componentes que não especificam um layout.</span><span class="sxs-lookup"><span data-stu-id="2055b-116">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="2055b-117">Os modelos de mais de um padrão `MainLayout` especificam o componente.</span><span class="sxs-lookup"><span data-stu-id="2055b-117">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="2055b-118">*MainLayout. Razor* está na pasta *compartilhada* do projeto de modelo.</span><span class="sxs-lookup"><span data-stu-id="2055b-118">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="2055b-119">Para obter mais informações sobre layouts, <xref:blazor/layouts>consulte.</span><span class="sxs-lookup"><span data-stu-id="2055b-119">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="2055b-120">Vários modelos de rota podem ser aplicados a um componente.</span><span class="sxs-lookup"><span data-stu-id="2055b-120">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="2055b-121">O componente a seguir responde a solicitações `/BlazorRoute` para `/DifferentBlazorRoute`o e o:</span><span class="sxs-lookup"><span data-stu-id="2055b-121">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> <span data-ttu-id="2055b-122">Para que as URLs sejam resolvidas corretamente, o aplicativo `<base>` deve incluir uma marca em seu arquivo *wwwroot/index.html* (Webassembly do mais de um ou mais de-App) ou arquivo *pages/_Host. cshtml* (servidor mais `href` incrivelmente) com o caminho base do aplicativo especificado no atributo (`<base href="/">`).</span><span class="sxs-lookup"><span data-stu-id="2055b-122">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="2055b-123">Para obter mais informações, consulte <xref:host-and-deploy/blazor/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="2055b-123">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="2055b-124">Fornecer conteúdo personalizado quando o conteúdo não for encontrado</span><span class="sxs-lookup"><span data-stu-id="2055b-124">Provide custom content when content isn't found</span></span>

<span data-ttu-id="2055b-125">O `Router` componente permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.</span><span class="sxs-lookup"><span data-stu-id="2055b-125">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="2055b-126">No arquivo *app. Razor* , defina conteúdo personalizado no `NotFound` parâmetro de `Router` modelo do componente:</span><span class="sxs-lookup"><span data-stu-id="2055b-126">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

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

<span data-ttu-id="2055b-127">O conteúdo das `<NotFound>` marcas pode incluir itens arbitrários, como outros componentes interativos.</span><span class="sxs-lookup"><span data-stu-id="2055b-127">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="2055b-128">Para aplicar um layout padrão ao `NotFound` conteúdo, consulte <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="2055b-128">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="2055b-129">Rotear para componentes de vários assemblies</span><span class="sxs-lookup"><span data-stu-id="2055b-129">Route to components from multiple assemblies</span></span>

<span data-ttu-id="2055b-130">Use o `AdditionalAssemblies` parâmetro para especificar assemblies adicionais para o `Router` componente a ser considerado ao procurar componentes roteáveis.</span><span class="sxs-lookup"><span data-stu-id="2055b-130">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="2055b-131">Os assemblies especificados são considerados além do `AppAssembly`assembly especificado.</span><span class="sxs-lookup"><span data-stu-id="2055b-131">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="2055b-132">No exemplo a seguir, `Component1` é um componente roteável definido em uma biblioteca de classes referenciada.</span><span class="sxs-lookup"><span data-stu-id="2055b-132">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="2055b-133">O exemplo `AdditionalAssemblies` a seguir resulta no suporte de `Component1`roteamento para:</span><span class="sxs-lookup"><span data-stu-id="2055b-133">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

<span data-ttu-id="2055b-134">< roteador AppAssembly = "typeof (programa). Assembly "AdditionalAssemblies =" New [] {typeof (Component1). Assembly} >...</Router></span><span class="sxs-lookup"><span data-stu-id="2055b-134"><Router AppAssembly="typeof(Program).Assembly" AdditionalAssemblies="new[] { typeof(Component1).Assembly }> ... </Router></span></span>

## <a name="route-parameters"></a><span data-ttu-id="2055b-135">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="2055b-135">Route parameters</span></span>

<span data-ttu-id="2055b-136">O roteador usa parâmetros de rota para popular os parâmetros de componente correspondentes com o mesmo nome (não diferencia maiúsculas de minúsculas):</span><span class="sxs-lookup"><span data-stu-id="2055b-136">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

<span data-ttu-id="2055b-137">Os parâmetros opcionais não têm suporte para aplicativos mais na versão prévia do ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="2055b-137">Optional parameters aren't supported for Blazor apps in ASP.NET Core 3.0 Preview.</span></span> <span data-ttu-id="2055b-138">Duas `@page` diretivas são aplicadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="2055b-138">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="2055b-139">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="2055b-139">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="2055b-140">A segunda `@page` diretiva usa o `{text}` parâmetro de rota e atribui o valor à `Text` propriedade.</span><span class="sxs-lookup"><span data-stu-id="2055b-140">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="2055b-141">Restrições de rota</span><span class="sxs-lookup"><span data-stu-id="2055b-141">Route constraints</span></span>

<span data-ttu-id="2055b-142">Uma restrição de rota impõe a correspondência de tipo em um segmento de rota para um componente.</span><span class="sxs-lookup"><span data-stu-id="2055b-142">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="2055b-143">No exemplo a seguir, a rota para o `Users` componente só corresponde se:</span><span class="sxs-lookup"><span data-stu-id="2055b-143">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="2055b-144">Um `Id` segmento de rota está presente na URL da solicitação.</span><span class="sxs-lookup"><span data-stu-id="2055b-144">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="2055b-145">O `Id` segmento é um inteiro (`int`).</span><span class="sxs-lookup"><span data-stu-id="2055b-145">The `Id` segment is an integer (`int`).</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="2055b-146">As restrições de rota mostradas na tabela a seguir estão disponíveis.</span><span class="sxs-lookup"><span data-stu-id="2055b-146">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="2055b-147">Para as restrições de rota que correspondem à cultura invariável, consulte o aviso abaixo da tabela para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="2055b-147">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="2055b-148">Restrição</span><span class="sxs-lookup"><span data-stu-id="2055b-148">Constraint</span></span> | <span data-ttu-id="2055b-149">Exemplo</span><span class="sxs-lookup"><span data-stu-id="2055b-149">Example</span></span>           | <span data-ttu-id="2055b-150">Correspondências de exemplo</span><span class="sxs-lookup"><span data-stu-id="2055b-150">Example Matches</span></span>                                                                  | <span data-ttu-id="2055b-151">Constante</span><span class="sxs-lookup"><span data-stu-id="2055b-151">Invariant</span></span><br><span data-ttu-id="2055b-152">cultura</span><span class="sxs-lookup"><span data-stu-id="2055b-152">culture</span></span><br><span data-ttu-id="2055b-153">correspondência</span><span class="sxs-lookup"><span data-stu-id="2055b-153">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="2055b-154">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="2055b-154">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="2055b-155">Não</span><span class="sxs-lookup"><span data-stu-id="2055b-155">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="2055b-156">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="2055b-156">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="2055b-157">Sim</span><span class="sxs-lookup"><span data-stu-id="2055b-157">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="2055b-158">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="2055b-158">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="2055b-159">Sim</span><span class="sxs-lookup"><span data-stu-id="2055b-159">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="2055b-160">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="2055b-160">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="2055b-161">Sim</span><span class="sxs-lookup"><span data-stu-id="2055b-161">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="2055b-162">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="2055b-162">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="2055b-163">Sim</span><span class="sxs-lookup"><span data-stu-id="2055b-163">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="2055b-164">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="2055b-164">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="2055b-165">Não</span><span class="sxs-lookup"><span data-stu-id="2055b-165">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="2055b-166">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="2055b-166">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="2055b-167">Sim</span><span class="sxs-lookup"><span data-stu-id="2055b-167">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="2055b-168">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="2055b-168">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="2055b-169">Sim</span><span class="sxs-lookup"><span data-stu-id="2055b-169">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="2055b-170">As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou `DateTime`) sempre usam a cultura invariável.</span><span class="sxs-lookup"><span data-stu-id="2055b-170">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="2055b-171">Essas restrições consideram que a URL não é localizável.</span><span class="sxs-lookup"><span data-stu-id="2055b-171">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="2055b-172">Roteamento com URLs que contêm pontos</span><span class="sxs-lookup"><span data-stu-id="2055b-172">Routing with URLs that contain dots</span></span>

<span data-ttu-id="2055b-173">Em aplicativos de servidor mais incrivelmente, a rota padrão em *_Host. cshtml* `/` é`@page "/"`().</span><span class="sxs-lookup"><span data-stu-id="2055b-173">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="2055b-174">Uma URL de solicitação que contém um ponto`.`() não é correspondida pela rota padrão porque a URL parece solicitar um arquivo.</span><span class="sxs-lookup"><span data-stu-id="2055b-174">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="2055b-175">Um aplicativo mais alto retorna uma resposta *404-não encontrada* para um arquivo estático que não existe.</span><span class="sxs-lookup"><span data-stu-id="2055b-175">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="2055b-176">Para usar rotas que contenham um ponto, configure *_Host. cshtml* com o seguinte modelo de rota:</span><span class="sxs-lookup"><span data-stu-id="2055b-176">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="2055b-177">O `"/{**path}"` modelo inclui:</span><span class="sxs-lookup"><span data-stu-id="2055b-177">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="2055b-178">Sintaxe *catch-all* de asterisco duplo (`**`) para capturar o caminho entre vários limites de pasta sem barras invertidas`/`de codificação ().</span><span class="sxs-lookup"><span data-stu-id="2055b-178">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="2055b-179">Um `path` nome de parâmetro de rota.</span><span class="sxs-lookup"><span data-stu-id="2055b-179">A `path` route parameter name.</span></span>

<span data-ttu-id="2055b-180">Para obter mais informações, consulte <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="2055b-180">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="2055b-181">Componente NavLink</span><span class="sxs-lookup"><span data-stu-id="2055b-181">NavLink component</span></span>

<span data-ttu-id="2055b-182">Use um `NavLink` componente no lugar de elementos de hiperlink HTML`<a>`() ao criar links de navegação.</span><span class="sxs-lookup"><span data-stu-id="2055b-182">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="2055b-183">Um `NavLink` componente se comporta como um `<a>` elemento, exceto que alterna uma `active` classe CSS com base no fato de sua `href` correspondência com a URL atual.</span><span class="sxs-lookup"><span data-stu-id="2055b-183">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="2055b-184">A `active` classe ajuda um usuário a entender qual página é a página ativa entre os links de navegação exibidos.</span><span class="sxs-lookup"><span data-stu-id="2055b-184">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="2055b-185">O componente `NavMenu` a seguir cria uma barra de navegação de [inicialização](https://getbootstrap.com/docs/) que demonstra `NavLink` como usar componentes:</span><span class="sxs-lookup"><span data-stu-id="2055b-185">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="2055b-186">Há duas `NavLinkMatch` opções que você pode atribuir `Match` ao atributo do `<NavLink>` elemento:</span><span class="sxs-lookup"><span data-stu-id="2055b-186">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="2055b-187">`NavLinkMatch.All`&ndash; O`NavLink` está ativo quando corresponde à URL atual inteira.</span><span class="sxs-lookup"><span data-stu-id="2055b-187">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="2055b-188">`NavLinkMatch.Prefix`(*padrão*) &ndash; O`NavLink` está ativo quando ele corresponde a qualquer prefixo da URL atual.</span><span class="sxs-lookup"><span data-stu-id="2055b-188">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="2055b-189">No exemplo anterior, a página inicial `NavLink` `href=""` corresponde à URL inicial e recebe apenas a `active` classe CSS na URL do caminho base padrão do aplicativo (por exemplo, `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="2055b-189">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="2055b-190">O segundo `NavLink` recebe a `active` classe quando o usuário visita qualquer URL com um `MyComponent` prefixo (por exemplo, `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="2055b-190">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="2055b-191">Atributos `NavLink` de componente adicionais são passados para a marca de âncora renderizada.</span><span class="sxs-lookup"><span data-stu-id="2055b-191">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="2055b-192">No exemplo a seguir, o `NavLink` componente inclui o `target` atributo:</span><span class="sxs-lookup"><span data-stu-id="2055b-192">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="2055b-193">A seguinte marcação HTML é renderizada:</span><span class="sxs-lookup"><span data-stu-id="2055b-193">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="2055b-194">Auxiliares de URI e estado de navegação</span><span class="sxs-lookup"><span data-stu-id="2055b-194">URI and navigation state helpers</span></span>

<span data-ttu-id="2055b-195">Use `Microsoft.AspNetCore.Components.NavigationManager` para trabalhar com URIs e navegação no C# código.</span><span class="sxs-lookup"><span data-stu-id="2055b-195">Use `Microsoft.AspNetCore.Components.NavigationManager` to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="2055b-196">`NavigationManager`fornece o evento e os métodos mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="2055b-196">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="2055b-197">Membro</span><span class="sxs-lookup"><span data-stu-id="2055b-197">Member</span></span> | <span data-ttu-id="2055b-198">Descrição</span><span class="sxs-lookup"><span data-stu-id="2055b-198">Description</span></span> |
| ------ | ----------- |
| `Uri` | <span data-ttu-id="2055b-199">Obtém o URI absoluto atual.</span><span class="sxs-lookup"><span data-stu-id="2055b-199">Gets the current absolute URI.</span></span> |
| `BaseUri` | <span data-ttu-id="2055b-200">Obtém o URI de base (com uma barra à direita) que pode ser anexado a caminhos de URI relativos para produzir um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="2055b-200">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="2055b-201">Normalmente, `BaseUri` corresponde `href` ao atributo no elemento `<base>` do documento em *wwwroot/index.html* (Webassembly de mais ou-de- *_Host. cshtml* ).</span><span class="sxs-lookup"><span data-stu-id="2055b-201">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> |
| `NavigateTo` | <span data-ttu-id="2055b-202">Navega para o URI especificado.</span><span class="sxs-lookup"><span data-stu-id="2055b-202">Navigates to the specified URI.</span></span> <span data-ttu-id="2055b-203">Se `forceLoad` for `true`:</span><span class="sxs-lookup"><span data-stu-id="2055b-203">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="2055b-204">O roteamento do lado do cliente é ignorado.</span><span class="sxs-lookup"><span data-stu-id="2055b-204">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="2055b-205">O navegador é forçado a carregar a nova página do servidor, seja ou não o URI normalmente manipulado pelo roteador do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="2055b-205">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| `LocationChanged` | <span data-ttu-id="2055b-206">Um evento que é acionado quando o local de navegação é alterado.</span><span class="sxs-lookup"><span data-stu-id="2055b-206">An event that fires when the navigation location has changed.</span></span> |
| `ToAbsoluteUri` | <span data-ttu-id="2055b-207">Converte um URI relativo em um URI absoluto.</span><span class="sxs-lookup"><span data-stu-id="2055b-207">Converts a relative URI into an absolute URI.</span></span> |
| `ToBaseRelativePath` | <span data-ttu-id="2055b-208">Dado um URI de base (por exemplo, um URI retornado anteriormente `GetBaseUri`pelo), converte um URI absoluto em um URI relativo ao prefixo de URI de base.</span><span class="sxs-lookup"><span data-stu-id="2055b-208">Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="2055b-209">O componente a seguir navega para o componente do `Counter` aplicativo quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="2055b-209">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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
