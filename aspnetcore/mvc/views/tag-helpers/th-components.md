---
title: Componentes do Auxiliar de Marca no ASP.NET Core
author: scottaddie
description: Saiba o que são os Componentes do Auxiliar de Marca e como usá-los no ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: b5b3abea6492cfaa7d6acd0e54073a8db12eb2a5
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034756"
---
# <a name="tag-helper-components-in-aspnet-core"></a>Componentes do Auxiliar de Marca no ASP.NET Core

Por [Scott Addie](https://twitter.com/Scott_Addie) e [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)

Um Componente do Auxiliar de Marca é um Auxiliar de Marca que permite que você modifique ou adicione condicionalmente elementos HTML de código do lado do servidor. Esse recurso está disponível no ASP.NET Core 2.0 ou posterior.

O ASP.NET Core inclui dois Componentes de Auxiliar de Marca internos: `head` e `body`. Eles estão localizados no namespace <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> e podem ser usados no MVC e no Razor Pages. Componentes do Auxiliar de Marca não requerem registro com o aplicativo em *_ViewImports.cshtml*.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="use-cases"></a>Casos de uso

Dois casos de uso comum dos Componentes do Auxiliar de Marca incluem:

1. [Injetar um `<link>` no `<head>`.](#inject-into-html-head-element)
1. [Injetar um `<script>` no `<body>`.](#inject-into-html-body-element)

As seções a seguir descrevem esses casos de uso.

### <a name="inject-into-html-head-element"></a>Injetar no elemento HTML head

Dentro do elemento HTML `<head>`, os arquivos CSS são geralmente importados com o elemento HTML `<link>`. O código a seguir injeta um elemento `<link>` no elemento `<head>` usando o Componente do Auxiliar de Marca `head`:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

No código anterior:

* `AddressStyleTagHelperComponent` implementa <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>. A abstração:
  * Permite a inicialização da classe com um <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.
  * Habilita o uso de Componentes do Auxiliar de Marca para adicionar ou modificar elementos HTML.
* A propriedade <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> define a ordem na qual os Componentes são renderizados. `Order` é necessário quando há vários usos de Componentes do Auxiliar de Marca em um aplicativo.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compara o valor da propriedade <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> do contexto de execução com `head`. Se a comparação for avaliada como verdadeira, o conteúdo do campo `_style` será injetado no elemento HTML `<head>`.

### <a name="inject-into-html-body-element"></a>Injetar no elemento HTML body

O Componente do Auxiliar de Marca `body` pode injetar um elemento `<script>` no elemento `<body>`. O código a seguir demonstra essa técnica:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

Um arquivo HTML separado é usado para armazenar o elemento `<script>`. O arquivo HTML torna o código mais limpo e mais sustentável. O código anterior lê o conteúdo de *TagHelpers/Templates/AddressToolTipScript.html* e acrescenta-o com a saída do Auxiliar de Marca. O arquivo *AddressToolTipScript.html* inclui a seguinte marcação:

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

O código anterior associa um [widget de Dica de ferramenta de inicialização](https://getbootstrap.com/docs/3.3/javascript/#tooltips) a qualquer elemento `<address>` que inclua um atributo `printable`. O efeito é visível quando um ponteiro do mouse focaliza o elemento.

## <a name="register-a-component"></a>Registrar um componente

Um Componente do Auxiliar de Marca precisa ser adicionado à coleção de Componentes do Auxiliar de Marca do aplicativo. Há três maneiras de adicioná-lo à coleção:

* [Componentes do Auxiliar de Marca no ASP.NET Core](#tag-helper-components-in-aspnet-core)
  * [Casos de uso](#use-cases)
    * [Injetar no elemento HTML head](#inject-into-html-head-element)
    * [Injetar no elemento HTML body](#inject-into-html-body-element)
  * [Registrar um componente](#register-a-component)
    * [Registro por contêiner de serviços](#registration-via-services-container)
    * [Registro por arquivo Razor](#registration-via-razor-file)
    * [Registro por Modelo de página ou controlador](#registration-via-page-model-or-controller)
  * [Criar um componente](#create-a-component)
  * [Recursos adicionais](#additional-resources)

### <a name="registration-via-services-container"></a>Registro por contêiner de serviços

Se a classe do Componente do Auxiliar de Marca não for gerenciada com <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, ela precisará ser registrada com o sistema de [DI (injeção de dependência)](xref:fundamentals/dependency-injection). O código `Startup.ConfigureServices` a seguir registra as classes `AddressStyleTagHelperComponent` e `AddressScriptTagHelperComponent` com um [tempo de vida transitório](xref:fundamentals/dependency-injection#lifetime-and-registration-options):

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a>Registro por arquivo Razor

Se o Componente do Auxiliar de Marca não estiver registrado com DI, ele poderá ser registrado por uma página do Razor Pages ou uma exibição do MVC. Essa técnica é usada para controlar a marcação injetada e o pedido de execução do componente de um arquivo Razor.

`ITagHelperComponentManager` é usado para adicionar Componentes do Auxiliar de Marca ou removê-los do aplicativo. O código a seguir demonstra essa técnica com `AddressTagHelperComponent`:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

No código anterior:

* A diretiva `@inject` fornece uma instância de `ITagHelperComponentManager`. A instância é atribuída a uma variável chamada `manager` para acesso downstream no arquivo Razor.
* Uma instância do `AddressTagHelperComponent` é adicionada à coleção de Componentes do Auxiliar de Marca do aplicativo.

`AddressTagHelperComponent` é modificado para acomodar um construtor que aceita os parâmetros `markup` e `order`:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

O parâmetro `markup` fornecido é usado em `ProcessAsync` da seguinte maneira:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a>Registro por Modelo de página ou controlador

Se o Componente do Auxiliar de Marca não estiver registrado com DI, ele poderá ser registrado por um modelo de página do Razor Pages ou um controlador do MVC. Essa técnica é útil para separar a lógica C# de arquivos Razor.

A injeção do construtor é usada para acessar uma instância de `ITagHelperComponentManager`. Um Componente do Auxiliar de Marca é adicionado à coleção de Componentes do Auxiliar de Marca da instância. O modelo de página do Razor Pages a seguir demonstra essa técnica com `AddressTagHelperComponent`:

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

No código anterior:

* A injeção do construtor é usada para acessar uma instância de `ITagHelperComponentManager`.
* Uma instância do `AddressTagHelperComponent` é adicionada à coleção de Componentes do Auxiliar de Marca do aplicativo.

## <a name="create-a-component"></a>Criar um componente

Para criar um Componente do Auxiliar de Marca personalizado:

* Crie uma classe pública derivada de <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.
* Aplique um atributo [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) à classe. Especifique o nome do elemento HTML de destino.
* *Opcional*: aplique um atributo [[EditorBrowsable(EditorBrowsableState.Never)]](xref:System.ComponentModel.EditorBrowsableAttribute) à classe para suprimir a exibição do tipo no IntelliSense.

O código a seguir cria um Componente do Auxiliar de Marca personalizado que tem como destino o elemento HTML `<address>`:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

Usar o Componente do Auxiliar de Marca `address` personalizado para inserir uma marcação HTML da seguinte maneira:

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open("
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

O método `ProcessAsync` anterior injeta o HTML fornecido para <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> no elemento `<address>` correspondente. A injeção ocorre quando:

* O valor da propriedade `TagName` do contexto de execução é igual a `address`.
* O elemento `<address>` correspondente tem um atributo `printable`.

Por exemplo, a instrução `if` é avaliada como verdadeira ao processar o seguinte elemento `<address>`:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
