---
title: ASP.NET Core a interoperabilidade de JavaScript mais incrivelmente
author: guardrex
description: Saiba como invocar funções JavaScript de métodos .NET e .NET do JavaScript em aplicativos mais Incrivelmenteos.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/07/2019
uid: blazor/javascript-interop
ms.openlocfilehash: fa485420c01e6a6d4181f733d6848de08ffca730
ms.sourcegitcommit: e7c56e8da5419bbc20b437c2dd531dedf9b0dc6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70878361"
---
# <a name="aspnet-core-blazor-javascript-interop"></a><span data-ttu-id="53781-103">ASP.NET Core a interoperabilidade de JavaScript mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="53781-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="53781-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="53781-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="53781-105">Um aplicativo mais incrivelmente pode invocar funções JavaScript de métodos .NET e .NET do código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="53781-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="53781-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="53781-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="53781-107">Invocar funções JavaScript de métodos .NET</span><span class="sxs-lookup"><span data-stu-id="53781-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="53781-108">Há ocasiões em que o código .NET é necessário para chamar uma função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="53781-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="53781-109">Por exemplo, uma chamada de JavaScript pode expor recursos de navegador ou funcionalidade de uma biblioteca JavaScript para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="53781-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span>

<span data-ttu-id="53781-110">Para chamar o JavaScript do .net, use a `IJSRuntime` abstração.</span><span class="sxs-lookup"><span data-stu-id="53781-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="53781-111">O `InvokeAsync<T>` método usa um identificador para a função JavaScript que você deseja invocar junto com qualquer número de argumentos serializáveis para JSON.</span><span class="sxs-lookup"><span data-stu-id="53781-111">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="53781-112">O identificador de função é relativo ao escopo global (`window`).</span><span class="sxs-lookup"><span data-stu-id="53781-112">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="53781-113">Se você quiser chamar `window.someScope.someFunction`, o identificador será. `someScope.someFunction`</span><span class="sxs-lookup"><span data-stu-id="53781-113">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="53781-114">Não é necessário registrar a função antes que ela seja chamada.</span><span class="sxs-lookup"><span data-stu-id="53781-114">There's no need to register the function before it's called.</span></span> <span data-ttu-id="53781-115">O tipo `T` de retorno também deve ser serializável em JSON.</span><span class="sxs-lookup"><span data-stu-id="53781-115">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="53781-116">Para aplicativos do lado do servidor:</span><span class="sxs-lookup"><span data-stu-id="53781-116">For server-side apps:</span></span>

* <span data-ttu-id="53781-117">Várias solicitações de usuário são processadas pelo aplicativo do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="53781-117">Multiple user requests are processed by the server-side app.</span></span> <span data-ttu-id="53781-118">Não chame `JSRuntime.Current` em um componente para invocar funções JavaScript.</span><span class="sxs-lookup"><span data-stu-id="53781-118">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="53781-119">Injetar `IJSRuntime` a abstração e usar o objeto injetado para emitir chamadas de interoperabilidade JavaScript.</span><span class="sxs-lookup"><span data-stu-id="53781-119">Inject the `IJSRuntime` abstraction and use the injected object to issue JavaScript interop calls.</span></span>
* <span data-ttu-id="53781-120">Embora um aplicativo mais incrivelmente seja o pré-processamento, não é possível chamar o JavaScript porque uma conexão com o navegador não foi estabelecida.</span><span class="sxs-lookup"><span data-stu-id="53781-120">While a Blazor app is prerendering, calling into JavaScript isn't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="53781-121">Para obter mais informações, consulte a seção [detectar quando um aplicativo mais incrivelmente está sendo renderizado](#detect-when-a-blazor-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="53781-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="53781-122">O exemplo a seguir é baseado em [textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), um decodificador experimental baseado em JavaScript.</span><span class="sxs-lookup"><span data-stu-id="53781-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="53781-123">O exemplo demonstra como invocar uma função JavaScript a partir C# de um método.</span><span class="sxs-lookup"><span data-stu-id="53781-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="53781-124">A função JavaScript aceita uma matriz de bytes de C# um método, decodifica a matriz e retorna o texto para o componente para exibição.</span><span class="sxs-lookup"><span data-stu-id="53781-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="53781-125">Dentro do `<head>` elemento de *wwwroot/index.html* (mais de um lado do cliente) ou de *pages/_Host. cshtml* (no lado do servidor mais alto), forneça uma `TextDecoder` função que o usa para decodificar uma matriz passada:</span><span class="sxs-lookup"><span data-stu-id="53781-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor client-side) or *Pages/_Host.cshtml* (Blazor server-side), provide a function that uses `TextDecoder` to decode a passed array:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

