---
title: Introdução a Páginas do Razor no ASP.NET Core
author: Rick-Anderson
description: Saiba como as Páginas Razor no ASP.NET Core tornam a codificação de cenários centrados em página mais fácil e mais produtiva do que com o uso de MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 10/07/2019
uid: razor-pages/index
ms.openlocfilehash: 67cc4f9b261372996d612f922c9f491f53948ece
ms.sourcegitcommit: ddc813f0f1fb293861a01597532919945b0e7fe5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74412075"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a>Introdução a Páginas do Razor no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Ryan Nowak](https://github.com/rynowak)

Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.

Se você estiver procurando um tutorial que utiliza a abordagem Modelo-Exibição-Controlador, consulte a [Introdução ao ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Este documento proporciona uma introdução a páginas do Razor. Este não é um tutorial passo a passo. Se você achar que algumas das seções são muito avançadas, consulte a [Introdução a Páginas do Razor](xref:tutorials/razor-pages/razor-pages-start). Para obter uma visão geral do ASP.NET Core, consulte a [Introdução ao ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Prerequisites

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

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

Considere uma página básica: <a name="OnGet"></a>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

O código anterior se assemelha muito a um [arquivo de exibição do Razor](xref:tutorials/first-mvc-app/adding-view) usado em um aplicativo ASP.NET Core com controladores e exibições. What makes it different is the [@page](xref:mvc/views/razor#page) directive. `@page` transforma o arquivo em uma ação do MVC – o que significa que ele trata solicitações diretamente, sem passar por um controlador. `@page` deve ser a primeira diretiva do Razor em uma página. `@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs. Razor Pages file names have a *.cshtml* suffix.

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

* O runtime procura arquivos de Páginas do Razor na pasta *Pages* por padrão.
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

A classe `PageModel` permite separar a lógica de uma página da respectiva apresentação. Ela define manipuladores para as solicitações enviadas e os dados usados para renderizar a página. This separation allows:

* Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).
* [Testes de unidade](xref:test/razor-pages-tests)

A página tem um *método de manipulador* `OnPostAsync`, que é executado em solicitações `POST` (quando um usuário posta o formulário). Handler methods for any HTTP verb can be added. Os manipuladores mais comuns são:

* `OnGet` para inicializar o estado necessário para a página. In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.
* `OnPost` para manipular envios de formulário.

O sufixo de nomenclatura `Async` é opcional, mas geralmente é usado por convenção para funções assíncronas. O código anterior é comum para as Páginas do Razor.

If you're familiar with ASP.NET apps using controllers and views:

* The `OnPostAsync` code in the preceding example looks similar to typical controller code.
* Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages. 

O método `OnPostAsync` anterior:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

O fluxo básico de `OnPostAsync`:

Verifique se há erros de validação.

* Se não houver nenhum erro, salve os dados e redirecione.
* Se houver erros, mostre a página novamente com as mensagens de validação. Em muitos casos, erros de validação seriam detectados no cliente e nunca enviados ao servidor.

O arquivo de exibição *Pages/Create.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

The rendered HTML from *Pages/Create.cshtml*:

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

In the previous code, posting the form:

* With valid data:

  * The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method. `RedirectToPage` retorna uma instância de <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>. `RedirectToPage`:

    * Is an action result.
    * Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).
    * Is customized for pages. Na amostra anterior, ele redireciona para a página de Índice raiz (`/Index`). `RedirectToPage` é descrito em detalhes na seção [Geração de URLs para páginas](#url_gen).

* With validation errors that are passed to the server:

  * The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method. `Page` retorna uma instância de <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>. Retornar `Page` é semelhante a como as ações em controladores retornam `View`. `PageResult` is the default return type for a handler method. Um método de manipulador que retorna `void` renderiza a página.
  * In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false. In this sample, no validation errors are displayed on the client. Validation error handing is covered later in this document.

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* With validation errors detected by client side validation:

  * Data is **not** posted to the server.
  * Client-side validation is explained later in this document.

The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client. For more information, see [Overposting](xref:data/ef-rp/crud#overposting).

O Razor Pages, por padrão, associa propriedades somente com verbos não `GET`. Binding to properties removes the need to writing code to convert HTTP data to the model type. A associação reduz o código usando a mesma propriedade para renderizar os campos de formulário (`<input asp-for="Customer.Name">`) e aceitar a entrada.

[!INCLUDE[](~/includes/bind-get.md)]

Reviewing the *Pages/Create.cshtml* view file:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.
* [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.

### <a name="the-home-page"></a>The home page

*Index.cshtml* is the home page:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

A classe `PageModel` (*Index.cshtml.cs*) associada:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

The *Index.cshtml* file contains the following markup:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page. O link contém dados de rota com a ID de contato. Por exemplo, `https://localhost:5001/Edit/1`. Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.

The *Index.cshtml* file contains markup to create a delete button for each customer contact:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

The rendered HTML:

```HTML
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:

* The customer contact ID, specified by the `asp-route-id` attribute.
* The `handler`, specified by the `asp-page-handler` attribute.

Quando o botão é selecionado, uma solicitação de formulário `POST` é enviada para o servidor. Por convenção, o nome do método do manipulador é selecionado com base no valor do parâmetro `handler` de acordo com o esquema `OnPost[handler]Async`.

Como o `handler` é `delete` neste exemplo, o método do manipulador `OnPostDeleteAsync` é usado para processar a solicitação `POST`. Se `asp-page-handler` for definido como um valor diferente, como `remove`, um método de manipulador com o nome `OnPostRemoveAsync` será selecionado.

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

O método `OnPostDeleteAsync`:

* Gets the `id` from the query string.
* Consulta o banco de dados para o contato de cliente com `FindAsync`.
* If the customer contact is found, it's removed and the database is updated.
* Chama <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> para redirecionar para a página de índice de raiz (`/Index`).

### <a name="the-editcshtml-file"></a>The Edit.cshtml file

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

A primeira linha contém a diretiva `@page "{id:int}"`. A restrição de roteamento `"{id:int}"` informa à página para aceitar solicitações para a página que contêm dados da rota `int`. Se uma solicitação para a página não contém dados de rota que podem ser convertidos em um `int`, o runtime retorna um erro HTTP 404 (não encontrado). Para tornar a ID opcional, acrescente `?` à restrição de rota:

 ```cshtml
@page "{id:int?}"
```

The *Edit.cshtml.cs* file:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a>Validação

Validation rules:

* Are declaratively specified in the model class.
* Are enforced everywhere in the app.

The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property. DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.

Consider the `Customer` model:

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Using the following *Create.cshtml* view file:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

O código anterior:

* Includes jQuery and jQuery validation scripts.
* Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:

  * Client-side validation.
  * Validation error rendering.

* Gera o seguinte HTML:

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

Posting the Create form without a name value displays the error message "The Name field is required." on the form. If JavaScript is enabled on the client, the browser displays the error without posting to the server.

The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML. `data-val-length-max` prevents browsers from entering more than the maximum length specified. If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:

* With the name longer than 10.
* The error message "The field Name must be a string with a maximum length of 10." is returned.

Consider the following `Movie` model:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

The validation attributes specify behavior to enforce on the model properties they're applied to:

* The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.
* O atributo `RegularExpression` é usado para limitar quais caracteres podem ser inseridos. No código anterior, "Gênero":

  * Deve usar apenas letras.
  * A primeira letra deve ser maiúscula. Espaços em branco, números e caracteres especiais não são permitidos.

* A "Classificação" `RegularExpression`:

  * Exige que o primeiro caractere seja uma letra maiúscula.
  * Allows special characters and numbers in subsequent spaces. "PG-13" é válido para uma classificação, mas é um erro em "Gênero".

* O atributo `Range` restringe um valor a um intervalo especificado.
* The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.
* Os tipos de valor (como `decimal`, `int`, `float`, `DateTime`) são inerentemente necessários e não precisam do atributo `[Required]`.

The Create page for the `Movie` model shows displays errors with invalid values:

![Formulário da exibição de filmes com vários erros de validação do lado do cliente do jQuery](~/tutorials/razor-pages/validation/_static/val.png)

Para obter mais informações, consulte:

* [Add validation to the Movie app](xref:tutorials/razor-pages/validation)
* [Model validation in ASP.NET Core](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Manipular solicitações HEAD com um fallback de manipulador OnGet

`HEAD` requests allow retrieving the headers for a specific resource. Diferente das solicitações `GET`, as solicitações `HEAD` não retornam um corpo de resposta.

Geralmente, um manipulador `OnHead` é criado e chamado para solicitações `HEAD`:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF e Páginas do Razor

Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery). The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Usando Layouts, parciais, modelos e auxiliares de marcas com Páginas do Razor

As Páginas funcionam com todos os recursos do mecanismo de exibição do Razor. Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.

Organizaremos essa página aproveitando alguns desses recursos.

Adicione uma [página de layout](xref:mvc/views/layout) a *Pages/Shared/_Layout.cshtml*:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

O [Layout](xref:mvc/views/layout):

* Controla o layout de cada página (a menos que a página opte por não usar o layout).
* Importa estruturas HTML como JavaScript e folhas de estilo.
* The contents of the Razor page are rendered where `@RenderBody()` is called.

For more information, see [layout page](xref:mvc/views/layout).

A propriedade [Layout](xref:mvc/views/layout#specifying-a-layout) é definida em *Pages/_ViewStart.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

O layout está na pasta *Pages/Shared*. As páginas buscam outras exibições (layouts, modelos, parciais) hierarquicamente, iniciando na mesma pasta que a página atual. Um layout na pasta *Pages/Shared* pode ser usado em qualquer página do Razor na pasta *Pages*.

O arquivo de layout deve entrar na pasta *Pages/Shared*.

Recomendamos que você **não** coloque o arquivo de layout na pasta *Views/Shared*. *Views/Shared* é um padrão de exibições do MVC. As Páginas do Razor devem confiar na hierarquia de pasta e não nas convenções de caminho.

A pesquisa de modo de exibição de uma Página do Razor inclui a pasta *Pages*. The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.

Adicione um arquivo *Pages/_ViewImports.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` é explicado posteriormente no tutorial. A diretiva `@addTagHelper` coloca os [auxiliares de marcas internos](xref:mvc/views/tag-helpers/builtin-th/Index) em todas as páginas na pasta *Pages*.

<a name="namespace"></a>

The `@namespace` directive set on a page:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

The `@namespace` directive sets the namespace for the page. A diretiva `@model` não precisa incluir o namespace.

Quando a diretiva `@namespace` está contida em *_ViewImports.cshtml*, o namespace especificado fornece o prefixo do namespace gerado na página que importa a diretiva `@namespace`. O restante do namespace gerado (a parte do sufixo) é o caminho relativo separado por ponto entre a pasta que contém *_ViewImports.cshtml* e a pasta que contém a página.

Por exemplo, a classe `PageModel` *Pages/Customers/Edit.cshtml.cs* define explicitamente o namespace:

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

O arquivo *Pages/_ViewImports.cshtml* define o namespace a seguir:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

O namespace gerado para o Razor Pages *Pages/Customers/Edit.cshtml* é o mesmo que a classe `PageModel`.

`@namespace` *também funciona com exibições do Razor convencionais.*

Consider the *Pages/Create.cshtml* view file:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers. The layout file imported the JavaScript files.

O [projeto inicial de Páginas do Razor](#rpvs17) contém o *Pages/_ValidationScriptsPartial.cshtml*, que conecta a validação do lado do cliente.

Para obter mais informações sobre exibições parciais, consulte <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Geração de URL para Páginas

A página `Create`, exibida anteriormente, usa `RedirectToPage`:

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

O aplicativo tem a estrutura de arquivos/pastas a seguir:

* */Pages*

  * *Index.cshtml*
  * *Privacy.cshtml*
  * */Clientes*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success. The string `./Index` is a relative page name used to access the preceding page. It is used to generate URLs to the *Pages/Customers/Index.cshtml* page. Por exemplo:

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page. Por exemplo:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

O nome da página é o caminho para a página da pasta raiz */Pages*, incluindo um `/` à direita (por exemplo, `/Index`). The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL. A geração de URL usa [roteamento](xref:mvc/controllers/routing) e pode gerar e codificar parâmetros de acordo com o modo como a rota é definida no caminho de destino.

A Geração de URL para páginas dá suporte a nomes relativos. The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.

| RedirectToPage(x)| Página |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*. O parâmetro `RedirectToPage` é *combinado* com o caminho da página atual para calcular o nome da página de destino.

Vinculação de nome relativo é útil ao criar sites com uma estrutura complexa. When relative names are used to link between pages in a folder:

* Renaming a folder doesn't break the relative links.
* Links are not broken because they don't include the folder name.

Para redirecionar para uma página em uma [área](xref:mvc/controllers/areas) diferente, especifique essa área:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Para obter mais informações, consulte <xref:mvc/controllers/areas> e <xref:razor-pages/razor-pages-conventions>.

## <a name="viewdata-attribute"></a>Atributo ViewData

Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>. Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.

In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:

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

ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>. Essa propriedade armazena dados até eles serem lidos. Os métodos <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> e <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> podem ser usados para examinar os dados sem exclusão. `TempData` is useful for redirection, when data is needed for more than a single request.

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

For more information, see [TempData](xref:fundamentals/app-state#tempdata).

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

## <a name="advanced-configuration-and-settings"></a>Advanced configuration and settings

The configuration and settings in following sections is not required by most apps.

To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages. For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).

To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Especificar que as Páginas Razor estão na raiz do conteúdo

Por padrão, as Páginas Razor estão na raiz do diretório */Pages*. Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Especificar que as Páginas Razor estão em um diretório raiz personalizado

Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a>Recursos adicionais

* See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction
* [Download or view sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
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

## <a name="prerequisites"></a>Prerequisites

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

* O runtime procura arquivos de Páginas do Razor na pasta *Pages* por padrão.
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

A classe `PageModel` permite separar a lógica de uma página da respectiva apresentação. Ela define manipuladores para as solicitações enviadas e os dados usados para renderizar a página. This separation allows:

* Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).
* [Unit testing](xref:test/razor-pages-tests) the pages.

A página tem um *método de manipulador* `OnPostAsync`, que é executado em solicitações `POST` (quando um usuário posta o formulário). Você pode adicionar métodos de manipulador para qualquer verbo HTTP. Os manipuladores mais comuns são:

* `OnGet` para inicializar o estado necessário para a página. Amostra de [OnGet](#OnGet).
* `OnPost` para manipular envios de formulário.

O sufixo de nomenclatura `Async` é opcional, mas geralmente é usado por convenção para funções assíncronas. O código anterior é comum para as Páginas do Razor.

If you're familiar with ASP.NET apps using controllers and views:

* The `OnPostAsync` code in the preceding example looks similar to typical controller code.
* Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.

O método `OnPostAsync` anterior:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

O fluxo básico de `OnPostAsync`:

Verifique se há erros de validação.

* Se não houver nenhum erro, salve os dados e redirecione.
* Se houver erros, mostre a página novamente com as mensagens de validação. A validação do lado do cliente é idêntica para aplicativos ASP.NET Core MVC tradicionais. Em muitos casos, erros de validação seriam detectados no cliente e nunca enviados ao servidor.

Quando os dados são inseridos com êxito, o método de manipulador `OnPostAsync` chama o método auxiliar `RedirectToPage` para retornar uma instância de `RedirectToPageResult`. `RedirectToPage` é um novo resultado de ação, semelhante a `RedirectToAction` ou `RedirectToRoute`, mas personalizado para páginas. Na amostra anterior, ele redireciona para a página de Índice raiz (`/Index`). `RedirectToPage` é descrito em detalhes na seção [Geração de URLs para páginas](#url_gen).

Quando o formulário enviado tem erros de validação (que são passados para o servidor), o método de manipulador `OnPostAsync` chama o método auxiliar `Page`. `Page` retorna uma instância de `PageResult`. Retornar `Page` é semelhante a como as ações em controladores retornam `View`. `PageResult` is the default return type for a handler method. Um método de manipulador que retorna `void` renderiza a página.

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

The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page. O link contém dados de rota com a ID de contato. Por exemplo, `https://localhost:5001/Edit/1`. Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor. Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

O arquivo *Pages/Edit.cshtml*:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

A primeira linha contém a diretiva `@page "{id:int}"`. A restrição de roteamento `"{id:int}"` informa à página para aceitar solicitações para a página que contêm dados da rota `int`. Se uma solicitação para a página não contém dados de rota que podem ser convertidos em um `int`, o runtime retorna um erro HTTP 404 (não encontrado). Para tornar a ID opcional, acrescente `?` à restrição de rota:

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

Como o `handler` é `delete` neste exemplo, o método do manipulador `OnPostDeleteAsync` é usado para processar a solicitação `POST`. Se `asp-page-handler` for definido como um valor diferente, como `remove`, um método de manipulador com o nome `OnPostRemoveAsync` será selecionado. The following code shows the `OnPostDeleteAsync` handler:

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

O método `OnPostDeleteAsync`:

* Aceita o `id` da cadeia de caracteres de consulta. If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data. The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.
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

Por padrão, as Páginas Razor estão na raiz do diretório */Pages*. Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:

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
