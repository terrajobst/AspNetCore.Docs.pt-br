---
title: Componentes do Auxiliar de Marca no ASP.NET Core
author: scottaddie
description: Saiba o que são os Componentes do Auxiliar de Marca e como usá-los no ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: de053dd0138224a9bece218010eaaedcf7e084cb
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583529"
---
# <a name="tag-helper-components-in-aspnet-core"></a><span data-ttu-id="13d18-103">Componentes do Auxiliar de Marca no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="13d18-103">Tag Helper Components in ASP.NET Core</span></span>

<span data-ttu-id="13d18-104">Por [Scott Addie](https://twitter.com/Scott_Addie) e [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span><span class="sxs-lookup"><span data-stu-id="13d18-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span></span>

<span data-ttu-id="13d18-105">Um Componente do Auxiliar de Marca é um Auxiliar de Marca que permite que você modifique ou adicione condicionalmente elementos HTML de código do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="13d18-105">A Tag Helper Component is a Tag Helper that allows you to conditionally modify or add HTML elements from server-side code.</span></span> <span data-ttu-id="13d18-106">Esse recurso está disponível no ASP.NET Core 2.0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="13d18-106">This feature is available in ASP.NET Core 2.0 or later.</span></span>

<span data-ttu-id="13d18-107">O ASP.NET Core inclui dois Componentes de Auxiliar de Marca internos: `head` e `body`.</span><span class="sxs-lookup"><span data-stu-id="13d18-107">ASP.NET Core includes two built-in Tag Helper Components: `head` and `body`.</span></span> <span data-ttu-id="13d18-108">Eles estão localizados no namespace <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> e podem ser usados no MVC e no Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="13d18-108">They're located in the <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> namespace and can be used in both MVC and Razor Pages.</span></span> <span data-ttu-id="13d18-109">Componentes do Auxiliar de Marca não requerem registro com o aplicativo em *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="13d18-109">Tag Helper Components don't require registration with the app in *_ViewImports.cshtml*.</span></span>

<span data-ttu-id="13d18-110">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="13d18-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="use-cases"></a><span data-ttu-id="13d18-111">Casos de uso</span><span class="sxs-lookup"><span data-stu-id="13d18-111">Use cases</span></span>

<span data-ttu-id="13d18-112">Dois casos de uso comum dos Componentes do Auxiliar de Marca incluem:</span><span class="sxs-lookup"><span data-stu-id="13d18-112">Two common use cases of Tag Helper Components include:</span></span>

1. [<span data-ttu-id="13d18-113">Injetar um `<link>` no `<head>`.</span><span class="sxs-lookup"><span data-stu-id="13d18-113">Injecting a `<link>` into the `<head>`.</span></span>](#inject-into-html-head-element)
1. [<span data-ttu-id="13d18-114">Injetar um `<script>` no `<body>`.</span><span class="sxs-lookup"><span data-stu-id="13d18-114">Injecting a `<script>` into the `<body>`.</span></span>](#inject-into-html-body-element)

<span data-ttu-id="13d18-115">As seções a seguir descrevem esses casos de uso.</span><span class="sxs-lookup"><span data-stu-id="13d18-115">The following sections describe these use cases.</span></span>

### <a name="inject-into-html-head-element"></a><span data-ttu-id="13d18-116">Injetar no elemento HTML head</span><span class="sxs-lookup"><span data-stu-id="13d18-116">Inject into HTML head element</span></span>

<span data-ttu-id="13d18-117">Dentro do elemento HTML `<head>`, os arquivos CSS são geralmente importados com o elemento HTML `<link>`.</span><span class="sxs-lookup"><span data-stu-id="13d18-117">Inside the HTML `<head>` element, CSS files are commonly imported with the HTML `<link>` element.</span></span> <span data-ttu-id="13d18-118">O código a seguir injeta um elemento `<link>` no elemento `<head>` usando o Componente do Auxiliar de Marca `head`:</span><span class="sxs-lookup"><span data-stu-id="13d18-118">The following code injects a `<link>` element into the `<head>` element using the `head` Tag Helper Component:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

<span data-ttu-id="13d18-119">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="13d18-119">In the preceding code:</span></span>

* <span data-ttu-id="13d18-120">`AddressStyleTagHelperComponent` implementa <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span><span class="sxs-lookup"><span data-stu-id="13d18-120">`AddressStyleTagHelperComponent` implements <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span></span> <span data-ttu-id="13d18-121">A abstração:</span><span class="sxs-lookup"><span data-stu-id="13d18-121">The abstraction:</span></span>
  * <span data-ttu-id="13d18-122">Permite a inicialização da classe com um <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span><span class="sxs-lookup"><span data-stu-id="13d18-122">Allows initialization of the class with a <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span></span>
  * <span data-ttu-id="13d18-123">Habilita o uso de Componentes do Auxiliar de Marca para adicionar ou modificar elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="13d18-123">Enables the use of Tag Helper Components to add or modify HTML elements.</span></span>
* <span data-ttu-id="13d18-124">A propriedade <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> define a ordem na qual os Componentes são renderizados.</span><span class="sxs-lookup"><span data-stu-id="13d18-124">The <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> property defines the order in which the Components are rendered.</span></span> <span data-ttu-id="13d18-125">`Order` é necessário quando há vários usos de Componentes do Auxiliar de Marca em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="13d18-125">`Order` is necessary when there are multiple usages of Tag Helper Components in an app.</span></span>
* <span data-ttu-id="13d18-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compara o valor da propriedade <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> do contexto de execução com `head`.</span><span class="sxs-lookup"><span data-stu-id="13d18-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compares the execution context's <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> property value to `head`.</span></span> <span data-ttu-id="13d18-127">Se a comparação for avaliada como verdadeira, o conteúdo do campo `_style` será injetado no elemento HTML `<head>`.</span><span class="sxs-lookup"><span data-stu-id="13d18-127">If the comparison evaluates to true, the content of the `_style` field is injected into the HTML `<head>` element.</span></span>

### <a name="inject-into-html-body-element"></a><span data-ttu-id="13d18-128">Injetar no elemento HTML body</span><span class="sxs-lookup"><span data-stu-id="13d18-128">Inject into HTML body element</span></span>

<span data-ttu-id="13d18-129">O Componente do Auxiliar de Marca `body` pode injetar um elemento `<script>` no elemento `<body>`.</span><span class="sxs-lookup"><span data-stu-id="13d18-129">The `body` Tag Helper Component can inject a `<script>` element into the `<body>` element.</span></span> <span data-ttu-id="13d18-130">O código a seguir demonstra essa técnica:</span><span class="sxs-lookup"><span data-stu-id="13d18-130">The following code demonstrates this technique:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

<span data-ttu-id="13d18-131">Um arquivo HTML separado é usado para armazenar o elemento `<script>`.</span><span class="sxs-lookup"><span data-stu-id="13d18-131">A separate HTML file is used to store the `<script>` element.</span></span> <span data-ttu-id="13d18-132">O arquivo HTML torna o código mais limpo e mais sustentável.</span><span class="sxs-lookup"><span data-stu-id="13d18-132">The HTML file makes the code cleaner and more maintainable.</span></span> <span data-ttu-id="13d18-133">O código anterior lê o conteúdo de *TagHelpers/Templates/AddressToolTipScript.html* e acrescenta-o com a saída do Auxiliar de Marca.</span><span class="sxs-lookup"><span data-stu-id="13d18-133">The preceding code reads the contents of *TagHelpers/Templates/AddressToolTipScript.html* and appends it with the Tag Helper output.</span></span> <span data-ttu-id="13d18-134">O arquivo *AddressToolTipScript.html* inclui a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="13d18-134">The *AddressToolTipScript.html* file includes the following markup:</span></span>

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

<span data-ttu-id="13d18-135">O código anterior associa um [widget de Dica de ferramenta de inicialização](https://getbootstrap.com/docs/3.3/javascript/#tooltips) a qualquer elemento `<address>` que inclua um atributo `printable`.</span><span class="sxs-lookup"><span data-stu-id="13d18-135">The preceding code binds a [Bootstrap tooltip widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) to any `<address>` element that includes a `printable` attribute.</span></span> <span data-ttu-id="13d18-136">O efeito é visível quando um ponteiro do mouse focaliza o elemento.</span><span class="sxs-lookup"><span data-stu-id="13d18-136">The effect is visible when a mouse pointer hovers over the element.</span></span>

## <a name="register-a-component"></a><span data-ttu-id="13d18-137">Registrar um componente</span><span class="sxs-lookup"><span data-stu-id="13d18-137">Register a Component</span></span>

<span data-ttu-id="13d18-138">Um Componente do Auxiliar de Marca precisa ser adicionado à coleção de Componentes do Auxiliar de Marca do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="13d18-138">A Tag Helper Component must be added to the app's Tag Helper Components collection.</span></span> <span data-ttu-id="13d18-139">Há três maneiras de adicioná-lo à coleção:</span><span class="sxs-lookup"><span data-stu-id="13d18-139">There are three ways to add to the collection:</span></span>

* [<span data-ttu-id="13d18-140">Registro por contêiner de serviços</span><span class="sxs-lookup"><span data-stu-id="13d18-140">Registration via services container</span></span>](#registration-via-services-container)
* [<span data-ttu-id="13d18-141">Registro por arquivo Razor</span><span class="sxs-lookup"><span data-stu-id="13d18-141">Registration via Razor file</span></span>](#registration-via-razor-file)
* [<span data-ttu-id="13d18-142">Registro por Modelo de página ou controlador</span><span class="sxs-lookup"><span data-stu-id="13d18-142">Registration via Page Model or controller</span></span>](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a><span data-ttu-id="13d18-143">Registro por contêiner de serviços</span><span class="sxs-lookup"><span data-stu-id="13d18-143">Registration via services container</span></span>

<span data-ttu-id="13d18-144">Se a classe do Componente do Auxiliar de Marca não for gerenciada com <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, ela precisará ser registrada com o sistema de [DI (injeção de dependência)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="13d18-144">If the Tag Helper Component class isn't managed with <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, it must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) system.</span></span> <span data-ttu-id="13d18-145">O código `Startup.ConfigureServices` a seguir registra as classes `AddressStyleTagHelperComponent` e `AddressScriptTagHelperComponent` com um [tempo de vida transitório](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span><span class="sxs-lookup"><span data-stu-id="13d18-145">The following `Startup.ConfigureServices` code registers the `AddressStyleTagHelperComponent` and `AddressScriptTagHelperComponent` classes with a [transient lifetime](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a><span data-ttu-id="13d18-146">Registro por arquivo Razor</span><span class="sxs-lookup"><span data-stu-id="13d18-146">Registration via Razor file</span></span>

<span data-ttu-id="13d18-147">Se o Componente do Auxiliar de Marca não estiver registrado com DI, ele poderá ser registrado por uma página do Razor Pages ou uma exibição do MVC.</span><span class="sxs-lookup"><span data-stu-id="13d18-147">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page or an MVC view.</span></span> <span data-ttu-id="13d18-148">Essa técnica é usada para controlar a marcação injetada e o pedido de execução do componente de um arquivo Razor.</span><span class="sxs-lookup"><span data-stu-id="13d18-148">This technique is used for controlling the injected markup and the component execution order from a Razor file.</span></span>

<span data-ttu-id="13d18-149">`ITagHelperComponentManager` é usado para adicionar Componentes do Auxiliar de Marca ou removê-los do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="13d18-149">`ITagHelperComponentManager` is used to add Tag Helper Components or remove them from the app.</span></span> <span data-ttu-id="13d18-150">O código a seguir demonstra essa técnica com `AddressTagHelperComponent`:</span><span class="sxs-lookup"><span data-stu-id="13d18-150">The following code demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

<span data-ttu-id="13d18-151">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="13d18-151">In the preceding code:</span></span>

* <span data-ttu-id="13d18-152">A diretiva `@inject` fornece uma instância de `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="13d18-152">The `@inject` directive provides an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="13d18-153">A instância é atribuída a uma variável chamada `manager` para acesso downstream no arquivo Razor.</span><span class="sxs-lookup"><span data-stu-id="13d18-153">The instance is assigned to a variable named `manager` for access downstream in the Razor file.</span></span>
* <span data-ttu-id="13d18-154">Uma instância do `AddressTagHelperComponent` é adicionada à coleção de Componentes do Auxiliar de Marca do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="13d18-154">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

<span data-ttu-id="13d18-155">`AddressTagHelperComponent` é modificado para acomodar um construtor que aceita os parâmetros `markup` e `order`:</span><span class="sxs-lookup"><span data-stu-id="13d18-155">`AddressTagHelperComponent` is modified to accommodate a constructor that accepts the `markup` and `order` parameters:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

<span data-ttu-id="13d18-156">O parâmetro `markup` fornecido é usado em `ProcessAsync` da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="13d18-156">The provided `markup` parameter is used in `ProcessAsync` as follows:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a><span data-ttu-id="13d18-157">Registro por Modelo de página ou controlador</span><span class="sxs-lookup"><span data-stu-id="13d18-157">Registration via Page Model or controller</span></span>

<span data-ttu-id="13d18-158">Se o Componente do Auxiliar de Marca não estiver registrado com DI, ele poderá ser registrado por um modelo de página do Razor Pages ou um controlador do MVC.</span><span class="sxs-lookup"><span data-stu-id="13d18-158">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page model or an MVC controller.</span></span> <span data-ttu-id="13d18-159">Essa técnica é útil para separar a lógica C# de arquivos Razor.</span><span class="sxs-lookup"><span data-stu-id="13d18-159">This technique is useful for separating C# logic from Razor files.</span></span>

<span data-ttu-id="13d18-160">A injeção do construtor é usada para acessar uma instância de `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="13d18-160">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="13d18-161">Um Componente do Auxiliar de Marca é adicionado à coleção de Componentes do Auxiliar de Marca da instância.</span><span class="sxs-lookup"><span data-stu-id="13d18-161">The Tag Helper Component is added to the instance's Tag Helper Components collection.</span></span> <span data-ttu-id="13d18-162">O modelo de página do Razor Pages a seguir demonstra essa técnica com `AddressTagHelperComponent`:</span><span class="sxs-lookup"><span data-stu-id="13d18-162">The following Razor Pages page model demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

<span data-ttu-id="13d18-163">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="13d18-163">In the preceding code:</span></span>

* <span data-ttu-id="13d18-164">A injeção do construtor é usada para acessar uma instância de `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="13d18-164">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span>
* <span data-ttu-id="13d18-165">Uma instância do `AddressTagHelperComponent` é adicionada à coleção de Componentes do Auxiliar de Marca do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="13d18-165">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

## <a name="create-a-component"></a><span data-ttu-id="13d18-166">Criar um componente</span><span class="sxs-lookup"><span data-stu-id="13d18-166">Create a Component</span></span>

<span data-ttu-id="13d18-167">Para criar um Componente do Auxiliar de Marca personalizado:</span><span class="sxs-lookup"><span data-stu-id="13d18-167">To create a custom Tag Helper Component:</span></span>

* <span data-ttu-id="13d18-168">Crie uma classe pública derivada de <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span><span class="sxs-lookup"><span data-stu-id="13d18-168">Create a public class deriving from <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span></span>
* <span data-ttu-id="13d18-169">Aplique um atributo [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) à classe.</span><span class="sxs-lookup"><span data-stu-id="13d18-169">Apply an [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) attribute to the class.</span></span> <span data-ttu-id="13d18-170">Especifique o nome do elemento HTML de destino.</span><span class="sxs-lookup"><span data-stu-id="13d18-170">Specify the name of the target HTML element.</span></span>
* <span data-ttu-id="13d18-171">*Opcional*: aplique um atributo [[EditorBrowsable(EditorBrowsableState.Never)]](xref:System.ComponentModel.EditorBrowsableAttribute) à classe para suprimir a exibição do tipo no IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="13d18-171">*Optional*: Apply an [[EditorBrowsable(EditorBrowsableState.Never)]](xref:System.ComponentModel.EditorBrowsableAttribute) attribute to the class to suppress the type's display in IntelliSense.</span></span>

<span data-ttu-id="13d18-172">O código a seguir cria um Componente do Auxiliar de Marca personalizado que tem como destino o elemento HTML `<address>`:</span><span class="sxs-lookup"><span data-stu-id="13d18-172">The following code creates a custom Tag Helper Component that targets the `<address>` HTML element:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

<span data-ttu-id="13d18-173">Usar o Componente do Auxiliar de Marca `address` personalizado para inserir uma marcação HTML da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="13d18-173">Use the custom `address` Tag Helper Component to inject HTML markup as follows:</span></span>

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
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

<span data-ttu-id="13d18-174">O método `ProcessAsync` anterior injeta o HTML fornecido para <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> no elemento `<address>` correspondente.</span><span class="sxs-lookup"><span data-stu-id="13d18-174">The preceding `ProcessAsync` method injects the HTML provided to <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> into the matching `<address>` element.</span></span> <span data-ttu-id="13d18-175">A injeção ocorre quando:</span><span class="sxs-lookup"><span data-stu-id="13d18-175">The injection occurs when:</span></span>

* <span data-ttu-id="13d18-176">O valor da propriedade `TagName` do contexto de execução é igual a `address`.</span><span class="sxs-lookup"><span data-stu-id="13d18-176">The execution context's `TagName` property value equals `address`.</span></span>
* <span data-ttu-id="13d18-177">O elemento `<address>` correspondente tem um atributo `printable`.</span><span class="sxs-lookup"><span data-stu-id="13d18-177">The corresponding `<address>` element has a `printable` attribute.</span></span>

<span data-ttu-id="13d18-178">Por exemplo, a instrução `if` é avaliada como verdadeira ao processar o seguinte elemento `<address>`:</span><span class="sxs-lookup"><span data-stu-id="13d18-178">For example, the `if` statement evaluates to true when processing the following `<address>` element:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a><span data-ttu-id="13d18-179">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="13d18-179">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