<span data-ttu-id="53781-126">O código JavaScript, como o código mostrado no exemplo anterior, também pode ser carregado de um arquivo JavaScript ( *. js*) com uma referência ao arquivo de script:</span><span class="sxs-lookup"><span data-stu-id="53781-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="53781-127">O seguinte componente:</span><span class="sxs-lookup"><span data-stu-id="53781-127">The following component:</span></span>

* <span data-ttu-id="53781-128">Invoca a `ConvertArray` função JavaScript usando `JsRuntime` quando um botão de componente (**matriz de conversão**) é selecionado.</span><span class="sxs-lookup"><span data-stu-id="53781-128">Invokes the `ConvertArray` JavaScript function using `JsRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="53781-129">Depois que a função JavaScript é chamada, a matriz passada é convertida em uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="53781-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="53781-130">A cadeia de caracteres é retornada para o componente para exibição.</span><span class="sxs-lookup"><span data-stu-id="53781-130">The string is returned to the component for display.</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

<span data-ttu-id="53781-131">Para usar a `IJSRuntime` abstração, adote qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="53781-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="53781-132">Injetar `IJSRuntime` a abstração no componente Razor ( *. Razor*):</span><span class="sxs-lookup"><span data-stu-id="53781-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* <span data-ttu-id="53781-133">Injetar `IJSRuntime` a abstração em uma classe ( *. cs*):</span><span class="sxs-lookup"><span data-stu-id="53781-133">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* <span data-ttu-id="53781-134">Para a geração de conteúdo dinâmico com [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), `[Inject]` use o atributo:</span><span class="sxs-lookup"><span data-stu-id="53781-134">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="53781-135">No aplicativo de exemplo do lado do cliente que acompanha este tópico, duas funções JavaScript estão disponíveis para o aplicativo do lado do cliente que interagem com o DOM para receber entrada do usuário e exibir uma mensagem de boas-vindas:</span><span class="sxs-lookup"><span data-stu-id="53781-135">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the client-side app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="53781-136">`showPrompt`&ndash; Produz uma solicitação para aceitar a entrada do usuário (o nome do usuário) e retorna o nome para o chamador.</span><span class="sxs-lookup"><span data-stu-id="53781-136">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="53781-137">`displayWelcome`Atribui uma mensagem de boas-vindas do chamador a um objeto DOM com `id` um `welcome`de. &ndash;</span><span class="sxs-lookup"><span data-stu-id="53781-137">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="53781-138">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="53781-138">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="53781-139">Coloque a `<script>` marca que faz referência ao arquivo JavaScript no arquivo *wwwroot/index.html* (mais alto do cliente) ou ao arquivo *pages/_Host. cshtml* (no lado do servidor).</span><span class="sxs-lookup"><span data-stu-id="53781-139">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor client-side) or *Pages/_Host.cshtml* file (Blazor server-side).</span></span>

<span data-ttu-id="53781-140">*wwwroot/index.html* (No lado do cliente de mais alto):</span><span class="sxs-lookup"><span data-stu-id="53781-140">*wwwroot/index.html* (Blazor client-side):</span></span>

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="53781-141">*Páginas/_Host. cshtml* (No lado do servidor do mais alto):</span><span class="sxs-lookup"><span data-stu-id="53781-141">*Pages/_Host.cshtml* (Blazor server-side):</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/_Host.cshtml?highlight=29)]

<span data-ttu-id="53781-142">Não coloque uma `<script>` marca em um arquivo de componente porque `<script>` a marca não pode ser atualizada dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="53781-142">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="53781-143">Os métodos .NET interoperam com as funções JavaScript no arquivo *exampleJsInterop. js* chamando `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="53781-143">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="53781-144">A `IJSRuntime` abstração é assíncrona para permitir cenários do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="53781-144">The `IJSRuntime` abstraction is asynchronous to allow for server-side scenarios.</span></span> <span data-ttu-id="53781-145">Se o aplicativo for executado no lado do cliente e você quiser invocar uma função JavaScript de forma `IJSInProcessRuntime` síncrona, `Invoke<T>` downcast e chame em seu lugar.</span><span class="sxs-lookup"><span data-stu-id="53781-145">If the app runs client-side and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="53781-146">Recomendamos que a maioria das bibliotecas de interoperabilidade JavaScript use as APIs assíncronas para garantir que as bibliotecas estejam disponíveis em todos os cenários, no lado do cliente ou no servidor.</span><span class="sxs-lookup"><span data-stu-id="53781-146">We recommend that most JavaScript interop libraries use the async APIs to ensure that the libraries are available in all scenarios, client-side or server-side.</span></span>

