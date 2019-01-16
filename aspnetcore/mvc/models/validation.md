---
title: Validação de modelo no ASP.NET Core MVC
author: tdykstra
description: Saiba mais sobre a validação do modelo no ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2019
uid: mvc/models/validation
ms.openlocfilehash: f3a34972006b5fdee307c9a8d9989b2cc1e36893
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099377"
---
# <a name="model-validation-in-aspnet-core-mvc"></a>Validação de modelo no ASP.NET Core MVC

Por [Rachel Appel](https://github.com/rachelappel)

## <a name="introduction-to-model-validation"></a>Introdução à validação do modelo

Antes que um aplicativo armazene dados em um banco de dados, ele deve validá-los. Os dados precisam ser verificados quanto a possíveis ameaças de segurança, se estão formatados corretamente por tipo e tamanho e precisam estar em conformidade com as regras. A validação é necessária, embora possa ser redundante e monótona de ser implementada. No MVC, a validação ocorre no cliente e no servidor.

Felizmente, o .NET abstraiu a validação para atributos de validação. Esses atributos contêm o código de validação, reduzindo a quantidade de código que precisa ser escrita.

No ASP.NET Core 2.2 e posterior, o tempo de execução do ASP.NET Core encurta a validação (ignora) se puder determinar que um determinado gráfico de modelo não requer validação. Ignorar a validação pode fornecer melhorias significativas no desempenho ao validar modelos que não podem ou não tem nenhum validador associado. A validação ignorada inclui objetos, como coleções de primitivos (`byte[]`, `string[]`, `Dictionary<string, string>` etc.) ou grafos de objeto complexo sem nenhum validador.

[Exibir ou baixar amostra do GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample).

## <a name="validation-attributes"></a>Atributos de validação

Os atributos de validação são uma maneira de configurar a validação do modelo; portanto, é conceitualmente semelhante à validação em campos de tabelas de banco de dados. Isso inclui restrições, como atribuição de tipos de dados ou campos obrigatórios. Outros tipos de validação incluem a aplicação de padrões a dados para impor regras de negócio, como um cartão de crédito, número de telefone ou endereço de email. Os atributos de validação simplificam e facilitam o uso da imposição desses requisitos.

Os atributos de validação são especificados no nível da propriedade: 

```csharp
[Required]
public string MyProperty { get; set; } 
```

Veja abaixo um modelo `Movie` anotado de um aplicativo que armazena informações sobre filmes e programas de TV. A maioria das propriedades é obrigatória e várias propriedades de cadeia de caracteres têm requisitos de tamanho. Além disso, há uma restrição de intervalo numérico em vigor para a propriedade `Price` de 0 a US$ 999,99, juntamente com um atributo de validação personalizado.

[!code-csharp[](validation/sample/Movie.cs?range=6-29)]

A simples leitura do modelo revela as regras sobre os dados para esse aplicativo, facilitando a manutenção do código. Veja abaixo vários atributos de validação internos populares:

* `[CreditCard]`: valida se a propriedade tem um formato de cartão de crédito.

* `[Compare]`: valida se duas propriedades em um modelo são correspondentes.

* `[EmailAddress]`: valida se a propriedade tem um formato de email.

* `[Phone]`: valida se a propriedade tem um formato de telefone.

* `[Range]`: valida se o valor da propriedade está dentro do intervalo especificado.

* `[RegularExpression]`: valida se os dados correspondem à expressão regular especificada.

* `[Required]`: torna uma propriedade obrigatória.

* `[StringLength]`: valida se uma propriedade de cadeia de caracteres tem, no máximo, o tamanho máximo especificado.

* `[Url]`: valida se a propriedade tem um formato de URL.

O MVC dá suporte a qualquer atributo derivado de `ValidationAttribute` para fins de validação. Muitos atributos de validação úteis podem ser encontrados no namespace [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations).

Pode haver ocasiões em que você precisa de mais recursos do que os atributos internos fornecem. Para essas ocasiões, é possível criar atributos de validação personalizados pela derivação de `ValidationAttribute` ou alteração do modelo para implementar `IValidatableObject`.

## <a name="notes-on-the-use-of-the-required-attribute"></a>Observações sobre o uso do atributo Required

Os [tipos de valor](/dotnet/csharp/language-reference/keywords/value-types) que não permitem valores nulos (como `decimal`, `int`, `float` e `DateTime`) são inerentemente necessários e não precisam do atributo `Required`. O aplicativo não executa nenhuma verificação de validação do lado do servidor para tipos que não permitem valor nulo marcados como `Required`.

O model binding do MVC, que não está preocupado com a validação nem com atributos de validação, rejeita o envio de um campo de formulário que contém um valor ausente ou espaço em branco para um tipo que não permite valor nulo. Na ausência de um atributo `BindRequired` na propriedade de destino, o model binding ignora dados ausentes para tipos que não permitem valor nulo, nos quais o campo de formulário está ausente nos dados de formulário de entrada.

O [atributo BindRequired](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.bindrequiredattribute) (veja também <xref:mvc/models/model-binding#customize-model-binding-behavior-with-attributes>) é útil para garantir que os dados do formulário sejam preenchidos. Quando aplicado a uma propriedade, o sistema de model binding exige um valor para essa propriedade. Quando aplicado a um tipo, o sistema de model binding exige valores para todas as propriedades desse tipo.

Quando você usa um tipo [Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) (por exemplo, `decimal?` ou `System.Nullable<decimal>`) e marca-o como `Required`, é realizada uma verificação de validação do lado do servidor, como se a propriedade fosse um tipo que permite valor nulo padrão (para exemplo, uma `string`).

A validação do lado do cliente exige um valor para um campo de formulário que corresponde a uma propriedade de modelo que foi marcada como `Required` e para uma propriedade de tipo que não permite valor nulo que ainda não foi marcada como `Required`. `Required` pode ser usado para controlar a mensagem de erro de validação do lado do cliente.

## <a name="model-state"></a>Estado do modelo

O estado do modelo representa erros de validação nos valores de formulário HTML enviados.

O MVC continuará validando campos até atingir o número máximo de erros (200 por padrão). Você pode configurar esse número com o seguinte código no `Startup.ConfigureServices`:

[!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors)]

## <a name="handle-model-state-errors"></a>Erros de estado do modelo do identificador

A validação de modelo ocorre antes da execução de uma ação de controlador. É responsabilidade da ação inspecionar `ModelState.IsValid` e reagir adequadamente. Em muitos casos, a resposta apropriada é retornar uma resposta de erro, de preferência, fornecendo detalhes sobre o motivo pelo qual houve falha na validação do modelo.

::: moniker range=">= aspnetcore-2.1"

Quando `ModelState.IsValid` for avaliada como `false` em controladores de API Web usando o atributo `[ApiController]`, uma resposta automática HTTP 400 contendo detalhes do problema será retornada. Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).

