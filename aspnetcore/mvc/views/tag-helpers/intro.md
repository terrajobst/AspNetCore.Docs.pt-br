---
title: Auxiliares de Marca no ASP.NET Core
author: rick-anderson
description: Saiba o que são Auxiliares de Marca e como usá-los no ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: 15f94fd1c619e9f69c5783f664eafc9ca28f86f9
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239853"
---
# <a name="tag-helpers-in-aspnet-core"></a>Auxiliares de Marca no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-are-tag-helpers"></a>O que são Auxiliares de Marca

Os Auxiliares de Marca permitem que o código do lado do servidor participe da criação e renderização de elementos HTML em arquivos do Razor. Por exemplo, o `ImageTagHelper` interno pode acrescentar um número de versão ao nome da imagem. Sempre que a imagem é alterada, o servidor gera uma nova versão exclusiva para a imagem, de modo que os clientes tenham a garantia de obter a imagem atual (em vez de uma imagem obsoleta armazenada em cache). Há muitos Auxiliares de Marca internos para tarefas comuns – como criação de formulários, links, carregamento de ativos e muito mais – e ainda outros disponíveis em repositórios GitHub públicos e como NuGet. Os Auxiliares de Marca são criados no C# e são direcionados a elementos HTML de acordo com o nome do elemento, o nome do atributo ou a marca pai. Por exemplo, o `LabelTagHelper` interno pode ser direcionado ao elemento `<label>` HTML quando os atributos `LabelTagHelper` são aplicados. Se você está familiarizado com [Auxiliares HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), os Auxiliares de Marca reduzem as transições explícitas entre HTML e C# em exibições do Razor. Em muitos casos, os Auxiliares HTML fornecem uma abordagem alternativa a um Auxiliar de Marca específico, mas é importante reconhecer que os Auxiliares de Marca não substituem os Auxiliares HTML e que não há um Auxiliar de Marca para cada Auxiliar HTML. [Comparação entre Auxiliares de Marca e Auxiliares HTML](#tag-helpers-compared-to-html-helpers) explica as diferenças mais detalhadamente.

## <a name="what-tag-helpers-provide"></a>O que os Auxiliares de Marca fornecem

**Uma experiência de desenvolvimento amigável a HTML** Na maioria dos casos, a marcação do Razor com Auxiliares de Marca parece um HTML padrão. Designers de front-end familiarizados com HTML/CSS/JavaScript podem editar o Razor sem aprender a sintaxe Razor do C#.

