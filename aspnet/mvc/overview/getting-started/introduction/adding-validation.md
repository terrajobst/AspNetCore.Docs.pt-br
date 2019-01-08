---
uid: mvc/overview/getting-started/introduction/adding-validation
title: Adicionando validação | Microsoft Docs
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 01/06/2019
ms.assetid: 9f35ca15-e216-4db6-9ebf-24380b0f31b4
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-validation
msc.type: authoredcontent
ms.openlocfilehash: 6831259ce19c3747c179d6fc1b7e2095051a603b
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099020"
---
<a name="adding-validation"></a>Adicionando uma Validação
====================
por [Rick Anderson]((https://twitter.com/RickAndMSFT))

[!INCLUDE [Tutorial Note](sample/code-location.md)]

Nesta seção, você adicionará lógica de validação para o `Movie` modelo e você vai garantir que as regras de validação são impostas sempre que um usuário tenta criar ou editar um filme usando o aplicativo.

## <a name="keeping-things-dry"></a>Manter o processo DRY

Um dos princípios de design de núcleo do ASP.NET MVC é [DRY](http://en.wikipedia.org/wiki/Don't_repeat_yourself) (&quot;não Repeat Yourself&quot;). ASP.NET MVC incentiva você a especificar a funcionalidade ou comportamento somente uma vez e, em seguida, refleti-lo em qualquer lugar em um aplicativo. Isso reduz a quantidade de código que você precisa escrever e torna o código escrito seja menos propenso a erro e mais fácil de manter.

O suporte de validação fornecido pelo ASP.NET MVC e ao Entity Framework Code First é um ótimo exemplo do princípio DRY em ação. Você pode especificar regras de validação declarativamente em um único lugar (na classe de modelo) e as regras são impostas em qualquer lugar no aplicativo.

Vamos dar uma olhada em como você pode tirar proveito desse suporte de validação no aplicativo de filme.

## <a name="adding-validation-rules-to-the-movie-model"></a>Adicionando regras de validação ao modelo de filme

Você começará adicionando alguma lógica de validação para o `Movie` classe.

Abra o arquivo *Movie.cs*. Observe que o [ `System.ComponentModel.DataAnnotations` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) namespace não contém `System.Web`. DataAnnotations fornece um conjunto interno de atributos de validação que você pode aplicar declarativamente a qualquer classe ou propriedade. (Ele também contém atributos de formatação como [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) que ajudam com a formatação e não fornecem nenhuma validação.)

Atualize agora o `Movie` classe podem aproveitar o interno [ `Required` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute.aspx), [ `StringLength` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx), [RegularExpression](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx), e [ `Range` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.rangeattribute.aspx) atributos de validação. Substitua o `Movie` classe com o seguinte:

[!code-csharp[Main](adding-validation/samples/sample1.cs?highlight=5,13-15,18-19,22-23)]

O [ `StringLength` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx) atributo define o comprimento máximo da cadeia de caracteres e ele define essa limitação no banco de dados, portanto o esquema de banco de dados será alterado. Clique com botão direito do **filmes** na tabela **Gerenciador de servidores** e clique em **abrir definição de tabela**:

![](adding-validation/_static/image1.png)

Na imagem acima, você pode ver todos os campos de cadeia de caracteres são definidos como [NVARCHAR (MAX)](https://technet.microsoft.com/library/ms186939.aspx). Usaremos as migrações para atualizar o esquema. Compile a solução e, em seguida, abra o **Package Manager Console** janela e digite os seguintes comandos:

[!code-console[Main](adding-validation/samples/sample2.cmd)]

Quando esse comando for concluído, o Visual Studio abre o arquivo de classe que define o novo `DbMIgration` classe derivada com o nome especificado (`DataAnnotations`) e, no `Up` método, você pode ver o código que atualiza as restrições de esquema:

[!code-csharp[Main](adding-validation/samples/sample3.cs)]

O `Genre` campo não é anulável (ou seja, você deve inserir um valor). O `Rating` campo tem um comprimento máximo de 5 e `Title` tem um comprimento máximo de 60. O comprimento mínimo de 3 na `Title` e o intervalo em `Price` não tiver criado as alterações de esquema.

Examine o esquema de filme:

![](adding-validation/_static/image2.png)

Os campos de cadeia de caracteres mostram os novos limites de comprimento e `Genre` não está marcada como anulável.

Os atributos de validação especificam o comportamento que você deseja impor nas propriedades de modelo às quais eles são aplicados. Os atributos `Required` e `MinimumLength` indicam que uma propriedade deve ter um valor; porém, nada impede que um usuário insira um espaço em branco para atender a essa validação. O [RegularExpression](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx) atributo é usado para limitar quais caracteres podem ser de entrada. No código acima, `Genre` e `Rating` devem usar apenas letras (espaço em branco, números e caracteres especiais não são permitidos). O [ `Range` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.rangeattribute.aspx) atributo restringe um valor para dentro do intervalo especificado. O atributo `StringLength` permite definir o tamanho máximo de uma propriedade de cadeia de caracteres e, opcionalmente, seu tamanho mínimo. Tipos de valor (como `decimal, int, float, DateTime`) são inerentemente necessários e não é necessário o `Required` atributo.

Código primeiro garante que as regras de validação que você especificar em uma classe de modelo são aplicadas antes que o aplicativo salva as alterações no banco de dados. Por exemplo, o código a seguir lançará uma [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception(v=vs.103).aspx) exceção quando o `SaveChanges` método é chamado, porque vários necessário `Movie` valores de propriedade estão ausentes:

[!code-csharp[Main](adding-validation/samples/sample4.cs)]

O código acima gera a seguinte exceção:

*Falha na validação de uma ou mais entidades. Consulte a propriedade 'EntityValidationErrors' para obter mais detalhes.*

Ter as regras de validação automaticamente impostas pelo .NET Framework o ajuda a tornar seu aplicativo de mais robusta. Também garante que você não se esqueça de validar algo e inadvertidamente permita dados incorretos no banco de dados.

## <a name="validation-error-ui-in-aspnet-mvc"></a>Erro de validação de interface do usuário no ASP.NET MVC

Execute o aplicativo e navegue até a */Movies* URL.

Clique o **criar novo** link para adicionar um novo filme. Preencha o formulário com alguns valores inválidos. Assim que a validação do lado do cliente do jQuery detecta o erro, ela exibe uma mensagem de erro.

![8_validationErrors](adding-validation/_static/image3.png)

> [!NOTE]
> para dar suporte a validação do jQuery para idiomas diferentes do inglês que usam uma vírgula (",") para um ponto decimal, você deve incluir o NuGet globalizar conforme descrito anteriormente neste tutorial.


Observe como o formulário tem usado automaticamente uma cor de borda vermelha para realçar as caixas de texto que contêm dados inválidos e emitida uma mensagem de erro de validação apropriado ao lado de cada um deles. Os erros são impostos no lado do cliente (usando o JavaScript e o jQuery) e no lado do servidor (caso um usuário tenha o JavaScript desabilitado).

Um benefício real é que você não precisa alterar uma única linha de código na `MoviesController` classe ou nos *Create. cshtml* exibição para permitir essa validação da interface do usuário. O controlador e as exibições criados anteriormente neste tutorial selecionaram automaticamente as regras de validação especificadas com atributos de validação nas propriedades da classe de modelo `Movie`. Teste a validação usando o método de ação `Edit` e a mesma validação é aplicada.

Os dados de formulário não são enviados no servidor até que não haja erros de validação do lado do cliente. Você pode verificar isso colocando um ponto de interrupção no método HTTP Post, usando o [ferramenta fiddler](http://fiddler2.com/fiddler2/), ou o IE [ferramentas de desenvolvedor F12](https://msdn.microsoft.com/ie/aa740478).

## <a name="how-validation-occurs-in-the-create-view-and-create-action-method"></a>Como a validação ocorre a criar exibe e cria o método de ação

Talvez você esteja se perguntando como a interface do usuário de validação foi gerada sem atualizações do código no controlador ou nas exibições. A listagem de Avançar mostra o que o `Create` métodos no `MovieController` a aparência de classe. Eles são modificados em relação a como são criados neste tutorial.

[!code-csharp[Main](adding-validation/samples/sample5.cs)]

O primeiro método de ação (HTTP GET) `Create` exibe o formulário Criar inicial. A segunda versão (`[HttpPost]`) manipula a postagem de formulário. A segunda `Create` método (o `HttpPost` versão) verifica `ModelState.IsValid` para ver se o filme tem erros de validação. Obter esta propriedade avalia os atributos de validação que foram aplicados ao objeto. Se o objeto tiver erros de validação, o `Create` método exibe novamente o formulário. Se não houver erros, o método salvará o novo filme no banco de dados. Em nosso exemplo de filme **o formulário não é postado no servidor quando há erros de validação detectados no lado do cliente; a segunda** `Create` **método nunca é chamado**. Se você desabilitar o JavaScript no navegador, a validação do cliente é desabilitado e o HTTP POST `Create` obtém método `ModelState.IsValid` para verificar se o filme tem erros de validação.

Defina um ponto de interrupção no método `HttpPost Create` e verifique se o método nunca é chamado; a validação do lado do cliente não enviará os dados de formulário quando forem detectados erros de validação. Se você desabilitar o JavaScript no navegador e, em seguida, enviar o formulário com erros, o ponto de interrupção será atingido. Você ainda pode obter uma validação completa sem o JavaScript. A imagem a seguir mostra como desabilitar o JavaScript no Internet Explorer.

![](adding-validation/_static/image5.png)

![](adding-validation/_static/image6.png)

A imagem a seguir mostra como desabilitar o JavaScript no navegador FireFox.

![](adding-validation/_static/image7.png)

A imagem a seguir mostra como desabilitar o JavaScript no navegador Chrome.

![](adding-validation/_static/image8.png)

Abaixo está o *Create. cshtml* modelo de exibição gerada por scaffolding anteriormente no tutorial. Ela é usada pelos métodos de ação mostrados acima para exibir o formulário inicial e exibi-lo novamente em caso de erro.

[!code-cshtml[Main](adding-validation/samples/sample6.cshtml?highlight=16-17)]

Observe como o código usa um `Html.EditorFor` auxiliar para a saída de `<input>` elemento para cada `Movie` propriedade. Ao lado deste auxiliar é uma chamada para o `Html.ValidationMessageFor` método auxiliar. Esses dois métodos auxiliares trabalham com o objeto de modelo que é passado pelo controlador para o modo de exibição (nesse caso, um `Movie` objeto). Eles parecem automaticamente para atributos de validação especificados em que as mensagens de erro de modelo e exibição conforme apropriado.

O que é realmente interessante nessa abordagem é que o controlador nem o modelo de exibição `Create` sabem nada sobre as regras de validação reais que estão sendo impostas ou as mensagens de erro específicas exibidas. As regras de validação e as cadeias de caracteres de erro são especificadas somente na classe `Movie`. Essas mesmas regras de validação são aplicadas automaticamente à exibição `Edit` e a outros modelos de exibição que podem ser criados e que editam o modelo.

Se você quiser alterar a lógica de validação mais tarde, você pode fazer isso em vigor exatamente uma adicionando atributos de validação para o modelo (neste exemplo, o `movie` classe). Você não precisa se preocupar se diferentes partes do aplicativo estão inconsistentes com a forma como as regras são impostas – toda a lógica de validação será definida em um lugar e usada em todos os lugares. Isso mantém o código muito limpo e torna-o mais fácil de manter e desenvolver. E isso significa que você respeitará totalmente o *DRY* princípio.

## <a name="using-datatype-attributes"></a>Usando atributos DataType

Abra o arquivo *Movie.cs* e examine a classe `Movie`. O [ `System.ComponentModel.DataAnnotations` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) namespace fornece os atributos de formatação além do conjunto interno de atributos de validação. Já aplicamos uma [ `DataType` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) valor de enumeração para a data de lançamento e para os campos de preço. O código a seguir mostra a `ReleaseDate` e `Price` propriedades com os devidos [ `DataType` ](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) atributo.

[!code-csharp[Main](adding-validation/samples/sample7.cs)]

O [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atributos fornecem apenas dicas para o mecanismo de exibição formate os dados (e fornece atributos como `<a>` para as URLs e `<a href="mailto:EmailAddress.com">` para email. Você pode usar o [RegularExpression](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx) atributo para validar o formato dos dados. O [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atributo é usado para especificar um tipo de dados que é mais específico do que o tipo intrínseco de banco de dados, eles são ***não*** atributos de validação. Nesse caso, apenas desejamos acompanhar a data, não a data e a hora. O [enumeração DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) fornece muitos tipos de dados, tais como *data, hora, PhoneNumber, moeda, EmailAddress* e muito mais. O atributo `DataType` também pode permitir que o aplicativo forneça automaticamente recursos específicos a um tipo. Por exemplo, uma `mailto:` link pode ser criado para [DataType.EmailAddress](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx), e um seletor de data pode ser fornecido para [DataType.Date](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) em navegadores que oferecem suporte a [HTML5](http://html5.org/). O [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atributos emite HTML 5 [dados -](http://ejohn.org/blog/html-5-data-attributes/) (pronuncia-se *dash dados*) atributos que navegadores HTML 5. O [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atributos não fornecem nenhuma validação.

`DataType.Date` não especifica o formato da data exibida. Por padrão, o campo de dados é exibido de acordo com os formatos padrão com base no servidor do [CultureInfo](https://msdn.microsoft.com/library/vstudio/system.globalization.cultureinfo(v=vs.110).aspx).

O atributo `DisplayFormat` é usado para especificar explicitamente o formato de data:


[!code-csharp[Main](adding-validation/samples/sample8.cs)]


O `ApplyFormatInEditMode` configuração especifica que a formatação especificado deve também ser aplicada quando o valor é exibido em uma caixa de texto para edição. (Talvez você não deseje isso em alguns campos – por exemplo, para valores de moeda, você talvez não deseja o símbolo de moeda na caixa de texto para edição.)

Você pode usar o [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) atributo por si só, mas geralmente é uma boa ideia usar o [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atributo também. O `DataType` atributo transmite o *semântica* dos dados conforme em vez de como renderizá-lo em uma tela e oferece os seguintes benefícios que você não obtém com `DisplayFormat`:

- O navegador pode habilitar recursos do HTML5 (por exemplo mostrar um controle de calendário, o símbolo de moeda apropriado de localidade, links de email, etc.).
- Por padrão, o navegador renderizará os dados usando o formato correto com base em sua [localidade](https://msdn.microsoft.com/library/vstudio/wyzd2bce.aspx).
- O [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx) atributo pode permitir que o MVC escolha o modelo de campo correto para renderizar os dados (o [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) se usado por si só, usa o modelo de cadeia de caracteres). Para obter mais informações, consulte Brad Wilson [modelos do ASP.NET MVC 2](http://bradwilson.typepad.com/blog/2009/10/aspnet-mvc-2-templates-part-1-introduction.html). (Embora escrito para MVC 2, neste artigo ainda se aplica à versão atual do ASP.NET MVC.)

Se você usar o `DataType` atributo com um campo de data, você deve especificar o `DisplayFormat` atributo também a fim de garantir que o campo seja renderizado corretamente em navegadores Chrome. Para obter mais informações, consulte [esse thread de StackOverflow](http://stackoverflow.com/questions/12633471/mvc4-datatype-date-editorfor-wont-display-date-value-in-chrome-fine-in-ie).

> [!NOTE]
> validação do jQuery não funciona com o [intervalo](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.rangeattribute.aspx) atributo e [DateTime](https://msdn.microsoft.com/library/system.datetime.aspx). Por exemplo, o seguinte código sempre exibirá um erro de validação do lado do cliente, mesmo quando a data estiver no intervalo especificado:
> 
> [!code-csharp[Main](adding-validation/samples/sample9.cs)]
> 
> Você precisará desabilitar a validação de data do jQuery para usar o [intervalo](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.rangeattribute.aspx) do atributo com [DateTime](https://msdn.microsoft.com/library/system.datetime.aspx). Geralmente, ele não é uma boa prática compilar datas rígidas nos modelos e, portanto, o uso de [intervalo](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.rangeattribute.aspx) atributo e [DateTime](https://msdn.microsoft.com/library/system.datetime.aspx) não é recomendado.


O seguinte código mostra como combinar atributos em uma linha:

[!code-csharp[Main](adding-validation/samples/sample10.cs?highlight=4,6,10,12)]

Na próxima parte da série, examinaremos o aplicativo e faremos algumas melhorias nos métodos `Details` e `Delete` gerados automaticamente.

> [!div class="step-by-step"]
> [Anterior](adding-a-new-field.md)
> [Próximo](examining-the-details-and-delete-methods.md)
