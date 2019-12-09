---
title: O que há de novo no ASP.NET Core 3,1
author: rick-anderson
description: Saiba mais sobre os novos recursos do ASP.NET Core 3,1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: 89c676b96ef66f648544a8a884593bdafa3876de
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944220"
---
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="3afcd-103">O que há de novo no ASP.NET Core 3,1</span><span class="sxs-lookup"><span data-stu-id="3afcd-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="3afcd-104">Este artigo destaca as alterações mais significativas no ASP.NET Core 3,1 com links para a documentação relevante.</span><span class="sxs-lookup"><span data-stu-id="3afcd-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-razor-components"></a><span data-ttu-id="3afcd-105">Suporte de classe parcial para componentes do Razor</span><span class="sxs-lookup"><span data-stu-id="3afcd-105">Partial class support for Razor components</span></span>

<span data-ttu-id="3afcd-106">Os componentes do Razor agora são gerados como classes parciais.</span><span class="sxs-lookup"><span data-stu-id="3afcd-106">Razor components are now generated as partial classes.</span></span> <span data-ttu-id="3afcd-107">O código para um componente Razor pode ser escrito usando um arquivo code-behind definido como uma classe parcial, em vez de definir todo o código para o componente em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="3afcd-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="3afcd-108">Para obter mais informações, consulte [suporte de classe parcial](xref:blazor/components#partial-class-support).</span><span class="sxs-lookup"><span data-stu-id="3afcd-108">For more information, see [Partial class support](xref:blazor/components#partial-class-support).</span></span>

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor<span data-ttu-id="3afcd-109"> o auxiliar de marca do componente e passar parâmetros para componentes de nível superior</span><span class="sxs-lookup"><span data-stu-id="3afcd-109"> Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="3afcd-110">Em Blazor com ASP.NET Core 3,0, os componentes eram renderizados em páginas e exibições usando um auxiliar HTML (`Html.RenderComponentAsync`).</span><span class="sxs-lookup"><span data-stu-id="3afcd-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="3afcd-111">No ASP.NET Core 3,1, processe um componente de uma página ou exibição com o novo auxiliar de marca de componente:</span><span class="sxs-lookup"><span data-stu-id="3afcd-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="3afcd-112">O auxiliar HTML permanece com suporte no ASP.NET Core 3,1, mas o auxiliar de marca de componente é recomendado.</span><span class="sxs-lookup"><span data-stu-id="3afcd-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

