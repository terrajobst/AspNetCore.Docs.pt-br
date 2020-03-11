---
title: Chamar uma API da Web de ASP.NET Core Blazor
author: guardrex
description: Saiba como chamar uma API da Web de um aplicativo Blazor usando auxiliares JSON, incluindo a criação de solicitações de compartilhamento de recursos entre origens (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: e6996f0e6731b05038d0a9329152b8afd5f6796d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660142"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a>Chamar uma API da Web de ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Juan de la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[Blazor aplicativos Webassembly](xref:blazor/hosting-models#blazor-webassembly) chamam APIs Web usando um serviço de `HttpClient` pré-configurado. Redação de solicitações, que podem incluir opções de [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de JavaScript, usando Blazor auxiliares JSON ou com <xref:System.Net.Http.HttpRequestMessage>. O serviço de `HttpClient` em Blazor aplicativos Webassembly é voltado para fazer solicitações de volta ao servidor de origem. As diretrizes neste tópico referem-se apenas a Blazor aplicativos Webassembly.

os aplicativos [doBlazor Server](xref:blazor/hosting-models#blazor-server) chamam APIs da web usando <xref:System.Net.Http.HttpClient> instâncias, normalmente criadas usando <xref:System.Net.Http.IHttpClientFactory>. As diretrizes neste tópico não pertencem a aplicativos do Blazor Server. Ao desenvolver aplicativos do Blazor Server, siga as orientações em <xref:fundamentals/http-requests>.

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample)) &ndash; selecione o aplicativo *BlazorWebAssemblySample* .

Consulte os seguintes componentes no aplicativo de exemplo *BlazorWebAssemblySample* :

* Chamar a API Web (*pages/CallWebAPI. Razor*)
* Testador de solicitação HTTP (*componentes/HTTPRequestTester. Razor*)

## <a name="packages"></a>Pacotes

