---
title: ASP.NET Core interoperabilidade Blazor JavaScript
author: guardrex
description: Saiba como invocar funções JavaScript de métodos .NET e .NET do JavaScript em Blazor aplicativos.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/02/2019
no-loc:
- Blazor
uid: blazor/javascript-interop
ms.openlocfilehash: 108fdac8667f407adba3470de4eb8e35883cefbf
ms.sourcegitcommit: 169ea5116de729c803685725d96450a270bc55b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74733824"
---
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a><span data-ttu-id="7ac47-103">ASP.NET Core interoperabilidade Blazor JavaScript</span><span class="sxs-lookup"><span data-stu-id="7ac47-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="7ac47-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7ac47-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="7ac47-105">Um aplicativo Blazor pode invocar funções JavaScript de métodos .NET e .NET do código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ac47-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="7ac47-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7ac47-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="7ac47-107">Invocar funções JavaScript de métodos .NET</span><span class="sxs-lookup"><span data-stu-id="7ac47-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="7ac47-108">Há ocasiões em que o código .NET é necessário para chamar uma função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ac47-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="7ac47-109">Por exemplo, uma chamada de JavaScript pode expor recursos de navegador ou funcionalidade de uma biblioteca JavaScript para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ac47-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span> <span data-ttu-id="7ac47-110">Esse cenário é chamado de *interoperabilidade JavaScript* (*js Interop*).</span><span class="sxs-lookup"><span data-stu-id="7ac47-110">This scenario is called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="7ac47-111">Para chamar o JavaScript do .NET, use a abstração de `IJSRuntime`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-111">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="7ac47-112">O método `InvokeAsync<T>` usa um identificador para a função JavaScript que você deseja invocar junto com qualquer número de argumentos serializáveis para JSON.</span><span class="sxs-lookup"><span data-stu-id="7ac47-112">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="7ac47-113">O identificador de função é relativo ao escopo global (`window`).</span><span class="sxs-lookup"><span data-stu-id="7ac47-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="7ac47-114">Se você quiser chamar `window.someScope.someFunction`, o identificador será `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="7ac47-115">Não é necessário registrar a função antes que ela seja chamada.</span><span class="sxs-lookup"><span data-stu-id="7ac47-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="7ac47-116">O tipo de retorno `T` também deve ser serializável como JSON.</span><span class="sxs-lookup"><span data-stu-id="7ac47-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="7ac47-117">`T` deve corresponder ao tipo .NET que melhor mapeia para o tipo JSON retornado.</span><span class="sxs-lookup"><span data-stu-id="7ac47-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="7ac47-118">Para aplicativos do Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="7ac47-118">For Blazor Server apps:</span></span>

* <span data-ttu-id="7ac47-119">Várias solicitações de usuário são processadas pelo aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="7ac47-119">Multiple user requests are processed by the Blazor Server app.</span></span> <span data-ttu-id="7ac47-120">Não chame `JSRuntime.Current` em um componente para invocar funções JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ac47-120">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="7ac47-121">Injetar a abstração de `IJSRuntime` e usar o objeto injetado para emitir chamadas de interoperabilidade JS.</span><span class="sxs-lookup"><span data-stu-id="7ac47-121">Inject the `IJSRuntime` abstraction and use the injected object to issue JS interop calls.</span></span>
* <span data-ttu-id="7ac47-122">Embora um aplicativo Blazor esteja sendo renderizado, não é possível chamar o JavaScript porque uma conexão com o navegador não foi estabelecida.</span><span class="sxs-lookup"><span data-stu-id="7ac47-122">While a Blazor app is prerendering, calling into JavaScript isn't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="7ac47-123">Para obter mais informações, consulte a seção [detectar quando um aplicativo de Blazor está sendo renderizado](#detect-when-a-blazor-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="7ac47-123">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="7ac47-124">O exemplo a seguir é baseado em [textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), um decodificador experimental baseado em JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ac47-124">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="7ac47-125">O exemplo demonstra como invocar uma função JavaScript a partir C# de um método.</span><span class="sxs-lookup"><span data-stu-id="7ac47-125">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="7ac47-126">A função JavaScript aceita uma matriz de bytes de C# um método, decodifica a matriz e retorna o texto para o componente para exibição.</span><span class="sxs-lookup"><span data-stu-id="7ac47-126">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="7ac47-127">Dentro do elemento `<head>` de *wwwroot/index.html* (Blazor Webassembly) ou *pages/_Host. cshtml* (servidorBlazor), forneça uma função JavaScript que usa `TextDecoder` para decodificar uma matriz passada e retornar o valor decodificado:</span><span class="sxs-lookup"><span data-stu-id="7ac47-127">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="7ac47-128">O código JavaScript, como o código mostrado no exemplo anterior, também pode ser carregado de um arquivo JavaScript ( *. js*) com uma referência ao arquivo de script:</span><span class="sxs-lookup"><span data-stu-id="7ac47-128">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="7ac47-129">O seguinte componente:</span><span class="sxs-lookup"><span data-stu-id="7ac47-129">The following component:</span></span>

* <span data-ttu-id="7ac47-130">Invoca a função `convertArray` JavaScript usando `JSRuntime` quando um botão de componente (**matriz de conversão**) é selecionado.</span><span class="sxs-lookup"><span data-stu-id="7ac47-130">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="7ac47-131">Depois que a função JavaScript é chamada, a matriz passada é convertida em uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="7ac47-131">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="7ac47-132">A cadeia de caracteres é retornada para o componente para exibição.</span><span class="sxs-lookup"><span data-stu-id="7ac47-132">The string is returned to the component for display.</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a><span data-ttu-id="7ac47-133">Uso de IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="7ac47-133">Use of IJSRuntime</span></span>

<span data-ttu-id="7ac47-134">Para usar a abstração de `IJSRuntime`, adote qualquer uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="7ac47-134">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="7ac47-135">Injetar a abstração de `IJSRuntime` no componente Razor ( *. Razor*):</span><span class="sxs-lookup"><span data-stu-id="7ac47-135">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="7ac47-136">Dentro do elemento `<head>` de *wwwroot/index.html* (Blazor Webassembly) ou *pages/_Host. cshtml* (servidorBlazor), forneça uma `handleTickerChanged` função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ac47-136">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="7ac47-137">A função é chamada com `IJSRuntime.InvokeVoidAsync` e não retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="7ac47-137">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="7ac47-138">Injetar a abstração de `IJSRuntime` em uma classe ( *. cs*):</span><span class="sxs-lookup"><span data-stu-id="7ac47-138">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="7ac47-139">Dentro do elemento `<head>` de *wwwroot/index.html* (Blazor Webassembly) ou *pages/_Host. cshtml* (servidorBlazor), forneça uma `handleTickerChanged` função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ac47-139">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="7ac47-140">A função é chamada com `JSRuntime.InvokeAsync` e retorna um valor:</span><span class="sxs-lookup"><span data-stu-id="7ac47-140">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="7ac47-141">Para a geração de conteúdo dinâmico com [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use o atributo `[Inject]`:</span><span class="sxs-lookup"><span data-stu-id="7ac47-141">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="7ac47-142">No aplicativo de exemplo do lado do cliente que acompanha este tópico, duas funções JavaScript estão disponíveis para o aplicativo que interagem com o DOM para receber a entrada do usuário e exibir uma mensagem de boas-vindas:</span><span class="sxs-lookup"><span data-stu-id="7ac47-142">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="7ac47-143">`showPrompt` &ndash; produz um prompt para aceitar a entrada do usuário (o nome do usuário) e retorna o nome para o chamador.</span><span class="sxs-lookup"><span data-stu-id="7ac47-143">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="7ac47-144">`displayWelcome` &ndash; atribui uma mensagem de boas-vindas do chamador a um objeto DOM com uma `id` de `welcome`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-144">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="7ac47-145">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="7ac47-145">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="7ac47-146">Coloque a `<script>` marca que faz referência ao arquivo JavaScript no arquivo *wwwroot/index.html* (Blazor Webassembly) ou ao arquivo *pages/_Host. cshtml* (servidorBlazor).</span><span class="sxs-lookup"><span data-stu-id="7ac47-146">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="7ac47-147">*wwwroot/index.html* (Blazor Webassembly):</span><span class="sxs-lookup"><span data-stu-id="7ac47-147">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="7ac47-148">*Pages/_Host. cshtml* (servidorBlazor):</span><span class="sxs-lookup"><span data-stu-id="7ac47-148">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=21)]

<span data-ttu-id="7ac47-149">Não coloque uma marca de `<script>` em um arquivo de componente porque a marca de `<script>` não pode ser atualizada dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="7ac47-149">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="7ac47-150">Os métodos .NET interoperam com as funções JavaScript no arquivo *exampleJsInterop. js* chamando `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-150">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="7ac47-151">A abstração de `IJSRuntime` é assíncrona para permitir cenários de Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="7ac47-151">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="7ac47-152">Se o aplicativo for um aplicativo Webassembly Blazor e você quiser invocar uma função JavaScript de forma síncrona, downcast para `IJSInProcessRuntime` e chame `Invoke<T>` em vez disso.</span><span class="sxs-lookup"><span data-stu-id="7ac47-152">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="7ac47-153">Recomendamos que a maioria das bibliotecas de interoperabilidade do JS use as APIs assíncronas para garantir que as bibliotecas estejam disponíveis em todos os cenários.</span><span class="sxs-lookup"><span data-stu-id="7ac47-153">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="7ac47-154">O aplicativo de exemplo inclui um componente para demonstrar a interoperabilidade do JS.</span><span class="sxs-lookup"><span data-stu-id="7ac47-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="7ac47-155">O componente:</span><span class="sxs-lookup"><span data-stu-id="7ac47-155">The component:</span></span>