<span data-ttu-id="3afcd-113">os aplicativos do Blazor Server agora podem passar parâmetros para componentes de nível superior durante a renderização inicial.</span><span class="sxs-lookup"><span data-stu-id="3afcd-113">Blazor Server apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="3afcd-114">Anteriormente, era possível apenas passar parâmetros para um componente de nível superior com [RenderMode. static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span><span class="sxs-lookup"><span data-stu-id="3afcd-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="3afcd-115">Com essa versão, os [processmode. Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) e [RenderModel. ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) têm suporte.</span><span class="sxs-lookup"><span data-stu-id="3afcd-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="3afcd-116">Todos os valores de parâmetro especificados são serializados como JSON e incluídos na resposta inicial.</span><span class="sxs-lookup"><span data-stu-id="3afcd-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="3afcd-117">Por exemplo, PreRender um componente `Counter` com um valor de incremento (`IncrementAmount`):</span><span class="sxs-lookup"><span data-stu-id="3afcd-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```razor
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="3afcd-118">Para obter mais informações, consulte [integrar componentes em aplicativos Razor Pages e MVC](xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps).</span><span class="sxs-lookup"><span data-stu-id="3afcd-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="3afcd-119">Suporte para filas compartilhadas em HTTP. sys</span><span class="sxs-lookup"><span data-stu-id="3afcd-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="3afcd-120">O [http. sys](xref:fundamentals/servers/httpsys) dá suporte à criação de filas de solicitações anônimas.</span><span class="sxs-lookup"><span data-stu-id="3afcd-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="3afcd-121">No ASP.NET Core 3,1, adicionamos a capacidade de criar ou anexar a uma fila de solicitações de HTTP. sys chamada existente.</span><span class="sxs-lookup"><span data-stu-id="3afcd-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="3afcd-122">A criação ou anexação a uma fila de solicitações HTTP. sys denominada permite cenários em que o processo do controlador HTTP. sys que possui a fila é independente do processo do ouvinte.</span><span class="sxs-lookup"><span data-stu-id="3afcd-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="3afcd-123">Essa independência possibilita preservar as conexões existentes e as solicitações enfileiradas entre as reinicializações do processo de ouvinte:</span><span class="sxs-lookup"><span data-stu-id="3afcd-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a><span data-ttu-id="3afcd-124">Alterações recentes para cookies SameSite</span><span class="sxs-lookup"><span data-stu-id="3afcd-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="3afcd-125">O comportamento dos cookies SameSite foi alterado para refletir as próximas alterações do navegador.</span><span class="sxs-lookup"><span data-stu-id="3afcd-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="3afcd-126">Isso pode afetar os cenários de autenticação como AzureAd, OpenIdConnect ou WsFederation.</span><span class="sxs-lookup"><span data-stu-id="3afcd-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="3afcd-127">Para obter mais informações, consulte <xref:security/samesite>.</span><span class="sxs-lookup"><span data-stu-id="3afcd-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a><span data-ttu-id="3afcd-128">Impedir ações padrão para eventos em aplicativos Blazor</span><span class="sxs-lookup"><span data-stu-id="3afcd-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="3afcd-129">Use o atributo de diretiva `@on{EVENT}:preventDefault` para evitar a ação padrão para um evento.</span><span class="sxs-lookup"><span data-stu-id="3afcd-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="3afcd-130">No exemplo a seguir, a ação padrão de exibir o caractere da chave na caixa de texto é impedida:</span><span class="sxs-lookup"><span data-stu-id="3afcd-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="3afcd-131">Para obter mais informações, consulte [impedir ações padrão](xref:blazor/components#prevent-default-actions).</span><span class="sxs-lookup"><span data-stu-id="3afcd-131">For more information, see [Prevent default actions](xref:blazor/components#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a><span data-ttu-id="3afcd-132">Parar a propagação de eventos em aplicativos Blazor</span><span class="sxs-lookup"><span data-stu-id="3afcd-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="3afcd-133">Use o atributo de diretiva `@on{EVENT}:stopPropagation` para parar a propagação do evento.</span><span class="sxs-lookup"><span data-stu-id="3afcd-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="3afcd-134">No exemplo a seguir, marcar a caixa de seleção impede que eventos de clique da `<div>` filho se propaguem para o `<div>`pai:</span><span class="sxs-lookup"><span data-stu-id="3afcd-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

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

<span data-ttu-id="3afcd-135">Para obter mais informações, consulte [parar a propagação do evento](xref:blazor/components#stop-event-propagation).</span><span class="sxs-lookup"><span data-stu-id="3afcd-135">For more information, see [Stop event propagation](xref:blazor/components#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-opno-locblazor-app-development"></a><span data-ttu-id="3afcd-136">Erros detalhados durante o desenvolvimento do aplicativo Blazor</span><span class="sxs-lookup"><span data-stu-id="3afcd-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="3afcd-137">Quando um aplicativo Blazor não está funcionando corretamente durante o desenvolvimento, receber informações detalhadas de erro do aplicativo ajuda a solucionar problemas e corrigir o problema.</span><span class="sxs-lookup"><span data-stu-id="3afcd-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="3afcd-138">Quando ocorre um erro, Blazor aplicativos exibem uma barra de ouro na parte inferior da tela:</span><span class="sxs-lookup"><span data-stu-id="3afcd-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="3afcd-139">Durante o desenvolvimento, a barra de ouro direciona você para o console do navegador, onde você pode ver a exceção.</span><span class="sxs-lookup"><span data-stu-id="3afcd-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="3afcd-140">Em produção, a barra de ouro notifica o usuário de que ocorreu um erro e recomenda a atualização do navegador.</span><span class="sxs-lookup"><span data-stu-id="3afcd-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="3afcd-141">Para obter mais informações, consulte [erros detalhados durante o desenvolvimento](xref:blazor/handle-errors#detailed-errors-during-development).</span><span class="sxs-lookup"><span data-stu-id="3afcd-141">For more information, see [Detailed errors during development](xref:blazor/handle-errors#detailed-errors-during-development).</span></span>
