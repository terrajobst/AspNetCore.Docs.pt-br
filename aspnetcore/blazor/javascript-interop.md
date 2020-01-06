---
title: ASP.NET Core interoperabilidade Blazor JavaScript
author: guardrex
description: Saiba como invocar funções JavaScript de métodos .NET e .NET do JavaScript em Blazor aplicativos.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/javascript-interop
ms.openlocfilehash: 2350870f8548a9c8df324182883a105706c12c20
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355743"
---
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a><span data-ttu-id="f4a26-103">ASP.NET Core interoperabilidade Blazor JavaScript</span><span class="sxs-lookup"><span data-stu-id="f4a26-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="f4a26-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f4a26-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="f4a26-105">Um aplicativo Blazor pode invocar funções JavaScript de métodos .NET e .NET do código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f4a26-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="f4a26-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f4a26-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="f4a26-107">Invocar funções JavaScript de métodos .NET</span><span class="sxs-lookup"><span data-stu-id="f4a26-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="f4a26-108">Há ocasiões em que o código .NET é necessário para chamar uma função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f4a26-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="f4a26-109">Por exemplo, uma chamada de JavaScript pode expor recursos de navegador ou funcionalidade de uma biblioteca JavaScript para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="f4a26-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span> <span data-ttu-id="f4a26-110">Esse cenário é chamado de *interoperabilidade JavaScript* (*js Interop*).</span><span class="sxs-lookup"><span data-stu-id="f4a26-110">This scenario is called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="f4a26-111">Para chamar o JavaScript do .NET, use a abstração de `IJSRuntime`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-111">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="f4a26-112">Para emitir chamadas de interoperabilidade JS, insira a abstração de `IJSRuntime` em seu componente.</span><span class="sxs-lookup"><span data-stu-id="f4a26-112">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="f4a26-113">O método `InvokeAsync<T>` usa um identificador para a função JavaScript que você deseja invocar junto com qualquer número de argumentos serializáveis para JSON.</span><span class="sxs-lookup"><span data-stu-id="f4a26-113">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="f4a26-114">O identificador de função é relativo ao escopo global (`window`).</span><span class="sxs-lookup"><span data-stu-id="f4a26-114">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="f4a26-115">Se você quiser chamar `window.someScope.someFunction`, o identificador será `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-115">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="f4a26-116">Não é necessário registrar a função antes que ela seja chamada.</span><span class="sxs-lookup"><span data-stu-id="f4a26-116">There's no need to register the function before it's called.</span></span> <span data-ttu-id="f4a26-117">O tipo de retorno `T` também deve ser serializável como JSON.</span><span class="sxs-lookup"><span data-stu-id="f4a26-117">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="f4a26-118">`T` deve corresponder ao tipo .NET que melhor mapeia para o tipo JSON retornado.</span><span class="sxs-lookup"><span data-stu-id="f4a26-118">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="f4a26-119">Para aplicativos do Blazor Server com pré-processamento habilitado, não é possível chamar o JavaScript durante o pré-processamento inicial.</span><span class="sxs-lookup"><span data-stu-id="f4a26-119">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="f4a26-120">As chamadas de interoperabilidade do JavaScript devem ser adiadas até que a conexão com o navegador seja estabelecida.</span><span class="sxs-lookup"><span data-stu-id="f4a26-120">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="f4a26-121">Para obter mais informações, consulte a seção [detectar quando um aplicativo de Blazor está sendo renderizado](#detect-when-a-blazor-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="f4a26-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="f4a26-122">O exemplo a seguir é baseado em [textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), um decodificador experimental baseado em JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f4a26-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="f4a26-123">O exemplo demonstra como invocar uma função JavaScript a partir C# de um método.</span><span class="sxs-lookup"><span data-stu-id="f4a26-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="f4a26-124">A função JavaScript aceita uma matriz de bytes de C# um método, decodifica a matriz e retorna o texto para o componente para exibição.</span><span class="sxs-lookup"><span data-stu-id="f4a26-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="f4a26-125">Dentro do elemento `<head>` de *wwwroot/index.html* (Blazor Webassembly) ou *pages/_Host. cshtml* (servidorBlazor), forneça uma função JavaScript que usa `TextDecoder` para decodificar uma matriz passada e retornar o valor decodificado:</span><span class="sxs-lookup"><span data-stu-id="f4a26-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="f4a26-126">O código JavaScript, como o código mostrado no exemplo anterior, também pode ser carregado de um arquivo JavaScript ( *. js*) com uma referência ao arquivo de script:</span><span class="sxs-lookup"><span data-stu-id="f4a26-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="f4a26-127">O seguinte componente:</span><span class="sxs-lookup"><span data-stu-id="f4a26-127">The following component:</span></span>

* <span data-ttu-id="f4a26-128">Invoca a função `convertArray` JavaScript usando `JSRuntime` quando um botão de componente (**matriz de conversão**) é selecionado.</span><span class="sxs-lookup"><span data-stu-id="f4a26-128">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="f4a26-129">Depois que a função JavaScript é chamada, a matriz passada é convertida em uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="f4a26-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="f4a26-130">A cadeia de caracteres é retornada para o componente para exibição.</span><span class="sxs-lookup"><span data-stu-id="f4a26-130">The string is returned to the component for display.</span></span>

[!code-razor[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a><span data-ttu-id="f4a26-131">Uso de IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="f4a26-131">Use of IJSRuntime</span></span>

<span data-ttu-id="f4a26-132">Para usar a abstração de `IJSRuntime`, adote qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="f4a26-132">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="f4a26-133">Injetar a abstração de `IJSRuntime` no componente Razor ( *. Razor*):</span><span class="sxs-lookup"><span data-stu-id="f4a26-133">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="f4a26-134">Dentro do elemento `<head>` de *wwwroot/index.html* (Blazor Webassembly) ou *pages/_Host. cshtml* (servidorBlazor), forneça uma `handleTickerChanged` função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f4a26-134">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="f4a26-135">A função é chamada com `IJSRuntime.InvokeVoidAsync` e não retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="f4a26-135">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="f4a26-136">Injetar a abstração de `IJSRuntime` em uma classe ( *. cs*):</span><span class="sxs-lookup"><span data-stu-id="f4a26-136">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="f4a26-137">Dentro do elemento `<head>` de *wwwroot/index.html* (Blazor Webassembly) ou *pages/_Host. cshtml* (servidorBlazor), forneça uma `handleTickerChanged` função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f4a26-137">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="f4a26-138">A função é chamada com `JSRuntime.InvokeAsync` e retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="f4a26-138">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="f4a26-139">Para a geração de conteúdo dinâmico com [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use o atributo `[Inject]`:</span><span class="sxs-lookup"><span data-stu-id="f4a26-139">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="f4a26-140">No aplicativo de exemplo do lado do cliente que acompanha este tópico, duas funções JavaScript estão disponíveis para o aplicativo que interagem com o DOM para receber a entrada do usuário e exibir uma mensagem de boas-vindas:</span><span class="sxs-lookup"><span data-stu-id="f4a26-140">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="f4a26-141">`showPrompt` &ndash; produz um prompt para aceitar a entrada do usuário (o nome do usuário) e retorna o nome para o chamador.</span><span class="sxs-lookup"><span data-stu-id="f4a26-141">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="f4a26-142">`displayWelcome` &ndash; atribui uma mensagem de boas-vindas do chamador a um objeto DOM com uma `id` de `welcome`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-142">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="f4a26-143">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="f4a26-143">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="f4a26-144">Coloque a `<script>` marca que faz referência ao arquivo JavaScript no arquivo *wwwroot/index.html* (Blazor Webassembly) ou ao arquivo *pages/_Host. cshtml* (servidorBlazor).</span><span class="sxs-lookup"><span data-stu-id="f4a26-144">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="f4a26-145">*wwwroot/index.html* (Blazor Webassembly):</span><span class="sxs-lookup"><span data-stu-id="f4a26-145">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="f4a26-146">*Pages/_Host. cshtml* (servidorBlazor):</span><span class="sxs-lookup"><span data-stu-id="f4a26-146">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=21)]

<span data-ttu-id="f4a26-147">Não coloque uma marca de `<script>` em um arquivo de componente porque a marca de `<script>` não pode ser atualizada dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="f4a26-147">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="f4a26-148">Os métodos .NET interoperam com as funções JavaScript no arquivo *exampleJsInterop. js* chamando `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-148">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="f4a26-149">A abstração de `IJSRuntime` é assíncrona para permitir cenários de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="f4a26-149">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="f4a26-150">Se o aplicativo for um aplicativo Webassembly Blazor e você quiser invocar uma função JavaScript de forma síncrona, downcast para `IJSInProcessRuntime` e chame `Invoke<T>` em vez disso.</span><span class="sxs-lookup"><span data-stu-id="f4a26-150">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="f4a26-151">Recomendamos que a maioria das bibliotecas de interoperabilidade do JS use as APIs assíncronas para garantir que as bibliotecas estejam disponíveis em todos os cenários.</span><span class="sxs-lookup"><span data-stu-id="f4a26-151">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="f4a26-152">O aplicativo de exemplo inclui um componente para demonstrar a interoperabilidade do JS.</span><span class="sxs-lookup"><span data-stu-id="f4a26-152">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="f4a26-153">O componente:</span><span class="sxs-lookup"><span data-stu-id="f4a26-153">The component:</span></span>