::: moniker-end

Alguns aplicativos optarão por seguir uma convenção padrão para lidar com erros de validação do modelo, caso em que um filtro pode ser um local adequado para implementar uma política como essa. É necessário testar o comportamento das ações com estados de modelo válidos e inválidos.

## <a name="manual-validation"></a>Validação manual

Após a conclusão da validação e de model binding, recomendamos repetir partes disso. Por exemplo, um usuário pode ter inserido um texto em um campo que espera um inteiro ou talvez você precise calcular um valor da propriedade de um modelo.

Talvez seja necessário executar a validação manualmente. Para fazer isso, chame o método `TryValidateModel`, conforme mostrado aqui:

[!code-csharp[](validation/sample/MoviesController.cs?range=52)]

## <a name="custom-validation"></a>Validação personalizada

Os atributos de validação funcionam para a maior parte das necessidades de validação. No entanto, algumas regras de validação são específicas ao negócio. As regras podem não ser técnicas comuns de validação de dados, como garantir que um campo seja obrigatório ou que ele esteja em conformidade com um intervalo de valores. Para esses cenários, os atributos de validação personalizados são uma ótima solução. É fácil criar seus próprios atributos de validação personalizados no MVC. Basta herdar do `ValidationAttribute` e substituir o método `IsValid`. O método `IsValid` aceita dois parâmetros: o primeiro é um objeto chamado *value* e o segundo é um objeto `ValidationContext` chamado *validationContext*. *Value* refere-se ao valor real do campo que o validador personalizado está validando.