**Um ambiente avançado do IntelliSense para criação do HTML e da marcação do Razor** Isso é um nítido contraste com Auxiliares HTML, a abordagem anterior para a criação do lado do servidor de marcação nas exibições do Razor. [Comparação entre Auxiliares de Marca e Auxiliares HTML](#tag-helpers-compared-to-html-helpers) explica as diferenças mais detalhadamente. [Suporte do IntelliSense para Auxiliares de Marca](#intellisense-support-for-tag-helpers) explica o ambiente do IntelliSense. Até mesmo desenvolvedores experientes com a sintaxe Razor do C# são mais produtivos usando Auxiliares de Marca do que escrevendo a marcação do Razor do C#.

**Uma maneira de fazer com que você fique mais produtivo e possa produzir um código mais robusto, confiável e possível de ser mantido usando as informações apenas disponíveis no servidor** Por exemplo, historicamente, o mantra da atualização de imagens era alterar o nome da imagem quando a imagem era alterada. As imagens devem ser armazenadas em cache de forma agressiva por motivos de desempenho e, a menos que você altere o nome de uma imagem, você corre o risco de os clientes obterem uma cópia obsoleta. Historicamente, depois que uma imagem era editada, o nome precisava ser alterado e cada referência à imagem no aplicativo Web precisava ser atualizada. Isso não só é muito trabalhoso, também é propenso a erros (você pode perder uma referência, inserir acidentalmente a cadeia de caracteres errada, etc.) O `ImageTagHelper` interno pode fazer isso para você automaticamente. O `ImageTagHelper` pode acrescentar um número de versão ao nome da imagem, de modo que sempre que a imagem é alterada, o servidor gera automaticamente uma nova versão exclusiva para a imagem. Os clientes têm a garantia de obter a imagem atual. Basicamente, essa economia na robustez e no trabalho é obtida gratuitamente com o `ImageTagHelper`.

A maioria dos auxiliares de marca internos é direcionada a elementos HTML padrão e fornece atributos do lado do servidor para o elemento. Por exemplo, o elemento `<input>` usado em várias exibições na pasta *Exibição/Conta* contém o atributo `asp-for`. Esse atributo extrai o nome da propriedade do modelo especificado no HTML renderizado. Considere uma exibição Razor com o seguinte modelo:

```csharp
public class Movie
{
    public int ID { get; set; }
    public string Title { get; set; }
    public DateTime ReleaseDate { get; set; }
    public string Genre { get; set; }
    public decimal Price { get; set; }
}
```

A seguinte marcação do Razor:

```cshtml
<label asp-for="Movie.Title"></label>
```

Gera o seguinte HTML:

```html
<label for="Movie_Title">Title</label>
```

O atributo `asp-for` é disponibilizado pela propriedade `For` no [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0). Confira [Auxiliares de marca de autor](xref:mvc/views/tag-helpers/authoring) para obter mais informações.

## <a name="managing-tag-helper-scope"></a>Gerenciando o escopo do Auxiliar de Marca

O escopo dos Auxiliares de Marca é controlado por uma combinação de `@addTagHelper`, `@removeTagHelper` e o caractere de recusa "!".

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a>`@addTagHelper` disponibiliza os Auxiliares de Marca

Se você criar um novo aplicativo Web ASP.NET Core chamado *AuthoringTagHelpers*, o seguinte arquivo *Views/_ViewImports.cshtml* será adicionado ao projeto:

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

A diretiva `@addTagHelper` disponibiliza os Auxiliares de Marca para a exibição. Nesse caso, o arquivo de exibição é *Pages/_ViewImports.cshtml*, que é herdado por padrão por todos os arquivos na pasta *Páginas* e suas subpastas, disponibilizando os Auxiliares de Marca. O código acima usa a sintaxe de curinga ("\*") para especificar que todos os Auxiliares de Marca no assembly especificado (*Microsoft.AspNetCore.Mvc.TagHelpers*) estarão disponíveis para todos os arquivos de exibição no diretório *Views* ou subdiretório. O primeiro parâmetro após `@addTagHelper` especifica os Auxiliares de Marca a serem carregados (estamos usando "\*" para todos os Auxiliares de Marca) e o segundo parâmetro "Microsoft.AspNetCore.Mvc.TagHelpers" especifica o assembly que contém os Auxiliares de Marca. *Microsoft.AspNetCore.Mvc.TagHelpers* é o assembly para os Auxiliares de Marca internos do ASP.NET Core.

Para expor todos os Auxiliares de Marca neste projeto (que cria um assembly chamado *AuthoringTagHelpers*), você usará o seguinte:

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

Se o projeto contém um `EmailTagHelper` com o namespace padrão (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), forneça o FQN ( nome totalmente qualificado) do Auxiliar de Marca:

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

Para adicionar um Auxiliar de Marca a uma exibição usando um FQN, primeiro adicione o FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) e, em seguida, o nome do assembly (*AuthoringTagHelpers*). A maioria dos desenvolvedores prefere usar a sintaxe de curinga "\*". A sintaxe de curinga permite que você insira o caractere curinga "\*" como o sufixo de um FQN. Por exemplo, uma das seguintes diretivas exibirá o `EmailTagHelper`:

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

Conforme mencionado anteriormente, a adição da diretiva `@addTagHelper` ao arquivo *Views/_ViewImports.cshtml* disponibiliza o Auxiliar de Marca para todos os arquivos de exibição no diretório *Views* e subdiretórios. Use a diretiva `@addTagHelper` nos arquivos de exibição específicos se você deseja aceitar a exposição do Auxiliar de Marca a apenas essas exibições.

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a>`@removeTagHelper` remove os Auxiliares de Marca

