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
# <a name="aspnet-core-blazor-routing"></a>Roteamento de ASP.NET Core mais

Por [Luke Latham](https://github.com/guardrex)

Saiba como rotear solicitações e como usar o `NavLink` componente para criar links de navegação em aplicativos mais incrivelmenteos.

## <a name="aspnet-core-endpoint-routing-integration"></a>Integração de roteamento de ponto de extremidade ASP.NET Core

O lado do servidor mais incrivelmente é integrado ao [Roteamento de ponto de extremidade ASP.NET Core](xref:fundamentals/routing). Um aplicativo ASP.NET Core está configurado para aceitar conexões de entrada para componentes interativos com `MapBlazorHub` o no: `Startup.Configure`

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

## <a name="route-templates"></a>Modelos de rota

O `Router` componente habilita o roteamento e um modelo de rota é fornecido para cada componente acessível. O `Router` componente aparece no arquivo *app. Razor* :

Em um aplicativo mais alto do lado do servidor ou do lado do cliente:

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

Quando um arquivo *. Razor* com uma `@page` diretiva é compilado, a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> classe gerada é fornecida especificando o modelo de rota. Em tempo de execução, o roteador procura classes de componentes `RouteAttribute` com um e renderiza o componente com um modelo de rota que corresponde à URL solicitada.

Vários modelos de rota podem ser aplicados a um componente. O componente a seguir responde a solicitações `/BlazorRoute` para `/DifferentBlazorRoute`o e o:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

> [!IMPORTANT]
> Para que as URLs sejam resolvidas corretamente, o aplicativo `<base>` deve incluir uma marca em seu arquivo *wwwroot/index.html* (mais de lado do cliente) ou no arquivo *pages/_Host. cshtml* (no lado do servidor) com o caminho base do `href` aplicativo especificado no atributo ( `<base href="/">`). Para obter mais informações, consulte <xref:host-and-deploy/blazor/client-side#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Fornecer conteúdo personalizado quando o conteúdo não for encontrado

O `Router` componente permite que o aplicativo especifique conteúdo personalizado se o conteúdo não for encontrado para a rota solicitada.

No arquivo *app. Razor* , defina conteúdo personalizado no `<NotFound>` parâmetro de `Router` modelo do componente:

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

O conteúdo de `<NotFound>` pode incluir itens arbitrários, como outros componentes interativos.

## <a name="route-parameters"></a>Parâmetros de rota

O roteador usa parâmetros de rota para popular os parâmetros de componente correspondentes com o mesmo nome (não diferencia maiúsculas de minúsculas):

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter&highlight=2,7-8)]

Os parâmetros opcionais não têm suporte para aplicativos mais na versão prévia do ASP.NET Core 3,0. Duas `@page` diretivas são aplicadas no exemplo anterior. O primeiro permite a navegação para o componente sem um parâmetro. A segunda `@page` diretiva usa o `{text}` parâmetro de rota e atribui o valor à `Text` propriedade.

## <a name="route-constraints"></a>Restrições de rota

Uma restrição de rota impõe a correspondência de tipo em um segmento de rota para um componente.

No exemplo a seguir, a rota para o `Users` componente só corresponde se:

* Um `Id` segmento de rota está presente na URL da solicitação.
* O `Id` segmento é um inteiro (`int`).

