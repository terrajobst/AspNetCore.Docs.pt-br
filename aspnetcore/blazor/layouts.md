---
title: Layouts de Blazor ASP.NET Core
author: guardrex
description: Saiba como criar componentes de layout reutilizáveis para aplicativos Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/layouts
ms.openlocfilehash: 90acfb0d4e9daadb12be79de6bd0c99fc545697a
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944051"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a><span data-ttu-id="f7bfc-103">Layouts de Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7bfc-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="f7bfc-104">Por [Rainer Stropek](https://www.timecockpit.com) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f7bfc-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f7bfc-105">Alguns elementos do aplicativo, como menus, mensagens de direitos autorais e logotipos da empresa, geralmente são parte do layout geral do aplicativo e usados por cada componente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="f7bfc-106">Copiar o código desses elementos em todos os componentes de um aplicativo não é uma abordagem eficiente&mdash;cada vez que um dos elementos requer uma atualização, cada componente deve ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="f7bfc-107">Essa duplicação é difícil de manter e pode levar a conteúdo inconsistente ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="f7bfc-108">Os *layouts* resolvem esse problema.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="f7bfc-109">Tecnicamente, um layout é apenas outro componente.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="f7bfc-110">Um layout é definido em um modelo Razor ou no C# código e pode usar [vinculação de dados](xref:blazor/components#data-binding), [injeção de dependência](xref:blazor/dependency-injection)e outros cenários de componente.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components#data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="f7bfc-111">Para transformar um *componente* em um *layout*, o componente:</span><span class="sxs-lookup"><span data-stu-id="f7bfc-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="f7bfc-112">Herda de `LayoutComponentBase`, que define uma propriedade `Body` para o conteúdo renderizado dentro do layout.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="f7bfc-113">Usa o sintaxe Razor `@Body` para especificar o local na marcação de layout onde o conteúdo é renderizado.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="f7bfc-114">O exemplo de código a seguir mostra o modelo do Razor de um componente de layout, *MainLayout. Razor*.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="f7bfc-115">O layout herda `LayoutComponentBase` e define o `@Body` entre a barra de navegação e o rodapé:</span><span class="sxs-lookup"><span data-stu-id="f7bfc-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="f7bfc-116">Em um aplicativo baseado em um dos modelos de aplicativo Blazor, o componente `MainLayout` (*MainLayout. Razor*) está na pasta *compartilhada* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="f7bfc-117">Layout padrão</span><span class="sxs-lookup"><span data-stu-id="f7bfc-117">Default layout</span></span>

<span data-ttu-id="f7bfc-118">Especifique o layout do aplicativo padrão no componente `Router` no arquivo *app. Razor* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="f7bfc-119">O componente `Router` a seguir, fornecido pelos modelos de Blazor padrão, define o layout padrão para o componente `MainLayout`:</span><span class="sxs-lookup"><span data-stu-id="f7bfc-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="f7bfc-120">Para fornecer um layout padrão para conteúdo de `NotFound`, especifique um `LayoutView` para `NotFound` conteúdo:</span><span class="sxs-lookup"><span data-stu-id="f7bfc-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="f7bfc-121">Para obter mais informações sobre o componente `Router`, consulte <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="f7bfc-122">A especificação do layout como um layout padrão no roteador é uma prática útil, pois pode ser substituída por componente ou por pasta.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="f7bfc-123">Prefira usar o roteador para definir o layout padrão do aplicativo porque ele é a técnica mais geral.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="f7bfc-124">Especificar um layout em um componente</span><span class="sxs-lookup"><span data-stu-id="f7bfc-124">Specify a layout in a component</span></span>

<span data-ttu-id="f7bfc-125">Use a diretiva Razor `@layout` para aplicar um layout a um componente.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="f7bfc-126">O compilador converte `@layout` em um `LayoutAttribute`, que é aplicado à classe de componente.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="f7bfc-127">O conteúdo do componente de `MasterList` a seguir é inserido na `MasterLayout` na posição de `@Body`:</span><span class="sxs-lookup"><span data-stu-id="f7bfc-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="f7bfc-128">Especificar o layout diretamente em um componente substitui um *Layout padrão* definido no roteador ou uma diretiva `@layout` importada de *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="f7bfc-129">Seleção de layout centralizado</span><span class="sxs-lookup"><span data-stu-id="f7bfc-129">Centralized layout selection</span></span>

<span data-ttu-id="f7bfc-130">Cada pasta de um aplicativo pode, opcionalmente, conter um arquivo de modelo chamado *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="f7bfc-131">O compilador inclui as diretivas especificadas no arquivo Imports em todos os modelos do Razor na mesma pasta e recursivamente em todas as suas subpastas.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="f7bfc-132">Portanto, um arquivo *_Imports. Razor* contendo `@layout MyCoolLayout` garante que todos os componentes em uma pasta usem `MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="f7bfc-133">Não há necessidade de adicionar `@layout MyCoolLayout` repetidamente a todos os arquivos *. Razor* dentro da pasta e das subpastas.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="f7bfc-134">`@using` diretivas também são aplicadas aos componentes da mesma maneira.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="f7bfc-135">As seguintes importações de arquivo *_Imports. Razor* :</span><span class="sxs-lookup"><span data-stu-id="f7bfc-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="f7bfc-136">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="f7bfc-137">Todos os componentes do Razor na mesma pasta e em todas as subpastas.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="f7bfc-138">O namespace `BlazorApp1.Data`.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="f7bfc-139">O arquivo *_Imports. Razor* é semelhante ao [arquivo _ViewImports. cshtml para exibições e páginas do Razor](xref:mvc/views/layout#importing-shared-directives) , mas aplicado especificamente aos arquivos de componente do Razor.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="f7bfc-140">A especificação de um layout em *_Imports. Razor* substitui um layout especificado como o *Layout padrão*do roteador.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="f7bfc-141">Layouts aninhados</span><span class="sxs-lookup"><span data-stu-id="f7bfc-141">Nested layouts</span></span>

<span data-ttu-id="f7bfc-142">Os aplicativos podem consistir em layouts aninhados.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="f7bfc-143">Um componente pode fazer referência a um layout que, por sua vez, faz referência a outro layout.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="f7bfc-144">Por exemplo, os layouts de aninhamento são usados para criar uma estrutura de menu de vários níveis.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="f7bfc-145">O exemplo a seguir mostra como usar layouts aninhados.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="f7bfc-146">O arquivo *EpisodesComponent. Razor* é o componente a ser exibido.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="f7bfc-147">O componente faz referência a `MasterListLayout`:</span><span class="sxs-lookup"><span data-stu-id="f7bfc-147">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="f7bfc-148">O arquivo *MasterListLayout. Razor* fornece o `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="f7bfc-149">O layout faz referência a outro layout, `MasterLayout`, onde ele é renderizado.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="f7bfc-150">`EpisodesComponent` é renderizado onde `@Body` aparece:</span><span class="sxs-lookup"><span data-stu-id="f7bfc-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="f7bfc-151">Por fim, `MasterLayout` no *MasterLayout. Razor* contém os elementos de layout de nível superior, como o cabeçalho, o menu principal e o rodapé.</span><span class="sxs-lookup"><span data-stu-id="f7bfc-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="f7bfc-152">`MasterListLayout` com o `EpisodesComponent` é renderizado onde `@Body` aparece:</span><span class="sxs-lookup"><span data-stu-id="f7bfc-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a><span data-ttu-id="f7bfc-153">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f7bfc-153">Additional resources</span></span>

* <xref:mvc/views/layout>