Referencie o [Microsoft. AspNetCore.Blazorexperimental. HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) pacote NuGet no arquivo de projeto. o `Microsoft.AspNetCore.Blazor.HttpClient` é baseado em `HttpClient` e [System. Text. JSON](https://www.nuget.org/packages/System.Text.Json/).

Para usar uma API estável, use o pacote [Microsoft. AspNet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) , que usa [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/)/[JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm). Usar a API estável no `Microsoft.AspNet.WebApi.Client` não fornece os auxiliares JSON descritos neste tópico, que são exclusivos para o pacote de `Microsoft.AspNetCore.Blazor.HttpClient` experimental.

## <a name="httpclient-and-json-helpers"></a>Auxiliares HttpClient e JSON

Em um aplicativo Webassembly Blazor, o [HttpClient](xref:fundamentals/http-requests) está disponível como um serviço pré-configurado para fazer solicitações de volta para o servidor de origem.

Um aplicativo do Blazor Server não inclui um serviço de `HttpClient` por padrão. Forneça um `HttpClient` ao aplicativo usando a [infraestrutura de fábrica do HttpClient](xref:fundamentals/http-requests).

os auxiliares `HttpClient` e JSON também são usados para chamar pontos de extremidade de API Web de terceiros. `HttpClient` é implementado usando a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de navegador e está sujeito a suas limitações, incluindo a imposição da mesma política de origem.

O endereço base do cliente é definido como o endereço do servidor de origem. Injetar uma instância de `HttpClient` usando a diretiva `@inject`:

```razor
@using System.Net.Http
@inject HttpClient Http
```

Nos exemplos a seguir, uma API Web todo processa as operações CRUD (criar, ler, atualizar e excluir). Os exemplos são baseados em uma classe `TodoItem` que armazena:

* ID (`Id`, `long`) &ndash; ID exclusiva do item.
* Nome (`Name`, `string`) &ndash; nome do item.
* Status (`IsComplete`, `bool`) &ndash; indica se o item de tarefas pendentes foi concluído.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Os métodos auxiliares JSON enviam solicitações para um URI (uma API Web nos exemplos a seguir) e processam a resposta:

* `GetJsonAsync` &ndash; envia uma solicitação HTTP GET e analisa o corpo da resposta JSON para criar um objeto.

  No código a seguir, os `_todoItems` são exibidos pelo componente. O método `GetTodoItems` é disparado quando o processamento do componente é concluído ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Consulte o aplicativo de exemplo para obter um exemplo completo.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostJsonAsync` &ndash; envia uma solicitação HTTP POST, incluindo conteúdo codificado em JSON, e analisa o corpo da resposta JSON para criar um objeto.

  No código a seguir, `_newItemName` é fornecido por um elemento associado do componente. O método `AddItem` é disparado selecionando um elemento `<button>`. Consulte o aplicativo de exemplo para obter um exemplo completo.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostJsonAsync("api/TodoItems", addItem);
      }
  }
  ```

* `PutJsonAsync` &ndash; envia uma solicitação HTTP PUT, incluindo conteúdo codificado em JSON.

  No código a seguir, `_editItem` valores para `Name` e `IsCompleted` são fornecidos por elementos associados do componente. O `Id` do item é definido quando o item é selecionado em outra parte da interface do usuário e `EditItem` é chamado. O método `SaveItem` é disparado selecionando o elemento salvar `<button>`. Consulte o aplicativo de exemplo para obter um exemplo completo.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```

o <xref:System.Net.Http> inclui métodos de extensão adicionais para enviar solicitações HTTP e receber respostas HTTP. [HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) é usado para enviar uma solicitação HTTP Delete para uma API da Web.

No código a seguir, o elemento delete `<button>` chama o método `DeleteItem`. O elemento `<input>` associado fornece a `id` do item a ser excluído. Consulte o aplicativo de exemplo para obter um exemplo completo.

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a>CORS (compartilhamento de recursos entre origens)

A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que atuou na página da Web. Essa restrição é chamada de *política de mesma origem*. A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site. Para fazer solicitações do navegador para um ponto de extremidade com uma origem diferente, o *ponto de extremidade* deve habilitar o [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/).

O [aplicativo de exemploBlazor Webassembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstra o uso do CORS no componente chamar API da Web (*pages/CallWebAPI. Razor*).

Para permitir que outros sites façam solicitações de compartilhamento de recursos entre origens (CORS) para seu aplicativo, consulte <xref:security/cors>.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient e HttpRequestMessage com opções de solicitação de API de busca

Ao executar em Webassembly em um aplicativo Webassembly Blazor, use [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> para personalizar solicitações. Por exemplo, você pode especificar o URI de solicitação, o método HTTP e todos os cabeçalhos de solicitação desejados.

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        Http.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                new StringContent(
                    @"{""name"":""A New Todo Item"",""isComplete"":false}")
        };

        requestMessage.Content.Headers.ContentType = 
            new System.Net.Http.Headers.MediaTypeHeaderValue(
                "application/json");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

Para obter mais informações sobre as opções de API de busca, consulte [MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Ao enviar credenciais (cookies/cabeçalhos de autorização) em solicitações de CORS, o cabeçalho de `Authorization` deve ser permitido pela política de CORS.

A política a seguir inclui a configuração para o:

* Origens de solicitação (`http://localhost:5000`, `https://localhost:5001`).
* Qualquer método (verbo).
* cabeçalhos de `Content-Type` e `Authorization`. Para permitir um cabeçalho personalizado (por exemplo, `x-custom-header`), liste o cabeçalho ao chamar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.
* As credenciais definidas pelo código JavaScript do lado do cliente (`credentials` propriedade definida como `include`).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Para obter mais informações, consulte <xref:security/cors> e o componente testador de solicitação HTTP do aplicativo de exemplo (*Components/HTTPRequestTester. Razor*).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Configuração de ponto de extremidade HTTPS Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [CORS (compartilhamento de recursos entre origens) no W3C](https://www.w3.org/TR/cors/)
