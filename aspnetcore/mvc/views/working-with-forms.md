---
title: Auxiliares de marca em formulários no ASP.NET Core
author: rick-anderson
description: Descreve os Auxiliares de marca internos usados com Formulários.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/working-with-forms
ms.openlocfilehash: 43a1c408ff1a03468989e5bb0839ca2cd245082b
ms.sourcegitcommit: b5e63714afc26e94be49a92619586df5189ed93a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739489"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a>Auxiliares de marca em formulários no ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette) e [Jerrie Pelser](https://github.com/jerriep)

Este documento demonstra como é o trabalho com Formulários e os elementos HTML usados comumente em um Formulário. O elemento HTML [Formulário](https://www.w3.org/TR/html401/interact/forms.html) fornece o mecanismo primário que os aplicativos Web usam para postar dados para o servidor. A maior parte deste documento descreve os [Auxiliares de marca](tag-helpers/intro.md) e como eles podem ajudar você a criar formulários HTML robustos de forma produtiva. É recomendável que você leia [Introdução ao auxiliares de marca](tag-helpers/intro.md) antes de ler este documento.

Em muitos casos, os Auxiliares HTML fornecem uma abordagem alternativa a um Auxiliar de Marca específico, mas é importante reconhecer que os Auxiliares de Marca não substituem os Auxiliares HTML e que não há um Auxiliar de Marca para cada Auxiliar HTML. Quando existe um Auxiliar HTML alternativo, ele é mencionado.

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a>O Auxiliar de marca de formulário

O Auxiliar de marca de [formulário](https://www.w3.org/TR/html401/interact/forms.html):

* Gera o valor do atributo HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` para uma ação do controlador MVC ou uma rota nomeada

* Gera um [Token de verificação de solicitação](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) oculto para evitar a falsificação de solicitações entre sites (quando usado com o atributo `[ValidateAntiForgeryToken]` no método de ação HTTP Post)

* Fornece o atributo `asp-route-<Parameter Name>`, em que `<Parameter Name>` é adicionado aos valores de rota. Os parâmetros `routeValues` para `Html.BeginForm` e `Html.BeginRouteForm` fornecem funcionalidade semelhante.

* Tem uma alternativa de Auxiliar HTML `Html.BeginForm` e `Html.BeginRouteForm`

Amostra:

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

O Auxiliar de marca de formulário acima gera o HTML a seguir:

```HTML
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

O tempo de execução do MVC gera o valor do atributo `action` dos atributos `asp-controller` e `asp-action` do Auxiliar de marca de formulário. O Auxiliar de marca de formulário também gera um [Token de verificação de solicitação](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) oculto para evitar a falsificação de solicitações entre sites (quando usado com o atributo `[ValidateAntiForgeryToken]` no método de ação HTTP Post). É difícil proteger um Formulário HTML puro contra falsificação de solicitações entre sites e o Auxiliar de marca de formulário fornece este serviço para você.

### <a name="using-a-named-route"></a>Usando uma rota nomeada

O atributo do Auxiliar de Marca `asp-route` também pode gerar a marcação para o atributo HTML `action`. Um aplicativo com uma [rota](../../fundamentals/routing.md) chamada `register` poderia usar a seguinte marcação para a página de registro:

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

Muitas das exibições na pasta *Modos de Exibição/Conta* (gerada quando você cria um novo aplicativo Web com *Contas de usuário individuais*) contêm o atributo [asp-route-returnurl](xref:mvc/views/working-with-forms):

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
>Com os modelos internos, `returnUrl` só é preenchido automaticamente quando você tenta acessar um recurso autorizado, mas não está autenticado ou autorizado. Quando você tenta fazer um acesso não autorizado, o middleware de segurança o redireciona para a página de logon com o `returnUrl` definido.

## <a name="the-form-action-tag-helper"></a>Auxiliar de Marcação de Ação de Formulário

O Auxiliar de Marcação de Ação de Formulário gera o atributo `formaction` na marcação `<button ...>` ou `<input type="image" ...>` gerada. O atributo `formaction` controla onde um formulário envia seus dados. Ele se associa à [\<entrada >](https://www.w3.org/wiki/HTML/Elements/input) elementos do tipo `image` e elementos [\<botão >](https://www.w3.org/wiki/HTML/Elements/button). O Auxiliar de Marcação de Ação de Formulário permite o uso de vários atributos [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` para controlar qual link `formaction` será gerado para o elemento correspondente.

Atributos [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) com suporte para controlar o valor de `formaction`:

|Atributo|DESCRIÇÃO|
|---|---|
|[asp-controller](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|O nome do controlador.|
|[asp-action](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|O nome do método da ação.|
|[asp-area](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|O nome da área.|
|[asp-page](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|O nome da Página do Razor.|
|[asp-page-handler](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|O nome do manipulador da Página do Razor.|
|[asp-route](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|O nome da rota.|
|[asp-route-{value}](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|Um único valor de rota de URL. Por exemplo, `asp-route-id="1234"`.|
|[asp-all-route-data](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|Todos os valores de rota.|
|[asp-fragment](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|O fragmento de URL.|

### <a name="submit-to-controller-example"></a>Exemplo de Enviar ao controlador

A marcação a seguir envia o formulário à ação `Index` de `HomeController` quando a entrada ou botão são escolhidos:

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

A marcação anterior gera o seguinte HTML:

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a>Exemplo de Enviar a uma página

A marcação a seguir envia o formulário à Página do Razor `About`:

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

A marcação anterior gera o seguinte HTML:

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a>Exemplo de Enviar a uma rota

Considere o ponto de extremidade `/Home/Test`:

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

A marcação a seguir envia o formulário ao ponto de extremidade `/Home/Test`.

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

A marcação anterior gera o seguinte HTML:

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a>O auxiliar de marca de entrada

O Auxiliar de marca de entrada associa um elemento HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) a uma expressão de modelo em sua exibição do Razor.

Sintaxe:

```HTML
<input asp-for="<Expression Name>">
```

O auxiliar de marca de entrada:

* Gera os atributos HTML `id` e `name` para o nome da expressão especificada no atributo `asp-for`. `asp-for="Property1.Property2"` equivale a `m => m.Property1.Property2`. O nome da expressão é o que é usado para o valor do atributo `asp-for`. Consulte a seção [Nomes de expressão](#expression-names) para obter informações adicionais.

* Define o valor do atributo HTML `type` com base nos atributos de tipo de modelo e [anotação de dados](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) aplicados à propriedade de modelo

* O valor do atributo HTML `type` não será substituído quando um for especificado

* Gera atributos de validação [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) de atributos de [anotação de dados](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) aplicados a propriedades de modelo

* Tem uma sobreposição de recursos de Auxiliar HTML com `Html.TextBoxFor` e `Html.EditorFor`. Consulte a seção **Alternativas de Auxiliar HTML ao Auxiliar de marca de entrada** para obter detalhes.

* Fornece tipagem forte. Se o nome da propriedade for alterado e você não atualizar o Auxiliar de marca, você verá um erro semelhante ao seguinte:

```HTML
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

O Auxiliar de marca `Input` define o atributo HTML `type` com base no tipo .NET. A tabela a seguir lista alguns tipos .NET comuns e o tipo HTML gerado (não estão listados todos os tipos .NET).

|Tipo .NET|Tipo de entrada|
|---|---|
|Bool|type="checkbox"|
|Cadeia de Caracteres|type="text"|
|DateTime|type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)|
|Byte|type="number"|
|int|type="number"|
|Single e Double|type="number"|

A tabela a seguir mostra alguns atributos de [anotações de dados](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) comuns que o auxiliar de marca de entrada mapeará para tipos de entrada específicos (não são listados todos os atributos de validação):

|Atributo|Tipo de entrada|
|---|---|
|[EmailAddress]|type="email"|
|[Url]|type="url"|
|[HiddenInput]|type="hidden"|
|[Phone]|type="tel"|
|[DataType(DataType.Password)]|type="password"|
|[DataType(DataType.Date)]|type="date"|
|[DataType(DataType.Time)]|type="time"|

Amostra:

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

O código acima gera o seguinte HTML:

```HTML
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

As anotações de dados aplicadas às propriedades `Email` e `Password` geram metadados no modelo. O Auxiliar de marca de entrada consome os metadados do modelo e produz atributos [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` (consulte [Validação de modelo](../models/validation.md)). Esses atributos descrevem os validadores a serem anexados aos campos de entrada. Isso fornece validação de [jQuery](https://jquery.com/) e HTML5 discreto. Os atributos discretos têm o formato `data-val-rule="Error Message"`, em que a regra é o nome da regra de validação (como `data-val-required`, `data-val-email`, `data-val-maxlength` etc.) Se uma mensagem de erro for fornecida no atributo, ela será exibida como o valor para do atributo `data-val-rule`. Também há atributos do formulário `data-val-ruleName-argumentName="argumentValue"` que fornecem detalhes adicionais sobre a regra, por exemplo, `data-val-maxlength-max="1024"`.

### <a name="html-helper-alternatives-to-input-tag-helper"></a>Alternativas de Auxiliar HTML ao Auxiliar de marca de entrada

`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` e `Html.EditorFor` têm recursos que se sobrepõem aos di Auxiliar de marca de entrada. O Auxiliar de marca de entrada define automaticamente o atributo `type`; `Html.TextBox` e `Html.TextBoxFor` não o fazem. `Html.Editor` e `Html.EditorFor` manipulam coleções, objetos complexos e modelos; o Auxiliar de marca de entrada não o faz. O Auxiliar de marca de entrada, `Html.EditorFor` e `Html.TextBoxFor` são fortemente tipados (eles usam expressões lambda); `Html.TextBox` e `Html.Editor` não usam (eles usam nomes de expressão).

### <a name="htmlattributes"></a>HtmlAttributes

`@Html.Editor()` e `@Html.EditorFor()` usam uma entrada `ViewDataDictionary` especial chamada `htmlAttributes` ao executar seus modelos padrão. Esse comportamento pode ser aumentado usando parâmetros `additionalViewData`. A chave "htmlAttributes" diferencia maiúsculas de minúsculas. A chave "htmlAttributes" é tratada de forma semelhante ao objeto `htmlAttributes` passado para auxiliares de entrada como `@Html.TextBox()`.

```HTML
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a>Nomes de expressão

O valor do atributo `asp-for` é um `ModelExpression` e o lado direito de uma expressão lambda. Portanto, `asp-for="Property1"` se torna `m => m.Property1` no código gerado e é por isso você não precisa colocar o prefixo `Model`. Você pode usar o caractere "\@" para iniciar uma expressão embutida e mover para antes de `m.`:

```HTML
@{
       var joe = "Joe";
   }
   <input asp-for="@joe">
```

Gera o seguinte:

```HTML
<input type="text" id="joe" name="joe" value="Joe">
```

Com propriedades de coleção, `asp-for="CollectionProperty[23].Member"` gera o mesmo nome que `asp-for="CollectionProperty[i].Member"` quando `i` tem o valor `23`.

Quando o ASP.NET Core MVC calcula o valor de `ModelExpression`, ele inspeciona várias fontes, inclusive o `ModelState`. Considere o `<input type="text" asp-for="@Name">`. O atributo `value` calculado é o primeiro valor não nulo:

* Da entrada de `ModelState` com a chave "Name".
* Do resultado da expressão `Model.Name`.

### <a name="navigating-child-properties"></a>Navegando para propriedades filho

Você também pode navegar para propriedades filho usando o caminho da propriedade do modelo de exibição. Considere uma classe de modelo mais complexa que contém uma propriedade `Address` filho.

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

Na exibição, associamos a `Address.AddressLine1`:

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

O HTML a seguir é gerado para `Address.AddressLine1`:

```HTML
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a>Nomes de expressão e coleções

Exemplo, um modelo que contém uma matriz de `Colors`:

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

O método de ação:

```csharp
public IActionResult Edit(int id, int colorIndex)
   {
       ViewData["Index"] = colorIndex;
       return View(GetPerson(id));
   }
```

O Razor a seguir mostra como você acessa um elemento `Color` específico:

[!code-HTML[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

O modelo *Views/Shared/EditorTemplates/String.cshtml*:

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

Exemplo usando `List<T>`:

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

O Razor a seguir mostra como iterar em uma coleção:

[!code-HTML[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

O modelo *Views/Shared/EditorTemplates/ToDoItem.cshtml*:

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

`foreach` deve ser usado, se possível, quando o valor está prestes a ser usado em um contexto equivalente `asp-for` ou `Html.DisplayFor`. Em geral, `for` é melhor do que `foreach` (se o cenário permitir) porque não é necessário alocar um enumerador; no entanto, avaliar um indexador em uma expressão LINQ pode ser caro, o que deve ser minimizado.

&nbsp;

>[!NOTE]
>O código de exemplo comentado acima mostra como você substituiria a expressão lambda pelo operador `@` para acessar cada `ToDoItem` na lista.

## <a name="the-textarea-tag-helper"></a>Auxiliar de marca de área de texto

O auxiliar de marca `Textarea Tag Helper` é semelhante ao Auxiliar de marca de entrada.

* Gera os atributos `id` e `name`, bem como os atributos de validação de dados do modelo para um elemento [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea).

* Fornece tipagem forte.

* Alternativa de Auxiliar HTML: `Html.TextAreaFor`

Amostra:

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

O HTML a seguir é gerado:

```HTML
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a>O auxiliar de marca de rótulo

* Gera a legenda do rótulo e o atributo `for` em um elemento [\<rótulo>](https://www.w3.org/wiki/HTML/Elements/label) para um nome de expressão

* Alternativa de Auxiliar HTML: `Html.LabelFor`.

O `Label Tag Helper` fornece os seguintes benefícios em comparação com um elemento de rótulo HTML puro:

* Você obtém automaticamente o valor do rótulo descritivo do atributo `Display`. O nome de exibição desejado pode mudar com o tempo e a combinação do atributo `Display` e do Auxiliar de Marca de Rótulo aplicará `Display` em qualquer lugar em que for usado.

* Menos marcação no código-fonte

* Tipagem forte com a propriedade de modelo.

Amostra:

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

O HTML a seguir é gerado para o elemento `<label>`:

```HTML
<label for="Email">Email Address</label>
```

O Auxiliar de marca de rótulo gerou o valor do atributo `for` de "Email", que é a ID associada ao elemento `<input>`. Auxiliares de marca geram elementos `id` e `for` consistentes para que eles possam ser associados corretamente. A legenda neste exemplo é proveniente do atributo `Display`. Se o modelo não contivesse um atributo `Display`, a legenda seria o nome da propriedade da expressão.

## <a name="the-validation-tag-helpers"></a>Os auxiliares de marca de validação

Há dois auxiliares de marca de validação. O `Validation Message Tag Helper` (que exibe uma mensagem de validação para uma única propriedade em seu modelo) e o `Validation Summary Tag Helper` (que exibe um resumo dos erros de validação). O `Input Tag Helper` adiciona atributos de validação do lado do cliente HTML5 para elementos de entrada baseados em atributos de anotação de dados em suas classes de modelo. A validação também é executada no servidor. O Auxiliar de marca de validação exibe essas mensagens de erro quando ocorre um erro de validação.

### <a name="the-validation-message-tag-helper"></a>O Auxiliar de marca de mensagem de validação

* Adiciona o atributo `data-valmsg-for="property"` [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) ao elemento [span](https://developer.mozilla.org/docs/Web/HTML/Element/span), que anexa as mensagens de erro de validação no campo de entrada da propriedade do modelo especificado. Quando ocorre um erro de validação do lado do cliente, [jQuery](https://jquery.com/) exibe a mensagem de erro no elemento `<span>`.

* A validação também é feita no servidor. Os clientes poderão ter o JavaScript desabilitado e parte da validação só pode ser feita no lado do servidor.

* Alternativa de Auxiliar HTML: `Html.ValidationMessageFor`

O `Validation Message Tag Helper` é usado com o atributo `asp-validation-for` em um elemento HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span).

```HTML
<span asp-validation-for="Email"></span>
```

O Auxiliar de marca de mensagem de validação gerará o HTML a seguir:

```HTML
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

Geralmente, você usa o `Validation Message Tag Helper` após um Auxiliar de marca `Input` para a mesma propriedade. Fazer isso exibe as mensagens de erro de validação próximo à entrada que causou o erro.

> [!NOTE]
> É necessário ter uma exibição com as referências de script [jQuery](https://jquery.com/) e JavaScript corretas em vigor para a validação do lado do cliente. Consulte [Validação de Modelo](../models/validation.md) para obter mais informações.

Quando ocorre um erro de validação do lado do servidor (por exemplo, quando você tem validação do lado do servidor personalizada ou a validação do lado do cliente está desabilitada), o MVC coloca essa mensagem de erro como o corpo do elemento `<span>`.

```HTML
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a>Auxiliar de marca de resumo de validação

* Tem como alvo elementos `<div>` com o atributo `asp-validation-summary`

* Alternativa de Auxiliar HTML: `@Html.ValidationSummary`

O `Validation Summary Tag Helper` é usado para exibir um resumo das mensagens de validação. O valor do atributo `asp-validation-summary` pode ser qualquer um dos seguintes:

|asp-validation-summary|Mensagens de validação exibidas|
|--- |--- |
|ValidationSummary.All|Nível da propriedade e do modelo|
|ValidationSummary.ModelOnly|Modelo|
|ValidationSummary.None|Nenhum|

### <a name="sample"></a>Amostra

No exemplo a seguir, o modelo de dados é decorado com atributos `DataAnnotation`, o que gera mensagens de erro de validação no elemento `<input>`.  Quando ocorre um erro de validação, o Auxiliar de marca de validação exibe a mensagem de erro:

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

O código HTML gerado (quando o modelo é válido):

```HTML
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a>Auxiliar de Marca de Seleção

* Gera [select](https://www.w3.org/wiki/HTML/Elements/select) e os elementos [option](https://www.w3.org/wiki/HTML/Elements/option) associados para as propriedades do modelo.

* Tem uma alternativa de Auxiliar HTML `Html.DropDownListFor` e `Html.ListBoxFor`

O `Select Tag Helper` `asp-for` especifica o nome da propriedade do modelo para o elemento [select](https://www.w3.org/wiki/HTML/Elements/select) e `asp-items` especifica os elementos [option](https://www.w3.org/wiki/HTML/Elements/option).  Por exemplo:

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

Amostra:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

O método `Index` inicializa o `CountryViewModel`, define o país selecionado e o transmite para a exibição `Index`.

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

O método `Index` HTTP POST exibe a seleção:

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

A exibição `Index`:

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

Que gera o seguinte HTML (com "CA" selecionado):

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> Não é recomendável usar `ViewBag` ou `ViewData` com o Auxiliar de Marca de Seleção. Um modelo de exibição é mais robusto para fornecer metadados MVC e, geralmente, menos problemático.

O valor do atributo `asp-for` é um caso especial e não requer um prefixo `Model`, os outros atributos do Auxiliar de marca requerem (como `asp-items`)

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a>Associação de enumeração

Geralmente, é conveniente usar `<select>` com uma propriedade `enum` e gerar os elementos `SelectListItem` dos valores `enum`.

Amostra:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

O método `GetEnumSelectList` gera um objeto `SelectList` para uma enumeração.

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

É possível decorar sua lista de enumeradores com o atributo `Display` para obter uma interface do usuário mais rica:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

O HTML a seguir é gerado:

```HTML
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a>Grupo de opções

O elemento HTML [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) é gerado quando o modelo de exibição contém um ou mais objetos `SelectListGroup`.

O `CountryViewModelGroup` agrupa os elementos `SelectListItem` nos grupos "América do Norte" e "Europa":

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

Os dois grupos são mostrados abaixo:

![exemplo de grupo de opções](working-with-forms/_static/grp.png)

O HTML gerado:

```HTML
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a>Seleção múltipla

O Auxiliar de Marca de Seleção gerará automaticamente o atributo [multiple = "multiple"](https://w3c.github.io/html-reference/select.html) se a propriedade especificada no atributo `asp-for` for um `IEnumerable`. Por exemplo, considerando o seguinte modelo:

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

Com a seguinte exibição:

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

Gera o seguinte HTML:

```HTML
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a>Nenhuma seleção

Se acabar usando a opção "não especificado" em várias páginas, você poderá criar um modelo para eliminar o HTML de repetição:

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

O modelo *Views/Shared/EditorTemplates/CountryViewModel.cshtml*:

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

O acréscimo de elementos HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) não está limitado ao caso de *Nenhuma seleção*. Por exemplo, o seguinte método de ação e exibição gerarão HTML semelhante ao código acima:

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-HTML[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

O elemento `<option>` correto será selecionado (contém o atributo `selected="selected"`) dependendo do valor atual de `Country`.

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```HTML
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/tag-helpers/intro>
* [Elemento de formulário HTML](https://www.w3.org/TR/html401/interact/forms.html)
* [Token de verificação de solicitação](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [Interface IAttributeAdapter](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [Snippets de código para este documento](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)
