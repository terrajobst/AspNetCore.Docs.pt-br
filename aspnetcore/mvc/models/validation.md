---
title: Validação de modelo no ASP.NET Core MVC
author: rick-anderson
description: Saiba mais sobre a validação de modelo no ASP.NET Core MVC e Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
monikerRange: '>= aspnetcore-2.1'
uid: mvc/models/validation
ms.openlocfilehash: eb18d3a701a4d1937ac6eb9f61916f348b95882a
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975254"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a>Validação de modelo no ASP.NET Core MVC e Razor Pages

Este artigo explica como validar a entrada do usuário em um aplicativo em ASP.NET Core MVC ou Razor Pages.

[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([como baixar](xref:index#how-to-download-a-sample)).

## <a name="model-state"></a>Estado do modelo

O estado do modelo representa erros que vêm de dois subsistemas: model binding e validação de modelo. Erros que se originam de [model binding](model-binding.md) geralmente são erros de conversão de dados (por exemplo, um "x" é inserido em um campo que espera um inteiro). A validação do modelo ocorre após o model binding e relata os erros em que os dados não estão em conformidade com as regras de negócio (por exemplo, um 0 é inserido em um campo que espera uma classificação entre 1 e 5).

O model binding e a validação ocorrem antes da execução de uma ação do controlador ou de um método de manipulador do Razor Pages. Nos aplicativos Web, é responsabilidade do aplicativo inspecionar `ModelState.IsValid` e reagir adequadamente. Geralmente, os aplicativos Web reexibem a página com uma mensagem de erro:

[!code-csharp[](validation/sample_snapshot/Create.cshtml.cs?name=snippet&highlight=3-6)]

Os controladores de API Web não precisarão verificar `ModelState.IsValid` se eles tiverem o atributo `[ApiController]`. Nesse caso, uma resposta automática HTTP 400 contendo detalhes do problema será retornada quando o estado do modelo for inválido. Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).

## <a name="rerun-validation"></a>Executar validação novamente

A validação é automática, mas talvez seja necessário repeti-la manualmente. Por exemplo, você pode calcular um valor para uma propriedade e executar novamente a validação após a configuração da propriedade com o valor computado. Para reexecutar a validação, chame o método `TryValidateModel`, conforme mostrado aqui:

[!code-csharp[](validation/sample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a>Atributos de validação

Os atributos de validação permitem que você especifique regras de validação para propriedades do modelo. O exemplo a seguir do [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) mostra uma classe de modelo que é anotada com atributos de validação. O atributo `[ClassicMovie]` é um atributo de validação personalizado e os outros são atributos internos. (O `[ClassicMovie2]` não está exibido e mostra uma maneira alternativa de implementar um atributo personalizado.)

[!code-csharp[](validation/sample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a>Atributos internos

Aqui estão alguns dos atributos de validação internos:

* `[CreditCard]`: valida se a propriedade tem um formato de cartão de crédito.
* `[Compare]`: valida se duas propriedades em um modelo são correspondentes.
* `[EmailAddress]`: valida se a propriedade tem um formato de email.
* `[Phone]`: valida se a propriedade tem um formato de número de telefone.
* `[Range]`: valida se o valor da propriedade está dentro de um intervalo especificado.
* `[RegularExpression]`: valida se o valor da propriedade corresponde à expressão regular especificada.
* `[Required]`: valida se o campo não é nulo. Consulte [atributo [Required]](#required-attribute) para obter detalhes sobre o comportamento desse atributo.
* `[StringLength]`: valida se um valor da propriedade de cadeia de caracteres não excede um limite de comprimento especificado.
* `[Url]`: valida se a propriedade tem um formato de URL.
* `[Remote]`: valida a entrada no cliente chamando um método de ação no servidor. Consulte [atributo [Remote]](#remote-attribute) para obter detalhes sobre o comportamento desse atributo.

Uma lista completa de atributos de validação pode ser encontrada no namespace [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations).

### <a name="error-messages"></a>Mensagens de erro

Os atributos de validação permitem que você especifique a mensagem de erro a ser exibido para uma entrada inválida. Por exemplo:

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

Internamente, a chamada de atributos `String.Format` com um espaço reservado para o nome do campo e, às vezes, espaços reservados adicionais. Por exemplo:

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

Quando aplicada a uma propriedade `Name`, a mensagem de erro criada pelo código anterior seria "Comprimento do nome ter entre 6 e 8.".

Para descobrir quais parâmetros são passados para `String.Format` no caso de uma mensagem de erro de um atributo específico, consulte o [código-fonte de DataAnnotations](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).

## <a name="required-attribute"></a>Atributo [Required]

Por padrão, o sistema de validação trata parâmetros não anuláveis ou propriedades como se tivessem um atributo `[Required]`. [Tipos de valor](/dotnet/csharp/language-reference/keywords/value-types) como `decimal` e `int` são não anuláveis.

### <a name="required-validation-on-the-server"></a>Validação de [Required] no servidor

No servidor, um valor obrigatório será considerado ausente se a propriedade for nula. Um campo não anulável é sempre válido e a mensagem de erro do atributo [Required] nunca é exibida.

No entanto, o model binding para uma propriedade que não permite valor nulo pode falhar, resultando em uma mensagem de erro como `The value '' is invalid`. Para especificar uma mensagem de erro personalizada para a validação de tipos não anuláveis do lado do servidor, você tem as seguintes opções:

* Tornar o campo anulável (por exemplo, `decimal?` em vez de `decimal`). Tipos de valor [Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) são tratados como tipos que permitem valor nulo padrão.
* Especifique a mensagem de erro padrão a ser usada pelo model binding, conforme mostrado no exemplo a seguir:

  [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  Para obter mais informações sobre erros de model binding para os quais você pode definir mensagens padrão, consulte <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.

### <a name="required-validation-on-the-client"></a>Validação de [Required] no cliente

Cadeias de caracteres e tipos que não permitem valor nulo são tratados de maneira diferente no cliente em comparação com servidor. No cliente:

* Um valor será considerado presente apenas se a entrada for inserida para ele. Portanto, a validação do lado do cliente lida com tipos que não permitem valor nulo da mesma maneira que com tipos que permitem valor nulo.
* O espaço em branco em um campo de cadeia de caracteres é considerado uma entrada válida pelo método [required](https://jqueryvalidation.org/required-method/) do jQuery Validation. A validação do lado do servidor considerará um campo de cadeia de caracteres necessário inválido somente se o espaço em branco for inserido.

Conforme observado anteriormente, os tipos que não permitem valor nulo são tratados como se tivessem um atributo `[Required]`. Isso significa que você obtém a validação do lado do cliente mesmo que não aplique o atributo `[Required]`. Mas se não for usar o atributo, você obterá uma mensagem de erro padrão. Para especificar uma mensagem de erro personalizada, use o atributo.

## <a name="remote-attribute"></a>Atributo [Remote]

O atributo `[Remote]` implementa a validação do lado do cliente, exigindo a chamada de um método no servidor para determinar se a entrada do campo é válida. Por exemplo, o aplicativo pode precisar verificar se um nome de usuário já está em uso.

Para implementar a validação remota:

1. Crie um método de ação para JavaScript para chamar.  O método [remoto](https://jqueryvalidation.org/remote-method/) do jQuery Validate espera uma resposta JSON:

   * `"true"` significa que os dados de entrada são válidos.
   * `"false"`, `undefined` ou `null` significa que a entrada é inválida.  Exiba a mensagem de erro padrão.
   * Qualquer outra cadeia de caracteres significa que a entrada é inválida. Exiba a cadeia de caracteres como uma mensagem de erro personalizada.

   Aqui está um exemplo de um método de ação que retorna uma mensagem de erro personalizada:

   [!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. Na classe de modelo, anote a propriedade com um atributo `[Remote]` que aponta para o método de ação de validação, conforme mostrado no exemplo a seguir:

   [!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   O atributo `[Remote]` está no namespace `Microsoft.AspNetCore.Mvc`. Instale o pacote NuGet [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) se você não estiver usando o metapacote `Microsoft.AspNetCore.App` ou `Microsoft.AspNetCore.All`.
   
### <a name="additional-fields"></a>Campos adicionais

A propriedade `AdditionalFields` do atributo `[Remote]` permite validar combinações de campos em relação aos dados no servidor. Por exemplo, se o modelo `User` tinha as propriedades `FirstName` e `LastName`, pode ser interessante verificar se nenhum usuário existente já tem esse par de nomes. O exemplo a seguir mostra como usar `AdditionalFields`:

[!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserNameProperties)]

`AdditionalFields` pode ser definido de forma explícita com as cadeias de caracteres `"FirstName"` e `"LastName"`, mas o uso do operador [`nameof`](/dotnet/csharp/language-reference/keywords/nameof), simplifica a refatoração posterior. O método de ação para essa validação deve aceitar os argumentos de primeiro nome e de sobrenome:

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyName)]

Quando o usuário insere o primeiro nome ou o sobrenome, o JavaScript faz uma chamada remota para ver se esse par de nomes foi usado.

Para validar dois ou mais campos adicionais, forneça-os como uma lista delimitada por vírgulas. Por exemplo, para adicionar uma propriedade `MiddleName` ao modelo, defina o atributo `[Remote]`, conforme mostrado no seguinte exemplo:

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`, como todos os argumentos de atributo, deve ser uma expressão de constante. Portanto, não use uma [cadeia de caracteres interpolada](/dotnet/csharp/language-reference/keywords/interpolated-strings) nem chame <xref:System.String.Join*> para inicializar `AdditionalFields`.

## <a name="alternatives-to-built-in-attributes"></a>Alternativas aos atributos internos

Se precisar de validação não fornecida por atributos internos, você poderá:

* [Criar atributos personalizados](#custom-attributes).
* [Implementar IValidatableObject](#ivalidatableobject).

## <a name="custom-attributes"></a>Atributos personalizados

Para cenários não compatíveis com os atributos de validação internos, você pode criar atributos de validação personalizados. Crie uma classe que herda de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> e substitua o método <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.

O método `IsValid` aceita um objeto chamado *valor*, que é a entrada a ser validada. Uma sobrecarga também aceita um objeto `ValidationContext`, que fornece informações adicionais, como a instância do modelo criada pelo model binding.

O exemplo a seguir valida se a data de lançamento de um filme no gênero *Clássico* não é posterior a um ano especificado. O atributo `[ClassicMovie2]` verificará o gênero primeiro e continuará apenas se for *Clássico*. Para filmes identificados como clássicos, ele verifica a data de lançamento para garantir que não seja posterior ao limite passado ao construtor de atributo).

[!code-csharp[](validation/sample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

A variável `movie` no exemplo anterior representa um objeto `Movie` que contém os dados do envio do formulário. O método `IsValid` verifica a data e o gênero. Após a validação bem-sucedida, o `IsValid` retorna um código `ValidationResult.Success`. Quando a validação falha, um `ValidationResult` com uma mensagem erro será retornado.

## <a name="ivalidatableobject"></a>IValidatableObject

O exemplo anterior funciona apenas com tipos `Movie`. Outra opção para validação de nível de classe é implementar `IValidatableObject` na classe de modelo, conforme mostrado no exemplo a seguir:

[!code-csharp[](validation/sample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>Validação de nó de nível superior

Os nós de nível superior incluem:

* Parâmetros de ação
* Propriedades do controlador
* Parâmetros do manipulador de página
* Propriedades do modelo de página

Os nós de nível superior associados ao modelo são validados além de validar as propriedades do modelo. No exemplo a seguir do aplicativo de exemplo, o método `VerifyPhone` usa o <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> para validar o parâmetro de ação `phone`:

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

Os nós de nível superior podem usar <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> com atributos de validação. No exemplo a seguir do aplicativo de exemplo, o método `CheckAge` especifica que o parâmetro `age` deve ser associado da cadeia de caracteres de consulta quando o formulário é enviado:

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_CheckAge)]

Na página Verificar Idade (*CheckAge.cshtml*), há dois formulários. O primeiro formulário envia um valor `Age` de `99` como uma cadeia de caracteres de consulta: `https://localhost:5001/Users/CheckAge?Age=99`.

Quando um parâmetro `age` formatado corretamente da cadeia de caracteres de consulta é enviado, o formulário é validado.

O segundo formulário na página Verificar Idade envia o valor `Age` no corpo da solicitação e a validação falha. A associação falha porque o parâmetro `age` deve vir de uma cadeia de caracteres de consulta.

Ao executar com `CompatibilityVersion.Version_2_1` ou posterior, a validação do nó de nível superior é habilitada por padrão. Caso contrário, a validação do nó de nível superior ficará desabilitada. A opção padrão pode ser substituída pela configuração da propriedade <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> em (`Startup.ConfigureServices`), conforme mostrado aqui:

[!code-csharp[](validation/sample_snapshot/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a>Máximo de erros

A validação para quando o número máximo de erros é atingido (200 por padrão). Você pode configurar esse número com o seguinte código no `Startup.ConfigureServices`:

[!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a>Recursão máxima

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> percorre o grafo de objeto do modelo que está sendo validado. Para modelos que são muito profundos ou que são infinitamente recursivos, a validação pode resultar em estouro de pilha. O [MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) fornece uma maneira de interromper a validação antes que a recursão visitante exceda uma profundidade configurada. O valor padrão de `MvcOptions.MaxValidationDepth` é 200 durante a execução com `CompatibilityVersion.Version_2_2` ou posterior. Para versões anteriores, o valor é nulo, o que significa que não há nenhuma restrição de profundidade.

## <a name="automatic-short-circuit"></a>Curto-circuito automático

A validação sofrerá curto-circuito (será ignorada) automaticamente se o grafo do modelo não exigir validação. Objetos em que o tempo de execução ignora a validação para inclusão de coleções de primitivos (como `byte[]`, `string[]` e `Dictionary<string, string>`) e grafos de objetos complexos que não têm um validador.

## <a name="disable-validation"></a>Desabilitar validação

Para desabilitar a validação:

1. Crie uma implementação de `IObjectModelValidator` que não marque nenhum campo como inválido.

   [!code-csharp[](validation/sample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. Adicione o seguinte código ao `Startup.ConfigureServices` para substituir a implementação `IObjectModelValidator` padrão no contêiner de injeção de dependência.

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_DisableValidation)]

Talvez você ainda veja erros de estado de modelo que se originam do model binding.

## <a name="client-side-validation"></a>Validação do lado do cliente

A validação do lado do cliente impede o envio até que o formulário seja válido. O botão Enviar executa o JavaScript que envia o formulário ou exibe mensagens de erro.

A validação do lado do cliente evita uma viagem de ida e volta desnecessária ao servidor quando há erros de entrada em um formulário. O script a seguir faz referência nas validações de suporte *_Layout.cshtml* e *_ValidationScriptsPartial.cshtml* no lado do cliente:

[!code-cshtml[](validation/sample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

O script do [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) é uma biblioteca de front-end personalizada da Microsoft que se baseia no popular plug-in [jQuery Validate](https://jqueryvalidation.org/). Sem o jQuery Unobtrusive Validation, você teria que codificar a mesma lógica de validação em dois locais: uma vez nos atributos de validação do lado do servidor nas propriedades do modelo e, em seguida, novamente nos scripts do lado do cliente. Em vez disso, os [Auxiliares de Marca](xref:mvc/views/tag-helpers/intro) e os [Auxiliares HTML](xref:mvc/views/overview) usam os atributos de validação e os metadados de tipo das propriedades do modelo para renderizar atributos `data-` de HTML 5 para os elementos de formulário que precisam de validação. O jQuery Unobtrusive Validation analisa os atributos `data-` e passa a lógica para o jQuery Validate, "copiando" efetivamente a lógica de validação do lado do servidor para o cliente. Você pode exibir erros de validação no cliente usando os auxiliares de marca conforme mostrado aqui:

[!code-cshtml[](validation/sample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

Os auxiliares de marca acima renderizam o HTML a seguir.

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

Observe que os atributos `data-` na saída HTML correspondem aos atributos de validação da propriedade `ReleaseDate`. O atributo `data-val-required` conterá uma mensagem de erro a ser exibida se o usuário não preencher o campo de data de lançamento. O jQuery Unobtrusive Validation passa esse valor para o método [`required()`](https://jqueryvalidation.org/required-method/) do jQuery Validate, que, por sua vez, exibe essa mensagem no elemento **\<span>** complementar.

A validação de tipo de dados é baseada no tipo .NET de uma propriedade, a menos que seja substituída por um atributo `[DataType]`. Os navegadores têm suas próprias mensagens de erro padrão, mas o pacote de validação do jQuery Validation Unobtrusive pode substituir essas mensagens. Os atributos `[DataType]` e as subclasses como `[EmailAddress]` permitem que você especifique a mensagem de erro.

### <a name="add-validation-to-dynamic-forms"></a>Adicionar validação a formulários dinâmicos

O jQuery Unobtrusive Validation passa parâmetros e a lógica de validação para o jQuery Validate quando a página é carregada pela primeira vez. Portanto, a validação não funciona automaticamente em formulários gerados dinamicamente. Para habilitar a validação é necessário instruir o jQuery Unobtrusive Validation a analisar o formulário dinâmico imediatamente depois de criá-lo. Por exemplo, o código a seguir define a validação do lado do cliente em um formulário adicionado por meio do AJAX.

```js
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

O método `$.validator.unobtrusive.parse()` aceita um seletor do jQuery para um argumento seu. Esse método instrui o jQuery Unobtrusive Validation a analisar os atributos `data-` de formulários nesse seletor. Depois, os valores desses atributos são passados para o plug-in do jQuery Validate.

### <a name="add-validation-to-dynamic-controls"></a>Adicionar validação a controles dinâmicos

O método `$.validator.unobtrusive.parse()` funciona em um formulário inteiro, não em controles individuais gerados dinamicamente, como `<input>` e `<select/>`. Para uma nova análise do formulário, remova os dados de validação que foram adicionados ao formulário mesmo quando foi analisado anteriormente, conforme mostrado no exemplo a seguir:

```js
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a>Validação personalizada do lado do cliente

A validação personalizada do lado do cliente é feita por meio da geração de atributos de HTML `data-` que funcionam com um adaptador do jQuery Validate personalizado. O seguinte código do adaptador de exemplo foi escrito para os atributos `ClassicMovie` e `ClassicMovie2` que foram apresentados no início deste artigo:

[!code-javascript[](validation/sample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

Para obter informações sobre como escrever adaptadores, consulte a [documentação do jQuery Validate](https://jqueryvalidation.org/documentation/).

O uso de um adaptador para um determinado campo é disparado por atributos `data-` que:

* Marcam o campo como sujeito à validação do sinalizador (`data-val="true"`).
* Identifique o nome de uma regra de validação e o texto da mensagem de erro (por exemplo, `data-val-rulename="Error message."`).
* Forneça os parâmetros adicionais que o validador precisa (por exemplo, `data-val-rulename-parm1="value"`).

A exemplo a seguir mostra os atributos `data-` para o atributo `ClassicMovie` do aplicativo de exemplo:

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

Conforme observado anteriormente, os [Auxiliares de Marca](xref:mvc/views/tag-helpers/intro) e [Auxiliares de HTML](xref:mvc/views/overview) usam informações de atributos de validação para renderizar atributos `data-`. Há duas opções para escrever código que resulte na criação de atributos HTML `data-` personalizados:

* Criar uma classe que deriva de `AttributeAdapterBase<TAttribute>` e uma classe que implementa `IValidationAttributeAdapterProvider` e registrar o atributo e o adaptador na DI. Este método segue a [entidade de segurança de responsabilidade única](https://wikipedia.org/wiki/Single_responsibility_principle) em que o código de validação relacionado ao cliente e ao servidor fica em classes separadas. O adaptador também tem a vantagem de que, uma vez que ele é registrado na DI, outros serviços da DI ficam disponíveis para ele, se necessário.
* Implementar `IClientModelValidator` em na classe `ValidationAttribute`. Esse método poderá ser adequado se o atributo não fizer nenhuma validação do lado do servidor e não precisar de um serviço da DI.

### <a name="attributeadapter-for-client-side-validation"></a>AttributeAdapter para validação do lado do cliente

Esse método de renderização de atributos `data-` em HTML é usado pelo atributo `ClassicMovie` no aplicativo de exemplo. Para adicionar a validação do cliente usando esse método:

1. Crie uma classe de adaptador de atributo para o atributo de validação personalizado. Derive a classe de [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2). Criar um método `AddValidation` que adiciona atributos `data-` à saída renderizada, conforme mostrado neste exemplo:

   [!code-csharp[](validation/sample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. Crie uma classe de provedor de adaptador que implementa <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>. No método `GetAttributeAdapter`, passe o atributo personalizado para o construtor do adaptador, conforme mostrado neste exemplo:

   [!code-csharp[](validation/sample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. Registre o provedor de adaptador para DI em `Startup.ConfigureServices`:

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>IClientModelValidator para validação do lado do cliente

Esse método de renderização de atributos `data-` em HTML é usado pelo atributo `ClassicMovie2` no aplicativo de exemplo. Para adicionar a validação do cliente usando esse método:

* No atributo de validação personalizado, implemente a interface `IClientModelValidator` e crie um método `AddValidation`. No método `AddValidation`, adicione atributos `data-` para validação, conforme mostrado no exemplo a seguir:

  [!code-csharp[](validation/sample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a>Desabilitar validação do lado do cliente

O código a seguir desabilita a validação de cliente nas exibições do MVC:

[!code-csharp[](validation/sample_snapshot/Startup2.cs?name=snippet_DisableClientValidation)]

E em Razor Pages:

[!code-csharp[](validation/sample_snapshot/Startup3.cs?name=snippet_DisableClientValidation)]

Outra opção para desabilitar a validação do cliente é comentar a referência ao `_ValidationScriptsPartial` em seu arquivo *.cshtml*.

## <a name="additional-resources"></a>Recursos adicionais

* [Namespace System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations)
* [Model binding](model-binding.md)
