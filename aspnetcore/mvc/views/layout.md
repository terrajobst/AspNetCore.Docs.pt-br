---
title: Layout no ASP.NET Core
author: ardalis
description: Saiba como usar layouts comuns, compartilhar diretivas e executar um código comum antes de renderizar exibições em um aplicativo ASP.NET Core.
ms.author: riande
ms.date: 07/30/2019
uid: mvc/views/layout
ms.openlocfilehash: 3ba2f459ca2b04a3001e261acab26880b6582500
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74288993"
---
# <a name="layout-in-aspnet-core"></a>Layout no ASP.NET Core

Por [Steve Smith](https://ardalis.com/) e [Dave Brock](https://twitter.com/daveabrock)

Páginas e exibições com frequência compartilham elementos visuais e programáticos. Este artigo demonstra como:

* Usar layouts comuns.
* Compartilhar diretivas.
* Executar o código comum antes de renderizar páginas ou modos de exibição.

Este documento discute os layouts para as duas abordagens diferentes ao ASP.NET Core MVC: Razor Pages e controladores com exibições. Para este tópico, as diferenças são mínimas:

* Razor Pages estão na pasta *Pages*.
* Controladores com exibições usam uma pasta *Views* pasta exibições.

## <a name="what-is-a-layout"></a>O que é um layout

A maioria dos aplicativos Web tem um layout comum que fornece aos usuários uma experiência consistente durante sua navegação de uma página a outra. O layout normalmente inclui elementos comuns de interface do usuário, como o cabeçalho do aplicativo, elementos de menu ou de navegação e rodapé.

![Exemplo de layout da página](layout/_static/page-layout.png)

Estruturas HTML comuns, como scripts e folhas de estilo, também são usadas com frequência por muitas páginas em um aplicativo. Todos esses elementos compartilhados podem ser definidos em um arquivo de *layout*, que pode então ser referenciado por qualquer exibição usada no aplicativo. Os layouts reduzem o código duplicado em exibições.

Por convenção, o layout padrão de um aplicativo ASP.NET Core é chamado *_Layout.cshtml*. Os arquivos de layout para novos projetos do ASP.NET Core criados com os modelos são:

* Razor Pages: *Pages/Shared/_Layout.cshtml*

  ![Pasta das páginas no Gerenciador de Soluções](layout/_static/rp-web-project-views.png)

* Controlador com exibições: *Views/Shared/_Layout.cshtml*

  ![Pasta das exibições no Gerenciador de Soluções](layout/_static/mvc-web-project-views.png)

O layout define um modelo de nível superior para exibições no aplicativo. Os aplicativos não exigem um layout. Os aplicativos podem definir mais de um layout, com diferentes exibições especificando diferentes layouts.

O código a seguir mostra o arquivo de layout para um modelo de projeto criado com um controlador e exibições:

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a>Especificando um layout

As exibições do Razor têm uma propriedade `Layout`. As exibições individuais especificam um layout com a configuração dessa propriedade:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

O layout especificado pode usar um caminho completo (por exemplo, */Pages/Shared/_Layout.cshtml* ou */Views/Shared/_Layout.cshtml*) ou um nome parcial (exemplo: `_Layout`). Quando um nome parcial for fornecido, o mecanismo de exibição do Razor pesquisará o arquivo de layout usando seu processo de descoberta padrão. A pasta em que o método do manipulador (ou controlador) existe é pesquisada primeiro, seguida pela pasta *Shared*. Esse processo de descoberta é idêntico àquele usado para descobrir [exibições parciais](xref:mvc/views/partial#partial-view-discovery).

Por padrão, todo layout precisa chamar `RenderBody`. Sempre que a chamada a `RenderBody` for feita, o conteúdo da exibição será renderizado.

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a>Seções

Um layout, opcionalmente, pode referenciar uma ou mais *seções*, chamando `RenderSection`. As seções fornecem uma maneira de organizar o local em que determinados elementos da página devem ser colocados. Cada chamada a `RenderSection` pode especificar se essa seção é obrigatória ou opcional:

```html
<script type="text/javascript" src="~/scripts/global.js"></script>

@RenderSection("Scripts", required: false)
```

Se uma seção obrigatória não for encontrada, uma exceção será gerada. As exibições individuais especificam o conteúdo a ser renderizado em uma seção usando a sintaxe Razor `@section`. Se uma página ou exibição definir uma seção, ela precisará ser renderizada (ou ocorrerá um erro).

Uma definição `@section` de exemplo na exibição do Razor Pages:

```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

No código anterior, *scripts/main.js* é adicionado à seção `scripts` em uma página ou exibição. Outras páginas ou exibições no mesmo aplicativo podem não exigir esse script e não definirão uma seção de scripts.

A marcação a seguir usa o [Auxiliar de Marca Parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) para renderizar *_ValidationScriptsPartial.cshtml*:

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

A marcação anterior foi gerada pela [Identidade de scaffolding](xref:security/authentication/scaffold-identity).

As seções definidas em uma página ou exibição estão disponíveis apenas em sua página de layout imediata. Elas não podem ser referenciadas em parciais, componentes de exibição ou outras partes do sistema de exibição.

### <a name="ignoring-sections"></a>Ignorando seções

Por padrão, o corpo e todas as seções de uma página de conteúdo precisam ser renderizados pela página de layout. O mecanismo de exibição do Razor impõe isso acompanhando se o corpo e cada seção foram renderizados.

Para instruir o mecanismo de exibição a ignorar o corpo ou as seções, chame os métodos `IgnoreBody` e `IgnoreSection`.

O corpo e cada seção em uma página do Razor precisam ser renderizados ou ignorados.

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a>Importando diretivas compartilhadas

Exibições e páginas podem usar diretivas do Razor para importar namespaces e usar [injeção de dependência](dependency-injection.md). Diretivas compartilhadas por diversas exibições podem ser especificadas em um arquivo *_ViewImports.cshtml* comum. O arquivo `_ViewImports` dá suporte às seguintes diretivas:

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

O arquivo não dá suporte a outros recursos do Razor, como funções e definições de seção.

Um arquivo `_ViewImports.cshtml` de exemplo:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

O arquivo *_ViewImports.cshtml* para um aplicativo ASP.NET Core MVC normalmente é colocado na pasta *Pages* (ou *Views*). Um arquivo *_ViewImports.cshtml* pode ser colocado em qualquer pasta, caso em que só será aplicado a páginas ou exibições nessa pasta e em suas subpastas. Arquivos `_ViewImports` são processados começando no nível raiz e, em seguida, para cada pasta até o local da página ou da exibição em si. As configurações `_ViewImports` especificadas no nível raiz podem ser substituídas no nível da pasta.

Por exemplo, suponha:

* O arquivo *_ViewImports.cshtml* no nível de raiz inclui `@model MyModel1` e `@addTagHelper *, MyTagHelper1`.
* Um arquivo *_ViewImports.cshtml* de subpasta inclui `@model MyModel2` e `@addTagHelper *, MyTagHelper2`.

Páginas e exibições na subpasta terão acesso a Auxiliares de Marca e ao modelo `MyModel2`.

Se vários arquivos *_ViewImports.cshtml* forem encontrados na hierarquia de arquivos, o comportamento combinado das diretivas será:

* `@addTagHelper`, `@removeTagHelper`: todos são executados, em ordem
* `@tagHelperPrefix`: o mais próximo à exibição substitui todos os outros
* `@model`: o mais próximo à exibição substitui todos os outros
* `@inherits`: o mais próximo à exibição substitui todos os outros
* `@using`: todos são incluídos; duplicatas são ignoradas
* `@inject`: para cada propriedade, o mais próximo à exibição substitui todos os outros com o mesmo nome de propriedade

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a>Executando o código antes de cada exibição

Código que precisa ser executado antes de cada exibição ou página precisa ser colocada no arquivo *_ViewStart.cshtml*. Por convenção, o arquivo *_ViewStart.cshtml* está localizado na pasta *Pages* (ou *Views*). As instruções listadas em *_ViewStart.cshtml* são executadas antes de cada exibição completa (não layouts nem exibições parciais). Como [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* é hierárquico. Se um arquivo *_ViewStart.cshtml* for definido na pasta de exibições ou páginas, ele será executado depois daquele definido na raiz da pasta *Pages* (ou *Views*) (se houver).

Um arquivo *_ViewStart.cshtml* de amostra:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

O arquivo acima especifica que todas as exibições usarão o layout *_Layout.cshtml*.

*_ViewStart.cshtml* é *_ViewImports.cshtml* normalmente **não** são colocados na pasta */Pages/Shared* (ou */Views/Shared*). As versões no nível do aplicativo desses arquivos devem ser colocadas diretamente na pasta */Pages* (ou */Views*).