* <span data-ttu-id="f4a26-154">Recebe a entrada do usuário por meio de um prompt do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f4a26-154">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="f4a26-155">Retorna o texto para o componente para processamento.</span><span class="sxs-lookup"><span data-stu-id="f4a26-155">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="f4a26-156">Chama uma segunda função JavaScript que interage com o DOM para exibir uma mensagem de boas-vindas.</span><span class="sxs-lookup"><span data-stu-id="f4a26-156">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="f4a26-157">*Páginas/JSInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="f4a26-157">*Pages/JSInterop.razor*:</span></span>

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        // showPrompt is implemented in wwwroot/exampleJsInterop.js
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");
        // displayWelcome is implemented in wwwroot/exampleJsInterop.js
        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. <span data-ttu-id="f4a26-158">Quando `TriggerJsPrompt` é executado selecionando o botão de **prompt de JavaScript** do componente de gatilho, a função JavaScript `showPrompt` fornecida no arquivo *wwwroot/exampleJsInterop. js* é chamada.</span><span class="sxs-lookup"><span data-stu-id="f4a26-158">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="f4a26-159">A função `showPrompt` aceita a entrada do usuário (o nome do usuário), que é codificado em HTML e retornada ao componente.</span><span class="sxs-lookup"><span data-stu-id="f4a26-159">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="f4a26-160">O componente armazena o nome do usuário em uma variável local, `name`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-160">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="f4a26-161">A cadeia de caracteres armazenada em `name` é incorporada a uma mensagem de boas-vindas, que é passada para uma função JavaScript, `displayWelcome`, que renderiza a mensagem de boas-vindas em uma marca de cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="f4a26-161">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="f4a26-162">Chamar uma função JavaScript void</span><span class="sxs-lookup"><span data-stu-id="f4a26-162">Call a void JavaScript function</span></span>