* <span data-ttu-id="7ac47-156">Recebe a entrada do usuário por meio de um prompt do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ac47-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="7ac47-157">Retorna o texto para o componente para processamento.</span><span class="sxs-lookup"><span data-stu-id="7ac47-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="7ac47-158">Chama uma segunda função JavaScript que interage com o DOM para exibir uma mensagem de boas-vindas.</span><span class="sxs-lookup"><span data-stu-id="7ac47-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="7ac47-159">*Páginas/JSInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="7ac47-159">*Pages/JSInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="7ac47-160">Quando `TriggerJsPrompt` é executado selecionando o botão de **prompt de JavaScript** do componente de gatilho, a função JavaScript `showPrompt` fornecida no arquivo *wwwroot/exampleJsInterop. js* é chamada.</span><span class="sxs-lookup"><span data-stu-id="7ac47-160">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="7ac47-161">A função `showPrompt` aceita a entrada do usuário (o nome do usuário), que é codificado em HTML e retornada ao componente.</span><span class="sxs-lookup"><span data-stu-id="7ac47-161">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="7ac47-162">O componente armazena o nome do usuário em uma variável local, `name`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-162">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="7ac47-163">A cadeia de caracteres armazenada em `name` é incorporada a uma mensagem de boas-vindas, que é passada para uma função JavaScript, `displayWelcome`, que renderiza a mensagem de boas-vindas em uma marca de cabeçalho.</span><span class="sxs-lookup"><span data-stu-id="7ac47-163">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="7ac47-164">Chamar uma função JavaScript void</span><span class="sxs-lookup"><span data-stu-id="7ac47-164">Call a void JavaScript function</span></span>

