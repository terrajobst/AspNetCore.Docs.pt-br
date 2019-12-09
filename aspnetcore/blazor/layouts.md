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
# <a name="aspnet-core-opno-locblazor-layouts"></a>Layouts de Blazor ASP.NET Core

Por [Rainer Stropek](https://www.timecockpit.com) e [Luke Latham](https://github.com/guardrex)

Alguns elementos do aplicativo, como menus, mensagens de direitos autorais e logotipos da empresa, geralmente são parte do layout geral do aplicativo e usados por cada componente no aplicativo. Copiar o código desses elementos em todos os componentes de um aplicativo não é uma abordagem eficiente&mdash;cada vez que um dos elementos requer uma atualização, cada componente deve ser atualizado. Essa duplicação é difícil de manter e pode levar a conteúdo inconsistente ao longo do tempo. Os *layouts* resolvem esse problema.

Tecnicamente, um layout é apenas outro componente. Um layout é definido em um modelo Razor ou no C# código e pode usar [vinculação de dados](xref:blazor/components#data-binding), [injeção de dependência](xref:blazor/dependency-injection)e outros cenários de componente.

Para transformar um *componente* em um *layout*, o componente:

* Herda de `LayoutComponentBase`, que define uma propriedade `Body` para o conteúdo renderizado dentro do layout.
* Usa o sintaxe Razor `@Body` para especificar o local na marcação de layout onde o conteúdo é renderizado.

O exemplo de código a seguir mostra o modelo do Razor de um componente de layout, *MainLayout. Razor*. O layout herda `LayoutComponentBase` e define o `@Body` entre a barra de navegação e o rodapé:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

Em um aplicativo baseado em um dos modelos de aplicativo Blazor, o componente `MainLayout` (*MainLayout. Razor*) está na pasta *compartilhada* do aplicativo.

## <a name="default-layout"></a>Layout padrão

Especifique o layout do aplicativo padrão no componente `Router` no arquivo *app. Razor* do aplicativo. O componente `Router` a seguir, fornecido pelos modelos de Blazor padrão, define o layout padrão para o componente `MainLayout`:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Para fornecer um layout padrão para conteúdo de `NotFound`, especifique um `LayoutView` para `NotFound` conteúdo:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Para obter mais informações sobre o componente `Router`, consulte <xref:blazor/routing>.

A especificação do layout como um layout padrão no roteador é uma prática útil, pois pode ser substituída por componente ou por pasta. Prefira usar o roteador para definir o layout padrão do aplicativo porque ele é a técnica mais geral.

## <a name="specify-a-layout-in-a-component"></a>Especificar um layout em um componente

Use a diretiva Razor `@layout` para aplicar um layout a um componente. O compilador converte `@layout` em um `LayoutAttribute`, que é aplicado à classe de componente.

O conteúdo do componente de `MasterList` a seguir é inserido na `MasterLayout` na posição de `@Body`:

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Especificar o layout diretamente em um componente substitui um *Layout padrão* definido no roteador ou uma diretiva `@layout` importada de *_Imports. Razor*.

## <a name="centralized-layout-selection"></a>Seleção de layout centralizado

Cada pasta de um aplicativo pode, opcionalmente, conter um arquivo de modelo chamado *_Imports. Razor*. O compilador inclui as diretivas especificadas no arquivo Imports em todos os modelos do Razor na mesma pasta e recursivamente em todas as suas subpastas. Portanto, um arquivo *_Imports. Razor* contendo `@layout MyCoolLayout` garante que todos os componentes em uma pasta usem `MyCoolLayout`. Não há necessidade de adicionar `@layout MyCoolLayout` repetidamente a todos os arquivos *. Razor* dentro da pasta e das subpastas. `@using` diretivas também são aplicadas aos componentes da mesma maneira.

As seguintes importações de arquivo *_Imports. Razor* :

* `MyCoolLayout`.
* Todos os componentes do Razor na mesma pasta e em todas as subpastas.
* O namespace `BlazorApp1.Data`.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

O arquivo *_Imports. Razor* é semelhante ao [arquivo _ViewImports. cshtml para exibições e páginas do Razor](xref:mvc/views/layout#importing-shared-directives) , mas aplicado especificamente aos arquivos de componente do Razor.

A especificação de um layout em *_Imports. Razor* substitui um layout especificado como o *Layout padrão*do roteador.

## <a name="nested-layouts"></a>Layouts aninhados

Os aplicativos podem consistir em layouts aninhados. Um componente pode fazer referência a um layout que, por sua vez, faz referência a outro layout. Por exemplo, os layouts de aninhamento são usados para criar uma estrutura de menu de vários níveis.

O exemplo a seguir mostra como usar layouts aninhados. O arquivo *EpisodesComponent. Razor* é o componente a ser exibido. O componente faz referência a `MasterListLayout`:

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

O arquivo *MasterListLayout. Razor* fornece o `MasterListLayout`. O layout faz referência a outro layout, `MasterLayout`, onde ele é renderizado. `EpisodesComponent` é renderizado onde `@Body` aparece:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Por fim, `MasterLayout` no *MasterLayout. Razor* contém os elementos de layout de nível superior, como o cabeçalho, o menu principal e o rodapé. `MasterListLayout` com o `EpisodesComponent` é renderizado onde `@Body` aparece:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/layout>
