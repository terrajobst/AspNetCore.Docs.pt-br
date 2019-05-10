---
title: Exibições no ASP.NET Core MVC
author: ardalis
description: Saiba como as exibições tratam da apresentação de dados do aplicativo e da interação com o usuário no ASP.NET Core MVC.
ms.author: riande
ms.date: 04/03/2019
uid: mvc/views/overview
ms.openlocfilehash: 5e56c6bb18cb5d2389c11eb3e4aa9869228da47d
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891341"
---
# <a name="views-in-aspnet-core-mvc"></a>Exibições no ASP.NET Core MVC

Por [Steve Smith](https://ardalis.com/) e [Luke Latham](https://github.com/guardrex)

Este documento explica as exibições usadas em aplicativos do ASP.NET Core MVC. Para obter informações sobre páginas do Razor, consulte [Introdução a Páginas do Razor](xref:razor-pages/index).

No padrão MVC (Modelo-Exibição-Controlador), a *exibição* cuida da apresentação de dados do aplicativo e da interação com o usuário. Uma exibição é um modelo HTML com [marcação Razor](xref:mvc/views/razor) inserida. A marcação Razor é um código que interage com a marcação HTML para produzir uma página da Web que é enviada ao cliente.

No ASP.NET Core MVC, as exibições são arquivos *.cshtml* que usam a [linguagem de programação C#](/dotnet/csharp/) na marcação Razor. Geralmente, arquivos de exibição são agrupados em pastas nomeadas para cada um dos [controladores](xref:mvc/controllers/actions) do aplicativo. As pastas são armazenadas em uma pasta chamada *Views* na raiz do aplicativo:

![A pasta Views no Gerenciador de Soluções do Visual Studio é aberta com a pasta Inicial aberta para mostrar os arquivos About.cshtml, Contact.cshtml e Index.cshtml](overview/_static/views_solution_explorer.png)

O controlador *Home* é representado por uma pasta *Home* dentro da pasta *Views*. A pasta *Home* contém as exibições das páginas da Web *About*, *Contact* e *Index* (home page). Quando um usuário solicita uma dessas três páginas da Web, ações do controlador *Home* determinam qual das três exibições é usada para compilar e retornar uma página da Web para o usuário.

Use [layouts](xref:mvc/views/layout) para fornecer seções de páginas da Web consistentes e reduzir repetições de código. Layouts geralmente contêm o cabeçalho, elementos de navegação e menu e o rodapé. O cabeçalho e o rodapé geralmente contêm marcações repetitivas para muitos elementos de metadados, bem como links para ativos de script e estilo. Layouts ajudam a evitar essa marcação repetitiva em suas exibições.

[Exibições parciais](xref:mvc/views/partial) reduzem a duplicação de código gerenciando as partes reutilizáveis das exibições. Por exemplo, uma exibição parcial é útil para uma biografia do autor que aparece em várias exibições em um site de blog. Uma biografia do autor é um conteúdo de exibição comum e não requer que um código seja executado para produzi-lo para a página da Web. O conteúdo da biografia do autor é disponibilizado para a exibição usando somente o model binding, de modo que usar uma exibição parcial para esse tipo de conteúdo é ideal.

[Componentes de exibição](xref:mvc/views/view-components) são semelhantes a exibições parciais no sentido em que permitem reduzir códigos repetitivos, mas são adequados para conteúdos de exibição que requerem que um código seja executado no servidor para renderizar a página da Web. Componentes de exibição são úteis quando o conteúdo renderizado requer uma interação com o banco de dados, como para o carrinho de compras de um site. Os componentes de exibição não ficam limitados ao model binding para produzir a saída da página da Web.

## <a name="benefits-of-using-views"></a>Benefícios do uso de exibições

As exibições ajudam a estabelecer uma [separação de Interesses](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) dentro de um aplicativo MVC, separando a marcação da interface do usuário de outras partes do aplicativo. Seguir um design de SoC faz com que seu aplicativo seja modular, o que fornece vários benefícios:

* A manutenção do aplicativo é mais fácil, porque ele é melhor organizado. Geralmente, as exibições são agrupadas segundo os recursos do aplicativo. Isso facilita encontrar exibições relacionadas ao trabalhar em um recurso.
* As partes do aplicativo ficam acopladas de forma flexível. Você pode compilar e atualizar as exibições do aplicativo separadamente da lógica de negócios e dos componentes de acesso a dados. É possível modificar os modos de exibição do aplicativo sem precisar necessariamente atualizar outras partes do aplicativo.
* É mais fácil testar as partes da interface do usuário do aplicativo porque as exibições são unidades separadas.
* Devido à melhor organização, é menos provável que você repita acidentalmente seções da interface do usuário.

## <a name="creating-a-view"></a>Criando uma exibição

Exibições que são específicas de um controlador são criadas na pasta *Views/ [NomeDoControlador]*. Exibições que são compartilhadas entre controladores são colocadas na pasta *Views/Shared*. Para criar uma exibição, adicione um novo arquivo e dê a ele o mesmo nome que o da ação de seu controlador associado, com a extensão de arquivo *.cshtml*. Para criar uma exibição correspondente à ação *About* no controlador *Home*, crie um arquivo *About.cshtml* na pasta *Views/Home*:

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

A marcação *Razor* começa com o símbolo `@`. Execute instruções em C# colocando o código C# dentro de [blocos de código Razor](xref:mvc/views/razor#razor-code-blocks) entre chaves (`{ ... }`). Por exemplo, consulte a atribuição de "About" para `ViewData["Title"]` mostrado acima. É possível exibir valores em HTML simplesmente referenciando o valor com o símbolo `@`. Veja o conteúdo dos elementos `<h2>` e `<h3>` acima.

O conteúdo da exibição mostrado acima é apenas uma parte da página da Web inteira que é renderizada para o usuário. O restante do layout da página e outros aspectos comuns da exibição são especificados em outros arquivos de exibição. Para saber mais, consulte o [tópico sobre Layout](xref:mvc/views/layout).

## <a name="how-controllers-specify-views"></a>Como controladores especificam exibições

Normalmente, as exibições são retornadas de ações como um [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), que é um tipo de [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult). O método de ação pode criar e retornar um `ViewResult` diretamente, mas normalmente isso não é feito. Como a maioria dos controladores herdam de [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), basta usar o método auxiliar `View` para retornar o `ViewResult`:

*HomeController.cs*

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

Quando essa ação é retornada, a exibição *About.cshtml* mostrada na seção anterior é renderizada como a seguinte página da Web:

![Página About renderizada no navegador Microsoft Edge](overview/_static/about-page.png)

O método auxiliar `View` tem várias sobrecargas. Opcionalmente, você pode especificar:

* Uma exibição explícita a ser retornada:

  ```csharp
  return View("Orders");
  ```

* Um [modelo](xref:mvc/models/model-binding) a ser passado para a exibição:

  ```csharp
  return View(Orders);
  ```

* Um modo de exibição e um modelo:

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>Descoberta de exibição

Quando uma ação retorna uma exibição, um processo chamado *descoberta de exibição* ocorre. Esse processo determina qual arquivo de exibição é usado com base no nome da exibição. 

O comportamento padrão do método `View` (`return View();`) é retornar uma exibição com o mesmo nome que o método de ação do qual ela é chamada. Por exemplo, o nome do método `ActionResult` de *About* do controlador é usado para pesquisar um arquivo de exibição chamado *About.cshtml*. Primeiro, o tempo de execução pesquisa pela exibição na pasta *Views/[NomeDoControlador]*. Se não encontrar uma exibição correspondente nela, ele procura pela exibição na pasta *Shared*.

Não importa se você retornar implicitamente o `ViewResult` com `return View();` ou se passar explicitamente o nome de exibição para o método `View` com `return View("<ViewName>");`. Nos dois casos, a descoberta de exibição pesquisa por um arquivo de exibição correspondente nesta ordem:

   1. *Views/\[ControllerName]/\[ViewName].cshtml*
   1. *Views/Shared/\[NomeDaExibição].cshtml*

Um caminho de arquivo de exibição pode ser fornecido em vez de um nome de exibição. Se um caminho absoluto que começa na raiz do aplicativo (ou é iniciado por "/" ou "~ /") estiver sendo usado, a extensão *.cshtml* deverá ser especificada:

```csharp
return View("Views/Home/About.cshtml");
```

Você também pode usar um caminho relativo para especificar exibições em diretórios diferentes sem a extensão *.cshtml*. Dentro do `HomeController`, você pode retornar a exibição *Index* de suas exibições *Manage* com um caminho relativo:

```csharp
return View("../Manage/Index");
```

De forma semelhante, você pode indicar o atual diretório específico do controlador com o prefixo ". /":

```csharp
return View("./About");
```

[Exibições parciais](xref:mvc/views/partial) e [componentes de exibição](xref:mvc/views/view-components) usam mecanismos de descoberta semelhantes (mas não idênticos).

É possível personalizar a convenção padrão de como as exibições ficam localizadas dentro do aplicativo usando um [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander) personalizado.

A descoberta de exibição depende da localização de arquivos de exibição pelo nome do arquivo. Se o sistema de arquivos subjacente diferenciar maiúsculas de minúsculas, os nomes de exibição provavelmente diferenciarão maiúsculas de minúsculas. Para fins de compatibilidade de sistemas operacionais, padronize as maiúsculas e minúsculas dos nomes de controladores e de ações e dos nomes de arquivos e pastas de exibição. Se encontrar um erro indicando que não é possível encontrar um arquivo de exibição ao trabalhar com um sistema de arquivos que diferencia maiúsculas de minúsculas, confirme que o uso de maiúsculas e minúsculas é correspondente entre o arquivo de exibição solicitado e o nome do arquivo de exibição real.

Siga a melhor prática de organizar a estrutura de arquivos de suas exibições de forma a refletir as relações entre controladores, ações e exibições para facilidade de manutenção e clareza.

## <a name="passing-data-to-views"></a>Passando dados para exibições

Passe dados para exibições usando várias abordagens:

* Dados fortemente tipados: viewmodel
* Dados fracamente tipados
  * `ViewData` (`ViewDataAttribute`)
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a>Dados fortemente tipados (viewmodel)

A abordagem mais robusta é especificar um tipo de [modelo](xref:mvc/models/model-binding) na exibição. Esse modelo é conhecido como *viewmodel*. Você passa uma instância do tipo viewmodel para a exibição da ação.

Usar um viewmodel para passar dados para uma exibição permite que a exibição tire proveito da verificação de tipo *forte*. *Tipagem forte* (ou *fortemente tipado*) significa que cada variável e constante têm um tipo definido explicitamente (por exemplo, `string`, `int` ou `DateTime`). A validade dos tipos usados em uma exibição é verificada em tempo de compilação.

O [Visual Studio](https://visualstudio.microsoft.com) e o [Visual Studio Code](https://code.visualstudio.com/) listam membros de classe fortemente tipados usando um recurso chamado [IntelliSense](/visualstudio/ide/using-intellisense). Quando quiser ver as propriedades de um viewmodel, digite o nome da variável para o viewmodel, seguido por um ponto final (`.`). Isso ajuda você a escrever código mais rapidamente e com menos erros.

Especifique um modelo usando a diretiva `@model`. Use o modelo com `@Model`:

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Para fornecer o modelo à exibição, o controlador o passa como um parâmetro:

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

Não há restrições quanto aos tipos de modelo que você pode fornecer a uma exibição. Recomendamos o uso de viewmodels do tipo POCO (objeto CRL básico) com pouco ou nenhum comportamento (métodos) definido. Geralmente, classes de viewmodel são armazenadas na pasta *Models* ou em uma pasta *ViewModels* separada na raiz do aplicativo. O viewmodel *Address* usado no exemplo acima é um viewmodel POCO armazenado em um arquivo chamado *Address.cs*:

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

Nada impede que você use as mesmas classes para seus tipos de viewmodel e seus tipos de modelo de negócios. No entanto, o uso de modelos separados permite que suas exibições variem independentemente das partes de lógica de negócios e de acesso a dados do aplicativo. A separação de modelos e viewmodels também oferece benefícios de segurança quando os modelos usam [model binding](xref:mvc/models/model-binding) e [validação](xref:mvc/models/validation) para dados enviados ao aplicativo pelo usuário.

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a>Dados fracamente tipados (ViewData, atributo ViewData e ViewBag)

`ViewBag` *não está disponível nas Páginas do Razor.*

Além de exibições fortemente tipadas, as exibições têm acesso a uma coleção de dados *fracamente tipados* (também chamada de *tipagem flexível*). Diferente dos tipos fortes, ter *tipos fracos* (ou *tipos flexíveis*) significa que você não declara explicitamente o tipo dos dados que está usando. Você pode usar a coleção de dados fracamente tipados para transmitir pequenas quantidades de dados para dentro e para fora dos controladores e das exibições.

| Passar dados entre...                        | Exemplo                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| Um controlador e uma exibição                             | Preencher uma lista suspensa com os dados.                                          |
| Uma exibição e uma [exibição de layout](xref:mvc/views/layout)   | Definir o conteúdo do elemento **\<title>** na exibição de layout de um arquivo de exibição.  |
| Uma [exibição parcial](xref:mvc/views/partial) e uma exibição | Um widget que exibe dados com base na página da Web que o usuário solicitou.      |

Essa coleção pode ser referenciada por meio das propriedades `ViewData` ou `ViewBag` em controladores e exibições. A propriedade `ViewData` é um dicionário de objetos fracamente tipados. A propriedade `ViewBag` é um wrapper em torno de `ViewData` que fornece propriedades dinâmicas à coleção de `ViewData` subjacente. Observação: as pesquisas de chave diferenciam maiúsculas de minúsculas tanto para `ViewData` quanto para `ViewBag`.

`ViewData` e `ViewBag` são resolvidos dinamicamente em tempo de execução. Uma vez que não oferecem verificação de tipo em tempo de compilação, geralmente ambos são mais propensos a erros do que quando um viewmodel é usado. Por esse motivo, alguns desenvolvedores preferem nunca usar `ViewData` e `ViewBag` ou usá-los o mínimo possível.

<a name="VD"></a>

**ViewData**

`ViewData` é um objeto [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) acessado por meio de chaves `string`. Dados de cadeias de caracteres podem ser armazenados e usados diretamente, sem a necessidade de conversão, mas você precisa converter os valores de outros objetos `ViewData` em tipos específicos quando extraí-los. Você pode usar `ViewData` para passar dados de controladores para exibições e dentro das exibições, incluindo [exibições parciais](xref:mvc/views/partial) e [layouts](xref:mvc/views/layout).

A seguir, temos um exemplo que define valores para uma saudação e um endereço usando `ViewData` em uma ação:

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

Trabalhar com os dados em uma exibição:

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

**Atributo ViewData**

Outra abordagem que usa o [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) é [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). As propriedades nos controladores ou nos modelos da Página do Razor decoradas com `[ViewData]` têm seus valores armazenados e carregados do dicionário.

No exemplo a seguir, o controlador Home contém uma propriedade `Title` decorada com `[ViewData]`. O método `About` define o título para a exibição About:

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

Na exibição About, acesse a propriedade `Title` como uma propriedade de modelo:

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

::: moniker-end

**ViewBag**

`ViewBag` *não está disponível nas Páginas do Razor.*

`ViewBag` é um objeto [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) que fornece acesso dinâmico aos objetos armazenados em `ViewData`. Pode ser mais conveniente trabalhar com `ViewBag`, pois ele não requer uma conversão. O exemplo a seguir mostra como usar `ViewBag` com o mesmo resultado que o uso de `ViewData` acima:

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

**Usando ViewData e ViewBag simultaneamente**

`ViewBag` *não está disponível nas Páginas do Razor.*

Como `ViewData` e `ViewBag` fazem referência à mesma coleção `ViewData` subjacente, você pode usar `ViewData` e `ViewBag`, além de misturá-los e combiná-los ao ler e gravar valores.

Defina o título usando `ViewBag` e a descrição usando `ViewData` na parte superior de uma exibição *About.cshtml*:

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

Leia as propriedades, mas inverta o uso de `ViewData` e `ViewBag`. No arquivo *_Layout.cshtml*, obtenha o título usando `ViewData` e a descrição usando `ViewBag`:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

Lembre-se de que cadeias de caracteres não exigem uma conversão para `ViewData`. Você pode usar `@ViewData["Title"]` sem converter.

Usar `ViewData` e `ViewBag` ao mesmo tempo funciona, assim como misturar e combinar e leitura e a gravação das propriedades. A seguinte marcação é renderizada:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**Resumo das diferenças entre ViewData e ViewBag**

 `ViewBag` não está disponível em páginas Razor.

* `ViewData`
  * Deriva de [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), de forma que tem propriedades de dicionário que podem ser úteis, como `ContainsKey`, `Add`, `Remove` e `Clear`.
  * Chaves no dicionário são cadeias de caracteres, de forma que espaços em branco são permitidos. Exemplo: `ViewData["Some Key With Whitespace"]`
  * Qualquer tipo diferente de `string` deve ser convertido na exibição para usar `ViewData`.
* `ViewBag`
  * Deriva de [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), de forma que permite a criação de propriedades dinâmicas usando a notação de ponto (`@ViewBag.SomeKey = <value or object>`) e nenhuma conversão é necessária. A sintaxe de `ViewBag` torna mais rápido adicioná-lo a controladores e exibições.
  * Mais simples de verificar quanto à presença de valores nulos. Exemplo: `@ViewBag.Person?.Name`

**Quando usar ViewData ou ViewBag**

`ViewData` e `ViewBag` são abordagens igualmente válidas para passar pequenas quantidades de dados entre controladores e exibições. A escolha de qual delas usar é baseada na preferência. Você pode misturar e combinar objetos `ViewData` e `ViewBag`, mas é mais fácil ler e manter o código quando uma abordagem é usada de maneira consistente. Ambas as abordagens são resolvidas dinamicamente em tempo de execução e, portanto, são propensas a causar erros de tempo de execução. Algumas equipes de desenvolvimento as evitam.

### <a name="dynamic-views"></a>Exibições dinâmicas

Exibições que não declaram um tipo de modelo usando `@model`, mas que têm uma instância de modelo passada a elas (por exemplo, `return View(Address);`) podem referenciar as propriedades da instância dinamicamente:

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Esse recurso oferece flexibilidade, mas não oferece proteção de compilação ou IntelliSense. Se a propriedade não existir, a geração da página da Web falhará em tempo de execução.

## <a name="more-view-features"></a>Mais recursos das exibições

[Auxiliares de Marca](xref:mvc/views/tag-helpers/intro) facilitam a adição do comportamento do lado do servidor às marcações HTML existentes. O uso de Auxiliares de marca evita a necessidade de escrever código personalizado ou auxiliares em suas exibições. Auxiliares de marca são aplicados como atributos a elementos HTML e são ignorados por editores que não podem processá-los. Isso permite editar e renderizar a marcação da exibição em várias ferramentas.

É possível gerar uma marcação HTML personalizada com muitos auxiliares HTML internos. Uma lógica de interface do usuário mais complexa pode ser tratada por [Componentes de exibição](xref:mvc/views/view-components). Componentes de exibição fornecem o mesmo SoC oferecido por controladores e exibições. Eles podem eliminar a necessidade de ações e exibições que lidam com os dados usados por elementos comuns da interface do usuário.

Como muitos outros aspectos do ASP.NET Core, as exibições dão suporte à [injeção de dependência](xref:fundamentals/dependency-injection), permitindo que serviços sejam [injetados em exibições](xref:mvc/views/dependency-injection).