[!code-cshtml[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

As restrições de rota mostradas na tabela a seguir estão disponíveis. Para as restrições de rota que correspondem à cultura invariável, consulte o aviso abaixo da tabela para obter mais informações.

| Restrição | Exemplo           | Correspondências de exemplo                                                                  | Constante<br>cultura<br>correspondência |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Não                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Sim                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Sim                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Sim                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Sim                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Não                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Sim                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Sim                              |

> [!WARNING]
> As restrições de rota que verificam a URL e são convertidas em um tipo CLR (como `int` ou `DateTime`) sempre usam a cultura invariável. Essas restrições consideram que a URL não é localizável.

### <a name="routing-with-urls-that-contain-dots"></a>Roteamento com URLs que contêm pontos

No mais alto aplicativo do lado do servidor, a rota padrão em *_Host. cshtml* `/` é`@page "/"`(). Uma URL de solicitação que contém um ponto`.`() não é correspondida pela rota padrão porque a URL parece solicitar um arquivo. Um aplicativo mais alto retorna uma resposta *404-não encontrada* para um arquivo estático que não existe. Para usar rotas que contenham um ponto, configure *_Host. cshtml* com o seguinte modelo de rota:

```cshtml
@page "/{**path}"
```

O `"/{**path}"` modelo inclui:

* Sintaxe *catch-all* de asterisco duplo (`**`) para capturar o caminho entre vários limites de pasta sem barras invertidas`/`de codificação ().
* Um `path` nome de parâmetro de rota.

Para obter mais informações, consulte <xref:fundamentals/routing>.

## <a name="navlink-component"></a>Componente NavLink

Use um `NavLink` componente no lugar de elementos de hiperlink HTML`<a>`() ao criar links de navegação. Um `NavLink` componente se comporta como um `<a>` elemento, exceto que alterna uma `active` classe CSS com base no fato de sua `href` correspondência com a URL atual. A `active` classe ajuda um usuário a entender qual página é a página ativa entre os links de navegação exibidos.

O componente `NavMenu` a seguir cria uma barra de navegação de [inicialização](https://getbootstrap.com/docs/) que demonstra `NavLink` como usar componentes:

[!code-cshtml[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Há duas `NavLinkMatch` opções que você pode atribuir `Match` ao atributo do `<NavLink>` elemento:

* `NavLinkMatch.All`&ndash; O`NavLink` está ativo quando corresponde à URL atual inteira.
* `NavLinkMatch.Prefix`(*padrão*) &ndash; O`NavLink` está ativo quando ele corresponde a qualquer prefixo da URL atual.

No exemplo anterior, a página inicial `NavLink` `href=""` corresponde à URL inicial e recebe apenas a `active` classe CSS na URL do caminho base padrão do aplicativo (por exemplo, `https://localhost:5001/`). O segundo `NavLink` recebe a `active` classe quando o usuário visita qualquer URL com um `MyComponent` prefixo (por exemplo, `https://localhost:5001/MyComponent` e `https://localhost:5001/MyComponent/AnotherSegment`).

Atributos `NavLink` de componente adicionais são passados para a marca de âncora renderizada. No exemplo a seguir, o `NavLink` componente inclui o `target` atributo:

```cshtml
<NavLink href="my-page" target="_blank">My page</NavLink>
```

A seguinte marcação HTML é renderizada:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>Auxiliares de URI e estado de navegação

Use `Microsoft.AspNetCore.Components.NavigationManager` para trabalhar com URIs e navegação no C# código. `NavigationManager`fornece o evento e os métodos mostrados na tabela a seguir.

| Membro | Descrição |
| ------ | ----------- |
| `Uri` | Obtém o URI absoluto atual. |
| `BaseUri` | Obtém o URI de base (com uma barra à direita) que pode ser anexado a caminhos de URI relativos para produzir um URI absoluto. Normalmente, `BaseUri` corresponde `href` ao atributo no elemento `<base>` do documento em *wwwroot/index.html* (mais de um lado do cliente) ou *pages/_Host. cshtml* (no lado do servidor). |
| `NavigateTo` | Navega para o URI especificado. Se `forceLoad` for `true`:<ul><li>O roteamento do lado do cliente é ignorado.</li><li>O navegador é forçado a carregar a nova página do servidor, seja ou não o URI normalmente manipulado pelo roteador do lado do cliente.</li></ul> |
| `LocationChanged` | Um evento que é acionado quando o local de navegação é alterado. |
| `ToAbsoluteUri` | Converte um URI relativo em um URI absoluto. |
| `ToBaseRelativePath` | Dado um URI de base (por exemplo, um URI retornado anteriormente `GetBaseUri`pelo), converte um URI absoluto em um URI relativo ao prefixo de URI de base. |

O componente a seguir navega para o componente do `Counter` aplicativo quando o botão é selecionado:

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

