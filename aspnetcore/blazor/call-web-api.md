---
title: Chamar uma API da Web de ASP.NET Core Blazor
author: guardrex
description: Saiba como chamar uma API da Web de um aplicativo Blazor usando auxiliares JSON, incluindo a criação de solicitações de compartilhamento de recursos entre origens (CORS).
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/23/2019
no-loc:
- Blazor
uid: blazor/call-web-api
ms.openlocfilehash: ffc9904c5746fbf0fafa10cf054666608942650c
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74680896"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a>Chamar uma API da Web de ASP.NET Core Blazor

Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Juan de la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor aplicativos Webassembly chamam APIs Web usando um serviço de `HttpClient` pré-configurado. Redação de solicitações, que podem incluir opções de [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de JavaScript, usando Blazor auxiliares JSON ou com <xref:System.Net.Http.HttpRequestMessage>.

os aplicativos do Blazor Server chamam APIs da Web usando <xref:System.Net.Http.HttpClient> instâncias normalmente criadas usando <xref:System.Net.Http.IHttpClientFactory>. Para obter mais informações, consulte <xref:fundamentals/http-requests>.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))

Para obter Blazor exemplos de Webassembly, consulte os seguintes componentes no aplicativo de exemplo:

* Chamar a API Web (*pages/CallWebAPI. Razor*)
* Testador de solicitação HTTP (*componentes/HTTPRequestTester. Razor*)

## <a name="httpclient-and-json-helpers"></a>Auxiliares HttpClient e JSON

Em Blazor aplicativos Webassembly, o [HttpClient](xref:fundamentals/http-requests) está disponível como um serviço pré-configurado para fazer solicitações de volta ao servidor de origem. Para usar `HttpClient` auxiliares JSON, adicione uma referência de pacote a `Microsoft.AspNetCore.Blazor.HttpClient`. os auxiliares `HttpClient` e JSON também são usados para chamar pontos de extremidade de API Web de terceiros. `HttpClient` é implementado usando a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de navegador e está sujeito a suas limitações, incluindo a imposição da mesma política de origem.

O endereço base do cliente é definido como o endereço do servidor de origem. Injetar uma instância de `HttpClient` usando a diretiva `@inject`:

```cshtml
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

  ```cshtml
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

  ```cshtml
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

  ```cshtml
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

```cshtml
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

O aplicativo de exemplo demonstra o uso de CORS no componente de chamada de API da Web (*pages/CallWebAPI. Razor*).

Para permitir que outros sites façam solicitações de compartilhamento de recursos entre origens (CORS) para seu aplicativo, consulte <xref:security/cors>.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient e HttpRequestMessage com opções de solicitação de API de busca

Ao executar em Webassembly em um aplicativo Webassembly Blazor, use [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> para personalizar solicitações. Por exemplo, você pode especificar o URI de solicitação, o método HTTP e todos os cabeçalhos de solicitação desejados.

Forneça opções de solicitação para a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de JavaScript subjacente usando a propriedade `WebAssemblyHttpMessageHandler.FetchArgs` na solicitação. Conforme mostrado no exemplo a seguir, a propriedade `credentials` é definida como qualquer um dos seguintes valores:

* `FetchCredentialsOption.Include` ("include") &ndash; aconselha o navegador a enviar credenciais (como cookies ou cabeçalhos de autenticação HTTP) mesmo para solicitações entre origens. Permitido somente quando a política CORS está configurada para permitir credenciais.
* `FetchCredentialsOption.Omit` ("omitir") &ndash; aconselha que o navegador nunca envie credenciais (como cookies ou cabeçalhos HTTP auth).
* `FetchCredentialsOption.SameOrigin` ("mesma origem") &ndash; aconselha o navegador a enviar credenciais (como cookies ou cabeçalhos de autenticação HTTP) somente se a URL de destino estiver na mesma origem que o aplicativo de chamada.

```cshtml
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
        
        requestMessage.Properties[WebAssemblyHttpMessageHandler.FetchArgs] = new
        { 
            credentials = FetchCredentialsOption.Include
        };

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