<span data-ttu-id="f4a26-163">As funções JavaScript que retornam [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) ou [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) são chamadas com `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-163">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a><span data-ttu-id="f4a26-164">Detectar quando um aplicativo Blazor está sendo renderizado</span><span class="sxs-lookup"><span data-stu-id="f4a26-164">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="f4a26-165">Capturar referências a elementos</span><span class="sxs-lookup"><span data-stu-id="f4a26-165">Capture references to elements</span></span>

<span data-ttu-id="f4a26-166">Alguns cenários de interoperabilidade JS exigem referências a elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="f4a26-166">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="f4a26-167">Por exemplo, uma biblioteca de interface do usuário pode exigir uma referência de elemento para inicialização ou você pode precisar chamar APIs do tipo comando em um elemento, como `focus` ou `play`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-167">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="f4a26-168">Capture referências a elementos HTML em um componente usando a seguinte abordagem:</span><span class="sxs-lookup"><span data-stu-id="f4a26-168">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="f4a26-169">Adicione um atributo `@ref` ao elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="f4a26-169">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="f4a26-170">Defina um campo do tipo `ElementReference` cujo nome corresponde ao valor do atributo `@ref`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-170">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="f4a26-171">O exemplo a seguir mostra a captura de uma referência para o elemento `username` `<input>`:</span><span class="sxs-lookup"><span data-stu-id="f4a26-171">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="f4a26-172">Use apenas uma referência de elemento para converter o conteúdo de um elemento vazio que não interaja com Blazor.</span><span class="sxs-lookup"><span data-stu-id="f4a26-172">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="f4a26-173">Esse cenário é útil quando uma API de terceiros fornece conteúdo para o elemento.</span><span class="sxs-lookup"><span data-stu-id="f4a26-173">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="f4a26-174">Como Blazor não interage com o elemento, não há possibilidade de um conflito entre a representação Blazordo elemento e o DOM.</span><span class="sxs-lookup"><span data-stu-id="f4a26-174">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="f4a26-175">No exemplo a seguir, é *perigoso* mutar o conteúdo da lista não ordenada (`ul`) porque Blazor interage com o dom para preencher os itens de lista deste elemento (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="f4a26-175">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="f4a26-176">Se a interoperabilidade do JS converter o conteúdo do elemento `MyList` e Blazor tentar aplicar diffs ao elemento, as diferenças não corresponderão ao DOM.</span><span class="sxs-lookup"><span data-stu-id="f4a26-176">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="f4a26-177">No que diz respeito ao código .NET, um `ElementReference` é um identificador opaco.</span><span class="sxs-lookup"><span data-stu-id="f4a26-177">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="f4a26-178">A *única* coisa que você pode fazer com `ElementReference` é passá-lo para o código JavaScript por meio da interoperabilidade do js.</span><span class="sxs-lookup"><span data-stu-id="f4a26-178">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="f4a26-179">Quando você faz isso, o código do lado do JavaScript recebe uma instância de `HTMLElement`, que pode ser usada com APIs DOM normais.</span><span class="sxs-lookup"><span data-stu-id="f4a26-179">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="f4a26-180">Por exemplo, o código a seguir define um método de extensão .NET que permite definir o foco em um elemento:</span><span class="sxs-lookup"><span data-stu-id="f4a26-180">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="f4a26-181">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="f4a26-181">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="f4a26-182">Para chamar uma função JavaScript que não retorna um valor, use `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-182">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="f4a26-183">O código a seguir define o foco na entrada de nome de usuário chamando a função JavaScript anterior com o `ElementReference`capturado:</span><span class="sxs-lookup"><span data-stu-id="f4a26-183">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="f4a26-184">Para usar um método de extensão, crie um método de extensão estático que receba a instância de `IJSRuntime`:</span><span class="sxs-lookup"><span data-stu-id="f4a26-184">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="f4a26-185">O método `Focus` é chamado diretamente no objeto.</span><span class="sxs-lookup"><span data-stu-id="f4a26-185">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="f4a26-186">O exemplo a seguir pressupõe que o método `Focus` está disponível no namespace `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="f4a26-186">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="f4a26-187">A variável `username` é populada apenas depois que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="f4a26-187">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="f4a26-188">Se um `ElementReference` não populado for passado para o código JavaScript, o código JavaScript receberá um valor de `null`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-188">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="f4a26-189">Para manipular referências de elemento após a conclusão da renderização do componente (para definir o foco inicial em um elemento), use os [métodos de ciclo de vida do componente OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="f4a26-189">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="f4a26-190">Ao trabalhar com tipos genéricos e retornar um valor, use [ValueTask\<t >](xref:System.Threading.Tasks.ValueTask`1):</span><span class="sxs-lookup"><span data-stu-id="f4a26-190">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="f4a26-191">`GenericMethod` é chamado diretamente no objeto com um tipo.</span><span class="sxs-lookup"><span data-stu-id="f4a26-191">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="f4a26-192">O exemplo a seguir pressupõe que o `GenericMethod` está disponível no namespace `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="f4a26-192">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component3.razor?highlight=17)]

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="f4a26-193">Invocar métodos .NET de funções JavaScript</span><span class="sxs-lookup"><span data-stu-id="f4a26-193">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="f4a26-194">Chamada de método .NET estático</span><span class="sxs-lookup"><span data-stu-id="f4a26-194">Static .NET method call</span></span>

