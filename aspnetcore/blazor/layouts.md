---
title: ASP.NET Core layouts mais incrivelmente
author: guardrex
description: Saiba como criar componentes de layout reutilizáveis para aplicativos mais úteis.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/layouts
ms.openlocfilehash: 2d652e149381f0a93e3135da978ab5737d47c6f1
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "68948216"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="146e2-103">ASP.NET Core layouts mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="146e2-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="146e2-104">Por [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="146e2-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="146e2-105">Alguns elementos do aplicativo, como menus, mensagens de direitos autorais e logotipos da empresa, geralmente são parte do layout geral do aplicativo e usados por cada componente no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="146e2-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="146e2-106">Copiar o código desses elementos em todos os componentes de um aplicativo não é uma abordagem&mdash;eficiente toda vez que um dos elementos requer uma atualização, cada componente deve ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="146e2-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="146e2-107">Essa duplicação é difícil de manter e pode levar a conteúdo inconsistente ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="146e2-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="146e2-108">Os layouts resolvem esse problema.</span><span class="sxs-lookup"><span data-stu-id="146e2-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="146e2-109">Tecnicamente, um layout é apenas outro componente.</span><span class="sxs-lookup"><span data-stu-id="146e2-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="146e2-110">Um layout é definido em um modelo Razor ou no C# código e pode usar [vinculação de dados](xref:blazor/components#data-binding), [injeção de dependência](xref:blazor/dependency-injection)e outros cenários de componente.</span><span class="sxs-lookup"><span data-stu-id="146e2-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components#data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="146e2-111">Para transformar um *componente* em um *layout*, o componente:</span><span class="sxs-lookup"><span data-stu-id="146e2-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="146e2-112">Herda de `LayoutComponentBase`, que define uma `Body` propriedade para o conteúdo renderizado dentro do layout.</span><span class="sxs-lookup"><span data-stu-id="146e2-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="146e2-113">Usa o sintaxe Razor `@Body` para especificar o local na marcação de layout onde o conteúdo é renderizado.</span><span class="sxs-lookup"><span data-stu-id="146e2-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="146e2-114">O exemplo de código a seguir mostra o modelo do Razor de um componente de layout, *MainLayout. Razor*.</span><span class="sxs-lookup"><span data-stu-id="146e2-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="146e2-115">O layout herda `LayoutComponentBase` e define o `@Body` entre a barra de navegação e o rodapé:</span><span class="sxs-lookup"><span data-stu-id="146e2-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="146e2-116">Especificar um layout em um componente</span><span class="sxs-lookup"><span data-stu-id="146e2-116">Specify a layout in a component</span></span>

<span data-ttu-id="146e2-117">Use a diretiva `@layout` Razor para aplicar um layout a um componente.</span><span class="sxs-lookup"><span data-stu-id="146e2-117">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="146e2-118">O compilador converte `@layout` em um `LayoutAttribute`, que é aplicado à classe de componente.</span><span class="sxs-lookup"><span data-stu-id="146e2-118">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="146e2-119">O conteúdo do seguinte componente, *masterlist. Razor*, é inserido `MainLayout` no na posição de: `@Body`</span><span class="sxs-lookup"><span data-stu-id="146e2-119">The content of the following component, *MasterList.razor*, is inserted into the `MainLayout` at the position of `@Body`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

## <a name="centralized-layout-selection"></a><span data-ttu-id="146e2-120">Seleção de layout centralizado</span><span class="sxs-lookup"><span data-stu-id="146e2-120">Centralized layout selection</span></span>

