---
title: Model binding no ASP.NET Core
author: rick-anderson
description: Saiba como funciona o model binding no ASP.NET Core e como personalizar seu comportamento.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
uid: mvc/models/model-binding
ms.openlocfilehash: 19580768679f30131683717792252c03aade68f9
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666274"
---
# <a name="model-binding-in-aspnet-core"></a>Model binding no ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Este artigo explica o que é model binding, como ele funciona e como personalizar seu comportamento.

[Exibir ou baixar um código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([como baixar](xref:index#how-to-download-a-sample)).

## <a name="what-is-model-binding"></a>O que é o model binding

Controladores e Razor Pages funcionam com os dados provenientes de solicitações HTTP. Por exemplo, dados de rota podem fornecer uma chave de registro e campos de formulário postados podem fornecer valores para as propriedades do modelo. Escrever código para recuperar cada um desses valores e convertê-los de cadeias de caracteres em tipos .NET seria uma tarefa entediante e propensa a erro. O model binding automatiza esse processo. O sistema de model binding:

* Recupera dados de várias fontes, como dados de rota, campos de formulário e cadeias de caracteres de consulta.
* Fornece os dados a controladores e Razor Pages em parâmetros de método e propriedades públicas.
* Converte dados de cadeia de caracteres em tipos .NET.
* Atualiza as propriedades de tipos complexos.

## <a name="example"></a>{1&gt;Exemplo&lt;1}

Suponha que você tenha o seguinte método de ação:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

E o aplicativo receba uma solicitação com esta URL:

```
http://contoso.com/api/pets/2?DogsOnly=true
```

A associação de modelo passa pelas seguintes etapas depois que o sistema de roteamento seleciona o método de ação:

* Localiza o primeiro parâmetro de `GetByID`, um número inteiro denominado `id`.
* Examina as fontes disponíveis na solicitação HTTP e localiza `id` = "2" em dados de rota.
* Converte a cadeia de caracteres "2" em inteiro 2.
* Localiza o próximo parâmetro de `GetByID`, um booliano chamado `dogsOnly`.
* Examina as fontes e localiza "DogsOnly=true" na cadeia de consulta. A correspondência de nomes não diferencia maiúsculas de minúsculas.
* Converte a cadeia de caracteres "true" no booliano `true`.

A estrutura então chama o método `GetById`, passando 2 para o parâmetro `id` e `true` para o parâmetro `dogsOnly`.

No exemplo anterior, os destinos do model binding são parâmetros de método que são tipos simples. Destinos também podem ser as propriedades de um tipo complexo. Depois de cada propriedade ser associada com êxito, a [validação do modelo](xref:mvc/models/validation) ocorre para essa propriedade. O registro de quais dados estão associados ao modelo, além de quaisquer erros de validação ou de associação, é armazenado em [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState). Para descobrir se esse processo foi bem-sucedido, o aplicativo verifica o sinalizador [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).

## <a name="targets"></a>Destinos

O model binding tenta encontrar valores para os seguintes tipos de destinos:

* Parâmetros do método de ação do controlador para o qual uma solicitação é roteada.
* Os parâmetros do método do manipulador do Razor Pages para o qual uma solicitação é roteada. 
* Propriedades públicas de um controlador ou classe `PageModel`, se especificadas por atributos.

### <a name="bindproperty-attribute"></a>Atributo [BindProperty]

Pode ser aplicado a uma propriedade pública de um controlador ou classe `PageModel` para fazer o model binding ter essa propriedade como destino:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a>Atributo [BindProperties]

Disponível no ASP.NET Core 2.1 e posteriores.  Pode ser aplicado a um controlador ou classe `PageModel` para informar o model binding para ter todas as propriedades públicas da classe como destino:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>Model binding para solicitações HTTP GET

Por padrão, as propriedades não são vinculadas para solicitações HTTP GET. Normalmente, tudo o que você precisa para uma solicitação GET é um parâmetro de ID de registro. A ID do registro é usada para pesquisar o item no banco de dados. Portanto, não é necessário associar uma propriedade que contém uma instância do modelo. Em cenários em que você deseja associar propriedades a dados de solicitações GET, defina a propriedade `SupportsGet` como `true`:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>Fontes

Por padrão, o model binding obtém dados na forma de pares chave-valor das seguintes fontes em uma solicitação HTTP:

1. Campos de formulário
1. O corpo da solicitação (para [controladores que têm o atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).
1. Rotear dados
1. Consultar parâmetros de cadeia de caracteres
1. Arquivos carregados

Para cada parâmetro ou propriedade de destino, as fontes são verificadas na ordem indicada na lista anterior. Há algumas exceções:

* Os valores de cadeia de caracteres de consulta e dados de rota são usados apenas para tipos simples.
* Arquivos carregados são associados apenas a tipos de destino que implementam `IFormFile` ou `IEnumerable<IFormFile>`.

Se a origem padrão não estiver correta, use um dos seguintes atributos para especificar a origem:

* [`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -obtém valores da cadeia de caracteres de consulta. 
* [`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -obtém valores de dados de rota.
* [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -obtém valores dos campos de formulário postados.
* [`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -obtém valores do corpo da solicitação.
* [`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -obtém valores de cabeçalhos HTTP.

Esses atributos:

* São adicionados às propriedades de modelo individualmente (não à classe de modelo), como no exemplo a seguir:

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* Opcionalmente, aceite um valor de nome de modelo no construtor. Essa opção é fornecida caso o nome da propriedade não corresponda ao valor na solicitação. Por exemplo, o valor na solicitação pode ser um cabeçalho com um hífen em seu nome, como no exemplo a seguir:

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>Atributo [FromBody]

Aplique o atributo `[FromBody]` a um parâmetro para popular suas propriedades do corpo de uma solicitação HTTP. O tempo de execução de ASP.NET Core delega a responsabilidade de ler o corpo para um formatador de entrada. O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).

Quando `[FromBody]` é aplicado a um parâmetro de tipo complexo, todos os atributos de origem de associação aplicados às suas propriedades são ignorados. Por exemplo, a ação a seguir `Create` especifica que seu parâmetro `pet` é populado a partir do corpo:

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

A classe `Pet` especifica que sua propriedade `Breed` é populada a partir de um parâmetro de cadeia de caracteres de consulta:

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

No exemplo anterior:

* O atributo `[FromQuery]` é ignorado.
* A propriedade `Breed` não é preenchida a partir de um parâmetro de cadeia de caracteres de consulta. 

Os formatadores de entrada lêem apenas o corpo e não entendem os atributos de origem da associação. Se um valor adequado for encontrado no corpo, esse valor será usado para popular a propriedade `Breed`.

Não aplique `[FromBody]` a mais de um parâmetro por método de ação. Depois que o fluxo de solicitação é lido por um formatador de entrada, ele não está mais disponível para ser lido novamente para associar outros parâmetros de `[FromBody]`.

### <a name="additional-sources"></a>Fontes adicionais

Os dados de origem são fornecidos ao sistema de model binding pelos *provedores de valor*. Você pode escrever e registrar provedores de valor personalizados que obtêm dados para model binding de outras fontes. Por exemplo, talvez você queira dados de cookies ou estado de sessão. Para obter dados de uma nova fonte:

* Crie uma classe que implementa `IValueProvider`.
* Crie uma classe que implementa `IValueProviderFactory`.
* Registre a classe de alocador em `Startup.ConfigureServices`.

O aplicativo de exemplo inclui um [provedor de valor](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) e um exemplo de [alocador](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) que obtém valores de cookies. Aqui está o código de registro em `Startup.ConfigureServices`:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

O código mostrado coloca o provedor de valor personalizado depois de todos os provedores de valor internos.  Para torná-lo o primeiro na lista, chame `Insert(0, new CookieValueProviderFactory())`, em vez de `Add`.

## <a name="no-source-for-a-model-property"></a>Nenhuma fonte para uma propriedade de modelo

Por padrão, um erro de estado de modelo não será criado se nenhum valor for encontrado para uma propriedade de modelo. A propriedade é definida como null ou um valor padrão:

* Tipos simples anuláveis definidos como `null`.
* Tipos de valor não anuláveis são definidos como `default(T)`. Por exemplo, um parâmetro `int id` é definido como 0.
* Para tipos complexos, o model binding cria uma instância usando o construtor padrão sem definir propriedades.
* As matrizes são definidas como `Array.Empty<T>()`, exceto que matrizes `byte[]` são definidas como `null`.

Se o estado do modelo deve ser invalidado quando nada for encontrado em campos de formulário para uma propriedade de modelo, use o atributo [`[BindRequired]`](#bindrequired-attribute) .

Observe que este comportamento `[BindRequired]` se aplica ao model binding de dados de formulário postados, não a dados JSON ou XML em um corpo da solicitação. Dados do corpo da solicitação são tratados pelos [formatadores de entrada](#input-formatters).

## <a name="type-conversion-errors"></a>Erros de conversão de tipos

Se uma fonte for encontrada, mas não puder ser convertida no tipo de destino, o estado do modelo será sinalizado como inválido. O parâmetro ou a propriedade de destino é definido como nulo ou um valor padrão, conforme observado na seção anterior.

Em um controlador de API que tem o atributo `[ApiController]`, um estado de modelo inválido resulta em uma resposta HTTP 400 automática.

Em uma Razor Page, exiba novamente a página com uma mensagem de erro:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

Validação no lado do cliente captura a maioria dos dados inválidos que de outra forma seriam enviados a um formulário do Razor Pages. Essa validação torna difícil disparar o código realçado anterior. O aplicativo de exemplo inclui um botão **Enviar com Data Inválida** que coloca os dados inválidos no campo **Data de Contratação** e envia o formulário. Esse botão mostra como o código para exibir novamente a página funciona quando ocorrem erros de conversão de dados.

Quando a página é exibida novamente pelo código anterior, a entrada inválida não é mostrada no campo de formulário. Isso ocorre porque a propriedade do modelo foi definida como nulo ou um valor padrão. A entrada inválida aparece em uma mensagem de erro. Porém, se você quiser exibir novamente os dados inválidos no campo de formulário, considere tornar a propriedade do modelo uma cadeia de caracteres e fazer a conversão de dados manualmente.

A mesma estratégia será recomendada se você não desejar que erros de conversão de tipo resultem em erros de estado de modelo. Nesse caso, torne a propriedade de modelo uma cadeia de caracteres.

## <a name="simple-types"></a>Tipos simples

Os tipos simples em que o associador de modelos pode converter cadeias de caracteres de origem incluem os seguintes:

* [Booliano](xref:System.ComponentModel.BooleanConverter)
* [Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)
* [Char](xref:System.ComponentModel.CharConverter)
* [DateTime](xref:System.ComponentModel.DateTimeConverter)
* [DateTimeOffset](xref:System.ComponentModel.DateTimeOffsetConverter)
* [Decimal](xref:System.ComponentModel.DecimalConverter)
* [Double](xref:System.ComponentModel.DoubleConverter)
* [Enum](xref:System.ComponentModel.EnumConverter)
* [Guid](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)
* [Single](xref:System.ComponentModel.SingleConverter)
* [TimeSpan](xref:System.ComponentModel.TimeSpanConverter)
* [UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)
* [Uri](xref:System.UriTypeConverter)
* [Versão](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a>Tipos complexos

Um tipo complexo deve ter um construtor padrão público e propriedades públicas graváveis para associar. Quando ocorre model binding, a classe é instanciada usando o construtor padrão público. 

Para cada propriedade do tipo complexo, o model binding examina as fontes em busca do nome padrão *prefix.property_name*. Se nada for encontrado, ela procurará apenas *property_name* sem o prefixo.

Para associação a um parâmetro, o prefixo é o nome do parâmetro. Para associação a uma propriedade pública `PageModel`, o prefixo é o nome da propriedade pública. Alguns atributos têm uma propriedade `Prefix` que permite substituir o uso padrão do nome da propriedade ou do parâmetro.

Por exemplo, suponha que o tipo complexo seja a seguinte classe `Instructor`:

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a>Prefixo = nome do parâmetro

Se o modelo a ser associado for um parâmetro chamado `instructorToUpdate`:

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

O model binding começa examinando as fontes para a chave `instructorToUpdate.ID`. Se ela não for encontrada, procurará `ID` sem um prefixo.

### <a name="prefix--property-name"></a>Prefixo = nome da propriedade

Se o modelo a ser associado for uma propriedade chamada `Instructor` do controlador ou da classe `PageModel`:

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

O model binding começa examinando as fontes para a chave `Instructor.ID`. Se ela não for encontrada, procurará `ID` sem um prefixo.

### <a name="custom-prefix"></a>Prefixo personalizado

Se o modelo a ser associado for um parâmetro denominado `instructorToUpdate` e um atributo `Bind` especificar `Instructor` como o prefixo:

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

O model binding começa examinando as fontes para a chave `Instructor.ID`. Se ela não for encontrada, procurará `ID` sem um prefixo.

### <a name="attributes-for-complex-type-targets"></a>Atributos para destinos de tipo complexo

Vários atributos internos estão disponíveis para controlar o model binding de tipos complexos:

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> Esses atributos afetam o model binding quando os dados de formulário postados são a origem dos valores. Eles não afetam os formatadores de entrada, que processam corpos de solicitação XML e JSON postados. O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).
>
> Veja também a discussão sobre o atributo `[Required]` em [Validação do modelo](xref:mvc/models/validation#required-attribute).

### <a name="bindrequired-attribute"></a>Atributo [BindRequired]

Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método. Faz com que o model binding adicione um erro de estado de modelo se a associação não puder ocorrer para a propriedade de um modelo. Aqui está um exemplo:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a>Atributo [BindNever]

Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método. Impede que o model binding configure a propriedade de um modelo. Aqui está um exemplo:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a>Atributo [Bind]

Pode ser aplicado a uma classe ou a um parâmetro de método. Especifica quais propriedades de um modelo devem ser incluídas no model binding.

No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando qualquer método de ação ou o manipulador é chamado:

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando o método `OnPost` é chamado:

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

O atributo `[Bind]` pode ser usado para proteção contra o excesso de postagem cenários de *criar*. Ele não funciona bem em cenários de edição, pois as propriedades excluídas são definidas como nulas ou um valor padrão, em vez de serem deixadas inalteradas. Para defesa contra o excesso de postagem, são recomendados modelos de exibição, em vez do atributo `[Bind]`. Para obter mais informações, veja [Observação de segurança sobre o excesso de postagem](xref:data/ef-mvc/crud#security-note-about-overposting).

## <a name="collections"></a>Coleções

Para destinos que são coleções de tipos simples, o model binding procura correspondências para *parameter_name* ou *property_name*. Se nenhuma correspondência for encontrada, procurará um dos formatos compatível sem o prefixo. Por exemplo:

* Suponha que o parâmetro a ser associado seja uma matriz chamada `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* Dados de cadeia de caracteres de consulta ou formulário podem estar em um dos seguintes formatos:
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* O formato a seguir é compatível apenas com os dados de formulário:

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* Para todos os formatos do exemplo anterior, o model binding passa uma matriz de dois itens para o parâmetro `selectedCourses`:

  * selectedCourses[0]=1050
  * selectedCourses[1]=2000

  Formatos de dados que usam números subscritos (... [0]... [1]...) devem garantir que eles estejam numerados em sequência, começando com zero. Se houver quaisquer intervalos na numeração de subscrito, todos os itens após o intervalo serão ignorados. Por exemplo, se os subscritos forem 0 e 2, em vez de 0 e 1, o segundo item será ignorado.

## <a name="dictionaries"></a>Dicionários

Para destinos `Dictionary`, o model binding procura correspondências para *parameter_name* ou *property_name*. Se nenhuma correspondência for encontrada, procurará um dos formatos compatível sem o prefixo. Por exemplo:

* Suponha que o parâmetro de destino seja um `Dictionary<int, string>` chamado `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* Os dados de cadeia de caracteres de consulta ou formulário postados podem se parecer com um dos exemplos a seguir:

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* Para todos os formatos do exemplo anterior, o model binding passa um dicionário de dois itens para o parâmetro `selectedCourses`:

  * selectedCourses["1050"]="Chemistry"
  * selectedCourses["2000"]="Economics"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a>Comportamento de globalização de dados de rota de associação de modelo e cadeias de consulta

O provedor de valor de rota ASP.NET Core e o provedor de valor de cadeia de consulta:

* Tratar valores como cultura invariável.
* Espere que as URLs sejam invariáveis de cultura.

Por outro lado, os valores provenientes de dados de formulário passam por uma conversão sensível à cultura. Isso ocorre por design para que as URLs sejam compartilháveis entre as localidades.

Para tornar o provedor de valor de rota ASP.NET Core e o provedor de valor da cadeia de consulta passam por uma conversão sensível à cultura:

* Herdam de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>
* Copie o código de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)
* Substitua o [valor de cultura](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passado para o construtor do provedor de valor por [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)
* Substitua o alocador de provedor de valor padrão nas opções do MVC por seu novo:

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a>Tipos de dados especiais

Há alguns tipos de dados especiais com o model binding pode lidar.

### <a name="iformfile-and-iformfilecollection"></a>IFormFile e IFormFileCollection

Um arquivo carregado incluído na solicitação HTTP.  Também compatível com `IEnumerable<IFormFile>` para vários arquivos.

### <a name="cancellationtoken"></a>CancellationToken

usado para cancelar a atividade em controladores assíncronos.

### <a name="formcollection"></a>FormCollection

Usado para recuperar todos os valores dos dados de formulário postados.

## <a name="input-formatters"></a>Formatadores de entrada

Dados no corpo da solicitação podem estar em JSON, XML ou algum outro formato. Para analisar esses dados, o model binding usa um *formatador de entrada* configurado para lidar com um determinado tipo de conteúdo. Por padrão, o ASP.NET Core inclui formatadores de entrada baseados em JSON para lidar com os dados JSON. Você pode adicionar outros formatadores para outros tipos de conteúdo.

O ASP.NET Core seleciona formatadores de entrada com base no atributo [Consome](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute). Se nenhum atributo estiver presente, ele usará o [cabeçalho Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).

Para usar os formatadores de entrada XML internos:

* Instale o pacote do NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.

* Em `Startup.ConfigureServices`, chame <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* Aplique o atributo `Consumes` às classes de controlador ou aos métodos de ação que devem esperar XML no corpo da solicitação.

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  Para obter mais informações, veja [Introdução à serialização XML](/dotnet/standard/serialization/introducing-xml-serialization).

### <a name="customize-model-binding-with-input-formatters"></a>Personalizar a associação de modelo com formatadores de entrada

Um formatador de entrada assume total responsabilidade pela leitura de dados do corpo da solicitação. Para personalizar esse processo, configure as APIs usadas pelo formatador de entrada. Esta seção descreve como personalizar o formatador de entrada baseado em `System.Text.Json`para entender um tipo personalizado chamado `ObjectId`. 

Considere o modelo a seguir, que contém uma propriedade de `ObjectId` personalizada chamada `Id`:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

Para personalizar o processo de associação de modelo ao usar `System.Text.Json`, crie uma classe derivada de <xref:System.Text.Json.Serialization.JsonConverter%601>:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

Para usar um conversor personalizado, aplique o atributo <xref:System.Text.Json.Serialization.JsonConverterAttribute> ao tipo. No exemplo a seguir, o tipo de `ObjectId` é configurado com `ObjectIdConverter` como seu conversor personalizado:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

Para obter mais informações, consulte [como escrever conversores personalizados](/dotnet/standard/serialization/system-text-json-converters-how-to).

## <a name="exclude-specified-types-from-model-binding"></a>Excluir tipos especificados do model binding

O comportamento do sistema de validação e model binding é orientado pelo [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata). Você pode personalizar `ModelMetadata` adicionando um provedor de detalhes [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders). Provedores de detalhes internos estão disponíveis para desabilitar o model binding ou a validação para tipos especificados.

Para desabilitar o model binding em todos os modelos de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> em `Startup.ConfigureServices`. Por exemplo, para desabilitar o model binding em todos os modelos do tipo `System.Version`:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

Para desabilitar a validação nas propriedades de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> em `Startup.ConfigureServices`. Por exemplo, para desabilitar a validação nas propriedades do tipo `System.Guid`:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a>Associadores de modelos personalizados

Você pode estender o model binding escrevendo um associador de modelos personalizado e usando o atributo `[ModelBinder]` para selecioná-la para um determinado destino. Saiba mais sobre o [model binding personalizado](xref:mvc/advanced/custom-model-binding).

## <a name="manual-model-binding"></a>Model binding manual 

O model binding pode ser invocado manualmente usando o método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>. O método é definido nas classes `ControllerBase` e `PageModel`. Sobrecargas de método permitem que você especifique o provedor de valor e prefixo a ser usado. O método retornará `false` se o model binding falhar. Aqui está um exemplo:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> usa provedores de valor para obter dados do corpo do formulário, Cadeia de caracteres de consulta e dados de rota. `TryUpdateModelAsync` normalmente é: 

* Usado com os aplicativos Razor Pages e MVC usando controladores e exibições para evitar o excesso de postagens.
* Não usado com uma API da Web, a menos que consumido de dados de formulário, cadeias de caracteres de consulta e dados de rota. Os pontos de extremidade da API Web que consomem JSON usam [formatadores de entrada](#input-formatters) para desserializar o corpo da solicitação em um objeto.

Para obter mais informações, consulte [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).

## <a name="fromservices-attribute"></a>Atributo [FromServices]

O nome do atributo segue o padrão dos atributos de model binding que especificam uma fonte de dados. Porém, não se trata de associar dados de um provedor de valor. Ele obtém uma instância de um tipo do contêiner de [injeção de dependência](xref:fundamentals/dependency-injection). Sua finalidade é oferecer uma alternativa à injeção de construtor para quando você precisa de um serviço somente se um determinado método for chamado.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

Este artigo explica o que é model binding, como ele funciona e como personalizar seu comportamento.

[Exibir ou baixar um código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([como baixar](xref:index#how-to-download-a-sample)).

## <a name="what-is-model-binding"></a>O que é o model binding

Controladores e Razor Pages funcionam com os dados provenientes de solicitações HTTP. Por exemplo, dados de rota podem fornecer uma chave de registro e campos de formulário postados podem fornecer valores para as propriedades do modelo. Escrever código para recuperar cada um desses valores e convertê-los de cadeias de caracteres em tipos .NET seria uma tarefa entediante e propensa a erro. O model binding automatiza esse processo. O sistema de model binding:

* Recupera dados de várias fontes, como dados de rota, campos de formulário e cadeias de caracteres de consulta.
* Fornece os dados a controladores e Razor Pages em parâmetros de método e propriedades públicas.
* Converte dados de cadeia de caracteres em tipos .NET.
* Atualiza as propriedades de tipos complexos.

## <a name="example"></a>{1&gt;Exemplo&lt;1}

Suponha que você tenha o seguinte método de ação:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

E o aplicativo receba uma solicitação com esta URL:

```
http://contoso.com/api/pets/2?DogsOnly=true
```

A associação de modelo passa pelas seguintes etapas depois que o sistema de roteamento seleciona o método de ação:

* Localiza o primeiro parâmetro de `GetByID`, um número inteiro denominado `id`.
* Examina as fontes disponíveis na solicitação HTTP e localiza `id` = "2" em dados de rota.
* Converte a cadeia de caracteres "2" em inteiro 2.
* Localiza o próximo parâmetro de `GetByID`, um booliano chamado `dogsOnly`.
* Examina as fontes e localiza "DogsOnly=true" na cadeia de consulta. A correspondência de nomes não diferencia maiúsculas de minúsculas.
* Converte a cadeia de caracteres "true" no booliano `true`.

A estrutura então chama o método `GetById`, passando 2 para o parâmetro `id` e `true` para o parâmetro `dogsOnly`.

No exemplo anterior, os destinos do model binding são parâmetros de método que são tipos simples. Destinos também podem ser as propriedades de um tipo complexo. Depois de cada propriedade ser associada com êxito, a [validação do modelo](xref:mvc/models/validation) ocorre para essa propriedade. O registro de quais dados estão associados ao modelo, além de quaisquer erros de validação ou de associação, é armazenado em [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) ou [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState). Para descobrir se esse processo foi bem-sucedido, o aplicativo verifica o sinalizador [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).

## <a name="targets"></a>Destinos

O model binding tenta encontrar valores para os seguintes tipos de destinos:

* Parâmetros do método de ação do controlador para o qual uma solicitação é roteada.
* Os parâmetros do método do manipulador do Razor Pages para o qual uma solicitação é roteada. 
* Propriedades públicas de um controlador ou classe `PageModel`, se especificadas por atributos.

### <a name="bindproperty-attribute"></a>Atributo [BindProperty]

Pode ser aplicado a uma propriedade pública de um controlador ou classe `PageModel` para fazer o model binding ter essa propriedade como destino:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a>Atributo [BindProperties]

Disponível no ASP.NET Core 2.1 e posteriores.  Pode ser aplicado a um controlador ou classe `PageModel` para informar o model binding para ter todas as propriedades públicas da classe como destino:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>Model binding para solicitações HTTP GET

Por padrão, as propriedades não são vinculadas para solicitações HTTP GET. Normalmente, tudo o que você precisa para uma solicitação GET é um parâmetro de ID de registro. A ID do registro é usada para pesquisar o item no banco de dados. Portanto, não é necessário associar uma propriedade que contém uma instância do modelo. Em cenários em que você deseja associar propriedades a dados de solicitações GET, defina a propriedade `SupportsGet` como `true`:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>Fontes

Por padrão, o model binding obtém dados na forma de pares chave-valor das seguintes fontes em uma solicitação HTTP:

1. Campos de formulário
1. O corpo da solicitação (para [controladores que têm o atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).
1. Rotear dados
1. Consultar parâmetros de cadeia de caracteres
1. Arquivos carregados

Para cada parâmetro ou propriedade de destino, as fontes são verificadas na ordem indicada na lista anterior. Há algumas exceções:

* Os valores de cadeia de caracteres de consulta e dados de rota são usados apenas para tipos simples.
* Arquivos carregados são associados apenas a tipos de destino que implementam `IFormFile` ou `IEnumerable<IFormFile>`.

Se a origem padrão não estiver correta, use um dos seguintes atributos para especificar a origem:

* [`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -obtém valores da cadeia de caracteres de consulta. 
* [`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -obtém valores de dados de rota.
* [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -obtém valores dos campos de formulário postados.
* [`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -obtém valores do corpo da solicitação.
* [`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -obtém valores de cabeçalhos HTTP.

Esses atributos:

* São adicionados às propriedades de modelo individualmente (não à classe de modelo), como no exemplo a seguir:

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* Opcionalmente, aceite um valor de nome de modelo no construtor. Essa opção é fornecida caso o nome da propriedade não corresponda ao valor na solicitação. Por exemplo, o valor na solicitação pode ser um cabeçalho com um hífen em seu nome, como no exemplo a seguir:

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>Atributo [FromBody]

Aplique o atributo `[FromBody]` a um parâmetro para popular suas propriedades do corpo de uma solicitação HTTP. O tempo de execução de ASP.NET Core delega a responsabilidade de ler o corpo para um formatador de entrada. O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).

Quando `[FromBody]` é aplicado a um parâmetro de tipo complexo, todos os atributos de origem de associação aplicados às suas propriedades são ignorados. Por exemplo, a ação a seguir `Create` especifica que seu parâmetro `pet` é populado a partir do corpo:

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

A classe `Pet` especifica que sua propriedade `Breed` é populada a partir de um parâmetro de cadeia de caracteres de consulta:

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

No exemplo anterior:

* O atributo `[FromQuery]` é ignorado.
* A propriedade `Breed` não é preenchida a partir de um parâmetro de cadeia de caracteres de consulta. 

Os formatadores de entrada lêem apenas o corpo e não entendem os atributos de origem da associação. Se um valor adequado for encontrado no corpo, esse valor será usado para popular a propriedade `Breed`.

Não aplique `[FromBody]` a mais de um parâmetro por método de ação. Depois que o fluxo de solicitação é lido por um formatador de entrada, ele não está mais disponível para ser lido novamente para associar outros parâmetros de `[FromBody]`.

### <a name="additional-sources"></a>Fontes adicionais

Os dados de origem são fornecidos ao sistema de model binding pelos *provedores de valor*. Você pode escrever e registrar provedores de valor personalizados que obtêm dados para model binding de outras fontes. Por exemplo, talvez você queira dados de cookies ou estado de sessão. Para obter dados de uma nova fonte:

* Crie uma classe que implementa `IValueProvider`.
* Crie uma classe que implementa `IValueProviderFactory`.
* Registre a classe de alocador em `Startup.ConfigureServices`.

O aplicativo de exemplo inclui um [provedor de valor](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) e um exemplo de [alocador](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) que obtém valores de cookies. Aqui está o código de registro em `Startup.ConfigureServices`:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

O código mostrado coloca o provedor de valor personalizado depois de todos os provedores de valor internos.  Para torná-lo o primeiro na lista, chame `Insert(0, new CookieValueProviderFactory())`, em vez de `Add`.

## <a name="no-source-for-a-model-property"></a>Nenhuma fonte para uma propriedade de modelo

Por padrão, um erro de estado de modelo não será criado se nenhum valor for encontrado para uma propriedade de modelo. A propriedade é definida como null ou um valor padrão:

* Tipos simples anuláveis definidos como `null`.
* Tipos de valor não anuláveis são definidos como `default(T)`. Por exemplo, um parâmetro `int id` é definido como 0.
* Para tipos complexos, o model binding cria uma instância usando o construtor padrão sem definir propriedades.
* As matrizes são definidas como `Array.Empty<T>()`, exceto que matrizes `byte[]` são definidas como `null`.

Se o estado do modelo deve ser invalidado quando nada for encontrado em campos de formulário para uma propriedade de modelo, use o atributo [`[BindRequired]`](#bindrequired-attribute) .

Observe que este comportamento `[BindRequired]` se aplica ao model binding de dados de formulário postados, não a dados JSON ou XML em um corpo da solicitação. Dados do corpo da solicitação são tratados pelos [formatadores de entrada](#input-formatters).

## <a name="type-conversion-errors"></a>Erros de conversão de tipos

Se uma fonte for encontrada, mas não puder ser convertida no tipo de destino, o estado do modelo será sinalizado como inválido. O parâmetro ou a propriedade de destino é definido como nulo ou um valor padrão, conforme observado na seção anterior.

Em um controlador de API que tem o atributo `[ApiController]`, um estado de modelo inválido resulta em uma resposta HTTP 400 automática.

Em uma Razor Page, exiba novamente a página com uma mensagem de erro:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

Validação no lado do cliente captura a maioria dos dados inválidos que de outra forma seriam enviados a um formulário do Razor Pages. Essa validação torna difícil disparar o código realçado anterior. O aplicativo de exemplo inclui um botão **Enviar com Data Inválida** que coloca os dados inválidos no campo **Data de Contratação** e envia o formulário. Esse botão mostra como o código para exibir novamente a página funciona quando ocorrem erros de conversão de dados.

Quando a página é exibida novamente pelo código anterior, a entrada inválida não é mostrada no campo de formulário. Isso ocorre porque a propriedade do modelo foi definida como nulo ou um valor padrão. A entrada inválida aparece em uma mensagem de erro. Porém, se você quiser exibir novamente os dados inválidos no campo de formulário, considere tornar a propriedade do modelo uma cadeia de caracteres e fazer a conversão de dados manualmente.

A mesma estratégia será recomendada se você não desejar que erros de conversão de tipo resultem em erros de estado de modelo. Nesse caso, torne a propriedade de modelo uma cadeia de caracteres.

## <a name="simple-types"></a>Tipos simples

Os tipos simples em que o associador de modelos pode converter cadeias de caracteres de origem incluem os seguintes:

* [Booliano](xref:System.ComponentModel.BooleanConverter)
* [Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)
* [Char](xref:System.ComponentModel.CharConverter)
* [DateTime](xref:System.ComponentModel.DateTimeConverter)
* [DateTimeOffset](xref:System.ComponentModel.DateTimeOffsetConverter)
* [Decimal](xref:System.ComponentModel.DecimalConverter)
* [Double](xref:System.ComponentModel.DoubleConverter)
* [Enum](xref:System.ComponentModel.EnumConverter)
* [Guid](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)
* [Single](xref:System.ComponentModel.SingleConverter)
* [TimeSpan](xref:System.ComponentModel.TimeSpanConverter)
* [UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)
* [Uri](xref:System.UriTypeConverter)
* [Versão](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a>Tipos complexos

Um tipo complexo deve ter um construtor padrão público e propriedades públicas graváveis para associar. Quando ocorre model binding, a classe é instanciada usando o construtor padrão público. 

Para cada propriedade do tipo complexo, o model binding examina as fontes em busca do nome padrão *prefix.property_name*. Se nada for encontrado, ela procurará apenas *property_name* sem o prefixo.

Para associação a um parâmetro, o prefixo é o nome do parâmetro. Para associação a uma propriedade pública `PageModel`, o prefixo é o nome da propriedade pública. Alguns atributos têm uma propriedade `Prefix` que permite substituir o uso padrão do nome da propriedade ou do parâmetro.

Por exemplo, suponha que o tipo complexo seja a seguinte classe `Instructor`:

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a>Prefixo = nome do parâmetro

Se o modelo a ser associado for um parâmetro chamado `instructorToUpdate`:

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

O model binding começa examinando as fontes para a chave `instructorToUpdate.ID`. Se ela não for encontrada, procurará `ID` sem um prefixo.

### <a name="prefix--property-name"></a>Prefixo = nome da propriedade

Se o modelo a ser associado for uma propriedade chamada `Instructor` do controlador ou da classe `PageModel`:

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

O model binding começa examinando as fontes para a chave `Instructor.ID`. Se ela não for encontrada, procurará `ID` sem um prefixo.

### <a name="custom-prefix"></a>Prefixo personalizado

Se o modelo a ser associado for um parâmetro denominado `instructorToUpdate` e um atributo `Bind` especificar `Instructor` como o prefixo:

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

O model binding começa examinando as fontes para a chave `Instructor.ID`. Se ela não for encontrada, procurará `ID` sem um prefixo.

### <a name="attributes-for-complex-type-targets"></a>Atributos para destinos de tipo complexo

Vários atributos internos estão disponíveis para controlar o model binding de tipos complexos:

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> Esses atributos afetam o model binding quando os dados de formulário postados são a origem dos valores. Eles não afetam os formatadores de entrada, que processam corpos de solicitação XML e JSON postados. O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).
>
> Veja também a discussão sobre o atributo `[Required]` em [Validação do modelo](xref:mvc/models/validation#required-attribute).

### <a name="bindrequired-attribute"></a>Atributo [BindRequired]

Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método. Faz com que o model binding adicione um erro de estado de modelo se a associação não puder ocorrer para a propriedade de um modelo. Aqui está um exemplo:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a>Atributo [BindNever]

Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método. Impede que o model binding configure a propriedade de um modelo. Aqui está um exemplo:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a>Atributo [Bind]

Pode ser aplicado a uma classe ou a um parâmetro de método. Especifica quais propriedades de um modelo devem ser incluídas no model binding.

No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando qualquer método de ação ou o manipulador é chamado:

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

No exemplo a seguir, somente as propriedades especificadas do modelo `Instructor` são associadas quando o método `OnPost` é chamado:

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

O atributo `[Bind]` pode ser usado para proteção contra o excesso de postagem cenários de *criar*. Ele não funciona bem em cenários de edição, pois as propriedades excluídas são definidas como nulas ou um valor padrão, em vez de serem deixadas inalteradas. Para defesa contra o excesso de postagem, são recomendados modelos de exibição, em vez do atributo `[Bind]`. Para obter mais informações, veja [Observação de segurança sobre o excesso de postagem](xref:data/ef-mvc/crud#security-note-about-overposting).

## <a name="collections"></a>Coleções

Para destinos que são coleções de tipos simples, o model binding procura correspondências para *parameter_name* ou *property_name*. Se nenhuma correspondência for encontrada, procurará um dos formatos compatível sem o prefixo. Por exemplo:

* Suponha que o parâmetro a ser associado seja uma matriz chamada `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* Dados de cadeia de caracteres de consulta ou formulário podem estar em um dos seguintes formatos:
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* O formato a seguir é compatível apenas com os dados de formulário:

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* Para todos os formatos do exemplo anterior, o model binding passa uma matriz de dois itens para o parâmetro `selectedCourses`:

  * selectedCourses[0]=1050
  * selectedCourses[1]=2000

  Formatos de dados que usam números subscritos (... [0]... [1]...) devem garantir que eles estejam numerados em sequência, começando com zero. Se houver quaisquer intervalos na numeração de subscrito, todos os itens após o intervalo serão ignorados. Por exemplo, se os subscritos forem 0 e 2, em vez de 0 e 1, o segundo item será ignorado.

## <a name="dictionaries"></a>Dicionários

Para destinos `Dictionary`, o model binding procura correspondências para *parameter_name* ou *property_name*. Se nenhuma correspondência for encontrada, procurará um dos formatos compatível sem o prefixo. Por exemplo:

* Suponha que o parâmetro de destino seja um `Dictionary<int, string>` chamado `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* Os dados de cadeia de caracteres de consulta ou formulário postados podem se parecer com um dos exemplos a seguir:

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* Para todos os formatos do exemplo anterior, o model binding passa um dicionário de dois itens para o parâmetro `selectedCourses`:

  * selectedCourses["1050"]="Chemistry"
  * selectedCourses["2000"]="Economics"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a>Comportamento de globalização de dados de rota de associação de modelo e cadeias de consulta

O provedor de valor de rota ASP.NET Core e o provedor de valor de cadeia de consulta:

* Tratar valores como cultura invariável.
* Espere que as URLs sejam invariáveis de cultura.

Por outro lado, os valores provenientes de dados de formulário passam por uma conversão sensível à cultura. Isso ocorre por design para que as URLs sejam compartilháveis entre as localidades.

Para tornar o provedor de valor de rota ASP.NET Core e o provedor de valor da cadeia de consulta passam por uma conversão sensível à cultura:

* Herdam de <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>
* Copie o código de [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) ou [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)
* Substitua o [valor de cultura](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passado para o construtor do provedor de valor por [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)
* Substitua o alocador de provedor de valor padrão nas opções do MVC por seu novo:

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a>Tipos de dados especiais

Há alguns tipos de dados especiais com o model binding pode lidar.

### <a name="iformfile-and-iformfilecollection"></a>IFormFile e IFormFileCollection

Um arquivo carregado incluído na solicitação HTTP.  Também compatível com `IEnumerable<IFormFile>` para vários arquivos.

### <a name="cancellationtoken"></a>CancellationToken

usado para cancelar a atividade em controladores assíncronos.

### <a name="formcollection"></a>FormCollection

Usado para recuperar todos os valores dos dados de formulário postados.

## <a name="input-formatters"></a>Formatadores de entrada

Dados no corpo da solicitação podem estar em JSON, XML ou algum outro formato. Para analisar esses dados, o model binding usa um *formatador de entrada* configurado para lidar com um determinado tipo de conteúdo. Por padrão, o ASP.NET Core inclui formatadores de entrada baseados em JSON para lidar com os dados JSON. Você pode adicionar outros formatadores para outros tipos de conteúdo.

O ASP.NET Core seleciona formatadores de entrada com base no atributo [Consome](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute). Se nenhum atributo estiver presente, ele usará o [cabeçalho Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).

Para usar os formatadores de entrada XML internos:

* Instale o pacote do NuGet `Microsoft.AspNetCore.Mvc.Formatters.Xml`.

* Em `Startup.ConfigureServices`, chame <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> ou <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* Aplique o atributo `Consumes` às classes de controlador ou aos métodos de ação que devem esperar XML no corpo da solicitação.

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  Para obter mais informações, veja [Introdução à serialização XML](/dotnet/standard/serialization/introducing-xml-serialization).

## <a name="exclude-specified-types-from-model-binding"></a>Excluir tipos especificados do model binding

O comportamento do sistema de validação e model binding é orientado pelo [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata). Você pode personalizar `ModelMetadata` adicionando um provedor de detalhes [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders). Provedores de detalhes internos estão disponíveis para desabilitar o model binding ou a validação para tipos especificados.

Para desabilitar o model binding em todos os modelos de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> em `Startup.ConfigureServices`. Por exemplo, para desabilitar o model binding em todos os modelos do tipo `System.Version`:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

Para desabilitar a validação nas propriedades de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> em `Startup.ConfigureServices`. Por exemplo, para desabilitar a validação nas propriedades do tipo `System.Guid`:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a>Associadores de modelos personalizados

Você pode estender o model binding escrevendo um associador de modelos personalizado e usando o atributo `[ModelBinder]` para selecioná-la para um determinado destino. Saiba mais sobre o [model binding personalizado](xref:mvc/advanced/custom-model-binding).

## <a name="manual-model-binding"></a>Model binding manual

O model binding pode ser invocado manualmente usando o método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>. O método é definido nas classes `ControllerBase` e `PageModel`. Sobrecargas de método permitem que você especifique o provedor de valor e prefixo a ser usado. O método retornará `false` se o model binding falhar. Aqui está um exemplo:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a>Atributo [FromServices]

O nome do atributo segue o padrão dos atributos de model binding que especificam uma fonte de dados. Porém, não se trata de associar dados de um provedor de valor. Ele obtém uma instância de um tipo do contêiner de [injeção de dependência](xref:fundamentals/dependency-injection). Sua finalidade é oferecer uma alternativa à injeção de construtor para quando você precisa de um serviço somente se um determinado método for chamado.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