<span data-ttu-id="f4a26-195">Para invocar um método .NET estático do JavaScript, use as funções `DotNet.invokeMethod` ou `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-195">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="f4a26-196">Passe o identificador do método estático que você deseja chamar, o nome do assembly que contém a função e os argumentos.</span><span class="sxs-lookup"><span data-stu-id="f4a26-196">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="f4a26-197">A versão assíncrona é preferida para dar suporte a cenários do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="f4a26-197">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="f4a26-198">Para invocar um método .NET do JavaScript, o método .NET deve ser público, estático e ter o atributo `[JSInvokable]`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-198">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="f4a26-199">Por padrão, o identificador de método é o nome do método, mas você pode especificar um identificador diferente usando o Construtor `JSInvokableAttribute`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-199">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="f4a26-200">Atualmente, não há suporte para chamar métodos genéricos abertos.</span><span class="sxs-lookup"><span data-stu-id="f4a26-200">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="f4a26-201">O aplicativo de exemplo inclui C# um método para retornar uma matriz de `int`s.</span><span class="sxs-lookup"><span data-stu-id="f4a26-201">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="f4a26-202">O atributo `JSInvokable` é aplicado ao método.</span><span class="sxs-lookup"><span data-stu-id="f4a26-202">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="f4a26-203">*Páginas/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="f4a26-203">*Pages/JsInterop.razor*:</span></span>

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="f4a26-204">O JavaScript servido para o cliente invoca o C# método .net.</span><span class="sxs-lookup"><span data-stu-id="f4a26-204">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="f4a26-205">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="f4a26-205">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="f4a26-206">Quando o botão **disparar o método estático do .net ReturnArrayAsync** for selecionado, examine a saída do console nas ferramentas de desenvolvedor da Web do navegador.</span><span class="sxs-lookup"><span data-stu-id="f4a26-206">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="f4a26-207">A saída do console é:</span><span class="sxs-lookup"><span data-stu-id="f4a26-207">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="f4a26-208">O quarto valor de matriz é enviado por push para a matriz (`data.push(4);`) retornada por `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-208">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="f4a26-209">Chamada de método de instância</span><span class="sxs-lookup"><span data-stu-id="f4a26-209">Instance method call</span></span>