<span data-ttu-id="146e2-121">Cada pasta de um aplicativo pode, opcionalmente, conter um arquivo de modelo chamado *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="146e2-121">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="146e2-122">O compilador inclui as diretivas especificadas no arquivo Imports em todos os modelos do Razor na mesma pasta e recursivamente em todas as suas subpastas.</span><span class="sxs-lookup"><span data-stu-id="146e2-122">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="146e2-123">Portanto, um arquivo *_Imports. Razor* que `@layout MainLayout` contém garante que todos os componentes em uma pasta usem `MainLayout`o.</span><span class="sxs-lookup"><span data-stu-id="146e2-123">Therefore, an *_Imports.razor* file containing `@layout MainLayout` ensures that all of the components in a folder use `MainLayout`.</span></span> <span data-ttu-id="146e2-124">Não há necessidade de adicionar `@layout MainLayout` repetidamente a todos os arquivos *. Razor* dentro da pasta e subpastas.</span><span class="sxs-lookup"><span data-stu-id="146e2-124">There's no need to repeatedly add `@layout MainLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="146e2-125">`@using`as diretivas também são aplicadas aos componentes da mesma maneira.</span><span class="sxs-lookup"><span data-stu-id="146e2-125">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="146e2-126">As seguintes importações de arquivo *_Imports. Razor* :</span><span class="sxs-lookup"><span data-stu-id="146e2-126">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="146e2-127">`MainLayout`.</span><span class="sxs-lookup"><span data-stu-id="146e2-127">`MainLayout`.</span></span>
* <span data-ttu-id="146e2-128">Todos os componentes do Razor na mesma pasta e em todas as subpastas.</span><span class="sxs-lookup"><span data-stu-id="146e2-128">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="146e2-129">O namespace `BlazorApp1.Data`.</span><span class="sxs-lookup"><span data-stu-id="146e2-129">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="146e2-130">O arquivo *_Imports. Razor* é semelhante ao [arquivo _ViewImports. cshtml para exibições e páginas do Razor](xref:mvc/views/layout#importing-shared-directives) , mas aplicado especificamente aos arquivos de componente do Razor.</span><span class="sxs-lookup"><span data-stu-id="146e2-130">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="146e2-131">Os modelos mais poseriais usam arquivos *_Imports. Razor* para seleção de layout.</span><span class="sxs-lookup"><span data-stu-id="146e2-131">The Blazor templates use *_Imports.razor* files for layout selection.</span></span> <span data-ttu-id="146e2-132">Um aplicativo criado com base em um modelo mais claro contém o arquivo *_Imports. Razor* na raiz do projeto e na pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="146e2-132">An app created from a Blazor template contains the *_Imports.razor* file in the root of the project and in the *Pages* folder.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="146e2-133">Layouts aninhados</span><span class="sxs-lookup"><span data-stu-id="146e2-133">Nested layouts</span></span>

<span data-ttu-id="146e2-134">Os aplicativos podem consistir em layouts aninhados.</span><span class="sxs-lookup"><span data-stu-id="146e2-134">Apps can consist of nested layouts.</span></span> <span data-ttu-id="146e2-135">Um componente pode fazer referência a um layout que, por sua vez, faz referência a outro layout.</span><span class="sxs-lookup"><span data-stu-id="146e2-135">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="146e2-136">Por exemplo, os layouts de aninhamento são usados para criar uma estrutura de menu de vários níveis.</span><span class="sxs-lookup"><span data-stu-id="146e2-136">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="146e2-137">O exemplo a seguir mostra como usar layouts aninhados.</span><span class="sxs-lookup"><span data-stu-id="146e2-137">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="146e2-138">O arquivo *EpisodesComponent. Razor* é o componente a ser exibido.</span><span class="sxs-lookup"><span data-stu-id="146e2-138">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="146e2-139">O componente faz referência `MasterListLayout`a:</span><span class="sxs-lookup"><span data-stu-id="146e2-139">The component references the `MasterListLayout`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="146e2-140">O arquivo *MasterListLayout. Razor* fornece o `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="146e2-140">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="146e2-141">O layout faz referência a outro `MasterLayout`layout,, onde é renderizado.</span><span class="sxs-lookup"><span data-stu-id="146e2-141">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="146e2-142">`EpisodesComponent`é renderizado `@Body` onde aparece:</span><span class="sxs-lookup"><span data-stu-id="146e2-142">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="146e2-143">Finalmente, `MasterLayout` no *MasterLayout. Razor* contém os elementos de layout de nível superior, como o cabeçalho, o menu principal e o rodapé.</span><span class="sxs-lookup"><span data-stu-id="146e2-143">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="146e2-144">`MasterListLayout`with `EpisodesComponent` são renderizados `@Body` onde aparece:</span><span class="sxs-lookup"><span data-stu-id="146e2-144">`MasterListLayout` with `EpisodesComponent` are rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a><span data-ttu-id="146e2-145">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="146e2-145">Additional resources</span></span>

* <xref:mvc/views/layout>
