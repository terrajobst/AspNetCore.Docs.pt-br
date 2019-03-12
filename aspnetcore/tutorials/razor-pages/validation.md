---
title: Adicionar validação a uma Página Razor do ASP.NET Core
author: rick-anderson
description: Saiba como adicionar validação a uma Página Razor no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/5/2018
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 961b41a63a5dd066a51468239bd855b7f723a3ae
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346035"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a>Adicionar validação a uma Página Razor do ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Nesta seção, a lógica de validação é adicionada para o modelo `Movie`. As regras de validação são impostas sempre que um usuário cria ou edita um filme.

## <a name="validation"></a>Validação

Um princípio-chave do desenvolvimento de software é chamado [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (“**D**on't **R**epeat **Y**ourself”). As Páginas Razor incentivam o desenvolvimento quando a funcionalidade é especificada uma vez e ela é refletida em todo o aplicativo. O DRY pode ajudar a:

* Reduzir a quantidade de código em um aplicativo.
* Fazer com que o código seja menos propenso a erros e mais fácil de ser testado e mantido.

O suporte de validação fornecido pelas Páginas Razor e pelo Entity Framework é um bom exemplo do princípio DRY. As regras de validação são especificadas de forma declarativa em um único lugar (na classe de modelo) e as regras são impostas em qualquer lugar no aplicativo.

### <a name="adding-validation-rules-to-the-movie-model"></a>Adicionando regras de validação ao modelo de filme

Abra o arquivo *Models/Movie.cs*. [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) fornece um conjunto interno de atributos de validação que são aplicados de forma declarativa a uma classe ou propriedade. DataAnnotations também contém atributos de formatação como `DataType`, que ajudam com a formatação e não fornecem validação.

Atualize a classe `Movie` para aproveitar os atributos de validação `Required`, `StringLength`, `RegularExpression` e `Range`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

Os atributos de validação especificam o comportamento que é imposto nas propriedades do modelo:

* Os atributos `Required` e `MinimumLength` indicam que uma propriedade deve ter um valor. No entanto, nada impede que um usuário digite espaços em branco para atender à restrição de validação para um tipo de permite valor nulo. Os [tipos de valor](/dotnet/csharp/language-reference/keywords/value-types) que não permitem valores nulos (como `decimal`, `int`, `float` e `DateTime`) são inerentemente necessários e não precisam do atributo `Required`.
* O atributo `RegularExpression` limita os caracteres que o usuário pode inserir. No código anterior, `Genre` precisa começar com uma ou mais letras maiúsculas e seguir com zero ou mais letras, aspas simples ou duplas, caracteres de espaço em branco ou traço. `Rating` precisa começar com uma ou mais letras maiúsculas e seguir com zero ou mais letras, números, aspas simples ou duplas, caracteres de espaço em branco ou traço.
* O atributo `Range` restringe um valor a um intervalo especificado.
* O atributo `StringLength` define o tamanho máximo de uma cadeia de caracteres e, opcionalmente, o tamanho mínimo. 

Ter as regras de validação automaticamente impostas pelo ASP.NET Core ajuda a tornar um aplicativo mais robusto. A validação automática em modelos ajuda a proteger o aplicativo porque você não precisa se lembrar de aplicá-los quando um novo código é adicionado.

### <a name="validation-error-ui-in-razor-pages"></a>Interface do usuário do erro de validação nas Páginas Razor

Execute o aplicativo e navegue para Pages/Movies.

Selecione o link **Criar Novo**. Preencha o formulário com alguns valores inválidos. Quando a validação do lado do cliente do jQuery detecta o erro, ela exibe uma mensagem de erro.

![Formulário da exibição de filmes com vários erros de validação do lado do cliente do jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/currency.md)]

Observe como o formulário renderizou automaticamente uma mensagem de erro de validação em cada campo que contém um valor inválido. Os erros são impostos no lado do cliente (usando o JavaScript e o jQuery) e no lado do servidor (quando um usuário tem o JavaScript desabilitado).

Uma vantagem significativa é que **nenhuma** alteração de código foi necessária nas páginas Criar ou Editar. Depois que DataAnnotations foi aplicado ao modelo, a interface do usuário de validação foi habilitada. As Páginas Razor criadas neste tutorial selecionaram automaticamente as regras de validação (usando os atributos de validação nas propriedades da classe do modelo `Movie`). Validação do teste usando a página Editar: a mesma validação é aplicada.

Os dados de formulário não serão postados no servidor enquanto houver erros de validação do lado do cliente. Verifique se os dados de formulário não são postados por uma ou mais das seguintes abordagens:

* Coloque um ponto de interrupção no método `OnPostAsync`. Envie o formulário (selecione **Criar** ou **Salvar**). O ponto de interrupção nunca é atingido.
* Use a [ferramenta Fiddler](http://www.telerik.com/fiddler).
* Use as ferramentas do desenvolvedor do navegador para monitorar o tráfego de rede.

### <a name="server-side-validation"></a>Validação do servidor

Quando o JavaScript está desabilitado no navegador, o envio do formulário com erros será postado no servidor.

(Opcional) Teste a validação do servidor:

* Desabilite o JavaScript no navegador. É possível fazer isso usando as ferramentas para desenvolvedores do navegador. Se você não conseguir desabilitar o JavaScript no navegador, tente outro navegador.
* Defina um ponto de interrupção no método `OnPostAsync` da página Criar ou Editar.
* Envie um formulário com erros de validação.
* Verifique se o estado do modelo é inválido:

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```

O código a seguir mostra uma parte da página *Create.cshtml* gerada por scaffolding anteriormente no tutorial. Ele é usado pelas páginas Criar e Editar para exibir o formulário inicial e exibir o formulário novamente, em caso de erro.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

O [Auxiliar de Marcação de Entrada](xref:mvc/views/working-with-forms) usa os atributos de [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produz os atributos HTML necessários para a Validação do jQuery no lado do cliente. O [Auxiliar de Marcação de Validação](xref:mvc/views/working-with-forms#the-validation-tag-helpers) exibe erros de validação. Consulte [Validação](xref:mvc/models/validation) para obter mais informações.

As páginas Criar e Editar não têm nenhuma regra de validação. As regras de validação e as cadeias de caracteres de erro são especificadas somente na classe `Movie`. Essas regras de validação são aplicadas automaticamente às Páginas Razor que editam o modelo `Movie`.

Quando a lógica de validação precisa ser alterada, ela é feita apenas no modelo. A validação é aplicada de forma consistente em todo o aplicativo (a lógica de validação é definida em um único lugar). A validação em um único lugar ajuda a manter o código limpo e facilita sua manutenção e atualização.

## <a name="using-datatype-attributes"></a>Usando atributos DataType

Examine a classe `Movie`. O namespace `System.ComponentModel.DataAnnotations` fornece atributos de formatação, além do conjunto interno de atributos de validação. O atributo `DataType` é aplicado às propriedades `ReleaseDate` e `Price`.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Os atributos `DataType` fornecem apenas dicas para que o mecanismo de exibição formate os dados (e fornece atributos como `<a>` para as URLs e `<a href="mailto:EmailAddress.com">` para o email). Use o atributo `RegularExpression` para validar o formato dos dados. O atributo `DataType` é usado para especificar um tipo de dados mais específico do que o tipo intrínseco de banco de dados. Os atributos `DataType` não são atributos de validação. No aplicativo de exemplo, apenas a data é exibida, sem a hora.

A Enumeração `DataType` fornece muitos tipos de dados, como Date, Time, PhoneNumber, Currency, EmailAddress e muito mais. O atributo `DataType` também pode permitir que o aplicativo forneça automaticamente recursos específicos a um tipo. Por exemplo, um link `mailto:` pode ser criado para `DataType.EmailAddress`. Um seletor de data pode ser fornecido para `DataType.Date` em navegadores que dão suporte a HTML5. Os atributos `DataType` emitem atributos `data-` HTML 5 (pronunciados “data dash”) que são consumidos pelos navegadores HTML 5. Os atributos `DataType` **não** fornecem nenhuma validação.

`DataType.Date` não especifica o formato da data exibida. Por padrão, o campo de dados é exibido de acordo com os formatos padrão com base nas `CultureInfo` do servidor.


A anotação de dados `[Column(TypeName = "decimal(18, 2)")]` é necessária para que o Entity Framework Core possa mapear corretamente o `Price` para a moeda no banco de dados. Para obter mais informações, veja [Tipos de Dados](/ef/core/modeling/relational/data-types).

O atributo `DisplayFormat` é usado para especificar explicitamente o formato de data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

A configuração `ApplyFormatInEditMode` especifica que a formatação deve ser aplicada quando o valor é exibido para edição. Não é recomendável ter esse comportamento em alguns campos. Por exemplo, em valores de moeda, você provavelmente não deseja que o símbolo de moeda seja exibido na interface do usuário de edição.

O atributo `DisplayFormat` pode ser usado por si só, mas geralmente é uma boa ideia usar o atributo `DataType`. O atributo `DataType` transmite a semântica dos dados, ao invés de apresentar como renderizá-lo em uma tela e oferece os seguintes benefícios que você não obtém com DisplayFormat:

* O navegador pode habilitar os recursos do HTML5 (por exemplo, mostrar um controle de calendário, o símbolo de moeda apropriado à localidade, links de email, etc.)
* Por padrão, o navegador renderizará os dados usando o formato correto de acordo com a localidade.
* O atributo `DataType` pode permitir que a estrutura ASP.NET Core escolha o modelo de campo correto para renderizar os dados. O `DisplayFormat`, se usado por si só, usa o modelo de cadeia de caracteres.

Observação: a validação do jQuery não funciona com os atributos `Range` e `DateTime`. Por exemplo, o seguinte código sempre exibirá um erro de validação do lado do cliente, mesmo quando a data estiver no intervalo especificado:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Geralmente, não é uma boa prática compilar datas rígidas nos modelos e, portanto, o uso do atributo `Range` e de `DateTime` não é recomendado.

O seguinte código mostra como combinar atributos em uma linha:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Comece com o Razor Pages e o EF Core](xref:data/ef-rp/intro) mostra operações mais avançadas do EF Core com o Razor Pages.

### <a name="publish-to-azure"></a>Publicar no Azure

Para obter informações sobre como implantar no Azure, consulte [Tutorial: Compilar um aplicativo ASP.NET no Azure com o Banco de Dados SQL](/azure/app-service/app-service-web-tutorial-dotnet-sqldatabase). Essas instruções são para um aplicativo ASP.NET, não um aplicativo ASP.NET Core, mas as etapas são as mesmas.

Obrigado por concluir esta introdução às Páginas Razor. A [Introdução ao Razor Pages e ao EF Core](xref:data/ef-rp/intro) é um excelente acompanhamento para este tutorial.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [Versão do YouTube deste tutorial](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [Anterior: adicionando um novo campo](xref:tutorials/razor-pages/new-field)
