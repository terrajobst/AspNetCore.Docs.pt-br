---
title: Chamar uma API Web do ASP.NET Core mais
author: guardrex
description: Saiba como chamar uma API da Web de um aplicativo mais novo usando auxiliares JSON, incluindo a criação de solicitações de CORS (compartilhamento de recursos entre origens).
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/call-web-api
ms.openlocfilehash: 23131ac0357b722e7f229fcfe5dab8590cf34739
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168054"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Chamar uma API Web do ASP.NET Core mais

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Aplicativos Webassembly mais incrivelmente chamam APIs da Web usando um `HttpClient` serviço pré-configurado. Redação de solicitações, que podem incluir opções de API de busca de JavaScript, usando auxiliares de <xref:System.Net.Http.HttpRequestMessage>JSON mais [populadores](https://developer.mozilla.org/docs/Web/API/Fetch_API) ou com o.

Aplicativos de servidor mais incrivelmente chamam APIs da <xref:System.Net.Http.HttpClient> Web usando instâncias normalmente <xref:System.Net.Http.IHttpClientFactory>criadas usando o. Para obter mais informações, consulte <xref:fundamentals/http-requests>.

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))

Para obter exemplos de Webassembly mais incrivelmente, consulte os seguintes componentes no aplicativo de exemplo:

* Chamar a API Web (*pages/CallWebAPI. Razor*)
* Testador de solicitação HTTP (*componentes/HTTPRequestTester. Razor*)

## <a name="httpclient-and-json-helpers"></a>Auxiliares HttpClient e JSON

Em aplicativos Webassembly mais podestas, o [HttpClient](xref:fundamentals/http-requests) está disponível como um serviço pré-configurado para fazer solicitações de volta ao servidor de origem. Para usar `HttpClient` auxiliares JSON, adicione uma referência de pacote `Microsoft.AspNetCore.Blazor.HttpClient`a. `HttpClient`e auxiliares JSON também são usados para chamar pontos de extremidade de API Web de terceiros. `HttpClient`é implementado usando a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de navegador e está sujeito a suas limitações, incluindo a imposição da mesma política de origem.

O endereço base do cliente é definido como o endereço do servidor de origem. Injetar `HttpClient` uma instância usando `@inject` a diretiva:

```cshtml
@using System.Net.Http
@inject HttpClient Http
```

Nos exemplos a seguir, uma API Web todo processa as operações CRUD (criar, ler, atualizar e excluir). Os exemplos são baseados em uma `TodoItem` classe que armazena:

* ID (`Id`, `long`) &ndash; ID exclusiva do item.
* Nome (`Name`, `string`) &ndash; nome do item.
* Indicação de`IsComplete`status `bool`( &ndash; ,) se o item de tarefas pendentes for concluído.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Os métodos auxiliares JSON enviam solicitações para um URI (uma API Web nos exemplos a seguir) e processam a resposta:

* `GetJsonAsync`&ndash; Envia uma solicitação HTTP Get e analisa o corpo da resposta JSON para criar um objeto.

  No código a seguir, os `_todoItems` são exibidos pelo componente. O `GetTodoItems` método é disparado quando o processamento do componente é concluído ([OnInitializedAsync](xref:blazor/components#lifecycle-methods)). Consulte o aplicativo de exemplo para obter um exemplo completo.

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/todo");
  }
  ```

* `PostJsonAsync`&ndash; Envia uma solicitação HTTP post, incluindo conteúdo codificado em JSON, e analisa o corpo da resposta JSON para criar um objeto.

  No código a seguir, `_newItemName` é fornecido por um elemento associado do componente. O `AddItem` método é disparado selecionando um `<button>` elemento. Consulte o aplicativo de exemplo para obter um exemplo completo.

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
          await Http.PostJsonAsync("api/todo", addItem);
      }
  }
  ```

* `PutJsonAsync`&ndash; Envia uma solicitação HTTP PUT, incluindo conteúdo codificado em JSON.

  No código a seguir, `_editItem` os valores `Name` para `IsCompleted` e são fornecidos pelos elementos associados do componente. O item `Id` é definido quando o item é selecionado em outra parte da interface do usuário e `EditItem` é chamado. O `SaveItem` método é disparado selecionando `<button>` o elemento Save. Consulte o aplicativo de exemplo para obter um exemplo completo.

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
          await Http.PutJsonAsync($"api/todo/{_editItem.Id}, _editItem);
  }
  ```

<xref:System.Net.Http>inclui métodos de extensão adicionais para enviar solicitações HTTP e receber respostas HTTP. [HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) é usado para enviar uma solicitação HTTP Delete para uma API da Web.

No código a seguir, o elemento `<button>` Delete chama o `DeleteItem` método. O elemento `<input>` associado fornece o `id` do item a ser excluído. Consulte o aplicativo de exemplo para obter um exemplo completo.

```cshtml
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/todo/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a>CORS (compartilhamento de recursos entre origens)

A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que atuou na página da Web. Essa restrição é chamada de *política de mesma origem*. A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site. Para fazer solicitações do navegador para um ponto de extremidade com uma origem diferente, o *ponto de extremidade* deve habilitar o [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/).

O aplicativo de exemplo demonstra o uso de CORS no componente de chamada de API da Web (*pages/CallWebAPI. Razor*).

Para permitir que outros sites façam solicitações de compartilhamento de recursos entre origens (CORS) para seu aplicativo, <xref:security/cors>consulte.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient e HttpRequestMessage com opções de solicitação de API de busca

Ao executar em Webassembly em um aplicativo Webassembly mais incrivelmente, use [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> para personalizar solicitações. Por exemplo, você pode especificar o URI de solicitação, o método HTTP e todos os cabeçalhos de solicitação desejados.

Forneça opções de solicitação para a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de JavaScript `WebAssemblyHttpMessageHandler.FetchArgs` subjacente usando a propriedade na solicitação. Conforme mostrado no exemplo a seguir, a `credentials` propriedade é definida como qualquer um dos seguintes valores:

* `FetchCredentialsOption.Include`("incluir") &ndash; Aconselha o navegador a enviar credenciais (como cookies ou cabeçalhos de autenticação http) mesmo para solicitações entre origens. Permitido somente quando a política CORS está configurada para permitir credenciais.
* `FetchCredentialsOption.Omit`("omitir") &ndash; Aconselha o navegador a nunca enviar credenciais (como cookies ou cabeçalhos de autenticação http).
* `FetchCredentialsOption.SameOrigin`("mesma origem") &ndash; Aconselha o navegador a enviar credenciais (como cookies ou cabeçalhos de autenticação http) somente se a URL de destino estiver na mesma origem que o aplicativo de chamada.

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
            RequestUri = new Uri("https://localhost:10000/api/todo"),
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

Para obter mais informações sobre as opções de API [de busca, consulte MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Ao enviar credenciais (cookies/cabeçalhos de autorização) em solicitações CORS, `Authorization` o cabeçalho deve ser permitido pela política CORS.

A política a seguir inclui a configuração para o:

* Origens da solicitação`http://localhost:5000`( `https://localhost:5001`,).
* Qualquer método (verbo).
* `Content-Type`cabeçalhos `Authorization` e. Para permitir um cabeçalho personalizado (por exemplo, `x-custom-header`), liste o cabeçalho ao <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>chamar.
* Credenciais definidas pelo código JavaScript do lado do cliente`credentials` (propriedade definida `include`como).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Para obter mais informações, <xref:security/cors> consulte e o componente testador de solicitação HTTP do aplicativo de exemplo (*Components/HTTPRequestTester. Razor*).

## <a name="additional-resources"></a>Recursos adicionais

* <xref:fundamentals/http-requests>
* [CORS (compartilhamento de recursos entre origens) no W3C](https://www.w3.org/TR/cors/)