O `@removeTagHelper` tem os mesmos dois parâmetros `@addTagHelper` e remove um Auxiliar de Marca adicionado anteriormente. Por exemplo, `@removeTagHelper` aplicado a uma exibição específica remove o Auxiliar de Marca especificado da exibição. O uso de `@removeTagHelper` em um arquivo *Views/Folder/_ViewImports.cshtml* remove o Auxiliar de Marca especificado de todas as exibições em *Folder*.

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a>Controlando o escopo do Auxiliar de Marca com o arquivo *_ViewImports.cshtml*

Adicione um *_ViewImports.cshtml* a qualquer pasta de exibição e o mecanismo de exibição aplicará as diretivas desse arquivo e do arquivo *Views/_ViewImports.cshtml*. Se você adicionou um arquivo *Views/Home/_ViewImports.cshtml* vazio às exibições *Home*, não haverá nenhuma alteração porque o arquivo *_ViewImports.cshtml* é aditivo. As diretivas `@addTagHelper` que você adicionar ao arquivo *Views/Home/_ViewImports.cshtml* (que não estão no arquivo *Views/_ViewImports.cshtml* padrão) exporão os Auxiliares de Marca às exibições somente na pasta *Home*.

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a>Recusando elementos individuais

Desabilite um Auxiliar de Marca no nível do elemento com o caractere de recusa do Auxiliar de Marca ("!"). Por exemplo, a validação `Email` está desabilitada no `<span>` com o caractere de recusa do Auxiliar de Marca:

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

É necessário aplicar o caractere de recusa do Auxiliar de Marca à marca de abertura e fechamento. (O editor do Visual Studio adiciona automaticamente o caractere de recusa à marca de fechamento quando um é adicionado à marca de abertura). Depois de adicionar o caractere de recusa, o elemento e os atributos do Auxiliar de Marca deixam de ser exibidos em uma fonte diferenciada.

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a>Usando `@tagHelperPrefix` para tornar explícito o uso do Auxiliar de Marca

A diretiva `@tagHelperPrefix` permite que você especifique uma cadeia de caracteres de prefixo de marca para habilitar o suporte do Auxiliar de Marca e tornar explícito o uso do Auxiliar de Marca. Por exemplo, você pode adicionar a seguinte marcação ao arquivo *Views/_ViewImports.cshtml*:

```cshtml
@tagHelperPrefix th:
```

Na imagem do código abaixo, o prefixo do Auxiliar de Marca é definido como `th:`; portanto, somente esses elementos que usam o prefixo `th:` dão suporte a Auxiliares de Marca (elementos habilitados para Auxiliar de Marca têm uma fonte diferenciada). Os elementos `<label>` e `<input>` têm o prefixo do Auxiliar de Marca e são habilitados para Auxiliar de Marca, ao contrário do elemento `<span>`.

![image](intro/_static/thp.png)

As mesmas regras de hierarquia que se aplicam a `@addTagHelper` também se aplicam a `@tagHelperPrefix`.

## <a name="self-closing-tag-helpers"></a>Auxiliares de Marca com autofechamento