<span data-ttu-id="f4a26-210">Você também pode chamar os métodos de instância do .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f4a26-210">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="f4a26-211">Para invocar um método de instância .NET do JavaScript:</span><span class="sxs-lookup"><span data-stu-id="f4a26-211">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="f4a26-212">Passe a instância do .NET para JavaScript, encapsulando-a em uma instância de `DotNetObjectReference`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-212">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectReference` instance.</span></span> <span data-ttu-id="f4a26-213">A instância do .NET é passada por referência ao JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f4a26-213">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="f4a26-214">Invoque os métodos de instância do .NET na instância usando as funções `invokeMethod` ou `invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-214">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="f4a26-215">A instância do .NET também pode ser passada como um argumento ao invocar outros métodos .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f4a26-215">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="f4a26-216">O aplicativo de exemplo registra as mensagens no console do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="f4a26-216">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="f4a26-217">Para os exemplos a seguir demonstrados pelo aplicativo de exemplo, examine a saída do console do navegador nas ferramentas de desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="f4a26-217">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="f4a26-218">Quando o botão do **método de instância .net do disparador HelloHelper. sayHello** é selecionado, `ExampleJsInterop.CallHelloHelperSayHello` é chamado e passa um nome, `Blazor`, para o método.</span><span class="sxs-lookup"><span data-stu-id="f4a26-218">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="f4a26-219">*Páginas/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="f4a26-219">*Pages/JsInterop.razor*:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

