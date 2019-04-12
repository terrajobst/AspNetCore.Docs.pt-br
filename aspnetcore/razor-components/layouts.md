---
title: Layouts de componentes do Razor
author: guardrex
description: Saiba como criar componentes reutilizáveis de layout para aplicativos de componentes do Razor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/layouts
ms.openlocfilehash: 31ed940ce40e3ae6e3744418cf241d396308f4fe
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59515340"
---
# <a name="razor-components-layouts"></a><span data-ttu-id="70bd1-103">Layouts de componentes do Razor</span><span class="sxs-lookup"><span data-stu-id="70bd1-103">Razor Components layouts</span></span>

<span data-ttu-id="70bd1-104">Por [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="70bd1-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="70bd1-105">Os aplicativos normalmente contêm mais de um componente.</span><span class="sxs-lookup"><span data-stu-id="70bd1-105">Apps typically contain more than one component.</span></span> <span data-ttu-id="70bd1-106">Elementos de layout, como menus, mensagens de direitos autorais e logotipos, devem estar presentes em todos os componentes.</span><span class="sxs-lookup"><span data-stu-id="70bd1-106">Layout elements, such as menus, copyright messages, and logos, must be present on all components.</span></span> <span data-ttu-id="70bd1-107">Copiando o código desses elementos de layout para todos os componentes de um aplicativo não é uma abordagem eficiente.</span><span class="sxs-lookup"><span data-stu-id="70bd1-107">Copying the code of these layout elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="70bd1-108">Essa duplicação é difícil de manter e provavelmente leva ao conteúdo inconsistente ao longo do tempo.</span><span class="sxs-lookup"><span data-stu-id="70bd1-108">Such duplication is hard to maintain and probably leads to inconsistent content over time.</span></span> <span data-ttu-id="70bd1-109">*Layouts* resolver esse problema.</span><span class="sxs-lookup"><span data-stu-id="70bd1-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="70bd1-110">Tecnicamente, um layout é apenas outro componente.</span><span class="sxs-lookup"><span data-stu-id="70bd1-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="70bd1-111">Um layout é definido em um modelo Razor ou em C# de código e pode conter [vinculação de dados](xref:razor-components/components#data-binding), [injeção de dependência](xref:razor-components/dependency-injection)e outros recursos comuns de componentes.</span><span class="sxs-lookup"><span data-stu-id="70bd1-111">A layout is defined in a Razor template or in C# code and can contain [data binding](xref:razor-components/components#data-binding), [dependency injection](xref:razor-components/dependency-injection), and other ordinary features of components.</span></span>

<span data-ttu-id="70bd1-112">Dois aspectos adicionais ativar uma *componente* em um *layout*</span><span class="sxs-lookup"><span data-stu-id="70bd1-112">Two additional aspects turn a *component* into a *layout*</span></span>

* <span data-ttu-id="70bd1-113">O componente de layout deve herdar de `LayoutComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="70bd1-113">The layout component must inherit from `LayoutComponentBase`.</span></span> `LayoutComponentBase` <span data-ttu-id="70bd1-114">define um `Body` propriedade que contém o conteúdo a ser renderizado dentro do layout.</span><span class="sxs-lookup"><span data-stu-id="70bd1-114">defines a `Body` property that contains the content to be rendered inside the layout.</span></span>
* <span data-ttu-id="70bd1-115">O componente de layout usa o `Body` propriedade para especificar onde o conteúdo do corpo deve ser renderizado usando a sintaxe do Razor `@Body`.</span><span class="sxs-lookup"><span data-stu-id="70bd1-115">The layout component uses the `Body` property to specify where the body content should be rendered using the Razor syntax `@Body`.</span></span> <span data-ttu-id="70bd1-116">Durante o processamento, `@Body` é substituída pelo conteúdo do layout.</span><span class="sxs-lookup"><span data-stu-id="70bd1-116">During rendering, `@Body` is replaced by the content of the layout.</span></span>

<span data-ttu-id="70bd1-117">O exemplo de código a seguir mostra o modelo do Razor de um componente de layout.</span><span class="sxs-lookup"><span data-stu-id="70bd1-117">The following code sample shows the Razor template of a layout component.</span></span> <span data-ttu-id="70bd1-118">Observe o uso de `LayoutComponentBase` e `@Body`:</span><span class="sxs-lookup"><span data-stu-id="70bd1-118">Note the use of `LayoutComponentBase` and `@Body`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.cshtml)]

## <a name="use-a-layout-in-a-component"></a><span data-ttu-id="70bd1-119">Usar um layout em um componente</span><span class="sxs-lookup"><span data-stu-id="70bd1-119">Use a layout in a component</span></span>

<span data-ttu-id="70bd1-120">Use a diretiva do Razor `@layout` para aplicar um layout para um componente.</span><span class="sxs-lookup"><span data-stu-id="70bd1-120">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="70bd1-121">O compilador converte essa diretiva em um `LayoutAttribute`, que é aplicado à classe de componente.</span><span class="sxs-lookup"><span data-stu-id="70bd1-121">The compiler converts this directive into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="70bd1-122">O exemplo de código a seguir demonstra o conceito.</span><span class="sxs-lookup"><span data-stu-id="70bd1-122">The following code sample demonstrates the concept.</span></span> <span data-ttu-id="70bd1-123">O conteúdo desse componente é inserido na *MasterLayout* na posição do `@Body`:</span><span class="sxs-lookup"><span data-stu-id="70bd1-123">The content of this component is inserted into the *MasterLayout* at the position of `@Body`:</span></span>

```cshtml
@layout MasterLayout
@page "/master-list"

<h2>Master Episode List</h2>
```

## <a name="centralized-layout-selection"></a><span data-ttu-id="70bd1-124">Seleção de layout centralizado</span><span class="sxs-lookup"><span data-stu-id="70bd1-124">Centralized layout selection</span></span>

<span data-ttu-id="70bd1-125">Todas as pastas de um um aplicativo pode, opcionalmente, conter um arquivo de modelo chamado *viewimports. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="70bd1-125">Every folder of a an app can optionally contain a template file named *_ViewImports.cshtml*.</span></span> <span data-ttu-id="70bd1-126">O compilador incluirá as diretivas especificadas no arquivo de importações de exibição em todos os modelos do Razor na mesma pasta e recursivamente em todas as suas subpastas.</span><span class="sxs-lookup"><span data-stu-id="70bd1-126">The compiler includes the directives specified in the view imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="70bd1-127">Portanto, uma *viewimports. cshtml* arquivo contendo `@layout MainLayout` garante que todos os componentes em uma pasta, use o *MainLayout* layout.</span><span class="sxs-lookup"><span data-stu-id="70bd1-127">Therefore, a *_ViewImports.cshtml* file containing `@layout MainLayout` ensures that all of the components in a folder use the *MainLayout* layout.</span></span> <span data-ttu-id="70bd1-128">Não é necessário adicionar repetidamente `@layout` a todos os *.razor* arquivos.</span><span class="sxs-lookup"><span data-stu-id="70bd1-128">There's no need to repeatedly add `@layout` to all of the *.razor* files.</span></span>

<span data-ttu-id="70bd1-129">Observe que o modelo padrão usa a *viewimports. cshtml* mecanismo para seleção de layout.</span><span class="sxs-lookup"><span data-stu-id="70bd1-129">Note that the default template uses the *_ViewImports.cshtml* mechanism for layout selection.</span></span> <span data-ttu-id="70bd1-130">Um aplicativo recém-criado contém o *viewimports. cshtml* de arquivo na *componentes/páginas* pasta.</span><span class="sxs-lookup"><span data-stu-id="70bd1-130">A newly created app contains the *_ViewImports.cshtml* file in the *Components/Pages* folder.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="70bd1-131">Layouts aninhados</span><span class="sxs-lookup"><span data-stu-id="70bd1-131">Nested layouts</span></span>

<span data-ttu-id="70bd1-132">Aplicativos podem consistir em layouts aninhados.</span><span class="sxs-lookup"><span data-stu-id="70bd1-132">Apps can consist of nested layouts.</span></span> <span data-ttu-id="70bd1-133">Um componente pode fazer referência a um layout que por sua vez faz referência a outro layout.</span><span class="sxs-lookup"><span data-stu-id="70bd1-133">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="70bd1-134">Por exemplo, os layouts de aninhamento podem ser usados para refletir uma estrutura de vários níveis de menu.</span><span class="sxs-lookup"><span data-stu-id="70bd1-134">For example, nesting layouts can be used to reflect a multi-level menu structure.</span></span>

<span data-ttu-id="70bd1-135">Os exemplos de código a seguir mostram como usar layouts aninhados.</span><span class="sxs-lookup"><span data-stu-id="70bd1-135">The following code samples show how to use nested layouts.</span></span> <span data-ttu-id="70bd1-136">O *EpisodesComponent.cshtml* arquivo é o componente para exibir.</span><span class="sxs-lookup"><span data-stu-id="70bd1-136">The *EpisodesComponent.cshtml* file is the component to display.</span></span> <span data-ttu-id="70bd1-137">Observe que o componente o referencia o layout `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="70bd1-137">Note that the component references the layout `MasterListLayout`.</span></span>

<span data-ttu-id="70bd1-138">*EpisodesComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="70bd1-138">*EpisodesComponent.cshtml*:</span></span>

```cshtml
@layout MasterListLayout
@page "/master-list/episodes"

<h1>Episodes</h1>
```

<span data-ttu-id="70bd1-139">O *MasterListLayout.cshtml* arquivo fornece o `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="70bd1-139">The *MasterListLayout.cshtml* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="70bd1-140">O layout faz referência a outro layout, `MasterLayout`, onde vai a ser inserido.</span><span class="sxs-lookup"><span data-stu-id="70bd1-140">The layout references another layout, `MasterLayout`, where it's going to be embedded.</span></span>

<span data-ttu-id="70bd1-141">*MasterListLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="70bd1-141">*MasterListLayout.cshtml*:</span></span>

```cshtml
@layout MasterLayout
@inherits LayoutComponentBase

<nav>
    <!-- Menu structure of master list -->
    ...
</nav>

@Body
```

<span data-ttu-id="70bd1-142">Por fim, `MasterLayout` contém os elementos de layout de nível superior, como o cabeçalho, rodapé e menu principal.</span><span class="sxs-lookup"><span data-stu-id="70bd1-142">Finally, `MasterLayout` contains the top-level layout elements, such as the header, footer, and main menu.</span></span>

<span data-ttu-id="70bd1-143">*MasterLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="70bd1-143">*MasterLayout.cshtml*:</span></span>

```cshtml
@inherits LayoutComponentBase

<header>...</header>
<nav>...</nav>

@Body
```