<span data-ttu-id="7ac47-165">As funções JavaScript que retornam [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) ou [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) são chamadas com `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-165">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a><span data-ttu-id="7ac47-166">Detectar quando um aplicativo Blazor está sendo renderizado</span><span class="sxs-lookup"><span data-stu-id="7ac47-166">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="7ac47-167">Capturar referências a elementos</span><span class="sxs-lookup"><span data-stu-id="7ac47-167">Capture references to elements</span></span>

<span data-ttu-id="7ac47-168">Alguns cenários de interoperabilidade JS exigem referências a elementos HTML.</span><span class="sxs-lookup"><span data-stu-id="7ac47-168">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="7ac47-169">Por exemplo, uma biblioteca de interface do usuário pode exigir uma referência de elemento para inicialização ou você pode precisar chamar APIs do tipo comando em um elemento, como `focus` ou `play`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-169">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="7ac47-170">Capture referências a elementos HTML em um componente usando a seguinte abordagem:</span><span class="sxs-lookup"><span data-stu-id="7ac47-170">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="7ac47-171">Adicione um atributo `@ref` ao elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="7ac47-171">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="7ac47-172">Defina um campo do tipo `ElementReference` cujo nome corresponde ao valor do atributo `@ref`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-172">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="7ac47-173">O exemplo a seguir mostra a captura de uma referência para o elemento `username` `<input>`:</span><span class="sxs-lookup"><span data-stu-id="7ac47-173">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="7ac47-174">Use apenas uma referência de elemento para converter o conteúdo de um elemento vazio que não interaja com Blazor.</span><span class="sxs-lookup"><span data-stu-id="7ac47-174">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="7ac47-175">Esse cenário é útil quando uma API de terceiros fornece conteúdo para o elemento.</span><span class="sxs-lookup"><span data-stu-id="7ac47-175">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="7ac47-176">Como Blazor não interage com o elemento, não há possibilidade de um conflito entre a representação Blazordo elemento e o DOM.</span><span class="sxs-lookup"><span data-stu-id="7ac47-176">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="7ac47-177">No exemplo a seguir, é *perigoso* mutar o conteúdo da lista não ordenada (`ul`) porque Blazor interage com o dom para preencher os itens de lista deste elemento (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="7ac47-177">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```cshtml
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="7ac47-178">Se a interoperabilidade do JS converter o conteúdo do elemento `MyList` e Blazor tentar aplicar diffs ao elemento, as diferenças não corresponderão ao DOM.</span><span class="sxs-lookup"><span data-stu-id="7ac47-178">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="7ac47-179">No que diz respeito ao código .NET, um `ElementReference` é um identificador opaco.</span><span class="sxs-lookup"><span data-stu-id="7ac47-179">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="7ac47-180">A *única* coisa que você pode fazer com `ElementReference` é passá-lo para o código JavaScript por meio da interoperabilidade do js.</span><span class="sxs-lookup"><span data-stu-id="7ac47-180">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="7ac47-181">Quando você faz isso, o código do lado do JavaScript recebe uma instância de `HTMLElement`, que pode ser usada com APIs DOM normais.</span><span class="sxs-lookup"><span data-stu-id="7ac47-181">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="7ac47-182">Por exemplo, o código a seguir define um método de extensão .NET que permite definir o foco em um elemento:</span><span class="sxs-lookup"><span data-stu-id="7ac47-182">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="7ac47-183">*exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="7ac47-183">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="7ac47-184">Para chamar uma função JavaScript que não retorna um valor, use `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-184">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="7ac47-185">O código a seguir define o foco na entrada de nome de usuário chamando a função JavaScript anterior com o `ElementReference`capturado:</span><span class="sxs-lookup"><span data-stu-id="7ac47-185">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="7ac47-186">Para usar um método de extensão, crie um método de extensão estático que receba a instância de `IJSRuntime`:</span><span class="sxs-lookup"><span data-stu-id="7ac47-186">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="7ac47-187">O método `Focus` é chamado diretamente no objeto.</span><span class="sxs-lookup"><span data-stu-id="7ac47-187">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="7ac47-188">O exemplo a seguir pressupõe que o método `Focus` está disponível no namespace `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="7ac47-188">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="7ac47-189">A variável `username` é populada apenas depois que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="7ac47-189">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="7ac47-190">Se um `ElementReference` não populado for passado para o código JavaScript, o código JavaScript receberá um valor de `null`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-190">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="7ac47-191">Para manipular referências de elemento após a conclusão da renderização do componente (para definir o foco inicial em um elemento), use os [métodos de ciclo de vida do componente OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="7ac47-191">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="7ac47-192">Ao trabalhar com tipos genéricos e retornar um valor, use [ValueTask\<t >](xref:System.Threading.Tasks.ValueTask`1):</span><span class="sxs-lookup"><span data-stu-id="7ac47-192">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="7ac47-193">`GenericMethod` é chamado diretamente no objeto com um tipo.</span><span class="sxs-lookup"><span data-stu-id="7ac47-193">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="7ac47-194">O exemplo a seguir pressupõe que o `GenericMethod` está disponível no namespace `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="7ac47-194">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component3.razor?highlight=17)]

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="7ac47-195">Invocar métodos .NET de funções JavaScript</span><span class="sxs-lookup"><span data-stu-id="7ac47-195">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="7ac47-196">Chamada de método .NET estático</span><span class="sxs-lookup"><span data-stu-id="7ac47-196">Static .NET method call</span></span>

