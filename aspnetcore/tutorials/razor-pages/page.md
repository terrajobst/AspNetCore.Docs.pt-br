---
title: Páginas do Razor geradas por scaffolding no ASP.NET Core
author: rick-anderson
description: Explica as Páginas do Razor geradas por scaffolding.
ms.author: riande
ms.date: 04/06/2019
uid: tutorials/razor-pages/page
ms.openlocfilehash: fcda567eb99ca5e32e7bebe5dd9e16ac134369b1
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887631"
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a>Páginas do Razor geradas por scaffolding no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Este tutorial examina as Páginas do Razor criadas por scaffolding no tutorial anterior.

[Exiba ou baixe](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) a amostra.

## <a name="the-create-delete-details-and-edit-pages"></a>As páginas Create, Delete, Details e Edit

Examine o Modelo de Página, *Pages/Movies/Index.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

As Páginas do Razor são derivadas de `PageModel`. Por convenção, a classe derivada de `PageModel` é chamada de `<PageName>Model`. O construtor usa [injeção de dependência](xref:fundamentals/dependency-injection) para adicionar o `RazorPagesMovieContext` à página. Todas as páginas geradas por scaffolding seguem esse padrão. Consulte [Código assíncrono](xref:data/ef-rp/intro#asynchronous-code) para obter mais informações sobre a programação assíncrona com o Entity Framework.

Quando uma solicitação é feita à página, o método `OnGetAsync` retorna uma lista de filmes para a Página do Razor. `OnGetAsync` ou `OnGet` é chamado em uma Página do Razor para inicializar o estado da página. Nesse caso, `OnGetAsync` obtém uma lista de filmes e os exibe.

Quando `OnGet` retorna `void` ou `OnGetAsync` retorna `Task`, então nenhum método de retorno é usado. Quando o tipo de retorno for `IActionResult` ou `Task<IActionResult>`, é necessário fornecer uma instrução de retorno. Por exemplo, o método `OnPostAsync` do arquivo *Pages/Movies/Create.cshtml.cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> Examine a Página do Razor *Pages/Movies/Index.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

O Razor pode fazer a transição do HTML em C# ou em marcação específica do Razor. Quando um símbolo `@` é seguido por uma [palavra-chave reservada do Razor](xref:mvc/views/razor#razor-reserved-keywords), ele faz a transição para marcação específica do Razor, caso contrário, ele faz a transição para C#.

A diretiva do Razor `@page` transforma o arquivo em uma ação do MVC, o que significa que ele pode manipular solicitações. `@page` deve ser a primeira diretiva do Razor em uma página. `@page` é um exemplo de transição para a marcação específica do Razor. Consulte [Sintaxe Razor](xref:mvc/views/razor#razor-syntax) para obter mais informações.

Examine a expressão lambda usada no auxiliar HTML a seguir:

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title))
```

O auxiliar HTML `DisplayNameFor` inspeciona a propriedade `Title` referenciada na expressão lambda para determinar o nome de exibição. A expressão lambda é inspecionada em vez de avaliada. Isso significa que não há nenhuma violação de acesso quando `model`, `model.Movie` ou `model.Movie[0]` são `null` ou vazios. Quando a expressão lambda é avaliada (por exemplo, com `@Html.DisplayFor(modelItem => item.Title)`), os valores de propriedade do modelo são avaliados.

<a name="md"></a>

### <a name="the-model-directive"></a>A diretiva @model

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

A diretiva `@model` especifica o tipo de modelo passado para a Página do Razor. No exemplo anterior, a linha `@model` torna a classe derivada de `PageModel` disponível para a Página do Razor. O modelo é usado nos [auxiliares HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` e `@Html.DisplayFor` na página.

### <a name="the-layout-page"></a>A página de layout

Selecione os links de menu (**RazorPagesMovie**, **Página Inicial** e **Privacidade**). Cada página mostra o mesmo layout de menu. O layout de menu é implementado no arquivo *Pages/Shared/_Layout.cshtml*. Abra o arquivo *Pages/Shared/_Layout.cshtml*.

Os modelos de [layout](xref:mvc/views/layout) permitem especificar o layout de contêiner HTML do site em um lugar e, em seguida, aplicá-lo a várias páginas do site. Localize a linha `@RenderBody()`. `RenderBody` é um espaço reservado em que todas as visualizações específicas da página criadas são mostradas, *encapsuladas* na página de layout. Por exemplo, se você selecionar o link **Privacidade**, a exibição **Pages/Privacy.cshtml** será renderizada dentro do método `RenderBody`.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData e layout

Considere o seguinte código do arquivo *Pages/Movies/Index.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

O código realçado anterior é um exemplo de transição do Razor para C#. Os caracteres `{` e `}` circunscrevem um bloco de código C#.

A classe base `PageModel` tem uma propriedade de dicionário `ViewData` que pode ser usada para adicionar os dados que você deseja passar para uma exibição. Você adiciona objetos ao dicionário `ViewData` usando um padrão de chave/valor. No exemplo anterior, a propriedade "Title" é adicionada ao dicionário `ViewData`.

A propriedade "Título" é usada no arquivo *Pages/_Layout.cshtml*. A marcação a seguir mostra as primeiras linhas do arquivo *Pages/_Layout.cshtml*.

