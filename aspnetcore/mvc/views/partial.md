---
title: Exibições parciais no ASP.NET Core
author: ardalis
description: Descubra como usar exibições parciais para dividir os arquivos de marcação grandes e reduzir a duplicação de marcações comuns em páginas da Web em aplicativos ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 09/11/2018
uid: mvc/views/partial
ms.openlocfilehash: b7c1545007086053e879bce6781802959da77901
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327372"
---
# <a name="partial-views-in-aspnet-core"></a>Exibições parciais no ASP.NET Core

Por [Steve Smith](https://ardalis.com/), [Luke Latham](https://github.com/guardrex), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Scott Sauber](https://twitter.com/scottsauber)

Uma exibição parcial é um arquivo de marcação [Razor](xref:mvc/views/razor) (*.cshtml*) que renderiza a saída HTML *dentro* da saída processada de outro arquivo de marcação.

::: moniker range=">= aspnetcore-2.1"

O termo *exibição parcial* é usado durante o desenvolvimento de um aplicativo MVC, no qual os arquivos de marcação são chamados de *exibições*, ou de um aplicativo Razor Pages, no qual os arquivos de marcação são chamados de *páginas*. Este tópico refere-se genericamente a exibições do MVC e a páginas do Razor Pages como *arquivos de marcação*.

::: moniker-end

[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="when-to-use-partial-views"></a>Quando usar exibições parciais

Exibições parciais são uma maneira eficaz de:

* Dividir arquivos de marcação grandes em componentes menores.

  Aproveitar a vantagem de trabalhar com cada parte isolada em uma exibição parcial em um arquivo de marcação grande e complexo, composto por diversas partes lógicas. O código no arquivo de marcação é gerenciável porque a marcação contém somente a estrutura de página geral e as referências a exibições parciais.
* Reduzir a duplicação de conteúdo de marcação comum em arquivos de marcação.

  Quando os mesmos elementos de marcação são usados nos arquivos de marcação, uma exibição parcial remove a duplicação de conteúdo de marcação em um arquivo de exibição parcial. Quando a marcação é alterada na exibição parcial, ela atualiza a saída renderizada dos arquivos de marcação que usam a exibição parcial.

As exibições parciais não podem ser usadas para manter os elementos de layout comuns. Os elementos de layout comuns precisam ser especificados nos arquivos [_Layout.cshtml](xref:mvc/views/layout).

Não use uma exibição parcial em que a lógica de renderização complexa ou a execução de código são necessárias para renderizar a marcação. Em vez de uma exibição parcial, use um [componente de exibição](xref:mvc/views/view-components).

## <a name="declare-partial-views"></a>Declarar exibições parciais

::: moniker range=">= aspnetcore-2.0"

Uma exibição parcial é um arquivo de marcação *.cshtml* mantido dentro da pasta *Exibições* (MVC) ou *Páginas* (Razor Pages).

No ASP.NET Core MVC, um <xref:Microsoft.AspNetCore.Mvc.ViewResult> do controlador é capaz de retornar uma exibição ou uma exibição parcial. Uma funcionalidade semelhante está planejada para o Razor Pages no ASP.NET Core 2.2. No Razor Pages, um <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> pode retornar um <xref:Microsoft.AspNetCore.Mvc.PartialViewResult>. A referência e a renderização de exibições parciais são descritas na seção [Referenciar uma exibição parcial](#reference-a-partial-view).

Ao contrário da exibição do MVC ou renderização de página, uma exibição parcial não executa *_ViewStart.cshtml*. Para obter mais informações sobre *_ViewStart.cshtml*, consulte <xref:mvc/views/layout>.

Nomes de arquivos de exibição parcial geralmente começam com um sublinhado (`_`). Essa convenção de nomenclatura não é obrigatória, mas ajuda a diferenciar visualmente as exibições parciais das exibições e das páginas.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Uma exibição parcial é um arquivo de marcação *.cshtml* mantido dentro da pasta *Exibições*.

Um <xref:Microsoft.AspNetCore.Mvc.ViewResult> do controlador é capaz de retornar uma exibição ou uma exibição parcial.

Ao contrário da renderização de exibição do MVC, uma exibição parcial não executa *_ViewStart.cshtml*. Para obter mais informações sobre *_ViewStart.cshtml*, consulte <xref:mvc/views/layout>.

Nomes de arquivos de exibição parcial geralmente começam com um sublinhado (`_`). Essa convenção de nomenclatura não é obrigatória, mas ajuda a diferenciar visualmente as exibições parciais das exibições.

::: moniker-end

## <a name="reference-a-partial-view"></a>Referenciar uma exibição parcial

::: moniker range=">= aspnetcore-2.1"

Há várias maneiras de referenciar uma exibição parcial em um arquivo de marcação. É recomendável que os aplicativos usem uma das seguintes abordagens de renderização assíncrona:

* [Auxiliar de marcação parcial](#partial-tag-helper)
* [Auxiliar de HTML assíncrono](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

Há duas maneiras de referenciar uma exibição parcial em um arquivo de marcação:

* [Auxiliar de HTML assíncrono](#asynchronous-html-helper)
* [Auxiliar de HTML síncrono](#synchronous-html-helper)

É recomendável que os aplicativos usem o [Auxiliar de HTML assíncrono](#asynchronous-html-helper).

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a>Auxiliar de marca parcial

O [Auxiliar de Marca Parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) exige o ASP.NET Core 2.1 ou posterior.

O Auxiliar de Marca Parcial renderiza o conteúdo de forma assíncrona e usa uma sintaxe semelhante a HTML:

```cshtml
<partial name="_PartialName" />
```

Quando houver uma extensão de arquivo, o Auxiliar de Marca fará referência a uma exibição parcial que precisa estar na mesma pasta que o arquivo de marcação que chama a exibição parcial:

```cshtml
<partial name="_PartialName.cshtml" />
```

O exemplo a seguir faz referência a uma exibição parcial da raiz do aplicativo. Caminhos que começam com um til-barra (`~/`) ou uma barra (`/`) referem-se à raiz do aplicativo:

**Páginas do Razor**

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

**MVC**

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

O exemplo a seguir faz referência a uma exibição parcial com um caminho relativo:

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

Para obter mais informações, consulte <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.

::: moniker-end

### <a name="asynchronous-html-helper"></a>Auxiliar HTML assíncrono

Ao usar um Auxiliar HTML, a melhor prática é usar <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>. `PartialAsync` retorna um tipo <xref:Microsoft.AspNetCore.Html.IHtmlContent> encapsulado em um <xref:System.Threading.Tasks.Task%601>. O método é referenciado prefixando a chamada em espera com um caractere `@`:

```cshtml
@await Html.PartialAsync("_PartialName")
```

Quando houver a extensão de arquivo, o Auxiliar de HTML fará referência a uma exibição parcial que precisa estar na mesma pasta que o arquivo de marcação que chama a exibição parcial:

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

O exemplo a seguir faz referência a uma exibição parcial da raiz do aplicativo. Caminhos que começam com um til-barra (`~/`) ou uma barra (`/`) referem-se à raiz do aplicativo:

::: moniker range=">= aspnetcore-2.1"

**Páginas do Razor**

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

**MVC**

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

O exemplo a seguir faz referência a uma exibição parcial com um caminho relativo:

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

Como alternativa, é possível renderizar uma exibição parcial com <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>. Esse método não retorna um <xref:Microsoft.AspNetCore.Html.IHtmlContent>. Ele transmite a saída renderizada diretamente para a resposta. Como o método não retorna nenhum resultado, ele precisa ser chamado dentro de um bloco de código Razor:

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

Como `RenderPartialAsync` transmite conteúdo renderizado, ele apresenta melhor desempenho em alguns cenários. Em situações cruciais para o desempenho, submeta a página a benchmark usando ambas as abordagens e use aquela que gera uma resposta mais rápida.

### <a name="synchronous-html-helper"></a>Auxiliar de HTML assíncrono

<xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> e <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> são os equivalentes síncronos de `PartialAsync` e `RenderPartialAsync`, respectivamente. Os equivalentes síncronos não são recomendados porque há cenários em que eles realizam deadlock. Os métodos síncronos estão programados para serem removidos em uma versão futura.

> [!IMPORTANT]
> Se for necessário executar código, use um [componente de exibição](xref:mvc/views/view-components) em vez de uma exibição parcial.

::: moniker range=">= aspnetcore-2.1"

Chamar `Partial` ou `RenderPartial` resulta em um aviso do analisador do Visual Studio. Por exemplo, a presença de `Partial` produz a seguinte mensagem de aviso:

> Uso de IHtmlHelper.Partial pode resultar em deadlocks de aplicativo. Considere usar o Auxiliar de Marca &lt;parcial&gt; ou IHtmlHelper.PartialAsync.

Substitua as chamadas para `@Html.Partial` por `@await Html.PartialAsync` ou o [Auxiliar de Marca Parcial](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper). Para obter mais informações sobre a migração do auxiliar de marca parcial, consulte [Migrar de um auxiliar HTML](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).

::: moniker-end

## <a name="partial-view-discovery"></a>Descoberta de exibição parcial

Quando uma exibição parcial é referenciada pelo nome sem uma extensão de arquivo, os seguintes locais são pesquisados na ordem indicada:

::: moniker range=">= aspnetcore-2.1"

**Páginas do Razor**

1. Pasta da página em execução no momento
1. Grafo do diretório acima da pasta da página
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

**MVC**

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

::: moniker-end

As convenções a seguir se aplicam à descoberta de exibição parcial:

* Diferentes exibições parciais com o mesmo nome de arquivo são permitidos quando as exibições parciais estão em pastas diferentes.
* Ao referenciar uma exibição parcial pelo nome sem uma extensão de arquivo e a exibição parcial está presente na pasta do chamador e na pasta *Compartilhada*, a exibição parcial na pasta do chamador fornece a exibição parcial. Se a exibição parcial não existir na pasta do chamador, ela será fornecida pela pasta *Compartilhada*. Exibições parciais na pasta *Compartilhada* são chamadas de *exibições parciais compartilhadas* ou *exibições parciais padrão*.
* Exibições parciais podem ser *encadeadas*&mdash;uma exibição parcial pode chamar outra se uma referência circular não tiver sido formada por chamadas. Caminhos relativos sempre são relativos ao arquivo atual, não à raiz ou ao pai do arquivo.

> [!NOTE]
> Um [Razor](xref:mvc/views/razor) `section` definido em uma exibição parcial é invisível para arquivos de marcação pai. A `section` só é visível para a exibição parcial na qual ela está definida.

## <a name="access-data-from-partial-views"></a>Acessar dados de exibições parciais

Quando uma exibição parcial é instanciada, ela recebe uma *cópia* do dicionário `ViewData` do pai. As atualizações feitas nos dados dentro da exibição parcial não são persistidas na exibição pai. Alterações a `ViewData` em uma exibição parcial são perdidas quando a exibição parcial retorna.

O exemplo a seguir demonstra como passar uma instância de [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) para uma exibição parcial:

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

Você pode passar um modelo para uma exibição parcial. O modelo pode ser um objeto personalizado. Você pode passar um modelo com `PartialAsync` (renderiza um bloco de conteúdo para o chamador) ou `RenderPartialAsync` (transmite o conteúdo para a saída):

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

**Páginas do Razor**

A marcação a seguir no aplicativo de exemplo é da página *Pages/ArticlesRP/ReadRP.cshtml*. A página contém duas exibições parciais. A segunda exibição parcial passa um modelo e `ViewData` para a exibição parcial. A sobrecarga do construtor `ViewDataDictionary` é usada para passar um novo dicionário `ViewData`, retendo ainda o dicionário `ViewData` existente.

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-19)]

*Pages/Shared/_AuthorPartialRP.cshtml* é a primeira exibição parcial referenciada pelo arquivo de marcação *ReadRP.cshtml*:

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

*Pages/ArticlesRP/_ArticleSectionRP.cshtml* é a segunda exibição parcial referenciada pelo arquivo de marcação *ReadRP.cshtml*:

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

**MVC**

::: moniker-end

A marcação a seguir no aplicativo de exemplo mostra a exibição *Views/Articles/Read.cshtml*. A exibição contém duas exibições parciais. A segunda exibição parcial passa um modelo e `ViewData` para a exibição parcial. A sobrecarga do construtor `ViewDataDictionary` é usada para passar um novo dicionário `ViewData`, retendo ainda o dicionário `ViewData` existente.

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-19)]

*Views/Shared/_AuthorPartial.cshtml* é a primeira exibição parcial referenciada pelo arquivo de marcação *ReadRP.cshtml*:

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

*Views/Articles/_ArticleSection.cshtml* é a segunda exibição parcial referenciada pelo arquivo de marcação *Read.cshtml*:

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

No tempo de execução, as parciais são renderizadas para a saída renderizada do arquivo de marcação pai que, por sua vez, é renderizada dentro do *_Layout.cshtml* compartilhado. A primeira exibição parcial renderiza a data de publicação e o nome do autor do artigo:

> Abraham Lincoln
>
> Esta exibição parcial do &lt;caminho do arquivo de exibição parcial compartilhada&gt;.
> 19/11/1863 12:00:00 AM

A segunda exibição parcial renderiza as seções do artigo:

> Section One Index: 0
>
> Pontuação de quatro e há sete anos...
>
> Section Two Index: 1
>
> Agora estamos envolvidos em uma grande guerra civil, testando...
>
> Section Three Index: 2
>
> Mas, em um sentido mais amplo, não podemos dedicar...

## <a name="additional-resources"></a>Recursos adicionais

::: moniker range=">= aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