Muitos Auxiliares de Marca não podem ser usados como marcações com autofechamento. Alguns Auxiliares de Marca são projetados para serem marcações com autofechamento. Usar um Auxiliar de Marca que não foi projetado para ser de autofechamento suprime a saída renderizada. Um Auxiliar de Marca com autofechamento resulta em uma marca com autofechamento na saída renderizada. Para obter mais informações, confira [esta observação](xref:mvc/views/tag-helpers/authoring#self-closing) em [Criando Auxiliares de Marca](xref:mvc/views/tag-helpers/authoring).

## <a name="c-in-tag-helpers-attributedeclaration"></a>C#em marcação atributo/declaração de auxiliares 

Os auxiliares de marcação não C# permitem no atributo ou na área de declaração de marca do elemento. Por exemplo, o código a seguir não é válido:

```cshtml
<input asp-for="LastName"  
       @(Model?.LicenseId == null ? "disabled" : string.Empty) />
```

O código anterior pode ser escrito como:

```cshtml
<input asp-for="LastName" 
       disabled="@(Model?.LicenseId == null)" />
```

## <a name="intellisense-support-for-tag-helpers"></a>Suporte do IntelliSense para Auxiliares de Marca

Quando você cria um novo aplicativo Web ASP.NET Core no Visual Studio, ele adiciona o pacote NuGet "Microsoft.AspNetCore.Razor.Tools". Esse é o pacote que adiciona ferramentas do Auxiliar de Marca.

Considere a escrita de um elemento `<label>` HTML. Assim que você insere `<l` no editor do Visual Studio, o IntelliSense exibe elementos correspondentes:

![image](intro/_static/label.png)

Não só você obtém a ajuda do HTML, mas também o ícone (o "@" symbol with "<>" abaixo dele).

![image](intro/_static/tagSym.png)

identifica o elemento como direcionado a Auxiliares de Marca. Elementos HTML puros (como o `fieldset`) exibem o ícone "<>".

Uma marca `<label>` HTML pura exibe a marca HTML (com o tema de cores padrão do Visual Studio) em uma fonte marrom, os atributos em vermelho e os valores de atributo em azul.

![image](intro/_static/LableHtmlTag.png)

Depois de inserir `<label`, o IntelliSense lista os atributos HTML/CSS disponíveis e os atributos direcionados ao Auxiliar de Marca:

![image](intro/_static/labelattr.png)

O preenchimento de declaração do IntelliSense permite que você pressione a tecla TAB para preencher a declaração com o valor selecionado:

![image](intro/_static/stmtcomplete.png)

Assim que um atributo do Auxiliar de Marca é inserido, as fontes da marca e do atributo são alteradas. Usando o tema de cores padrão "Azul" ou "Claro" do Visual Studio, a fonte é roxo em negrito. Se estiver usando o tema "Escuro", a fonte será azul-petróleo em negrito. As imagens deste documento foram obtidas usando o tema padrão.

![image](intro/_static/labelaspfor2.png)

Insira o atalho *CompleteWord* do Visual Studio – Ctrl + barra de espaços é o [padrão](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) dentro das aspas duplas ("") e você está agora no C#, exatamente como estaria em uma classe do C#. O IntelliSense exibe todos os métodos e propriedades no modelo de página. Os métodos e as propriedades estão disponíveis porque o tipo de propriedade é `ModelExpression`. Na imagem abaixo, estou editando a exibição `Register` e, portanto, o `RegisterViewModel` está disponível.

![image](intro/_static/intellemail.png)

O IntelliSense lista as propriedades e os métodos disponíveis para o modelo na página. O ambiente avançado de IntelliSense ajuda você a selecionar a classe CSS:

![image](intro/_static/iclass.png)

![image](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a>Comparação entre Auxiliares de Marca e Auxiliares HTML

Os Auxiliares de Marca são anexados a elementos HTML em exibições do Razor, enquanto os [Auxiliares HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) são invocados como métodos intercalados com HTML nas exibições do Razor. Considere a seguinte marcação do Razor, que cria um rótulo HTML com a classe CSS "caption":

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

O símbolo de arroba (`@`) informa o Razor de que este é o início do código. Os dois próximos parâmetros ("FirstName" e "First Name:") são cadeias de caracteres; portanto, o [IntelliSense](/visualstudio/ide/using-intellisense) não pode ajudar. O último argumento:

```cshtml
new {@class="caption"}
```

É um objeto anônimo usado para representar atributos. Como a `class` é uma palavra-chave reservada no C#, use o símbolo `@` para forçar o C# a interpretar `@class=` como um símbolo (nome da propriedade). Para um designer de front-end (alguém familiarizado com HTML/CSS/JavaScript e outras tecnologias de cliente, mas não familiarizado com o C# e Razor), a maior parte da linha é estranha. Toda a linha precisa ser criada sem nenhuma ajuda do IntelliSense.

Usando o `LabelTagHelper`, a mesma marcação pode ser escrita como:

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

Com a versão do Auxiliar de Marca, assim que você insere `<l` no editor do Visual Studio, o IntelliSense exibe elementos correspondentes:

![image](intro/_static/label.png)

O IntelliSense ajuda você a escrever a linha inteira.

A imagem de código a seguir mostra a parte do Formulário da exibição do Razor *Views/Account/Register.cshtml* gerada com base no modelo do ASP.NET 4.5 MVC incluído com o Visual Studio.

![image](intro/_static/regCS.png)

O editor do Visual Studio exibe o código C# com uma tela de fundo cinza. Por exemplo, o Auxiliar HTML `AntiForgeryToken`:

```cshtml
@Html.AntiForgeryToken()
```

é exibido com uma tela de fundo cinza. A maior parte da marcação na exibição Register é C#. Compare isso com a abordagem equivalente ao uso de Auxiliares de Marca:

![image](intro/_static/regTH.png)

A marcação é muito mias limpa e fácil de ler, editar e manter que a abordagem dos Auxiliares HTML. O código C# é reduzido ao mínimo que o servidor precisa conhecer. O editor do Visual Studio exibe a marcação direcionada por um Auxiliar de Marca em uma fonte diferenciada.

Considere o grupo *Email*:

[!code-csharp[](intro/sample/Register.cshtml?range=12-18)]

Cada um dos atributos "asp-" tem um valor "Email", mas "Email" não é uma cadeia de caracteres. Nesse contexto, "Email" é a propriedade da expressão do modelo C# para o `RegisterViewModel`.

O editor do Visual Studio ajuda você a escrever **toda** a marcação na abordagem do Auxiliar de Marca de formulário de registro, enquanto o Visual Studio não fornece nenhuma ajuda para a maioria do código na abordagem de Auxiliares HTML. [Suporte do IntelliSense para Auxiliares de Marca](#intellisense-support-for-tag-helpers) apresenta detalhes sobre como trabalhar com Auxiliares de Marca no editor do Visual Studio.

## <a name="tag-helpers-compared-to-web-server-controls"></a>Comparação entre Auxiliares de Marca e Controles de Servidor Web

* Os Auxiliares de Marca não têm o elemento ao qual estão associados; simplesmente participam da renderização do elemento e do conteúdo. Os [controles de Servidor Web](https://msdn.microsoft.com/library/7698y1f0.aspx) do ASP.NET são declarados e invocados em uma página.

* Os [controles de Servidor Web](https://msdn.microsoft.com/library/zsyt68f1.aspx) têm um ciclo de vida não trivial que pode dificultar o desenvolvimento e a depuração.

* Os controles de Servidor Web permitem que você adicione a funcionalidade aos elementos DOM (Modelo de Objeto do Documento) do cliente usando um controle de cliente. Os Auxiliares de Marca não tem nenhum DOM.

* Os controles de Servidor Web incluem a detecção automática do navegador. Os Auxiliares de Marca não têm nenhum conhecimento sobre o navegador.

* Vários Auxiliares de Marca podem atuar no mesmo elemento (consulte [Evitando conflitos do Auxiliar de Marca](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts)), embora normalmente não seja possível compor controles de Servidor Web.

* Os Auxiliares de Marca podem modificar a marca e o conteúdo de elementos HTML no escopo com o qual foram definidos, mas não modificam diretamente todo o resto em uma página. Os controles de Servidor Web têm um escopo menos específico e podem executar ações que afetam outras partes da página, permitindo efeitos colaterais não intencionais.

* Os controles de Servidor Web usam conversores de tipo para converter cadeias de caracteres em objetos. Com os Auxiliares de Marca, você trabalha nativamente no C# e, portanto, não precisa fazer a conversão de tipo.

* Os controles de Servidor Web usam [System.ComponentModel](/dotnet/api/system.componentmodel) para implementar o comportamento de componentes e controles em tempo de execução e em tempo de design. `System.ComponentModel` inclui as interfaces e as classes base para implementar atributos e conversores de tipo, associar a fontes de dados e licenciar componentes. Compare isso com os Auxiliares de Marca, que normalmente são derivados de `TagHelper`, e a classe base `TagHelper` expõe apenas dois métodos, `Process` e `ProcessAsync`.

## <a name="customizing-the-tag-helper-element-font"></a>Personalizando a fonte de elemento do Auxiliar de Marca

Personalize a fonte e a colorização em **Ferramentas** > **Opções** > **Ambiente** > **Fontes e Cores**:

![image](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a>Recursos adicionais

* [Auxiliares de marca de autor](xref:mvc/views/tag-helpers/authoring)
* [Trabalhando com formulários](xref:mvc/views/working-with-forms)
* [TagHelperSamples no GitHub](https://github.com/dpaquette/TagHelperSamples) contém amostras de Auxiliar de Marca para trabalhar com o [Bootstrap](https://getbootstrap.com/).