<!-- we need a snapshot copy of layout because we are
changing in in the next step.
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

A linha `@*Markup removed for brevity.*@` é um comentário do Razor que não é exibida no arquivo de layout. Ao contrário de comentários HTML (`<!-- -->`), comentários do Razor não são enviados ao cliente.

### <a name="update-the-layout"></a>Atualizar o layout

Altere o elemento `<title>` no arquivo *Pages/Shared/_Layout.cshtml* para exibir **Movie**, em vez de **RazorPagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Localizar o elemento de âncora a seguir no arquivo *Pages/Shared/_Layout.cshtml*.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Substitua o elemento anterior pela marcação a seguir.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

O elemento de âncora anterior é um [Auxiliar de Marcas](xref:mvc/views/tag-helpers/intro). Nesse caso, ele é o [Auxiliar de Marcas de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). O atributo e valor do auxiliar de marcas `asp-page="/Movies/Index"` cria um link para a Página do Razor `/Movies/Index`. O valor do atributo `asp-area` está vazio e, portanto, a área não é usada no link. Confira [Áreas](xref:mvc/controllers/areas) para obter mais informações.

Salve suas alterações e teste o aplicativo clicando no link **RpMovie**. Confira o arquivo [_Layout.cshtml](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) no GitHub caso tenha problemas.

Teste os outros links (**Home**, **RpMovie**, **Create**, **Edit** e **Delete**). Cada página define o título, que pode ser visto na guia do navegador. Quando você coloca um indicador em uma página, o título é usado para o indicador.

> [!NOTE]
> Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`. Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades de idiomas diferentes do inglês que usam uma vírgula (“,”) para um ponto decimal e formatos de data diferentes do inglês dos EUA, você deve tomar medidas para globalizar o aplicativo. Veja [Problema 4076 do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) para obter instruções sobre como adicionar casas decimais.

A propriedade `Layout` é definida no arquivo *Pages/_ViewStart.cshtml*:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

A marcação anterior define o arquivo de layout como *Pages/Shared/_Layout.cshtml* para todos os arquivos do Razor na pasta *Pages*. Veja [Layout](xref:razor-pages/index#layout) para obter mais informações.

### <a name="the-create-page-model"></a>O modelo Criar página

Examine o modelo de página *Pages/Movies/Create.cshtml.cs*:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

O método `OnGet` inicializa qualquer estado necessário para a página. A página Criar não tem nenhum estado para inicializar, assim, `Page` é retornado. Mais adiante no tutorial, você verá o estado de inicialização do método `OnGet`. O método `Page` cria um objeto `PageResult` que renderiza a página *Create.cshtml*.

A propriedade `Movie` usa o atributo `[BindProperty]` para aceitar o [model binding](xref:mvc/models/model-binding). Quando o formulário Criar posta os valores de formulário, o tempo de execução do ASP.NET Core associa os valores postados ao modelo `Movie`.

O método `OnPostAsync` é executado quando a página posta dados de formulário:

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

Se há algum erro de modelo, o formulário é reexibido juntamente com quaisquer dados de formulário postados. A maioria dos erros de modelo podem ser capturados no lado do cliente antes do formulário ser enviado. Um exemplo de um erro de modelo é postar, para o campo de data, um valor que não pode ser convertido em uma data. A validação do lado do cliente e a validação de modelo são abordadas mais adiante no tutorial.

Se não há nenhum erro de modelo, os dados são salvos e o navegador é redirecionado à página Índice.

### <a name="the-create-razor-page"></a>A Página do Razor Criar

Examine o arquivo na Página do Razor *Pages/Movies/Create.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

O Visual Studio exibe a marca `<form method="post">` em uma fonte em negrito diferente usada para Auxiliares de Marcas:

![Exibição de VS17 da página Create.cshtml](page/_static/th.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Para obter mais informações sobre Auxiliares de Marcas, como `<form method="post">`, confira [Auxiliares de Marcas no ASP.NET Core](xref:mvc/views/tag-helpers/intro).

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

O Visual Studio para Mac exibe a marca `<form method="post">` em uma fonte em negrito diferente usada para Auxiliares de Marcas.

---

O elemento `<form method="post">` é um [auxiliar de marcas de formulário](xref:mvc/views/working-with-forms#the-form-tag-helper). O auxiliar de marcas de formulário inclui automaticamente um [token antifalsificação](xref:security/anti-request-forgery).

O mecanismo de scaffolding cria marcação do Razor para cada campo no modelo (exceto a ID) semelhante ao seguinte:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

Os [auxiliares de marcas de validação](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` e `<span asp-validation-for`) exibem erros de validação. A validação será abordada em mais detalhes posteriormente nesta série.

O [auxiliar de marcas de rótulo](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) gera a legenda do rótulo e o atributo `for` para a propriedade `Title`.

O [auxiliar de marcas de entrada](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) usa os atributos [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produz os atributos HTML necessários para validação jQuery no lado do cliente.

## <a name="additional-resources"></a>Recursos adicionais

* [Versão do YouTube deste tutorial](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> [Anterior: Adicionar um modelo](xref:tutorials/razor-pages/model)
> [Próximo: Banco de dados](xref:tutorials/razor-pages/sql)
