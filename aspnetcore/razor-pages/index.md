---
title: Introdução a Páginas do Razor no ASP.NET Core
author: Rick-Anderson
description: Saiba como as Páginas Razor no ASP.NET Core tornam a codificação de cenários centrados em página mais fácil e mais produtiva do que com o uso de MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 09/19/2019
uid: razor-pages/index
ms.openlocfilehash: 284fb0fa64b26cf51f822b9ef42fe9bb7247e421
ms.sourcegitcommit: e7dc89620fa02c2ff80bee1e3f77297f97616968
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71151161"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a>Introdução a Páginas do Razor no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)

Razor Pages pode tornar os cenários voltados para a página de codificação mais fáceis e produtivos do que usar controladores e exibições.

Se você estiver procurando um tutorial que utiliza a abordagem Modelo-Exibição-Controlador, consulte a [Introdução ao ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Este documento proporciona uma introdução a páginas do Razor. Este não é um tutorial passo a passo. Se você achar que algumas das seções são muito avançadas, consulte a [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start). Para obter uma visão geral do ASP.NET Core, consulte a [Introdução ao ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Criar um projeto do Razor Pages

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Confira a [Introdução ao Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obter instruções detalhadas sobre como criar um projeto do Razor Pages.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Da linha de comando, execute `dotnet new webapp`.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Da linha de comando, execute `dotnet new webapp`.

Abra o arquivo *.csproj* gerado do Visual Studio para Mac.

---

## <a name="razor-pages"></a>Páginas do Razor

O Páginas do Razor está habilitado em *Startup.cs*:

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12)]

Considere uma página básica: <a name="OnGet"></a>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

O código anterior se assemelha muito a um [arquivo de exibição do Razor](xref:tutorials/first-mvc-app/adding-view) usado em um aplicativo ASP.NET Core com controladores e exibições. O que o torna diferente é [@page](xref:mvc/views/razor#page) a diretiva. `@page` transforma o arquivo em uma ação do MVC – o que significa que ele trata solicitações diretamente, sem passar por um controlador. `@page` deve ser a primeira diretiva do Razor em uma página. `@page`afeta o comportamento de outras construções do [Razor](xref:mvc/views/razor) . Razor Pages nomes de arquivo têm um sufixo *. cshtml* .

Uma página semelhante, usando uma classe `PageModel`, é mostrada nos dois arquivos a seguir. O arquivo *Pages/Index2.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

O modelo de página *Pages/Index2.cshtml.cs*:

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Por convenção, o arquivo de classe `PageModel` tem o mesmo nome que o arquivo na Página do Razor com *.cs* acrescentado. Por exemplo, a Página do Razor anterior é *Pages/Index2.cshtml*. O arquivo que contém a classe `PageModel` é chamado *Pages/Index2.cshtml.cs*.

As associações de caminhos de URL para páginas são determinadas pelo local da página no sistema de arquivos. A tabela a seguir mostra um caminho de Página do Razor e a URL correspondente:

| Caminho e nome do arquivo               | URL correspondente |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` ou `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` ou `/Store/Index` |

Notas:

* O tempo de execução procura arquivos de Páginas do Razor na pasta *Pages* por padrão.
* `Index` é a página padrão quando uma URL não inclui uma página.

## <a name="write-a-basic-form"></a>Escrever um formulário básico

Páginas do Razor foi projetado para facilitar a implementação de padrões comuns usados com navegadores da Web ao criar um aplicativo. [Model binding](xref:mvc/models/model-binding), [auxiliares de marcas](xref:mvc/views/tag-helpers/intro) e auxiliares HTML *funcionam todos apenas* com as propriedades definidas em uma classe de Página do Razor. Considere uma página que implementa um formulário básico "Fale conosco" para o modelo `Contact`:

Para as amostras neste documento, o `DbContext` é inicializado no arquivo [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

O modelo de dados:

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

O contexto do banco de dados:

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

O arquivo de exibição *Pages/Create.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

O modelo de página *Pages/Create.cshtml.cs*:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

Por convenção, a classe `PageModel` é chamada de `<PageName>Model` e está no mesmo namespace que a página.

A classe `PageModel` permite separar a lógica de uma página da respectiva apresentação. Ela define manipuladores para as solicitações enviadas e os dados usados para renderizar a página. Essa separação permite:

* Gerenciamento de dependências de página por meio de [injeção de dependência](xref:fundamentals/dependency-injection).
* [Teste de unidade](xref:test/razor-pages-tests)

A página tem um *método de manipulador* `OnPostAsync`, que é executado em solicitações `POST` (quando um usuário posta o formulário). Métodos de manipulador para qualquer verbo HTTP podem ser adicionados. Os manipuladores mais comuns são:

* `OnGet` para inicializar o estado necessário para a página. No código anterior, o `OnGet` método exibe a página Razor *CREATEMODEL. cshtml* .
* `OnPost` para manipular envios de formulário.

O sufixo de nomenclatura `Async` é opcional, mas geralmente é usado por convenção para funções assíncronas. O código anterior é comum para as Páginas do Razor.

Se você estiver familiarizado com os aplicativos ASP.NET usando os controladores e exibições:

* O `OnPostAsync` código no exemplo anterior é semelhante ao código de controlador típico.
* A maioria dos primitivos MVC, como [Associação de modelo](xref:mvc/models/model-binding), [validação](xref:mvc/models/validation)e resultados de ação, funcionam da mesma forma com controladores e Razor Pages. 

O método `OnPostAsync` anterior:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

O fluxo básico de `OnPostAsync`:

Verifique se há erros de validação.

* Se não houver nenhum erro, salve os dados e redirecione.
* Se houver erros, mostre a página novamente com as mensagens de validação. Em muitos casos, erros de validação seriam detectados no cliente e nunca enviados ao servidor.

O arquivo de exibição *Pages/Create.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

O HTML renderizado de *pages/Create. cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

No código anterior, postando o formulário:

* Com dados válidos:

  * O `OnPostAsync` método de manipulador chama <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> o método auxiliar. `RedirectToPage` retorna uma instância de <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>. `RedirectToPage`:

    * É um resultado de ação.
    * É semelhante a `RedirectToAction` ou `RedirectToRoute` (usado em controladores e exibições).
    * É personalizado para páginas. Na amostra anterior, ele redireciona para a página de Índice raiz (`/Index`). `RedirectToPage` é descrito em detalhes na seção [Geração de URLs para páginas](#url_gen).

* Com erros de validação que são passados para o servidor:

  * O `OnPostAsync` método de manipulador chama <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> o método auxiliar. `Page` retorna uma instância de <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>. Retornar `Page` é semelhante a como as ações em controladores retornam `View`. `PageResult`é o tipo de retorno padrão para um método de manipulador. Um método de manipulador que retorna `void` renderiza a página.
  * No exemplo anterior, a postagem do formulário sem nenhum resultado em [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) retorna false. Neste exemplo, nenhum erro de validação é exibido no cliente. A entrega do erro de validação será abordada posteriormente neste documento.

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* Com erros de validação detectados pela validação no lado do cliente:

  * Os dados **não** são postados no servidor.
  * A validação no lado do cliente é explicada posteriormente neste documento.

A `Customer` propriedade usa [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) o atributo para aceitar a associação de modelo:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

`[BindProperty]`**não** deve ser usado em modelos que contêm propriedades que não devem ser alteradas pelo cliente. Para obter mais informações, consulte [superpostando](xref:data/ef-rp/crud#overposting)

O Razor Pages, por padrão, associa propriedades somente com verbos não `GET`. A associação a Propriedades remove a necessidade de escrever código para converter dados HTTP no tipo de modelo. A associação reduz o código usando a mesma propriedade para renderizar os campos de formulário (`<input asp-for="Customer.Name">`) e aceitar a entrada.

[!INCLUDE[](~/includes/bind-get.md)]

Examinando o arquivo de exibição *páginas/Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* No código anterior, o [auxiliar](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` de marca de entrada associa o elemento `<input>` HTML à expressão `Customer.Name` do modelo.
* [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)torna os auxiliares de marca disponíveis.

### <a name="the-home-page"></a>O home page

*Index. cshtml* é o Home Page:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

A classe `PageModel` (*Index.cshtml.cs*) associada:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

O arquivo *index. cshtml* contém a seguinte marcação:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

O `<a /a>` [auxiliar de marca](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) de âncora `asp-route-{value}` usou o atributo para gerar um link para a página de edição. O link contém dados de rota com a ID de contato. Por exemplo, `https://localhost:5001/Edit/1`. Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.

O arquivo *index. cshtml* contém marcação para criar um botão de exclusão para cada contato de cliente:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

O HTML renderizado:

```HTML
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

Quando o botão de exclusão é renderizado em HTML, seu [formsaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) inclui parâmetros para:

* A ID de contato do cliente, especificada `asp-route-id` pelo atributo.
* O `handler`, especificado `asp-page-handler` pelo atributo.

Quando o botão é selecionado, uma solicitação de formulário `POST` é enviada para o servidor. Por convenção, o nome do método do manipulador é selecionado com base no valor do parâmetro `handler` de acordo com o esquema `OnPost[handler]Async`.

Como o `handler` é `delete` neste exemplo, o método do manipulador `OnPostDeleteAsync` é usado para processar a solicitação `POST`. Se `asp-page-handler` for definido como um valor diferente, como `remove`, um método de manipulador com o nome `OnPostRemoveAsync` será selecionado.

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

O método `OnPostDeleteAsync`:

* Obtém o `id` da cadeia de caracteres de consulta.
* Consulta o banco de dados para o contato de cliente com `FindAsync`.
* Se o contato do cliente for encontrado, ele é removido e o banco de dados é atualizado.
* Chama <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> para redirecionar para a página de índice de raiz (`/Index`).

### <a name="the-editcshtml-file"></a>O arquivo Edit. cshtml

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

A primeira linha contém a diretiva `@page "{id:int}"`. A restrição de roteamento `"{id:int}"` informa à página para aceitar solicitações para a página que contêm dados da rota `int`. Se uma solicitação para a página não contém dados de rota que podem ser convertidos em um `int`, o tempo de execução retorna um erro HTTP 404 (não encontrado). Para tornar a ID opcional, acrescente `?` à restrição de rota:

 ```cshtml
@page "{id:int?}"
```

O arquivo *Edit.cshtml.cs* :

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a>Validação

Regras de validação:

* São especificadas declarativamente na classe de modelo.
* São impostos em todos os lugares no aplicativo.

O <xref:System.ComponentModel.DataAnnotations> namespace fornece um conjunto de atributos de validação internos que são aplicados declarativamente a uma classe ou propriedade. Annotations também contém atributos de [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) formatação como essa ajuda com a formatação e não fornecem nenhuma validação.

Considere o `Customer` modelo:

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

Usando o seguinte arquivo de exibição *Create. cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

O código anterior:

* Inclui scripts de validação jQuery e jQuery.
* Usa os `<div />` [auxiliares de marcação](xref:mvc/views/tag-helpers/intro) e `<span />` para habilitar:

  * Validação no lado do cliente.
  * Renderização de erro de validação.

* Gera o seguinte HTML:[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

A postagem do valor de criar formulário sem um nome exibe a mensagem de erro "o campo nome é obrigatório". no formulário. Se o JavaScript estiver habilitado no cliente, o navegador exibirá o erro sem postar no servidor.

O `[StringLength(10)]` atributo é `data-val-length-max="10"` gerado no HTML renderizado. `data-val-length-max`impede que os navegadores insiram mais do que o comprimento máximo especificado. Se uma ferramenta como o [Fiddler](https://www.telerik.com/fiddler) for usada para editar e reproduzir a postagem:

* Com o nome maior que 10.
* A mensagem de erro "o nome do campo deve ser uma cadeia de caracteres com um comprimento máximo de 10". é retornado.

Considere o seguinte `Movie` modelo:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Os atributos de validação especificam o comportamento a ser aplicado nas propriedades de modelo às quais eles são aplicados:

* Os atributos `Required` e `MinimumLength` indicam que uma propriedade deve ter um valor; porém, nada impede que um usuário insira um espaço em branco para atender a essa validação.
* O atributo `RegularExpression` é usado para limitar quais caracteres podem ser inseridos. No código anterior, "Gênero":

  * Deve usar apenas letras.
  * A primeira letra deve ser maiúscula. Espaços em branco, números e caracteres especiais não são permitidos.

* A "Classificação" `RegularExpression`:

  * Exige que o primeiro caractere seja uma letra maiúscula.
  * Permite caracteres especiais e números em espaços subsequentes. "PG-13" é válido para uma classificação, mas é um erro em "Gênero".

* O atributo `Range` restringe um valor a um intervalo especificado.
* O `StringLength` atributo define o comprimento máximo de uma propriedade de cadeia de caracteres e, opcionalmente, seu comprimento mínimo.
* Os tipos de valor (como `decimal`, `int`, `float`, `DateTime`) são inerentemente necessários e não precisam do atributo `[Required]`.

A página criar para o `Movie` modelo mostra exibe erros com valores inválidos:

![Formulário da exibição de filmes com vários erros de validação do lado do cliente do jQuery](~/tutorials/razor-pages/validation/_static/val.png)

Para obter mais informações, consulte:

* [Adicionar validação ao aplicativo de filme](xref:tutorials/razor-pages/validation)
* [Validação de modelo no ASP.NET Core](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Manipular solicitações HEAD com um fallback de manipulador OnGet

`HEAD`as solicitações permitem recuperar os cabeçalhos de um recurso específico. Diferente das solicitações `GET`, as solicitações `HEAD` não retornam um corpo de resposta.

Geralmente, um manipulador `OnHead` é criado e chamado para solicitações `HEAD`:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor Pages retornará para chamar o `OnGet` manipulador se nenhum `OnHead` manipulador for definido.

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF e Páginas do Razor

Razor Pages são protegidos pela[validação de antifalsificação](xref:security/anti-request-forgery). O [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injeta tokens de antifalsificação em elementos de formulário HTML.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Usando Layouts, parciais, modelos e auxiliares de marcas com Páginas do Razor

As Páginas funcionam com todos os recursos do mecanismo de exibição do Razor. Layouts, parciais, modelos, auxiliares de marcas, *_ViewStart.cshtml* e *_ViewImports.cshtml* funcionam da mesma forma que funcionam exibições convencionais do Razor.

Organizaremos essa página aproveitando alguns desses recursos.

Adicione uma [página de layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

O [Layout](xref:mvc/views/layout):

* Controla o layout de cada página (a menos que a página opte por não usar o layout).
* Importa estruturas HTML como JavaScript e folhas de estilo.
* O conteúdo da página Razor é renderizado onde `@RenderBody()` é chamado.

Para obter mais informações, consulte [página de layout](xref:mvc/views/layout)..

A propriedade [Layout](xref:mvc/views/layout#specifying-a-layout) é definida em *Pages/_ViewStart.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

O layout está na pasta *Pages/Shared*. As páginas buscam outras exibições (layouts, modelos, parciais) hierarquicamente, iniciando na mesma pasta que a página atual. Um layout na pasta *Pages/Shared* pode ser usado em qualquer página do Razor na pasta *Pages*.

O arquivo de layout deve entrar na pasta *Pages/Shared*.

Recomendamos que você **não** coloque o arquivo de layout na pasta *Views/Shared*. *Views/Shared* é um padrão de exibições do MVC. As Páginas do Razor devem confiar na hierarquia de pasta e não nas convenções de caminho.

A pesquisa de modo de exibição de uma Página do Razor inclui a pasta *Pages*. Os layouts, modelos e parciais usados com controladores MVC e exibições convencionais do Razor *funcionam apenas*.

Adicione um arquivo *Pages/_ViewImports.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` é explicado posteriormente no tutorial. A diretiva `@addTagHelper` coloca os [auxiliares de marcas internos](xref:mvc/views/tag-helpers/builtin-th/Index) em todas as páginas na pasta *Pages*.

<a name="namespace"></a>

A `@namespace` diretiva definida em uma página:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

A `@namespace` diretiva define o namespace para a página. A diretiva `@model` não precisa incluir o namespace.

Quando a diretiva `@namespace` está contida em *_ViewImports.cshtml*, o namespace especificado fornece o prefixo do namespace gerado na página que importa a diretiva `@namespace`. O restante do namespace gerado (a parte do sufixo) é o caminho relativo separado por ponto entre a pasta que contém *_ViewImports.cshtml* e a pasta que contém a página.

Por exemplo, a classe `PageModel` *Pages/Customers/Edit.cshtml.cs* define explicitamente o namespace:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

O arquivo *Pages/_ViewImports.cshtml* define o namespace a seguir:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

O namespace gerado para o Razor Pages *Pages/Customers/Edit.cshtml* é o mesmo que a classe `PageModel`.

`@namespace` *também funciona com exibições do Razor convencionais.*

Considere o arquivo de exibição *páginas/Create. cshtml* :[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

O arquivo de exibição *páginas/Create. cshtml* atualizado com *_ViewImports. cshtml* e o arquivo de layout anterior:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

No código anterior, o *_ViewImports. cshtml* importou os auxiliares de namespace e de marca. O arquivo de layout importou os arquivos JavaScript.

O [projeto inicial de Páginas do Razor](#rpvs17) contém o *Pages/_ValidationScriptsPartial.cshtml*, que conecta a validação do lado do cliente.

Para obter mais informações sobre exibições parciais, consulte <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Geração de URL para Páginas

A página `Create`, exibida anteriormente, usa `RedirectToPage`:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

O aplicativo tem a estrutura de arquivos/pastas a seguir:

* */Pages*

  * *Index.cshtml*
  * *Privacy.cshtml*
  * */Clientes*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

As páginas *pages/Customers/Create. cshtml* e *pages/Customers/Edit. cshtml* redirecionam para *pages/Customers/index. cshtml* após o êxito. A cadeia de caracteres `./Index` faz parte do URI para acessar a página anterior. A cadeia `./Index` de caracteres pode ser usada para gerar URIs para a página *pages/Customers/index. cshtml* . Por exemplo:

* `Url.Page("./Index", ...)`
* `<a asp-page="/Customers/Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

A cadeia de caracteres `/Index` pode ser usada para gerar URIs para a página *Pages/Index.cshtml*. Por exemplo:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

O nome da página é o caminho para a página da pasta raiz */Pages*, incluindo um `/` à direita (por exemplo, `/Index`). Os exemplos de geração de URL anteriores oferecem opções avançadas e recursos funcionais por meio da codificação de uma URL. A geração de URL usa [roteamento](xref:mvc/controllers/routing) e pode gerar e codificar parâmetros de acordo com o modo como a rota é definida no caminho de destino.

A Geração de URL para páginas dá suporte a nomes relativos. A tabela a seguir mostra qual página de índice é selecionada `RedirectToPage` usando parâmetros diferentes em *pages/Customers/Create. cshtml*.

| RedirectToPage(x)| Página |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

`RedirectToPage("Index")`, `RedirectToPage("./Index")` e`RedirectToPage("../Index")` são *nomes relativos*. O parâmetro `RedirectToPage` é *combinado* com o caminho da página atual para calcular o nome da página de destino.

Vinculação de nome relativo é útil ao criar sites com uma estrutura complexa. Quando nomes relativos são usados para vincular entre páginas em uma pasta:

* Renomear uma pasta não interrompe os links relativos.
* Os links não são desfeitos porque não incluem o nome da pasta.

Para redirecionar para uma página em uma [área](xref:mvc/controllers/areas) diferente, especifique essa área:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Para obter mais informações, consulte <xref:mvc/controllers/areas> e <xref:razor-pages/razor-pages-conventions>.

## <a name="viewdata-attribute"></a>Atributo ViewData

Os dados podem ser passados para uma página <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>com. As propriedades com o atributo [ViewData] têm seus valores armazenados e carregados do <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.

No exemplo a seguir, o `AboutModel` aplica o `[ViewData]` atributo à `Title` Propriedade:

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

Na página Sobre, acesse a propriedade `Title` como uma propriedade de modelo:

```cshtml
<h1>@Model.Title</h1>
```

No layout, o título é lido a partir do dicionário ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core expõe o <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>. Essa propriedade armazena dados até eles serem lidos. Os métodos <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> e <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> podem ser usados para examinar os dados sem exclusão. `TempData`é útil para redirecionamento, quando os dados são necessários para mais do que uma única solicitação.

Os conjuntos de código a seguir definem o valor de `Message` usando `TempData`:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

A marcação a seguir no arquivo *Pages/Customers/Index.cshtml* exibe o valor de `Message` usando `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

O modelo de página *Pages/Customers/Index.cshtml.cs* aplica o atributo `[TempData]` à propriedade `Message`.

```cs
[TempData]
public string Message { get; set; }
```

Para obter mais informações, confira [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Vários manipuladores por página

A página a seguir gera marcação para dois manipuladores usando o auxiliar de marcação `asp-page-handler`:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

O formulário no exemplo anterior tem dois botões de envio, cada um usando o `FormActionTagHelper` para enviar para uma URL diferente. O atributo `asp-page-handler` é um complemento para `asp-page`. `asp-page-handler` gera URLs que enviam para cada um dos métodos de manipulador definidos por uma página. `asp-page` não foi especificado porque a amostra está vinculando à página atual.

O modelo de página:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

O código anterior usa *métodos de manipulador nomeados*. Métodos de manipulador nomeados são criados colocando o texto no nome após `On<HTTP Verb>` e antes de `Async` (se houver). No exemplo anterior, os métodos de página são OnPost**JoinList**Async e OnPost**JoinListUC**Async. Com *OnPost* e *Async* removidos, os nomes de manipulador são `JoinList` e `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Rotas personalizadas

Use a diretiva `@page` para:

* Especifique uma rota personalizada para uma página. Por exemplo, a rota para a página Sobre pode ser definida como `/Some/Other/Path` com `@page "/Some/Other/Path"`.
* Acrescente segmentos à rota padrão de uma página. Por exemplo, um segmento de "item" pode ser adicionado à rota padrão da página com `@page "item"`.
* Acrescente parâmetros à rota padrão de uma página. Por exemplo, um parâmetro de ID, `id`, pode ser necessário para uma página com `@page "{id}"`.

Há suporte para um caminho relativo à raiz designado por um til (`~`) no início do caminho. Por exemplo, `@page "~/Some/Other/Path"` é o mesmo que `@page "/Some/Other/Path"`.

Você pode alterar a cadeia de caracteres de consulta `?handler=JoinList` na URL para um segmento de rota `/JoinList` ao especificar o modelo de rota `@page "{handler?}"`.

Se você não deseja a cadeia de consulta `?handler=JoinList` na URL, você pode alterar a rota para colocar o nome do manipulador na parte do caminho da URL. Você pode personalizar a rota adicionando um modelo de rota entre aspas duplas após a diretiva `@page`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH/JoinList`. O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

O `?` após `handler` significa que o parâmetro de rota é opcional.

## <a name="advanced-configuration-and-settings"></a>Configuração e configurações avançadas

A configuração e as configurações nas seções a seguir não são exigidas pela maioria dos aplicativos.

Para configurar opções avançadas, use o método <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>de extensão:

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

Use o <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> para definir o diretório raiz para páginas ou adicione convenções de modelo de aplicativo para páginas. Para obter mais informações sobre convenções, consulte [Razor Pages convenções de autorização](xref:security/authorization/razor-pages-authorization).

Para pré-compilar exibições, consulte [Compilação de exibição do Razor](xref:mvc/views/view-compilation).

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Especificar que as Páginas Razor estão na raiz do conteúdo

Por padrão, as Páginas Razor estão na raiz do diretório */Pages*. Adicionar <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> para especificar que seus Razor Pages estão na raiz do conteúdo (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) do aplicativo:

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Especificar que as Páginas Razor estão em um diretório raiz personalizado

Adicionar <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> para especificar que Razor Pages estão em um diretório raiz personalizado no aplicativo (forneça um caminho relativo):

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a>Recursos adicionais

* Consulte a [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start), que se baseia nesta introdução.
* [Baixar ou exibir código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample).
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)

Páginas do Razor é um novo aspecto do ASP.NET Core MVC que torna a codificação de cenários focados em página mais fácil e produtiva.

Se você estiver procurando um tutorial que utiliza a abordagem Modelo-Exibição-Controlador, consulte a [Introdução ao ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Este documento proporciona uma introdução a páginas do Razor. Este não é um tutorial passo a passo. Se você achar que algumas das seções são muito avançadas, consulte a [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start). Para obter uma visão geral do ASP.NET Core, consulte a [Introdução ao ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Criar um projeto do Razor Pages

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Confira a [Introdução ao Razor Pages](xref:tutorials/razor-pages/razor-pages-start) para obter instruções detalhadas sobre como criar um projeto do Razor Pages.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Da linha de comando, execute `dotnet new webapp`.

Abra o arquivo *.csproj* gerado do Visual Studio para Mac.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Da linha de comando, execute `dotnet new webapp`.

---

## <a name="razor-pages"></a>Páginas do Razor

O Páginas do Razor está habilitado em *Startup.cs*:

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Considere uma página básica: <a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

O código anterior se assemelha muito a um [arquivo de exibição do Razor](xref:tutorials/first-mvc-app/adding-view) usado em um aplicativo ASP.NET Core com controladores e exibições. O que o torna diferentes é a diretiva `@page`. `@page` transforma o arquivo em uma ação do MVC – o que significa que ele trata solicitações diretamente, sem passar por um controlador. `@page` deve ser a primeira diretiva do Razor em uma página. `@page` afeta o comportamento de outros constructos do Razor.

Uma página semelhante, usando uma classe `PageModel`, é mostrada nos dois arquivos a seguir. O arquivo *Pages/Index2.cshtml*:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

O modelo de página *Pages/Index2.cshtml.cs*:

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Por convenção, o arquivo de classe `PageModel` tem o mesmo nome que o arquivo na Página do Razor com *.cs* acrescentado. Por exemplo, a Página do Razor anterior é *Pages/Index2.cshtml*. O arquivo que contém a classe `PageModel` é chamado *Pages/Index2.cshtml.cs*.

As associações de caminhos de URL para páginas são determinadas pelo local da página no sistema de arquivos. A tabela a seguir mostra um caminho de Página do Razor e a URL correspondente:

| Caminho e nome do arquivo               | URL correspondente |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` ou `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` ou `/Store/Index` |

Notas:

* O tempo de execução procura arquivos de Páginas do Razor na pasta *Pages* por padrão.
* `Index` é a página padrão quando uma URL não inclui uma página.

## <a name="write-a-basic-form"></a>Escrever um formulário básico

Páginas do Razor foi projetado para facilitar a implementação de padrões comuns usados com navegadores da Web ao criar um aplicativo. [Model binding](xref:mvc/models/model-binding), [auxiliares de marcas](xref:mvc/views/tag-helpers/intro) e auxiliares HTML *funcionam todos apenas* com as propriedades definidas em uma classe de Página do Razor. Considere uma página que implementa um formulário básico "Fale conosco" para o modelo `Contact`:

Para as amostras neste documento, o `DbContext` é inicializado no arquivo [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

O modelo de dados:

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

O contexto do banco de dados:

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

O arquivo de exibição *Pages/Create.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

O modelo de página *Pages/Create.cshtml.cs*:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Por convenção, a classe `PageModel` é chamada de `<PageName>Model` e está no mesmo namespace que a página.

A classe `PageModel` permite separar a lógica de uma página da respectiva apresentação. Ela define manipuladores para as solicitações enviadas e os dados usados para renderizar a página. Essa separação permite:

* Gerenciamento de dependências de página por meio de [injeção de dependência](xref:fundamentals/dependency-injection).
* [Teste de unidade](xref:test/razor-pages-tests) das páginas.

A página tem um *método de manipulador* `OnPostAsync`, que é executado em solicitações `POST` (quando um usuário posta o formulário). Você pode adicionar métodos de manipulador para qualquer verbo HTTP. Os manipuladores mais comuns são:

* `OnGet` para inicializar o estado necessário para a página. Amostra de [OnGet](#OnGet).
* `OnPost` para manipular envios de formulário.

O sufixo de nomenclatura `Async` é opcional, mas geralmente é usado por convenção para funções assíncronas. O código anterior é comum para as Páginas do Razor.

Se você estiver familiarizado com os aplicativos ASP.NET usando os controladores e exibições:

* O `OnPostAsync` código no exemplo anterior é semelhante ao código de controlador típico.
* A maioria dos primitivos MVC, como [Associação de modelo](xref:mvc/models/model-binding), [validação](xref:mvc/models/validation), [validação](xref:mvc/models/validation)e resultados de ação, é compartilhada.

O método `OnPostAsync` anterior:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

O fluxo básico de `OnPostAsync`:

Verifique se há erros de validação.

* Se não houver nenhum erro, salve os dados e redirecione.
* Se houver erros, mostre a página novamente com as mensagens de validação. A validação do lado do cliente é idêntica para aplicativos ASP.NET Core MVC tradicionais. Em muitos casos, erros de validação seriam detectados no cliente e nunca enviados ao servidor.

Quando os dados são inseridos com êxito, o método de manipulador `OnPostAsync` chama o método auxiliar `RedirectToPage` para retornar uma instância de `RedirectToPageResult`. `RedirectToPage` é um novo resultado de ação, semelhante a `RedirectToAction` ou `RedirectToRoute`, mas personalizado para páginas. Na amostra anterior, ele redireciona para a página de Índice raiz (`/Index`). `RedirectToPage` é descrito em detalhes na seção [Geração de URLs para páginas](#url_gen).

Quando o formulário enviado tem erros de validação (que são passados para o servidor), o método de manipulador `OnPostAsync` chama o método auxiliar `Page`. `Page` retorna uma instância de `PageResult`. Retornar `Page` é semelhante a como as ações em controladores retornam `View`. `PageResult`é o tipo de retorno padrão para um método de manipulador. Um método de manipulador que retorna `void` renderiza a página.

A propriedade `Customer` usa o atributo `[BindProperty]` para aceitar o model binding.

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

O Razor Pages, por padrão, associa propriedades somente com verbos não `GET`. A associação de propriedades pode reduzir a quantidade de código que você precisa escrever. A associação reduz o código usando a mesma propriedade para renderizar os campos de formulário (`<input asp-for="Customer.Name">`) e aceitar a entrada.

[!INCLUDE[](~/includes/bind-get.md)]

A home page (*Index.cshtml*):

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

A classe `PageModel` (*Index.cshtml.cs*) associada:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

O arquivo *cshtml* contém a marcação a seguir para criar um link de edição para cada contato:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

O `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [auxiliar de marca](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) de âncora `asp-route-{value}` usou o atributo para gerar um link para a página de edição. O link contém dados de rota com a ID de contato. Por exemplo, `https://localhost:5001/Edit/1`. Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor. Os auxiliares de marcação são habilitados por`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

O arquivo *Pages/Edit.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

A primeira linha contém a diretiva `@page "{id:int}"`. A restrição de roteamento `"{id:int}"` informa à página para aceitar solicitações para a página que contêm dados da rota `int`. Se uma solicitação para a página não contém dados de rota que podem ser convertidos em um `int`, o tempo de execução retorna um erro HTTP 404 (não encontrado). Para tornar a ID opcional, acrescente `?` à restrição de rota:

 ```cshtml
@page "{id:int?}"
```

O arquivo *Pages/Edit.cshtml.cs*:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

O arquivo *Index.cshtml* também contém a marcação para criar um botão de exclusão para cada contato de cliente:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Quando o botão de exclusão é renderizado em HTML, seu `formaction` inclui parâmetros para:

* A ID de contato do cliente especificada pelo atributo `asp-route-id`.
* O `handler` especificado pelo atributo `asp-page-handler`.

Este é um exemplo de um botão de exclusão renderizado com uma ID de contato do cliente de `1`:

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Quando o botão é selecionado, uma solicitação de formulário `POST` é enviada para o servidor. Por convenção, o nome do método do manipulador é selecionado com base no valor do parâmetro `handler` de acordo com o esquema `OnPost[handler]Async`.

Como o `handler` é `delete` neste exemplo, o método do manipulador `OnPostDeleteAsync` é usado para processar a solicitação `POST`. Se `asp-page-handler` for definido como um valor diferente, como `remove`, um método de manipulador com o nome `OnPostRemoveAsync` será selecionado. O código a seguir mostra `OnPostDeleteAsync` o manipulador:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

O método `OnPostDeleteAsync`:

* Aceita o `id` da cadeia de caracteres de consulta. Se a diretiva de página *index. cshtml* continha `"{id:int?}"`a `id` restrição de roteamento, ela virá de dados de rota. Os dados de rota `id` para são especificados no URI `https://localhost:5001/Customers/2`, como.
* Consulta o banco de dados para o contato de cliente com `FindAsync`.
* Se o contato do cliente for encontrado, eles serão removidos da lista de contatos do cliente. O banco de dados é atualizado.
* Chama `RedirectToPage` para redirecionar para a página de índice de raiz (`/Index`).

## <a name="mark-page-properties-as-required"></a>Marque as propriedades da página conforme necessário

As propriedades em um `PageModel` podem ser decoradas com o atributo [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute):

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Para obter mais informações, confira [Validação de modelo](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Manipular solicitações HEAD com um fallback de manipulador OnGet

As solicitações `HEAD` permitem recuperar os cabeçalhos de um recurso específico. Diferente das solicitações `GET`, as solicitações `HEAD` não retornam um corpo de resposta.

Geralmente, um manipulador `OnHead` é criado e chamado para solicitações `HEAD`: 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

No ASP.NET Core 2.1 ou superior, o Razor Pages volta a chamar o manipulador `OnGet` quando nenhum manipulador `OnHead` está definido. Esse comportamento é habilitado pela chamada para [SetCompatibilityVersion](xref:mvc/compatibility-version) em `Startup.ConfigureServices`:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

Os modelos padrão geram a chamada `SetCompatibilityVersion` no ASP.NET Core 2.1 e 2.2. `SetCompatibilityVersion` define de forma eficiente a opção de Páginas Razor `AllowMappingHeadRequestsToGetHandler` como `true`.

Em vez de aceitar todos os comportamentos com `SetCompatibilityVersion`, você pode aceitar explicitamente participar de comportamentos *específicos*. O código a seguir aceita permitir que solicitações `HEAD` sejam mapeadas para o manipulador `OnGet`:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF e Páginas do Razor

Você não precisa escrever nenhum código para [validação antifalsificação](xref:security/anti-request-forgery). Validação e geração de token antifalsificação são automaticamente incluídas nas Páginas do Razor.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Usando Layouts, parciais, modelos e auxiliares de marcas com Páginas do Razor

As Páginas funcionam com todos os recursos do mecanismo de exibição do Razor. Layouts, parciais, modelos, auxiliares de marcas, *_ViewStart.cshtml* e *_ViewImports.cshtml* funcionam da mesma forma que funcionam exibições convencionais do Razor.

Organizaremos essa página aproveitando alguns desses recursos.

Adicione uma [página de layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

O [Layout](xref:mvc/views/layout):

* Controla o layout de cada página (a menos que a página opte por não usar o layout).
* Importa estruturas HTML como JavaScript e folhas de estilo.

Veja [página de layout](xref:mvc/views/layout) para obter mais informações.

A propriedade [Layout](xref:mvc/views/layout#specifying-a-layout) é definida em *Pages/_ViewStart.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

O layout está na pasta *Pages/Shared*. As páginas buscam outras exibições (layouts, modelos, parciais) hierarquicamente, iniciando na mesma pasta que a página atual. Um layout na pasta *Pages/Shared* pode ser usado em qualquer página do Razor na pasta *Pages*.

O arquivo de layout deve entrar na pasta *Pages/Shared*.

Recomendamos que você **não** coloque o arquivo de layout na pasta *Views/Shared*. *Views/Shared* é um padrão de exibições do MVC. As Páginas do Razor devem confiar na hierarquia de pasta e não nas convenções de caminho.

A pesquisa de modo de exibição de uma Página do Razor inclui a pasta *Pages*. Os layouts, modelos e parciais que você está usando com controladores MVC e exibições do Razor convencionais *apenas funcionam*.

Adicione um arquivo *Pages/_ViewImports.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` é explicado posteriormente no tutorial. A diretiva `@addTagHelper` coloca os [auxiliares de marcas internos](xref:mvc/views/tag-helpers/builtin-th/Index) em todas as páginas na pasta *Pages*.

<a name="namespace"></a>

Quando a diretiva `@namespace` é usada explicitamente em uma página:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

A diretiva define o namespace da página. A diretiva `@model` não precisa incluir o namespace.

Quando a diretiva `@namespace` está contida em *_ViewImports.cshtml*, o namespace especificado fornece o prefixo do namespace gerado na página que importa a diretiva `@namespace`. O restante do namespace gerado (a parte do sufixo) é o caminho relativo separado por ponto entre a pasta que contém *_ViewImports.cshtml* e a pasta que contém a página.

Por exemplo, a classe `PageModel` *Pages/Customers/Edit.cshtml.cs* define explicitamente o namespace:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

O arquivo *Pages/_ViewImports.cshtml* define o namespace a seguir:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

O namespace gerado para o Razor Pages *Pages/Customers/Edit.cshtml* é o mesmo que a classe `PageModel`.

`@namespace` *também funciona com exibições do Razor convencionais.*

O arquivo de exibição *Pages/Create.cshtml* original:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

O arquivo de exibição *Pages/Create.cshtml* atualizado:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

O [projeto inicial de Páginas do Razor](#rpvs17) contém o *Pages/_ValidationScriptsPartial.cshtml*, que conecta a validação do lado do cliente.

Para obter mais informações sobre exibições parciais, consulte <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Geração de URL para Páginas

A página `Create`, exibida anteriormente, usa `RedirectToPage`:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

O aplicativo tem a estrutura de arquivos/pastas a seguir:

* */Pages*

  * *Index.cshtml*
  * */Clientes*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

As páginas *Pages/Customers/Create.cshtml* e *Pages/Customers/Edit.cshtml* redirecionam para o *Pages/Index.cshtml* após êxito. A cadeia de caracteres `/Index` faz parte do URI para acessar a página anterior. A cadeia de caracteres `/Index` pode ser usada para gerar URIs para a página *Pages/Index.cshtml*. Por exemplo:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

O nome da página é o caminho para a página da pasta raiz */Pages*, incluindo um `/` à direita (por exemplo, `/Index`). Os exemplos anteriores de geração de URL oferecem opções avançadas e recursos funcionais para codificar uma URL. A geração de URL usa [roteamento](xref:mvc/controllers/routing) e pode gerar e codificar parâmetros de acordo com o modo como a rota é definida no caminho de destino.

A Geração de URL para páginas dá suporte a nomes relativos. A tabela a seguir mostra qual página de Índice é selecionada com diferentes parâmetros `RedirectToPage` de *Pages/Customers/Create.cshtml*:

| RedirectToPage(x)| Página |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")` e `RedirectToPage("../Index")` são *nomes relativos*. O parâmetro `RedirectToPage` é *combinado* com o caminho da página atual para calcular o nome da página de destino.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

Vinculação de nome relativo é útil ao criar sites com uma estrutura complexa. Se você usar nomes relativos para vincular entre páginas em uma pasta, você poderá renomear essa pasta. Todos os links ainda funcionarão (porque eles não incluirão o nome da pasta).

Para redirecionar para uma página em uma [área](xref:mvc/controllers/areas) diferente, especifique essa área:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Para obter mais informações, consulte <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>Atributo ViewData

Os dados podem ser passados para uma página com [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). As propriedades nos controladores ou nos modelos da Página Razor decoradas com `[ViewData]` têm seus valores armazenados e carregados em [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).

No exemplo a seguir, o `AboutModel` contém uma propriedade `Title` decorada com `[ViewData]`. A propriedade `Title` está definida como o título da página Sobre:

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

Na página Sobre, acesse a propriedade `Title` como uma propriedade de modelo:

```cshtml
<h1>@Model.Title</h1>
```

No layout, o título é lido a partir do dicionário ViewData:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

O ASP.NET Core expõe a propriedade [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) em um [controlador](/dotnet/api/microsoft.aspnetcore.mvc.controller). Essa propriedade armazena dados até eles serem lidos. Os métodos `Keep` e `Peek` podem ser usados para examinar os dados sem exclusão. `TempData` é útil para redirecionamento nos casos em que os dados são necessários para mais de uma única solicitação.

Os conjuntos de código a seguir definem o valor de `Message` usando `TempData`:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

A marcação a seguir no arquivo *Pages/Customers/Index.cshtml* exibe o valor de `Message` usando `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

O modelo de página *Pages/Customers/Index.cshtml.cs* aplica o atributo `[TempData]` à propriedade `Message`.

```cs
[TempData]
public string Message { get; set; }
```

Para obter mais informações, confira [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Vários manipuladores por página

A página a seguir gera marcação para dois manipuladores usando o auxiliar de marcação `asp-page-handler`:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

O formulário no exemplo anterior tem dois botões de envio, cada um usando o `FormActionTagHelper` para enviar para uma URL diferente. O atributo `asp-page-handler` é um complemento para `asp-page`. `asp-page-handler` gera URLs que enviam para cada um dos métodos de manipulador definidos por uma página. `asp-page` não foi especificado porque a amostra está vinculando à página atual.

O modelo de página:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

O código anterior usa *métodos de manipulador nomeados*. Métodos de manipulador nomeados são criados colocando o texto no nome após `On<HTTP Verb>` e antes de `Async` (se houver). No exemplo anterior, os métodos de página são OnPost**JoinList**Async e OnPost**JoinListUC**Async. Com *OnPost* e *Async* removidos, os nomes de manipulador são `JoinList` e `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Rotas personalizadas

Use a diretiva `@page` para:

* Especifique uma rota personalizada para uma página. Por exemplo, a rota para a página Sobre pode ser definida como `/Some/Other/Path` com `@page "/Some/Other/Path"`.
* Acrescente segmentos à rota padrão de uma página. Por exemplo, um segmento de "item" pode ser adicionado à rota padrão da página com `@page "item"`.
* Acrescente parâmetros à rota padrão de uma página. Por exemplo, um parâmetro de ID, `id`, pode ser necessário para uma página com `@page "{id}"`.

Há suporte para um caminho relativo à raiz designado por um til (`~`) no início do caminho. Por exemplo, `@page "~/Some/Other/Path"` é o mesmo que `@page "/Some/Other/Path"`.

Você pode alterar a cadeia de caracteres de consulta `?handler=JoinList` na URL para um segmento de rota `/JoinList` ao especificar o modelo de rota `@page "{handler?}"`.

Se você não deseja a cadeia de consulta `?handler=JoinList` na URL, você pode alterar a rota para colocar o nome do manipulador na parte do caminho da URL. Você pode personalizar a rota adicionando um modelo de rota entre aspas duplas após a diretiva `@page`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Usando o código anterior, o caminho da URL que envia a `OnPostJoinListAsync` é `https://localhost:5001/Customers/CreateFATH/JoinList`. O caminho da URL que envia a `OnPostJoinListUCAsync` é `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

O `?` após `handler` significa que o parâmetro de rota é opcional.

## <a name="configuration-and-settings"></a>Configuração e definições

Para configurar opções avançadas, use o método de extensão `AddRazorPagesOptions` no construtor de MVC:

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

No momento, você pode usar o `RazorPagesOptions` para definir o diretório raiz para páginas ou adicionar as convenções de modelo de aplicativo para páginas. Permitiremos mais extensibilidade dessa maneira no futuro.

Para pré-compilar exibições, consulte [Compilação de exibição do Razor](xref:mvc/views/view-compilation).

[Baixar ou exibir código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).

Consulte a [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start), que se baseia nesta introdução.

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Especificar que as Páginas Razor estão na raiz do conteúdo

Por padrão, as Páginas Razor estão na raiz do diretório */Pages*. Adicione [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) em [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que as Páginas Razor estão na raiz do conteúdo ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) do aplicativo:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Especificar que as Páginas Razor estão em um diretório raiz personalizado

Adicione [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) em [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) para especificar que as Páginas Razor estão em um diretório raiz personalizado no aplicativo (forneça um caminho relativo):

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