<span data-ttu-id="7ac47-197">Para invocar um método .NET estático do JavaScript, use as funções `DotNet.invokeMethod` ou `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-197">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="7ac47-198">Passe o identificador do método estático que você deseja chamar, o nome do assembly que contém a função e os argumentos.</span><span class="sxs-lookup"><span data-stu-id="7ac47-198">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="7ac47-199">A versão assíncrona é preferida para dar suporte a cenários do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="7ac47-199">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="7ac47-200">Para invocar um método .NET do JavaScript, o método .NET deve ser público, estático e ter o atributo `[JSInvokable]`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-200">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="7ac47-201">Por padrão, o identificador de método é o nome do método, mas você pode especificar um identificador diferente usando o Construtor `JSInvokableAttribute`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-201">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="7ac47-202">Atualmente, não há suporte para chamar métodos genéricos abertos.</span><span class="sxs-lookup"><span data-stu-id="7ac47-202">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="7ac47-203">O aplicativo de exemplo inclui C# um método para retornar uma matriz de `int`s.</span><span class="sxs-lookup"><span data-stu-id="7ac47-203">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="7ac47-204">O atributo `JSInvokable` é aplicado ao método.</span><span class="sxs-lookup"><span data-stu-id="7ac47-204">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="7ac47-205">*Páginas/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="7ac47-205">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="7ac47-206">O JavaScript servido para o cliente invoca o C# método .net.</span><span class="sxs-lookup"><span data-stu-id="7ac47-206">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="7ac47-207">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="7ac47-207">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="7ac47-208">Quando o botão **disparar o método estático do .net ReturnArrayAsync** for selecionado, examine a saída do console nas ferramentas de desenvolvedor da Web do navegador.</span><span class="sxs-lookup"><span data-stu-id="7ac47-208">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="7ac47-209">A saída do console é:</span><span class="sxs-lookup"><span data-stu-id="7ac47-209">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="7ac47-210">O quarto valor de matriz é enviado por push para a matriz (`data.push(4);`) retornada por `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-210">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="7ac47-211">Chamada de método de instância</span><span class="sxs-lookup"><span data-stu-id="7ac47-211">Instance method call</span></span>

