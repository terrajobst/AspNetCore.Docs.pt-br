---
title: O que há de novo no ASP.NET Core 3,1
author: rick-anderson
description: Saiba mais sobre os novos recursos do ASP.NET Core 3,1.
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: 4737747f84a59780fe70f63195f7580bd812e4de
ms.sourcegitcommit: 169ea5116de729c803685725d96450a270bc55b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74734022"
---
# <a name="whats-new-in-aspnet-core-31"></a>O que há de novo no ASP.NET Core 3,1

Este artigo destaca as alterações mais significativas no ASP.NET Core 3,1 com links para a documentação relevante.

## <a name="partial-class-support-for-razor-components"></a>Suporte de classe parcial para componentes do Razor

Os componentes do Razor agora são gerados como classes parciais. O código para um componente Razor pode ser escrito usando um arquivo code-behind definido como uma classe parcial, em vez de definir todo o código para o componente em um único arquivo. Para obter mais informações, consulte [suporte de classe parcial](xref:blazor/components#partial-class-support).

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor o auxiliar de marca do componente e passar parâmetros para componentes de nível superior

Em Blazor com ASP.NET Core 3,0, os componentes eram renderizados em páginas e exibições usando um auxiliar HTML (`Html.RenderComponentAsync`). No ASP.NET Core 3,1, processe um componente de uma página ou exibição com o novo auxiliar de marca de componente:

```razor
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

O auxiliar HTML permanece com suporte no ASP.NET Core 3,1, mas o auxiliar de marca de componente é recomendado.

os aplicativos do Blazor Server agora podem passar parâmetros para componentes de nível superior durante a renderização inicial. Anteriormente, era possível apenas passar parâmetros para um componente de nível superior com [RenderMode. static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static). Com essa versão, os [processmode. Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) e [RenderModel. ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) têm suporte. Todos os valores de parâmetro especificados são serializados como JSON e incluídos na resposta inicial.

Por exemplo, PreRender um componente `Counter` com um valor de incremento (`IncrementAmount`):

```razor
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Para obter mais informações, consulte [integrar componentes em aplicativos Razor Pages e MVC](xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps).

## <a name="support-for-shared-queues-in-httpsys"></a>Suporte para filas compartilhadas em HTTP. sys

O [http. sys](xref:fundamentals/servers/httpsys) dá suporte à criação de filas de solicitações anônimas. No ASP.NET Core 3,1, adicionamos a capacidade de criar ou anexar a uma fila de solicitações de HTTP. sys chamada existente. Criar ou anexar a uma fila de solicitações HTTP. sys denominada existente permite cenários em que o HTTP. O processo do controlador sys que possui a fila é independente do processo do ouvinte. Essa independência possibilita preservar as conexões existentes e as solicitações enfileiradas entre as reinicializações do processo de ouvinte:

[!code-csharp[](sample/Program.cs?name=snippet)]

<!-- TODO
## Breaking changes for SameSite cookies
-->

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a>Impedir ações padrão para eventos em aplicativos Blazor

Use o atributo de diretiva `@on{EVENT}:preventDefault` para evitar a ação padrão para um evento. No exemplo a seguir, a ação padrão de exibir o caractere da chave na caixa de texto é impedida:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Para obter mais informações, consulte [impedir ações padrão](xref:blazor/components#prevent-default-actions).

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a>Parar a propagação de eventos em aplicativos Blazor

Use o atributo de diretiva `@on{EVENT}:stopPropagation` para parar a propagação do evento. No exemplo a seguir, marcar a caixa de seleção impede que eventos de clique da `<div>` filho se propaguem para o `<div>`pai:

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

Para obter mais informações, consulte [parar a propagação do evento](xref:blazor/components#stop-event-propagation).

## <a name="detailed-errors-during-opno-locblazor-app-development"></a>Erros detalhados durante o desenvolvimento do aplicativo Blazor

Quando um aplicativo Blazor não está funcionando corretamente durante o desenvolvimento, receber informações detalhadas de erro do aplicativo ajuda a solucionar problemas e corrigir o problema. Quando ocorre um erro, Blazor aplicativos exibem uma barra de ouro na parte inferior da tela:

* Durante o desenvolvimento, a barra de ouro direciona você para o console do navegador, onde você pode ver a exceção.
* Em produção, a barra de ouro notifica o usuário de que ocorreu um erro e recomenda a atualização do navegador.

Para obter mais informações, consulte [erros detalhados durante o desenvolvimento](xref:blazor/handle-errors#detailed-errors-during-development).
