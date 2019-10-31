---
title: Model binding no ASP.NET Core
author: rick-anderson
description: Saiba como funciona o model binding no ASP.NET Core e como personalizar seu comportamento.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 05/31/2019
uid: mvc/models/model-binding
ms.openlocfilehash: aeb2da7e11df1eab5a17e2ae0a3971420c9383b4
ms.sourcegitcommit: 032113208bb55ecfb2faeb6d3e9ea44eea827950
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73190599"
---
# <a name="model-binding-in-aspnet-core"></a>Model binding no ASP.NET Core

Este artigo explica o que é model binding, como ele funciona e como personalizar seu comportamento.

[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([como baixar](xref:index#how-to-download-a-sample)).

## <a name="what-is-model-binding"></a>O que é o model binding

Controladores e Razor Pages funcionam com os dados provenientes de solicitações HTTP. Por exemplo, dados de rota podem fornecer uma chave de registro e campos de formulário postados podem fornecer valores para as propriedades do modelo. Escrever código para recuperar cada um desses valores e convertê-los de cadeias de caracteres em tipos .NET seria uma tarefa entediante e propensa a erro. O model binding automatiza esse processo. O sistema de model binding:

* Recupera dados de várias fontes, como dados de rota, campos de formulário e cadeias de caracteres de consulta.
* Fornece os dados a controladores e Razor Pages em parâmetros de método e propriedades públicas.
* Converte dados de cadeia de caracteres em tipos .NET.
* Atualiza as propriedades de tipos complexos.

## <a name="example"></a>Exemplo

Suponha que você tenha o seguinte método de ação:

[!code-csharp[](model-binding/samples/2.x/Controllers/PetsController.cs?name=snippet_DogsOnly)]

E o aplicativo receba uma solicitação com esta URL:

```
http://contoso.com/api/pets/2?DogsOnly=true
```

O model binding passa pelas etapas a seguir depois que o sistema de roteamento seleciona o método de ação:

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

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=7-8)]

### <a name="bindpropertiesattribute"></a>Atributo [BindProperties]

Disponível no ASP.NET Core 2.1 e posteriores.  Pode ser aplicado a um controlador ou classe `PageModel` para informar o model binding para ter todas as propriedades públicas da classe como destino:

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>Model binding para solicitações HTTP GET

Por padrão, as propriedades não são vinculadas para solicitações HTTP GET. Normalmente, tudo o que você precisa para uma solicitação GET é um parâmetro de ID de registro. A ID do registro é usada para pesquisar o item no banco de dados. Portanto, não é necessário associar uma propriedade que contém uma instância do modelo. Em cenários em que você deseja associar propriedades a dados de solicitações GET, defina a propriedade `SupportsGet` como `true`:

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>Origens

Por padrão, o model binding obtém dados na forma de pares chave-valor das seguintes fontes em uma solicitação HTTP:

1. Campos de formulário 
1. O corpo da solicitação (para [controladores que têm o atributo [ApiController]](xref:web-api/index#binding-source-parameter-inference)).
1. Dados de rota
1. Parâmetros de cadeia de caracteres de consulta
1. Arquivos carregados 

Para cada propriedade ou parâmetro de destino, as fontes são verificadas na ordem indicada nesta lista. Há algumas exceções:

* Os valores de cadeia de caracteres de consulta e dados de rota são usados apenas para tipos simples.
* Arquivos carregados são associados apenas a tipos de destino que implementam `IFormFile` ou `IEnumerable<IFormFile>`.

Se o comportamento padrão não obtiver os resultados certos, você poderá usar um dos seguintes atributos para especificar a origem a ser usada para qualquer determinado destino. 

* [[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) – obtém valores da cadeia de caracteres de consulta. 
* [[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) – obtém valores dos dados de rota.
* [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) – obtém valores de campos de formulário postados.
* [[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) – obtém os valores do corpo da solicitação.
* [[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) – obtém valores de cabeçalhos HTTP.

Esses atributos:

* São adicionados às propriedades de modelo individualmente (não à classe de modelo), como no exemplo a seguir:

  [!code-csharp[](model-binding/samples/2.x/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* Opcionalmente, aceite um valor de nome de modelo no construtor. Essa opção é fornecida caso o nome da propriedade não corresponda ao valor na solicitação. Por exemplo, o valor na solicitação pode ser um cabeçalho com um hífen em seu nome, como no exemplo a seguir:

  [!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>Atributo [FromBody]

Os dados do corpo da solicitação são analisados usando formatadores de entrada específicos do tipo de conteúdo da solicitação. O formatadores de entrada são explicados [posteriormente neste artigo](#input-formatters).

Não aplique `[FromBody]` a mais de um parâmetro por método de ação. O runtime do ASP.NET Core delega a responsabilidade de ler o fluxo da solicitação ao formatador de entrada. Depois que o fluxo da solicitação é lido, ele não fica mais disponível para ser lido novamente para associar outros parâmetros `[FromBody]`.

### <a name="additional-sources"></a>Fontes adicionais

Os dados de origem são fornecidos ao sistema de model binding pelos *provedores de valor*. Você pode escrever e registrar provedores de valor personalizados que obtêm dados para model binding de outras fontes. Por exemplo, talvez você queira dados de cookies ou estado de sessão. Para obter dados de uma nova fonte:

* Crie uma classe que implementa `IValueProvider`.
* Crie uma classe que implementa `IValueProviderFactory`.
* Registre a classe de alocador em `Startup.ConfigureServices`.

O aplicativo de exemplo inclui um [provedor de valor](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProvider.cs) e um exemplo de [alocador](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/CookieValueProviderFactory.cs) que obtém valores de cookies. Aqui está o código de registro em `Startup.ConfigureServices`:

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=3)]

O código mostrado coloca o provedor de valor personalizado depois de todos os provedores de valor internos.  Para torná-lo o primeiro na lista, chame `Insert(0, new CookieValueProviderFactory())`, em vez de `Add`.

## <a name="no-source-for-a-model-property"></a>Nenhuma fonte para uma propriedade de modelo

Por padrão, um erro de estado de modelo não será criado se nenhum valor for encontrado para uma propriedade de modelo. A propriedade é definida como null ou um valor padrão:

* Tipos simples anuláveis definidos como `null`.
* Tipos de valor não anuláveis são definidos como `default(T)`. Por exemplo, um parâmetro `int id` é definido como 0.
* Para tipos complexos, o model binding cria uma instância usando o construtor padrão sem definir propriedades.
* As matrizes são definidas como `Array.Empty<T>()`, exceto que matrizes `byte[]` são definidas como `null`.

Se o estado do modelo precisar ser invalidado quando nada for encontrado em campos de formulário para uma propriedade de modelo, use o [atributo [BindRequired]](#bindrequired-attribute).

Observe que este comportamento `[BindRequired]` se aplica ao model binding de dados de formulário postados, não a dados JSON ou XML em um corpo da solicitação. Dados do corpo da solicitação são tratados pelos [formatadores de entrada](#input-formatters).

## <a name="type-conversion-errors"></a>Erros de conversão de tipos

Se uma fonte for encontrada, mas não puder ser convertida no tipo de destino, o estado do modelo será sinalizado como inválido. O parâmetro ou a propriedade de destino é definido como nulo ou um valor padrão, conforme observado na seção anterior.

Em um controlador de API que tem o atributo `[ApiController]`, um estado de modelo inválido resulta em uma resposta HTTP 400 automática.

Em uma Razor Page, exiba novamente a página com uma mensagem de erro:

[!code-csharp[](model-binding/samples/2.x/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

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
* [Duplo](xref:System.ComponentModel.DoubleConverter)
* [Enum](xref:System.ComponentModel.EnumConverter)
* [Guid](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)
* [Simples](xref:System.ComponentModel.SingleConverter)
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

Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método. Faz com que o model binding adicione um erro de estado de modelo se a associação não puder ocorrer para a propriedade de um modelo. Veja um exemplo:

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a>Atributo [BindNever]

Somente pode ser aplicado às propriedades de modelo, não aos parâmetros do método. Impede que o model binding configure a propriedade de um modelo. Veja um exemplo:

[!code-csharp[](model-binding/samples/2.x/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

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

  [!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=9)]

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

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

Para desabilitar a validação nas propriedades de um tipo especificado, adicione um <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> em `Startup.ConfigureServices`. Por exemplo, para desabilitar a validação nas propriedades do tipo `System.Guid`:

[!code-csharp[](model-binding/samples/2.x/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a>Associadores de modelos personalizados

Você pode estender o model binding escrevendo um associador de modelos personalizado e usando o atributo `[ModelBinder]` para selecioná-la para um determinado destino. Saiba mais sobre o [model binding personalizado](xref:mvc/advanced/custom-model-binding).

## <a name="manual-model-binding"></a>Model binding manual

O model binding pode ser invocado manualmente usando o método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>. O método é definido nas classes `ControllerBase` e `PageModel`. Sobrecargas de método permitem que você especifique o provedor de valor e prefixo a ser usado. O método retornará `false` se o model binding falhar. Veja um exemplo:

[!code-csharp[](model-binding/samples/2.x/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a>Atributo [FromServices]

O nome do atributo segue o padrão dos atributos de model binding que especificam uma fonte de dados. Porém, não se trata de associar dados de um provedor de valor. Ele obtém uma instância de um tipo do contêiner de [injeção de dependência](xref:fundamentals/dependency-injection). Sua finalidade é oferecer uma alternativa à injeção de construtor para quando você precisa de um serviço somente se um determinado método for chamado.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>
