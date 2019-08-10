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
# <a name="aspnet-core-blazor-layouts"></a>ASP.NET Core layouts mais incrivelmente

Por [Rainer Stropek](https://www.timecockpit.com)

Alguns elementos do aplicativo, como menus, mensagens de direitos autorais e logotipos da empresa, geralmente são parte do layout geral do aplicativo e usados por cada componente no aplicativo. Copiar o código desses elementos em todos os componentes de um aplicativo não é uma abordagem&mdash;eficiente toda vez que um dos elementos requer uma atualização, cada componente deve ser atualizado. Essa duplicação é difícil de manter e pode levar a conteúdo inconsistente ao longo do tempo. Os layouts resolvem esse problema.

Tecnicamente, um layout é apenas outro componente. Um layout é definido em um modelo Razor ou no C# código e pode usar [vinculação de dados](xref:blazor/components#data-binding), [injeção de dependência](xref:blazor/dependency-injection)e outros cenários de componente.

Para transformar um *componente* em um *layout*, o componente:

* Herda de `LayoutComponentBase`, que define uma `Body` propriedade para o conteúdo renderizado dentro do layout.
* Usa o sintaxe Razor `@Body` para especificar o local na marcação de layout onde o conteúdo é renderizado.

O exemplo de código a seguir mostra o modelo do Razor de um componente de layout, *MainLayout. Razor*. O layout herda `LayoutComponentBase` e define o `@Body` entre a barra de navegação e o rodapé:

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

## <a name="specify-a-layout-in-a-component"></a>Especificar um layout em um componente

Use a diretiva `@layout` Razor para aplicar um layout a um componente. O compilador converte `@layout` em um `LayoutAttribute`, que é aplicado à classe de componente.

O conteúdo do seguinte componente, *masterlist. Razor*, é inserido `MainLayout` no na posição de: `@Body`

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

## <a name="centralized-layout-selection"></a>Seleção de layout centralizado

Cada pasta de um aplicativo pode, opcionalmente, conter um arquivo de modelo chamado *_Imports. Razor*. O compilador inclui as diretivas especificadas no arquivo Imports em todos os modelos do Razor na mesma pasta e recursivamente em todas as suas subpastas. Portanto, um arquivo *_Imports. Razor* que `@layout MainLayout` contém garante que todos os componentes em uma pasta usem `MainLayout`o. Não há necessidade de adicionar `@layout MainLayout` repetidamente a todos os arquivos *. Razor* dentro da pasta e subpastas. `@using`as diretivas também são aplicadas aos componentes da mesma maneira.

As seguintes importações de arquivo *_Imports. Razor* :

* `MainLayout`.
* Todos os componentes do Razor na mesma pasta e em todas as subpastas.
* O namespace `BlazorApp1.Data`.
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

O arquivo *_Imports. Razor* é semelhante ao [arquivo _ViewImports. cshtml para exibições e páginas do Razor](xref:mvc/views/layout#importing-shared-directives) , mas aplicado especificamente aos arquivos de componente do Razor.

Os modelos mais poseriais usam arquivos *_Imports. Razor* para seleção de layout. Um aplicativo criado com base em um modelo mais claro contém o arquivo *_Imports. Razor* na raiz do projeto e na pasta *páginas* .

## <a name="nested-layouts"></a>Layouts aninhados

Os aplicativos podem consistir em layouts aninhados. Um componente pode fazer referência a um layout que, por sua vez, faz referência a outro layout. Por exemplo, os layouts de aninhamento são usados para criar uma estrutura de menu de vários níveis.

O exemplo a seguir mostra como usar layouts aninhados. O arquivo *EpisodesComponent. Razor* é o componente a ser exibido. O componente faz referência `MasterListLayout`a:

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

O arquivo *MasterListLayout. Razor* fornece o `MasterListLayout`. O layout faz referência a outro `MasterLayout`layout,, onde é renderizado. `EpisodesComponent`é renderizado `@Body` onde aparece:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Finalmente, `MasterLayout` no *MasterLayout. Razor* contém os elementos de layout de nível superior, como o cabeçalho, o menu principal e o rodapé. `MasterListLayout`with `EpisodesComponent` são renderizados `@Body` onde aparece:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/layout>