<span data-ttu-id="53781-147">O aplicativo de exemplo inclui um componente para demonstrar a interoperabilidade JavaScript.</span><span class="sxs-lookup"><span data-stu-id="53781-147">The sample app includes a component to demonstrate JavaScript interop.</span></span> <span data-ttu-id="53781-148">O componente:</span><span class="sxs-lookup"><span data-stu-id="53781-148">The component:</span></span>

* <span data-ttu-id="53781-149">Recebe a entrada do usuário por meio de um prompt do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="53781-149">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="53781-150">Retorna o texto para o componente para processamento.</span><span class="sxs-lookup"><span data-stu-id="53781-150">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="53781-151">Chama uma segunda função JavaScript que interage com o DOM para exibir uma mensagem de boas-vindas.</span><span class="sxs-lookup"><span data-stu-id="53781-151">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="53781-152">*Páginas/JSInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="53781-152">*Pages/JSInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="53781-153">Quando `TriggerJsPrompt` é executado selecionando o botão de **prompt do JavaScript** do componente de gatilho `showPrompt` , a função JavaScript fornecida no arquivo *wwwroot/exampleJsInterop. js* é chamada.</span><span class="sxs-lookup"><span data-stu-id="53781-153">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="53781-154">A `showPrompt` função aceita a entrada do usuário (o nome do usuário), que é codificado em HTML e retornada ao componente.</span><span class="sxs-lookup"><span data-stu-id="53781-154">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="53781-155">O componente armazena o nome do usuário em uma variável local, `name`.</span><span class="sxs-lookup"><span data-stu-id="53781-155">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="53781-156">A cadeia de caracteres `name` armazenada em é incorporada a uma mensagem de boas-vindas, `displayWelcome`que é passada para uma função JavaScript,, que renderiza a mensagem de boas-vindas em uma marca de cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="53781-156">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="53781-157">Chamar uma função JavaScript void</span><span class="sxs-lookup"><span data-stu-id="53781-157">Call a void JavaScript function</span></span>

