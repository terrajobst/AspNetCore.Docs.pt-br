---
title: Chamar métodos .NET de funções JavaScript no ASP.NET Core Blazor
author: guardrex
description: Saiba como invocar métodos .NET de funções JavaScript em aplicativos Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: dbf44fe7923998c65119e42d97c304890fa95523
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218785"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="e64c5-103">Chamar métodos .NET de funções JavaScript no ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e64c5-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="e64c5-104">Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e64c5-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="e64c5-105">Um aplicativo Blazor pode invocar funções JavaScript de métodos .NET e métodos .NET de funções JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e64c5-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="e64c5-106">Esses cenários são chamados de *interoperabilidade JavaScript* (*operabilidade do js*).</span><span class="sxs-lookup"><span data-stu-id="e64c5-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="e64c5-107">Este artigo aborda a invocação de métodos .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e64c5-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="e64c5-108">Para obter informações sobre como chamar funções JavaScript do .NET, consulte <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="e64c5-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="e64c5-109">[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e64c5-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="e64c5-110">Chamada de método .NET estático</span><span class="sxs-lookup"><span data-stu-id="e64c5-110">Static .NET method call</span></span>

<span data-ttu-id="e64c5-111">Para invocar um método .NET estático do JavaScript, use as funções `DotNet.invokeMethod` ou `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="e64c5-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="e64c5-112">Passe o identificador do método estático que você deseja chamar, o nome do assembly que contém a função e os argumentos.</span><span class="sxs-lookup"><span data-stu-id="e64c5-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="e64c5-113">A versão assíncrona é preferida para dar suporte a cenários do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="e64c5-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="e64c5-114">O método .NET deve ser público, estático e ter o atributo `[JSInvokable]`.</span><span class="sxs-lookup"><span data-stu-id="e64c5-114">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="e64c5-115">Atualmente, não há suporte para chamar métodos genéricos abertos.</span><span class="sxs-lookup"><span data-stu-id="e64c5-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="e64c5-116">O aplicativo de exemplo inclui C# um método para retornar uma matriz de `int`.</span><span class="sxs-lookup"><span data-stu-id="e64c5-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="e64c5-117">O atributo `JSInvokable` é aplicado ao método.</span><span class="sxs-lookup"><span data-stu-id="e64c5-117">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="e64c5-118">*Páginas/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="e64c5-118">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="e64c5-119">O JavaScript servido para o cliente invoca o C# método .net.</span><span class="sxs-lookup"><span data-stu-id="e64c5-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="e64c5-120">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="e64c5-120">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="e64c5-121">Quando o botão **disparar o método estático do .net ReturnArrayAsync** for selecionado, examine a saída do console nas ferramentas de desenvolvedor da Web do navegador.</span><span class="sxs-lookup"><span data-stu-id="e64c5-121">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="e64c5-122">A saída do console é:</span><span class="sxs-lookup"><span data-stu-id="e64c5-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="e64c5-123">O quarto valor de matriz é enviado por push para a matriz (`data.push(4);`) retornada por `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="e64c5-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="e64c5-124">Por padrão, o identificador de método é o nome do método, mas você pode especificar um identificador diferente usando o construtor de `JSInvokableAttribute`:</span><span class="sxs-lookup"><span data-stu-id="e64c5-124">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="e64c5-125">No arquivo JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="e64c5-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="e64c5-126">Chamada de método de instância</span><span class="sxs-lookup"><span data-stu-id="e64c5-126">Instance method call</span></span>

<span data-ttu-id="e64c5-127">Você também pode chamar os métodos de instância do .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e64c5-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="e64c5-128">Para invocar um método de instância .NET do JavaScript:</span><span class="sxs-lookup"><span data-stu-id="e64c5-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="e64c5-129">Passe a instância do .NET por referência ao JavaScript:</span><span class="sxs-lookup"><span data-stu-id="e64c5-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="e64c5-130">Faça uma chamada estática para `DotNetObjectReference.Create`.</span><span class="sxs-lookup"><span data-stu-id="e64c5-130">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="e64c5-131">Empacote a instância em uma instância de `DotNetObjectReference` e chame `Create` na instância de `DotNetObjectReference`.</span><span class="sxs-lookup"><span data-stu-id="e64c5-131">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="e64c5-132">Descartar objetos de `DotNetObjectReference` (um exemplo aparece mais adiante nesta seção).</span><span class="sxs-lookup"><span data-stu-id="e64c5-132">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="e64c5-133">Invoque os métodos de instância do .NET na instância usando as funções `invokeMethod` ou `invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="e64c5-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="e64c5-134">A instância do .NET também pode ser passada como um argumento ao invocar outros métodos .NET do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e64c5-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="e64c5-135">O aplicativo de exemplo registra as mensagens no console do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="e64c5-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="e64c5-136">Para os exemplos a seguir demonstrados pelo aplicativo de exemplo, examine a saída do console do navegador nas ferramentas de desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="e64c5-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="e64c5-137">Quando o botão do **método de instância .net do disparador HelloHelper. sayHello** é selecionado, `ExampleJsInterop.CallHelloHelperSayHello` é chamado e passa um nome, `Blazor`, para o método.</span><span class="sxs-lookup"><span data-stu-id="e64c5-137">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="e64c5-138">*Páginas/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="e64c5-138">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="e64c5-139">`CallHelloHelperSayHello` invoca a função JavaScript `sayHello` com uma nova instância de `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="e64c5-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="e64c5-140">*JsInteropClasses/ExampleJsInterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="e64c5-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="e64c5-141">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="e64c5-141">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="e64c5-142">O nome é passado para o construtor de `HelloHelper`, que define a propriedade `HelloHelper.Name`.</span><span class="sxs-lookup"><span data-stu-id="e64c5-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="e64c5-143">Quando a função JavaScript `sayHello` é executada, `HelloHelper.SayHello` retorna a mensagem de `Hello, {Name}!`, que é gravada no console pela função JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e64c5-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="e64c5-144">*JsInteropClasses/HelloHelper. cs*:</span><span class="sxs-lookup"><span data-stu-id="e64c5-144">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="e64c5-145">Saída do console nas ferramentas de desenvolvedor da Web do navegador:</span><span class="sxs-lookup"><span data-stu-id="e64c5-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="e64c5-146">Para evitar um vazamento de memória e permitir a coleta de lixo em um componente que cria uma `DotNetObjectReference`, adote uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="e64c5-146">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, adopt one of the following approaches:</span></span>