<span data-ttu-id="f4a26-220">`CallHelloHelperSayHello` invoca a função JavaScript `sayHello` com uma nova instância de `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-220">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="f4a26-221">*JsInteropClasses/ExampleJsInterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="f4a26-221">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="f4a26-222">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="f4a26-222">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="f4a26-223">O nome é passado para o construtor de `HelloHelper`, que define a propriedade `HelloHelper.Name`.</span><span class="sxs-lookup"><span data-stu-id="f4a26-223">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="f4a26-224">Quando a função JavaScript `sayHello` é executada, `HelloHelper.SayHello` retorna a mensagem de `Hello, {Name}!`, que é gravada no console pela função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f4a26-224">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="f4a26-225">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="f4a26-225">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="f4a26-226">Saída do console nas ferramentas de desenvolvedor da Web do navegador:</span><span class="sxs-lookup"><span data-stu-id="f4a26-226">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="f4a26-227">Compartilhar código de interoperabilidade em uma biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="f4a26-227">Share interop code in a class library</span></span>

<span data-ttu-id="f4a26-228">O código de interoperabilidade JS pode ser incluído em uma biblioteca de classes, que permite que você compartilhe o código em um pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="f4a26-228">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="f4a26-229">A biblioteca de classes lida com a inserção de recursos JavaScript no assembly compilado.</span><span class="sxs-lookup"><span data-stu-id="f4a26-229">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="f4a26-230">Os arquivos JavaScript são colocados na pasta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="f4a26-230">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="f4a26-231">As ferramentas cuidam da inserção dos recursos quando a biblioteca é criada.</span><span class="sxs-lookup"><span data-stu-id="f4a26-231">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="f4a26-232">O pacote NuGet criado é referenciado no arquivo de projeto do aplicativo da mesma forma que qualquer pacote NuGet é referenciado.</span><span class="sxs-lookup"><span data-stu-id="f4a26-232">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="f4a26-233">Depois que o pacote é restaurado, o código do aplicativo pode chamar o JavaScript C#como se fosse.</span><span class="sxs-lookup"><span data-stu-id="f4a26-233">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="f4a26-234">Para obter mais informações, consulte <xref:blazor/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="f4a26-234">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="f4a26-235">Proteger chamadas Interop JS</span><span class="sxs-lookup"><span data-stu-id="f4a26-235">Harden JS interop calls</span></span>

<span data-ttu-id="f4a26-236">A interoperabilidade JS pode falhar devido a erros de rede e deve ser tratada como não confiável.</span><span class="sxs-lookup"><span data-stu-id="f4a26-236">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="f4a26-237">Por padrão, um aplicativo de servidor Blazor expira as chamadas de interoperabilidade JS no servidor após um minuto.</span><span class="sxs-lookup"><span data-stu-id="f4a26-237">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="f4a26-238">Se um aplicativo puder tolerar um tempo limite mais agressivo, como 10 segundos, defina o tempo limite usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="f4a26-238">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="f4a26-239">Globalmente em `Startup.ConfigureServices`, especifique o tempo limite:</span><span class="sxs-lookup"><span data-stu-id="f4a26-239">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="f4a26-240">Por invocação no código do componente, uma única chamada pode especificar o tempo limite:</span><span class="sxs-lookup"><span data-stu-id="f4a26-240">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="f4a26-241">Para obter mais informações sobre esgotamento de recursos, consulte <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="f4a26-241">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f4a26-242">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f4a26-242">Additional resources</span></span>

* [<span data-ttu-id="f4a26-243">Exemplo de InteropComponent. Razor (repositório GitHub ASPNET/AspNetCore, Branch de lançamento 3,0)</span><span class="sxs-lookup"><span data-stu-id="f4a26-243">InteropComponent.razor example (aspnet/AspNetCore GitHub repository, 3.0 release branch)</span></span>](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