<span data-ttu-id="7ac47-212">Você também pode chamar os métodos de instância do .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ac47-212">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="7ac47-213">Para invocar um método de instância .NET do JavaScript:</span><span class="sxs-lookup"><span data-stu-id="7ac47-213">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="7ac47-214">Passe a instância do .NET para JavaScript, encapsulando-a em uma instância de `DotNetObjectReference`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-214">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectReference` instance.</span></span> <span data-ttu-id="7ac47-215">A instância do .NET é passada por referência ao JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ac47-215">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="7ac47-216">Invoque os métodos de instância do .NET na instância usando as funções `invokeMethod` ou `invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-216">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="7ac47-217">A instância do .NET também pode ser passada como um argumento ao invocar outros métodos .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ac47-217">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="7ac47-218">O aplicativo de exemplo registra as mensagens no console do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="7ac47-218">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="7ac47-219">Para os exemplos a seguir demonstrados pelo aplicativo de exemplo, examine a saída do console do navegador nas ferramentas de desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="7ac47-219">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="7ac47-220">Quando o botão do **método de instância .net do disparador HelloHelper. sayHello** é selecionado, `ExampleJsInterop.CallHelloHelperSayHello` é chamado e passa um nome, `Blazor`, para o método.</span><span class="sxs-lookup"><span data-stu-id="7ac47-220">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="7ac47-221">*Páginas/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="7ac47-221">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