Na amostra a seguir, uma regra de negócios indica que os usuários não podem definir o gênero como *Clássico* para um filme lançado após 1960. O atributo `[ClassicMovie]` verifica o gênero primeiro e, se ele for um clássico, verificará a data de lançamento para ver se ela é posterior a 1960. Se ele tiver sido lançado após 1960, a validação falhará. O atributo aceita um parâmetro de inteiro que representa o ano que pode ser usado para validar os dados. Capture o valor do parâmetro no construtor do atributo, conforme mostrado aqui:

[!code-csharp[](validation/sample/ClassicMovieAttribute.cs?range=9-28)]

A variável `movie` acima representa um objeto `Movie` que contém os dados do envio do formulário a serem validados. Nesse caso, o código de validação verifica a data e o gênero no método `IsValid` da classe `ClassicMovieAttribute` de acordo com as regras. Após a validação bem-sucedida, o `IsValid` retorna um código `ValidationResult.Success`. Quando a validação falha, um `ValidationResult` com uma mensagem erro será retornado:

[!code-csharp[](validation/sample/ClassicMovieAttribute.cs?range=55-58)]

Quando um usuário modificar o campo `Genre` e enviar o formulário, o método `IsValid` da `ClassicMovieAttribute` verificará se o filme é um clássico. Como qualquer atributo interno, aplique o atributo `ClassicMovieAttribute` a uma propriedade como `ReleaseDate` para garantir que a validação ocorra, conforme mostrado no exemplo de código anterior. Como o exemplo funciona apenas com tipos `Movie`, a melhor opção é usar `IValidatableObject`, conforme mostrado no parágrafo a seguir.

Como alternativa, esse mesmo código pode ser colocado no modelo implementando o método `Validate` na interface `IValidatableObject`. Embora os atributos de validação personalizados funcionem bem para validar propriedades individuais, a implementação de `IValidatableObject` pode ser usada para implementar a validação no nível da classe como visto aqui.

[!code-csharp[](validation/sample/MovieIValidatable.cs?range=32-40)]

## <a name="client-side-validation"></a>Validação do lado do cliente

A validação do lado do cliente é uma ótima conveniência para usuários. Ela economiza tempo que de outra forma seria gasto aguardando uma viagem de ida e volta para o servidor. Em termos de negócios, até mesmo algumas frações de segundos multiplicados por centenas de vezes por dia chega a ser muito tempo, despesa e frustração. A validação imediata e simples permite que os usuários trabalhem com mais eficiência e façam contribuições e produzam entradas e saídas de melhor qualidade.

É necessário ter uma exibição com as referências de script JavaScript corretas em vigor para a validação do lado do cliente para que ela funcione como visto aqui.

[!code-cshtml[](validation/sample/Views/Shared/_Layout.cshtml?range=37)]

[!code-cshtml[](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml)]

