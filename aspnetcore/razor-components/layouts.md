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
# <a name="razor-components-layouts"></a>Layouts de componentes do Razor

Por [Rainer Stropek](https://www.timecockpit.com)

Os aplicativos normalmente contêm mais de um componente. Elementos de layout, como menus, mensagens de direitos autorais e logotipos, devem estar presentes em todos os componentes. Copiando o código desses elementos de layout para todos os componentes de um aplicativo não é uma abordagem eficiente. Essa duplicação é difícil de manter e provavelmente leva ao conteúdo inconsistente ao longo do tempo. *Layouts* resolver esse problema.

Tecnicamente, um layout é apenas outro componente. Um layout é definido em um modelo Razor ou em C# de código e pode conter [vinculação de dados](xref:razor-components/components#data-binding), [injeção de dependência](xref:razor-components/dependency-injection)e outros recursos comuns de componentes.

Dois aspectos adicionais ativar uma *componente* em um *layout*

* O componente de layout deve herdar de `LayoutComponentBase`. `LayoutComponentBase` define um `Body` propriedade que contém o conteúdo a ser renderizado dentro do layout.
* O componente de layout usa o `Body` propriedade para especificar onde o conteúdo do corpo deve ser renderizado usando a sintaxe do Razor `@Body`. Durante o processamento, `@Body` é substituída pelo conteúdo do layout.

O exemplo de código a seguir mostra o modelo do Razor de um componente de layout. Observe o uso de `LayoutComponentBase` e `@Body`:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.cshtml)]

## <a name="use-a-layout-in-a-component"></a>Usar um layout em um componente

Use a diretiva do Razor `@layout` para aplicar um layout para um componente. O compilador converte essa diretiva em um `LayoutAttribute`, que é aplicado à classe de componente.

O exemplo de código a seguir demonstra o conceito. O conteúdo desse componente é inserido na *MasterLayout* na posição do `@Body`:

```cshtml
@layout MasterLayout
@page "/master-list"

<h2>Master Episode List</h2>
```

## <a name="centralized-layout-selection"></a>Seleção de layout centralizado

Todas as pastas de um um aplicativo pode, opcionalmente, conter um arquivo de modelo chamado *viewimports. cshtml*. O compilador incluirá as diretivas especificadas no arquivo de importações de exibição em todos os modelos do Razor na mesma pasta e recursivamente em todas as suas subpastas. Portanto, uma *viewimports. cshtml* arquivo contendo `@layout MainLayout` garante que todos os componentes em uma pasta, use o *MainLayout* layout. Não é necessário adicionar repetidamente `@layout` a todos os *.razor* arquivos.

Observe que o modelo padrão usa a *viewimports. cshtml* mecanismo para seleção de layout. Um aplicativo recém-criado contém o *viewimports. cshtml* de arquivo na *componentes/páginas* pasta.

## <a name="nested-layouts"></a>Layouts aninhados

Aplicativos podem consistir em layouts aninhados. Um componente pode fazer referência a um layout que por sua vez faz referência a outro layout. Por exemplo, os layouts de aninhamento podem ser usados para refletir uma estrutura de vários níveis de menu.

Os exemplos de código a seguir mostram como usar layouts aninhados. O *EpisodesComponent.cshtml* arquivo é o componente para exibir. Observe que o componente o referencia o layout `MasterListLayout`.

*EpisodesComponent.cshtml*:

```cshtml
@layout MasterListLayout
@page "/master-list/episodes"

<h1>Episodes</h1>
```

O *MasterListLayout.cshtml* arquivo fornece o `MasterListLayout`. O layout faz referência a outro layout, `MasterLayout`, onde vai a ser inserido.

*MasterListLayout.cshtml*:

```cshtml
@layout MasterLayout
@inherits LayoutComponentBase

<nav>
    <!-- Menu structure of master list -->
    ...
</nav>

@Body
```

Por fim, `MasterLayout` contém os elementos de layout de nível superior, como o cabeçalho, rodapé e menu principal.

*MasterLayout.cshtml*:

```cshtml
@inherits LayoutComponentBase

<header>...</header>
<nav>...</nav>

@Body
```