<span data-ttu-id="7ac47-222">`CallHelloHelperSayHello` invoca a função JavaScript `sayHello` com uma nova instância de `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-222">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="7ac47-223">*JsInteropClasses/ExampleJsInterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="7ac47-223">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="7ac47-224">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="7ac47-224">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="7ac47-225">O nome é passado para o construtor de `HelloHelper`, que define a propriedade `HelloHelper.Name`.</span><span class="sxs-lookup"><span data-stu-id="7ac47-225">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="7ac47-226">Quando a função JavaScript `sayHello` é executada, `HelloHelper.SayHello` retorna a mensagem de `Hello, {Name}!`, que é gravada no console pela função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7ac47-226">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="7ac47-227">*JsInteropClasses/HelloHelper. cs*:</span><span class="sxs-lookup"><span data-stu-id="7ac47-227">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="7ac47-228">Saída do console nas ferramentas de desenvolvedor da Web do navegador:</span><span class="sxs-lookup"><span data-stu-id="7ac47-228">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="7ac47-229">Compartilhar código de interoperabilidade em uma biblioteca de classes</span><span class="sxs-lookup"><span data-stu-id="7ac47-229">Share interop code in a class library</span></span>

<span data-ttu-id="7ac47-230">O código de interoperabilidade JS pode ser incluído em uma biblioteca de classes, que permite que você compartilhe o código em um pacote NuGet.</span><span class="sxs-lookup"><span data-stu-id="7ac47-230">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="7ac47-231">A biblioteca de classes lida com a inserção de recursos JavaScript no assembly compilado.</span><span class="sxs-lookup"><span data-stu-id="7ac47-231">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="7ac47-232">Os arquivos JavaScript são colocados na pasta *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="7ac47-232">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="7ac47-233">As ferramentas cuidam da inserção dos recursos quando a biblioteca é criada.</span><span class="sxs-lookup"><span data-stu-id="7ac47-233">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="7ac47-234">O pacote NuGet criado é referenciado no arquivo de projeto do aplicativo da mesma forma que qualquer pacote NuGet é referenciado.</span><span class="sxs-lookup"><span data-stu-id="7ac47-234">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="7ac47-235">Depois que o pacote é restaurado, o código do aplicativo pode chamar o JavaScript C#como se fosse.</span><span class="sxs-lookup"><span data-stu-id="7ac47-235">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="7ac47-236">Para obter mais informações, consulte <xref:blazor/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="7ac47-236">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="7ac47-237">Proteger chamadas Interop JS</span><span class="sxs-lookup"><span data-stu-id="7ac47-237">Harden JS interop calls</span></span>

<span data-ttu-id="7ac47-238">A interoperabilidade JS pode falhar devido a erros de rede e deve ser tratada como não confiável.</span><span class="sxs-lookup"><span data-stu-id="7ac47-238">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="7ac47-239">Por padrão, um aplicativo de servidor Blazor expira as chamadas de interoperabilidade JS no servidor após um minuto.</span><span class="sxs-lookup"><span data-stu-id="7ac47-239">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="7ac47-240">Se um aplicativo puder tolerar um tempo limite mais agressivo, como 10 segundos, defina o tempo limite usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="7ac47-240">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="7ac47-241">Globalmente em `Startup.ConfigureServices`, especifique o tempo limite:</span><span class="sxs-lookup"><span data-stu-id="7ac47-241">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="7ac47-242">Por invocação no código do componente, uma única chamada pode especificar o tempo limite:</span><span class="sxs-lookup"><span data-stu-id="7ac47-242">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="7ac47-243">Para obter mais informações sobre esgotamento de recursos, consulte <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="7ac47-243">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ac47-244">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7ac47-244">Additional resources</span></span>

* [<span data-ttu-id="7ac47-245">Exemplo de InteropComponent. Razor (repositório GitHub ASPNET/AspNetCore, Branch de lançamento 3,0)</span><span class="sxs-lookup"><span data-stu-id="7ac47-245">InteropComponent.razor example (aspnet/AspNetCore GitHub repository, 3.0 release branch)</span></span>](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