* <span data-ttu-id="e64c5-147">Descarte o objeto na classe que criou a instância de `DotNetObjectReference`:</span><span class="sxs-lookup"><span data-stu-id="e64c5-147">Dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime _jsRuntime;
      private DotNetObjectReference<HelloHelper> _objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          _jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          _objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return _jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              _objRef);
      }

      public void Dispose()
      {
          _objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="e64c5-148">O padrão anterior mostrado na classe `ExampleJsInterop` também pode ser implementado em um componente:</span><span class="sxs-lookup"><span data-stu-id="e64c5-148">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using BlazorSample.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JSRuntime

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> _objRef;

      public async Task TriggerNetInstanceMethod()
      {
          _objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              _objRef);
      }

      public void Dispose()
      {
          _objRef?.Dispose();
      }
  }
  ```

* <span data-ttu-id="e64c5-149">Quando o componente ou a classe não descartar o `DotNetObjectReference`, descarte o objeto no cliente chamando `.dispose()`:</span><span class="sxs-lookup"><span data-stu-id="e64c5-149">When the component or class doesn't dispose of the `DotNetObjectReference`, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="e64c5-150">Chamada de método de instância de componente</span><span class="sxs-lookup"><span data-stu-id="e64c5-150">Component instance method call</span></span>

<span data-ttu-id="e64c5-151">Para invocar os métodos .NET de um componente:</span><span class="sxs-lookup"><span data-stu-id="e64c5-151">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="e64c5-152">Use a função `invokeMethod` ou `invokeMethodAsync` para fazer uma chamada de método estático para o componente.</span><span class="sxs-lookup"><span data-stu-id="e64c5-152">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="e64c5-153">O método estático do componente encapsula a chamada para seu método de instância como um `Action`invocado.</span><span class="sxs-lookup"><span data-stu-id="e64c5-153">The component's static method wraps the call to its instance method as an invoked `Action`.</span></span>

<span data-ttu-id="e64c5-154">No JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="e64c5-154">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="e64c5-155">*Páginas/JSInteropComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="e64c5-155">*Pages/JSInteropComponent.razor*:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @_message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action _action;
    private string _message = "Select the button.";

    protected override void OnInitialized()
    {
        _action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

<span data-ttu-id="e64c5-156">Quando há vários componentes, cada um com métodos de instância para chamar, use uma classe auxiliar para invocar os métodos de instância (como `Action`s) de cada componente.</span><span class="sxs-lookup"><span data-stu-id="e64c5-156">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as `Action`s) of each component.</span></span>

<span data-ttu-id="e64c5-157">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="e64c5-157">In the following example:</span></span>

* <span data-ttu-id="e64c5-158">O componente `JSInterop` contém vários componentes do `ListItem`.</span><span class="sxs-lookup"><span data-stu-id="e64c5-158">The `JSInterop` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="e64c5-159">Cada componente de `ListItem` é composto por uma mensagem e um botão.</span><span class="sxs-lookup"><span data-stu-id="e64c5-159">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="e64c5-160">Quando um botão de componente `ListItem` é selecionado, o método de `UpdateMessage` do `ListItem`altera o texto do item da lista e oculta o botão.</span><span class="sxs-lookup"><span data-stu-id="e64c5-160">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="e64c5-161">*MessageUpdateInvokeHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="e64c5-161">*MessageUpdateInvokeHelper.cs*:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action _action;

    public MessageUpdateInvokeHelper(Action action)
    {
        _action = action;
    }

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

<span data-ttu-id="e64c5-162">No JavaScript do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="e64c5-162">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="e64c5-163">*Compartilhado/ListItem. Razor*:</span><span class="sxs-lookup"><span data-stu-id="e64c5-163">*Shared/ListItem.razor*:</span></span>

```razor
@inject IJSRuntime JsRuntime

<li>
    @_message
    <button @onclick="InteropCall" style="display:@_display">InteropCall</button>
</li>

@code {
    private string _message = "Select one of these list item buttons.";
    private string _display = "inline-block";
    private MessageUpdateInvokeHelper _messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        _messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(_messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        _display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="e64c5-164">*Páginas/JSInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="e64c5-164">*Pages/JSInterop.razor*:</span></span>

```razor
@page "/JSInterop"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="additional-resources"></a><span data-ttu-id="e64c5-165">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="e64c5-165">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="e64c5-166">Exemplo de InteropComponent. Razor (AspNetCore dotnet/repositório GitHub, Branch de lançamento 3,1)</span><span class="sxs-lookup"><span data-stu-id="e64c5-166">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="e64c5-167">[Executar transferências de dados grandes em aplicativos do Blazor Server](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="e64c5-167">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