O script do [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) é uma biblioteca de front-end personalizada da Microsoft que se baseia no popular plug-in [jQuery Validate](https://jqueryvalidation.org/). Sem o jQuery Unobtrusive Validation, você precisará codificar a mesma lógica de validação em dois locais: uma vez nos atributos de validação do lado do servidor nas propriedades do modelo e, em seguida, novamente nos scripts do lado do cliente (os exemplos para o método [`validate()`](https://jqueryvalidation.org/validate/) do jQuery Validate mostram a complexidade que isso pode gerar). Em vez disso, os [Auxiliares de Marca](xref:mvc/views/tag-helpers/intro) e os [Auxiliares HTML](xref:mvc/views/overview) do MVC podem usar os atributos de validação e os metadados de tipo das propriedades do modelo para renderizar [atributos de dados](http://w3c.github.io/html/dom.html#embedding-custom-non-visible-data-with-the-data-attributes) HTML 5 nos elementos de formulário que precisam de validação. O MVC gera os atributos `data-` para atributos internos e personalizados. O jQuery Unobtrusive Validation analisa os atributos `data-` e passa a lógica para o jQuery Validate, "copiando" efetivamente a lógica de validação do lado do servidor para o cliente. Exiba erros de validação no cliente usando os auxiliares de marca relevantes, conforme mostrado aqui:

[!code-cshtml[](validation/sample/Views/Movies/Create.cshtml?highlight=4,5&range=19-25)]

Os auxiliares de marca acima renderizam o HTML abaixo. Observe que os atributos `data-` na saída HTML correspondem aos atributos de validação da propriedade `ReleaseDate`. O atributo `data-val-required` abaixo contém uma mensagem de erro a ser exibida se o usuário não preencher o campo de data de lançamento. O jQuery Unobtrusive Validation passa esse valor para o método [`required()`](https://jqueryvalidation.org/required-method/) do jQuery Validate, que, por sua vez, exibe essa mensagem no elemento **\<span>** complementar.

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
                id="ReleaseDate" name="ReleaseDate" value="" />
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

A validação do lado do cliente impede o envio até que o formulário seja válido. O botão Enviar executa o JavaScript que envia o formulário ou exibe mensagens de erro.

O MVC determina os valores de atributo de tipo com base no tipo de dados .NET de uma propriedade, possivelmente, substituído com o uso de atributos `[DataType]`. O atributo `[DataType]` base não faz nenhuma validação real do lado do servidor. Os navegadores escolhem suas próprias mensagens de erro e exibem esses erros da maneira desejada. No entanto, o pacote Unobtrusive Validation do jQuery pode substituir as mensagens e exibi-las de forma consistente com as outras. Isso ocorre de forma mais evidente quando os usuários aplicam subclasses `[DataType]`, como `[EmailAddress]`.

### <a name="add-validation-to-dynamic-forms"></a>Adicionar validação a formulários dinâmicos

Como o jQuery Unobtrusive Validation passa parâmetros e a lógica de validação para o jQuery Validate quando a página é carregada pela primeira vez, os formulários gerados dinamicamente não exibem a validação de forma automática. Em vez disso, é necessário instruir o jQuery Unobtrusive Validation a analisar o formulário dinâmico imediatamente depois de criá-lo. Por exemplo, o código abaixo mostra como você pode configurar a validação do lado do cliente em um formulário adicionado por meio do AJAX.

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

O método `$.validator.unobtrusive.parse()` aceita um seletor do jQuery para um argumento seu. Esse método instrui o jQuery Unobtrusive Validation a analisar os atributos `data-` de formulários nesse seletor. Os valores desses atributos são então passados para o plug-in jQuery Validate, de modo que o formulário exiba as regras de validação do lado do cliente desejadas.

### <a name="add-validation-to-dynamic-controls"></a>Adicionar validação a controles dinâmicos

Também atualize as regras de validação em um formulário quando controles individuais, como `<input/>`s e `<select/>`s, são gerados dinamicamente. Não é possível passar seletores para esses elementos para o método `parse()` diretamente, porque o formulário adjacente já foi analisado e não será atualizado. Em vez disso, primeiro remova os dados de validação existentes e, em seguida, analise novamente todo o formulário, conforme mostrado abaixo:

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

## <a name="iclientmodelvalidator"></a>IClientModelValidator

Você pode criar uma lógica do lado do cliente para o atributo personalizado, e o [unobtrusive validation](http://bradwilson.typepad.com/blog/2010/10/mvc3-unobtrusive-validation.html), que cria um adaptador para a [validação do jQuery](http://jqueryvalidation.org/documentation/), será executado no cliente automaticamente como parte da validação. A primeira etapa é controlar quais atributos de dados são adicionados, pela implementação da interface `IClientModelValidator`, conforme mostrado aqui:

[!code-csharp[](validation/sample/ClassicMovieAttribute.cs?range=30-42)]

Atributos que implementam essa interface podem adicionar atributos HTML a campos gerados. O exame da saída do elemento `ReleaseDate` revela um HTML semelhante ao exemplo anterior, exceto que agora há um atributo `data-val-classicmovie` que foi definido no método `AddValidation` de `IClientModelValidator`.

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="" />
```

A validação não invasiva usa os dados nos atributos `data-` para exibir mensagens de erro. No entanto, o jQuery não reconhece regras ou mensagens até que você adicione-as ao objeto `validator` do jQuery. Isso é mostrado no exemplo a seguir, que adiciona um método de validação de cliente `classicmovie` personalizado ao objeto `validator` do jQuery. Para obter uma explicação sobre o método `unobtrusive.adapters.add`, confira [Unobtrusive Client Validation no ASP.NET MVC](http://bradwilson.typepad.com/blog/2010/10/mvc3-unobtrusive-validation.html).

[!code-javascript[](validation/sample/Views/Movies/Create.cshtml?name=snippet_UnobtrusiveValidation)]

Com o código anterior, o método `classicmovie` executa a validação do lado do cliente na data de lançamento do filme. A mensagem de erro é exibida se o método retornar `false`.

## <a name="remote-validation"></a>Validação remota

A validação remota é um ótimo recurso a ser usado quando você precisa validar os dados no cliente em relação aos dados no servidor. Por exemplo, o aplicativo pode precisar verificar se um email ou nome de usuário já está em uso e precisa consultar uma grande quantidade de dados para fazer isso. O download de conjuntos de dados grandes para validar um ou alguns campos consome muitos recursos. Isso também pode expor informações confidenciais. Uma alternativa é fazer uma solicitação de ida e volta para validar um campo.

Implemente a validação remota em um processo de duas etapas. Primeiro, é necessário anotar o modelo com o atributo `[Remote]`. O atributo `[Remote]` aceita várias sobrecargas que podem ser usadas para direcionar o JavaScript do lado do cliente para o código apropriado a ser chamado. O exemplo abaixo aponta para o método de ação `VerifyEmail` do controlador `Users`.

[!code-csharp[](validation/sample/User.cs?range=7-8)]

A segunda etapa é colocar o código de validação no método de ação correspondente, conforme definido no atributo `[Remote]`. De acordo com a documentação do método jQuery Validate[remoto](https://jqueryvalidation.org/remote-method/), a resposta do servidor deve ser uma cadeia JSON que seja:

* `"true"` para elementos válidos.
* `"false"`, `undefined` ou `null` para elementos inválidos, usando a mensagem de erro padrão.

Se a resposta do servidor for uma cadeia de caracteres (por exemplo, `"That name is already taken, try peter123 instead"`), a cadeia de caracteres é exibida como uma mensagem de erro personalizada no lugar da cadeia de caracteres padrão.

A definição do método `VerifyEmail` segue essas regras, conforme mostrado abaixo. Ela retorna uma mensagem de erro de validação se o email já está sendo usado ou `true` se o email está livre e encapsula o resultado em um objeto `JsonResult`. O lado do cliente pode então usar o valor retornado para continuar ou exibir o erro, se necessário.

[!code-csharp[](validation/sample/UsersController.cs?range=19-28)]

Agora, quando os usuários inserem um email, o JavaScript na exibição faz uma chamada remota para ver se o email já está sendo usado e, em caso afirmativo, exibe a mensagem de erro. Caso contrário, o usuário pode enviar o formulário como de costume.

A propriedade `AdditionalFields` do atributo `[Remote]` é útil para validação de combinações de campos em relação aos dados no servidor. Por exemplo, se o modelo `User` acima tiver duas propriedades adicionais chamadas `FirstName` e `LastName`, é recomendável verificar se não há usuários que já têm esse par de nomes. Defina as novas propriedades, conforme mostrado no seguinte código:

[!code-csharp[](validation/sample/User.cs?range=10-13)]

`AdditionalFields` poderia ter sido definido de forma explícita com as cadeias de caracteres `"FirstName"` e `"LastName"`, mas o uso do operador [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) dessa forma, simplifica a refatoração posterior. Em seguida, o método de ação para executar a validação precisa aceitar dois argumentos, um para o valor de `FirstName` e outro para o valor de `LastName`.

[!code-csharp[](validation/sample/UsersController.cs?range=30-39)]

Agora, quando os usuários inserem um nome e sobrenome, o JavaScript:

* Faz uma chamada remota para ver se esse par de nomes já está sendo usado.
* Se o par já está sendo usado, uma mensagem de erro é exibida.
* Caso contrário, o usuário pode enviar o formulário.

Se você precisa validar dois ou mais campos adicionais com o atributo `[Remote]`, forneça-os como uma lista delimitada por vírgula. Por exemplo, para adicionar uma propriedade `MiddleName` ao modelo, defina o atributo `[Remote]`, conforme mostrado no seguinte código:

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`, como todos os argumentos de atributo, deve ser uma expressão de constante. Portanto, você não deve usar uma [cadeia de caracteres interpolada](/dotnet/csharp/language-reference/keywords/interpolated-strings) ou chamar [`string.Join()`](https://msdn.microsoft.com/library/system.string.join(v=vs.110).aspx) para inicializar `AdditionalFields`. Para cada campo adicional adicionado ao atributo `[Remote]`, é necessário adicionar outro argumento ao método de ação do controlador correspondente.
