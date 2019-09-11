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
# <a name="aspnet-core-blazor-javascript-interop"></a>ASP.NET Core a interoperabilidade de JavaScript mais incrivelmente

Por [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27)e [Luke Latham](https://github.com/guardrex)

Um aplicativo mais incrivelmente pode invocar funções JavaScript de métodos .NET e .NET do código JavaScript.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="invoke-javascript-functions-from-net-methods"></a>Invocar funções JavaScript de métodos .NET

Há ocasiões em que o código .NET é necessário para chamar uma função JavaScript. Por exemplo, uma chamada de JavaScript pode expor recursos de navegador ou funcionalidade de uma biblioteca JavaScript para o aplicativo.

Para chamar o JavaScript do .net, use a `IJSRuntime` abstração. O `InvokeAsync<T>` método usa um identificador para a função JavaScript que você deseja invocar junto com qualquer número de argumentos serializáveis para JSON. O identificador de função é relativo ao escopo global (`window`). Se você quiser chamar `window.someScope.someFunction`, o identificador será. `someScope.someFunction` Não é necessário registrar a função antes que ela seja chamada. O tipo `T` de retorno também deve ser serializável em JSON.

Para aplicativos do lado do servidor:

* Várias solicitações de usuário são processadas pelo aplicativo do lado do servidor. Não chame `JSRuntime.Current` em um componente para invocar funções JavaScript.
* Injetar `IJSRuntime` a abstração e usar o objeto injetado para emitir chamadas de interoperabilidade JavaScript.
* Embora um aplicativo mais incrivelmente seja o pré-processamento, não é possível chamar o JavaScript porque uma conexão com o navegador não foi estabelecida. Para obter mais informações, consulte a seção [detectar quando um aplicativo mais incrivelmente está sendo renderizado](#detect-when-a-blazor-app-is-prerendering) .

O exemplo a seguir é baseado em [textdecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), um decodificador experimental baseado em JavaScript. O exemplo demonstra como invocar uma função JavaScript a partir C# de um método. A função JavaScript aceita uma matriz de bytes de C# um método, decodifica a matriz e retorna o texto para o componente para exibição.

Dentro do `<head>` elemento de *wwwroot/index.html* (mais de um lado do cliente) ou de *pages/_Host. cshtml* (no lado do servidor mais alto), forneça uma `TextDecoder` função que o usa para decodificar uma matriz passada:

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

O código JavaScript, como o código mostrado no exemplo anterior, também pode ser carregado de um arquivo JavaScript ( *. js*) com uma referência ao arquivo de script:

```html
<script src="exampleJsInterop.js"></script>
```

O seguinte componente:

* Invoca a `ConvertArray` função JavaScript usando `JsRuntime` quando um botão de componente (**matriz de conversão**) é selecionado.
* Depois que a função JavaScript é chamada, a matriz passada é convertida em uma cadeia de caracteres. A cadeia de caracteres é retornada para o componente para exibição.

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

Para usar a `IJSRuntime` abstração, adote qualquer uma das seguintes abordagens:

* Injetar `IJSRuntime` a abstração no componente Razor ( *. Razor*):

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* Injetar `IJSRuntime` a abstração em uma classe ( *. cs*):

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* Para a geração de conteúdo dinâmico com [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), `[Inject]` use o atributo:

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

No aplicativo de exemplo do lado do cliente que acompanha este tópico, duas funções JavaScript estão disponíveis para o aplicativo do lado do cliente que interagem com o DOM para receber entrada do usuário e exibir uma mensagem de boas-vindas:

* `showPrompt`&ndash; Produz uma solicitação para aceitar a entrada do usuário (o nome do usuário) e retorna o nome para o chamador.
* `displayWelcome`Atribui uma mensagem de boas-vindas do chamador a um objeto DOM com `id` um `welcome`de. &ndash;

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Coloque a `<script>` marca que faz referência ao arquivo JavaScript no arquivo *wwwroot/index.html* (mais alto do cliente) ou ao arquivo *pages/_Host. cshtml* (no lado do servidor).

*wwwroot/index.html* (No lado do cliente de mais alto):

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

*Páginas/_Host. cshtml* (No lado do servidor do mais alto):

[!code-cshtml[](javascript-interop/samples_snapshot/_Host.cshtml?highlight=29)]

Não coloque uma `<script>` marca em um arquivo de componente porque `<script>` a marca não pode ser atualizada dinamicamente.

Os métodos .NET interoperam com as funções JavaScript no arquivo *exampleJsInterop. js* chamando `IJSRuntime.InvokeAsync<T>`.

A `IJSRuntime` abstração é assíncrona para permitir cenários do lado do servidor. Se o aplicativo for executado no lado do cliente e você quiser invocar uma função JavaScript de forma `IJSInProcessRuntime` síncrona, `Invoke<T>` downcast e chame em seu lugar. Recomendamos que a maioria das bibliotecas de interoperabilidade JavaScript use as APIs assíncronas para garantir que as bibliotecas estejam disponíveis em todos os cenários, no lado do cliente ou no servidor.

O aplicativo de exemplo inclui um componente para demonstrar a interoperabilidade JavaScript. O componente:

* Recebe a entrada do usuário por meio de um prompt do JavaScript.
* Retorna o texto para o componente para processamento.
* Chama uma segunda função JavaScript que interage com o DOM para exibir uma mensagem de boas-vindas.

*Páginas/JSInterop. Razor*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. Quando `TriggerJsPrompt` é executado selecionando o botão de **prompt do JavaScript** do componente de gatilho `showPrompt` , a função JavaScript fornecida no arquivo *wwwroot/exampleJsInterop. js* é chamada.
1. A `showPrompt` função aceita a entrada do usuário (o nome do usuário), que é codificado em HTML e retornada ao componente. O componente armazena o nome do usuário em uma variável local, `name`.
1. A cadeia de caracteres `name` armazenada em é incorporada a uma mensagem de boas-vindas, `displayWelcome`que é passada para uma função JavaScript,, que renderiza a mensagem de boas-vindas em uma marca de cabeçalho.

## <a name="call-a-void-javascript-function"></a>Chamar uma função JavaScript void

As funções JavaScript que retornam [void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) ou [indefinido](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) são `IJSRuntime.InvokeAsync<object>`chamadas com, `null`que retorna.

## <a name="detect-when-a-blazor-app-is-prerendering"></a>Detectar quando um aplicativo mais incrivelmente está prerenderizando
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Capturar referências a elementos

Alguns cenários de [interoperabilidade do JavaScript](xref:blazor/javascript-interop) exigem referências a elementos HTML. Por exemplo, uma biblioteca de interface do usuário pode exigir uma referência de elemento para inicialização, ou talvez seja necessário chamar APIs do tipo comando em um elemento `focus` , `play`como ou.

Capture referências a elementos HTML em um componente usando a seguinte abordagem:

* Adicione um `@ref` atributo ao elemento HTML.
* Defina um campo do tipo `ElementReference` cujo nome corresponde ao valor `@ref` do atributo.

O exemplo a seguir mostra a captura de uma `username` referência ao `<input>` elemento:

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!NOTE]
> Não **use referências** de elemento capturadas como uma forma de popular ou manipular o dom quando o mais fácil interage com os elementos referenciados. Isso pode interferir no modelo de renderização declarativa.

No que diz respeito ao código .net, um `ElementReference` é um identificador opaco. A *única* coisa que você pode fazer `ElementReference` com o é passá-lo para o código JavaScript por meio da interoperabilidade do JavaScript. Quando você faz isso, o código do lado do JavaScript recebe `HTMLElement` uma instância, que pode ser usada com APIs dom normais.

Por exemplo, o código a seguir define um método de extensão .NET que permite definir o foco em um elemento:

*exampleJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Use `IJSRuntime.InvokeAsync<T>` e chame `exampleJsFunctions.focusElement` com um `ElementReference` para focalizar um elemento:

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Para usar um método de extensão para concentrar um elemento, crie um método de extensão estático que `IJSRuntime` receba a instância:

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

O método é chamado diretamente no objeto. O exemplo a seguir pressupõe que o `Focus` método estático está disponível `JsInteropClasses` no namespace:

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> A `username` variável é populada apenas depois que o componente é renderizado. Se um não populado `ElementReference` for passado para o código JavaScript, o código JavaScript receberá um valor de. `null` Para manipular referências de elemento após a conclusão da renderização do componente (para definir o foco inicial em um elemento) `OnAfterRenderAsync` , `OnAfterRender` use os métodos de ciclo de vida do [componente](xref:blazor/components#lifecycle-methods)ou.

## <a name="invoke-net-methods-from-javascript-functions"></a>Invocar métodos .NET de funções JavaScript

### <a name="static-net-method-call"></a>Chamada de método .NET estático

Para invocar um método .net estático do JavaScript, use `DotNet.invokeMethod` as `DotNet.invokeMethodAsync` funções ou. Passe o identificador do método estático que você deseja chamar, o nome do assembly que contém a função e os argumentos. A versão assíncrona é preferida para dar suporte a cenários do lado do servidor. Para invocar um método .net do JavaScript, o método .NET deve ser público, estático e ter o `[JSInvokable]` atributo. Por padrão, o identificador de método é o nome do método, mas você pode especificar um identificador diferente `JSInvokableAttribute` usando o construtor. Atualmente, não há suporte para chamar métodos genéricos abertos.

O aplicativo de exemplo inclui C# um método para retornar uma matriz `int`de s. O `JSInvokable` atributo é aplicado ao método.

*Páginas/JsInterop. Razor*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

O JavaScript servido para o cliente invoca o C# método .net.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Quando o botão **disparar o método estático do .net ReturnArrayAsync** for selecionado, examine a saída do console nas ferramentas de desenvolvedor da Web do navegador.

A saída do console é:

```console
Array(4) [ 1, 2, 3, 4 ]
```

O quarto valor de matriz é enviado por push para`data.push(4);`a matriz ( `ReturnArrayAsync`) retornada por.

### <a name="instance-method-call"></a>Chamada de método de instância

Você também pode chamar os métodos de instância do .NET do JavaScript. Para invocar um método de instância .NET do JavaScript:

* Passe a instância do .net para JavaScript, encapsulando- `DotNetObjectReference` a em uma instância. A instância do .NET é passada por referência ao JavaScript.
* Invoque os métodos de instância do .net na instância `invokeMethod` usando `invokeMethodAsync` as funções ou. A instância do .NET também pode ser passada como um argumento ao invocar outros métodos .NET do JavaScript.

> [!NOTE]
> O aplicativo de exemplo registra as mensagens no console do lado do cliente. Para os exemplos a seguir demonstrados pelo aplicativo de exemplo, examine a saída do console do navegador nas ferramentas de desenvolvedor do navegador.

Quando o botão do **método de instância .net de gatilho HelloHelper. sayHello** é selecionado, `ExampleJsInterop.CallHelloHelperSayHello` é chamado e `Blazor`passa um nome para o método.

*Páginas/JsInterop. Razor*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

`CallHelloHelperSayHello`Invoca a função `sayHello` JavaScript com uma nova instância do `HelloHelper`.

*JsInteropClasses/ExampleJsInterop. cs*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

O nome é passado para `HelloHelper`o construtor de, que define `HelloHelper.Name` a propriedade. Quando a função `sayHello` JavaScript é executada, `HelloHelper.SayHello` retorna a `Hello, {Name}!` mensagem, que é gravada no console pela função JavaScript.

*JsInteropClasses/HelloHelper.cs*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Saída do console nas ferramentas de desenvolvedor da Web do navegador:

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a>Compartilhar código de interoperabilidade em uma biblioteca de classes

O código de interoperabilidade JavaScript pode ser incluído em uma biblioteca de classes, que permite que você compartilhe o código em um pacote NuGet.

A biblioteca de classes lida com a inserção de recursos JavaScript no assembly compilado. Os arquivos JavaScript são colocados na pasta *wwwroot* . As ferramentas cuidam da inserção dos recursos quando a biblioteca é criada.

O pacote NuGet criado é referenciado no arquivo de projeto do aplicativo da mesma forma que qualquer pacote NuGet é referenciado. Depois que o pacote é restaurado, o código do aplicativo pode chamar o JavaScript C#como se fosse.

Para obter mais informações, consulte <xref:blazor/class-libraries>.

## <a name="harden-js-interop-calls"></a>Proteger chamadas Interop JS

A interoperabilidade JS pode falhar devido a erros de rede e deve ser tratada como não confiável. Por padrão, um aplicativo de servidor mais incrivelmente atinge as chamadas de interoperabilidade JS no servidor após um minuto. Se um aplicativo puder tolerar um tempo limite mais agressivo, como 10 segundos, defina o tempo limite usando uma das seguintes abordagens:

* Globalmente no `Startup.ConfigureServices`, especifique o tempo limite:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Por invocação no código do componente, uma única chamada pode especificar o tempo limite:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Para obter mais informações sobre esgotamento de <xref:security/blazor/server-side>recursos, consulte.