<span data-ttu-id="53781-158">As funções JavaScript que retornam [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) ou [indefinido](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) são `IJSRuntime.InvokeAsync<object>`chamadas com, `null`que retorna.</span><span class="sxs-lookup"><span data-stu-id="53781-158">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeAsync<object>`, which returns `null`.</span></span>

## <a name="detect-when-a-blazor-app-is-prerendering"></a><span data-ttu-id="53781-159">Detectar quando um aplicativo mais incrivelmente está prerenderizando</span><span class="sxs-lookup"><span data-stu-id="53781-159">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="53781-160">Capturar referências a elementos</span><span class="sxs-lookup"><span data-stu-id="53781-160">Capture references to elements</span></span>

<span data-ttu-id="53781-161">Alguns cenários de [interoperabilidade do JavaScript](xref:blazor/javascript-interop) exigem referências a elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="53781-161">Some [JavaScript interop](xref:blazor/javascript-interop) scenarios require references to HTML elements.</span></span> <span data-ttu-id="53781-162">Por exemplo, uma biblioteca de interface do usuário pode exigir uma referência de elemento para inicialização, ou talvez seja necessário chamar APIs do tipo comando em um elemento `focus` , `play`como ou.</span><span class="sxs-lookup"><span data-stu-id="53781-162">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="53781-163">Capture referências a elementos HTML em um componente usando a seguinte abordagem:</span><span class="sxs-lookup"><span data-stu-id="53781-163">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="53781-164">Adicione um `@ref` atributo ao elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="53781-164">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="53781-165">Defina um campo do tipo `ElementReference` cujo nome corresponde ao valor `@ref` do atributo.</span><span class="sxs-lookup"><span data-stu-id="53781-165">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="53781-166">O exemplo a seguir mostra a captura de uma `username` referência ao `<input>` elemento:</span><span class="sxs-lookup"><span data-stu-id="53781-166">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!NOTE]
> <span data-ttu-id="53781-167">Não **use referências** de elemento capturadas como uma forma de popular ou manipular o dom quando o mais fácil interage com os elementos referenciados.</span><span class="sxs-lookup"><span data-stu-id="53781-167">Do **not** use captured element references as a way of populating or manipulating the DOM when Blazor interacts with the elements referenced.</span></span> <span data-ttu-id="53781-168">Isso pode interferir no modelo de renderização declarativa.</span><span class="sxs-lookup"><span data-stu-id="53781-168">Doing so may interfere with the declarative rendering model.</span></span>

<span data-ttu-id="53781-169">No que diz respeito ao código .net, um `ElementReference` é um identificador opaco.</span><span class="sxs-lookup"><span data-stu-id="53781-169">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="53781-170">A *única* coisa que você pode fazer `ElementReference` com o é passá-lo para o código JavaScript por meio da interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="53781-170">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JavaScript interop.</span></span> <span data-ttu-id="53781-171">Quando você faz isso, o código do lado do JavaScript recebe `HTMLElement` uma instância, que pode ser usada com APIs dom normais.</span><span class="sxs-lookup"><span data-stu-id="53781-171">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="53781-172">Por exemplo, o código a seguir define um método de extensão .NET que permite definir o foco em um elemento:</span><span class="sxs-lookup"><span data-stu-id="53781-172">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="53781-173">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="53781-173">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="53781-174">Use `IJSRuntime.InvokeAsync<T>` e chame `exampleJsFunctions.focusElement` com um `ElementReference` para focalizar um elemento:</span><span class="sxs-lookup"><span data-stu-id="53781-174">Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementReference` to focus an element:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="53781-175">Para usar um método de extensão para concentrar um elemento, crie um método de extensão estático que `IJSRuntime` receba a instância:</span><span class="sxs-lookup"><span data-stu-id="53781-175">To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="53781-176">O método é chamado diretamente no objeto.</span><span class="sxs-lookup"><span data-stu-id="53781-176">The method is called directly on the object.</span></span> <span data-ttu-id="53781-177">O exemplo a seguir pressupõe que o `Focus` método estático está disponível `JsInteropClasses` no namespace:</span><span class="sxs-lookup"><span data-stu-id="53781-177">The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> <span data-ttu-id="53781-178">A `username` variável é populada apenas depois que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="53781-178">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="53781-179">Se um não populado `ElementReference` for passado para o código JavaScript, o código JavaScript receberá um valor de. `null`</span><span class="sxs-lookup"><span data-stu-id="53781-179">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="53781-180">Para manipular referências de elemento após a conclusão da renderização do componente (para definir o foco inicial em um elemento) `OnAfterRenderAsync` , `OnAfterRender` use os métodos de ciclo de vida do [componente](xref:blazor/components#lifecycle-methods)ou.</span><span class="sxs-lookup"><span data-stu-id="53781-180">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:blazor/components#lifecycle-methods).</span></span>

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="53781-181">Invocar métodos .NET de funções JavaScript</span><span class="sxs-lookup"><span data-stu-id="53781-181">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="53781-182">Chamada de método .NET estático</span><span class="sxs-lookup"><span data-stu-id="53781-182">Static .NET method call</span></span>

<span data-ttu-id="53781-183">Para invocar um método .net estático do JavaScript, use `DotNet.invokeMethod` as `DotNet.invokeMethodAsync` funções ou.</span><span class="sxs-lookup"><span data-stu-id="53781-183">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="53781-184">Passe o identificador do método estático que você deseja chamar, o nome do assembly que contém a função e os argumentos.</span><span class="sxs-lookup"><span data-stu-id="53781-184">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="53781-185">A versão assíncrona é preferida para dar suporte a cenários do lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="53781-185">The asynchronous version is preferred to support server-side scenarios.</span></span> <span data-ttu-id="53781-186">Para invocar um método .net do JavaScript, o método .NET deve ser público, estático e ter o `[JSInvokable]` atributo.</span><span class="sxs-lookup"><span data-stu-id="53781-186">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="53781-187">Por padrão, o identificador de método é o nome do método, mas você pode especificar um identificador diferente `JSInvokableAttribute` usando o construtor.</span><span class="sxs-lookup"><span data-stu-id="53781-187">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="53781-188">Atualmente, não há suporte para chamar métodos genéricos abertos.</span><span class="sxs-lookup"><span data-stu-id="53781-188">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="53781-189">O aplicativo de exemplo inclui C# um método para retornar uma matriz `int`de s.</span><span class="sxs-lookup"><span data-stu-id="53781-189">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="53781-190">O `JSInvokable` atributo é aplicado ao método.</span><span class="sxs-lookup"><span data-stu-id="53781-190">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="53781-191">*Páginas/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="53781-191">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="53781-192">O JavaScript servido para o cliente invoca o C# método .net.</span><span class="sxs-lookup"><span data-stu-id="53781-192">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="53781-193">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="53781-193">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="53781-194">Quando o botão **disparar o método estático do .net ReturnArrayAsync** for selecionado, examine a saída do console nas ferramentas de desenvolvedor da Web do navegador.</span><span class="sxs-lookup"><span data-stu-id="53781-194">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="53781-195">A saída do console é:</span><span class="sxs-lookup"><span data-stu-id="53781-195">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="53781-196">O quarto valor de matriz é enviado por push para`data.push(4);`a matriz ( `ReturnArrayAsync`) retornada por.</span><span class="sxs-lookup"><span data-stu-id="53781-196">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="53781-197">Chamada de método de instância</span><span class="sxs-lookup"><span data-stu-id="53781-197">Instance method call</span></span>

<span data-ttu-id="53781-198">Você também pode chamar os métodos de instância do .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="53781-198">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="53781-199">Para invocar um método de instância .NET do JavaScript:</span><span class="sxs-lookup"><span data-stu-id="53781-199">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="53781-200">Passe a instância do .net para JavaScript, encapsulando- `DotNetObjectReference` a em uma instância.</span><span class="sxs-lookup"><span data-stu-id="53781-200">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectReference` instance.</span></span> <span data-ttu-id="53781-201">A instância do .NET é passada por referência ao JavaScript.</span><span class="sxs-lookup"><span data-stu-id="53781-201">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="53781-202">Invoque os métodos de instância do .net na instância `invokeMethod` usando `invokeMethodAsync` as funções ou.</span><span class="sxs-lookup"><span data-stu-id="53781-202">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="53781-203">A instância do .NET também pode ser passada como um argumento ao invocar outros métodos .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="53781-203">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="53781-204">O aplicativo de exemplo registra as mensagens no console do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="53781-204">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="53781-205">Para os exemplos a seguir demonstrados pelo aplicativo de exemplo, examine a saída do console do navegador nas ferramentas de desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="53781-205">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="53781-206">Quando o botão do **método de instância .net de gatilho HelloHelper. sayHello** é selecionado, `ExampleJsInterop.CallHelloHelperSayHello` é chamado e `Blazor`passa um nome para o método.</span><span class="sxs-lookup"><span data-stu-id="53781-206">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="53781-207">*Páginas/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="53781-207">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

<span data-ttu-id="53781-208">`CallHelloHelperSayHello`Invoca a função `sayHello` JavaScript com uma nova instância do `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="53781-208">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="53781-209">*JsInteropClasses/ExampleJsInterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="53781-209">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="53781-210">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="53781-210">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="53781-211">O nome é passado para `HelloHelper`o construtor de, que define `HelloHelper.Name` a propriedade.</span><span class="sxs-lookup"><span data-stu-id="53781-211">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="53781-212">Quando a função `sayHello` JavaScript é executada, `HelloHelper.SayHello` retorna a `Hello, {Name}!` mensagem, que é gravada no console pela função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="53781-212">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="53781-213">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="53781-213">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="53781-214">Saída do console nas ferramentas de desenvolvedor da Web do navegador:</span><span class="sxs-lookup"><span data-stu-id="53781-214">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="53781-215">Compartilhar código de interoperabilidade em uma biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="53781-215">Share interop code in a class library</span></span>

<span data-ttu-id="53781-216">O código de interoperabilidade JavaScript pode ser incluído em uma biblioteca de classes, que permite que você compartilhe o código em um pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="53781-216">JavaScript interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="53781-217">A biblioteca de classes lida com a inserção de recursos JavaScript no assembly compilado.</span><span class="sxs-lookup"><span data-stu-id="53781-217">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="53781-218">Os arquivos JavaScript são colocados na pasta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="53781-218">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="53781-219">As ferramentas cuidam da inserção dos recursos quando a biblioteca é criada.</span><span class="sxs-lookup"><span data-stu-id="53781-219">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="53781-220">O pacote NuGet criado é referenciado no arquivo de projeto do aplicativo da mesma forma que qualquer pacote NuGet é referenciado.</span><span class="sxs-lookup"><span data-stu-id="53781-220">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="53781-221">Depois que o pacote é restaurado, o código do aplicativo pode chamar o JavaScript C#como se fosse.</span><span class="sxs-lookup"><span data-stu-id="53781-221">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="53781-222">Para obter mais informações, consulte <xref:blazor/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="53781-222">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="53781-223">Proteger chamadas Interop JS</span><span class="sxs-lookup"><span data-stu-id="53781-223">Harden JS interop calls</span></span>

<span data-ttu-id="53781-224">A interoperabilidade JS pode falhar devido a erros de rede e deve ser tratada como não confiável.</span><span class="sxs-lookup"><span data-stu-id="53781-224">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="53781-225">Por padrão, um aplicativo de servidor mais incrivelmente atinge as chamadas de interoperabilidade JS no servidor após um minuto.</span><span class="sxs-lookup"><span data-stu-id="53781-225">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="53781-226">Se um aplicativo puder tolerar um tempo limite mais agressivo, como 10 segundos, defina o tempo limite usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="53781-226">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="53781-227">Globalmente no `Startup.ConfigureServices`, especifique o tempo limite:</span><span class="sxs-lookup"><span data-stu-id="53781-227">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="53781-228">Por invocação no código do componente, uma única chamada pode especificar o tempo limite:</span><span class="sxs-lookup"><span data-stu-id="53781-228">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="53781-229">Para obter mais informações sobre esgotamento de <xref:security/blazor/server-side>recursos, consulte.</span><span class="sxs-lookup"><span data-stu-id="53781-229">For more information on resource exhaustion, see <xref:security/blazor/server-side>.</span></span>
