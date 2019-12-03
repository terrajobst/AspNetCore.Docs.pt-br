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
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="a2127-103">Chamar uma API da Web de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="a2127-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="a2127-104">Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="a2127-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="a2127-105"> aplicativos Webassembly chamam APIs Web usando um serviço de `HttpClient` pré-configurado.</span><span class="sxs-lookup"><span data-stu-id="a2127-105"> WebAssembly apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="a2127-106">Redação de solicitações, que podem incluir opções de [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de JavaScript, usando Blazor auxiliares JSON ou com <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="a2127-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span>

<span data-ttu-id="a2127-107">os aplicativos do Blazor Server chamam APIs da Web usando <xref:System.Net.Http.HttpClient> instâncias normalmente criadas usando <xref:System.Net.Http.IHttpClientFactory>.</span><span class="sxs-lookup"><span data-stu-id="a2127-107">Blazor Server apps call web APIs using <xref:System.Net.Http.HttpClient> instances typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="a2127-108">Para obter mais informações, consulte <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="a2127-108">For more information, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="a2127-109">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a2127-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a2127-110">Para obter Blazor exemplos de Webassembly, consulte os seguintes componentes no aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="a2127-110">For Blazor WebAssembly examples, see the following components in the sample app:</span></span>

* <span data-ttu-id="a2127-111">Chamar a API Web (*pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="a2127-111">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="a2127-112">Testador de solicitação HTTP (*componentes/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="a2127-112">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="a2127-113">Auxiliares HttpClient e JSON</span><span class="sxs-lookup"><span data-stu-id="a2127-113">HttpClient and JSON helpers</span></span>

<span data-ttu-id="a2127-114">Em Blazor aplicativos Webassembly, o [HttpClient](xref:fundamentals/http-requests) está disponível como um serviço pré-configurado para fazer solicitações de volta ao servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="a2127-114">In Blazor WebAssembly apps, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span> <span data-ttu-id="a2127-115">Para usar `HttpClient` auxiliares JSON, adicione uma referência de pacote a `Microsoft.AspNetCore.Blazor.HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="a2127-115">To use `HttpClient` JSON helpers, add a package reference to `Microsoft.AspNetCore.Blazor.HttpClient`.</span></span> <span data-ttu-id="a2127-116">os auxiliares `HttpClient` e JSON também são usados para chamar pontos de extremidade de API Web de terceiros.</span><span class="sxs-lookup"><span data-stu-id="a2127-116">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="a2127-117">`HttpClient` é implementado usando a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de navegador e está sujeito a suas limitações, incluindo a imposição da mesma política de origem.</span><span class="sxs-lookup"><span data-stu-id="a2127-117">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="a2127-118">O endereço base do cliente é definido como o endereço do servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="a2127-118">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="a2127-119">Injetar uma instância de `HttpClient` usando a diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="a2127-119">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```cshtml
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="a2127-120">Nos exemplos a seguir, uma API Web todo processa as operações CRUD (criar, ler, atualizar e excluir).</span><span class="sxs-lookup"><span data-stu-id="a2127-120">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="a2127-121">Os exemplos são baseados em uma classe `TodoItem` que armazena:</span><span class="sxs-lookup"><span data-stu-id="a2127-121">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="a2127-122">ID (`Id`, `long`) &ndash; ID exclusiva do item.</span><span class="sxs-lookup"><span data-stu-id="a2127-122">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="a2127-123">Nome (`Name`, `string`) &ndash; nome do item.</span><span class="sxs-lookup"><span data-stu-id="a2127-123">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="a2127-124">Status (`IsComplete`, `bool`) &ndash; indica se o item de tarefas pendentes foi concluído.</span><span class="sxs-lookup"><span data-stu-id="a2127-124">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="a2127-125">Os métodos auxiliares JSON enviam solicitações para um URI (uma API Web nos exemplos a seguir) e processam a resposta:</span><span class="sxs-lookup"><span data-stu-id="a2127-125">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="a2127-126">`GetJsonAsync` &ndash; envia uma solicitação HTTP GET e analisa o corpo da resposta JSON para criar um objeto.</span><span class="sxs-lookup"><span data-stu-id="a2127-126">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="a2127-127">No código a seguir, os `_todoItems` são exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="a2127-127">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="a2127-128">O método `GetTodoItems` é disparado quando o processamento do componente é concluído ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="a2127-128">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="a2127-129">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="a2127-129">See the sample app for a complete example.</span></span>

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="a2127-130">`PostJsonAsync` &ndash; envia uma solicitação HTTP POST, incluindo conteúdo codificado em JSON, e analisa o corpo da resposta JSON para criar um objeto.</span><span class="sxs-lookup"><span data-stu-id="a2127-130">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="a2127-131">No código a seguir, `_newItemName` é fornecido por um elemento associado do componente.</span><span class="sxs-lookup"><span data-stu-id="a2127-131">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="a2127-132">O método `AddItem` é disparado selecionando um elemento `<button>`.</span><span class="sxs-lookup"><span data-stu-id="a2127-132">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="a2127-133">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="a2127-133">See the sample app for a complete example.</span></span>

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

* <span data-ttu-id="a2127-134">`PutJsonAsync` &ndash; envia uma solicitação HTTP PUT, incluindo conteúdo codificado em JSON.</span><span class="sxs-lookup"><span data-stu-id="a2127-134">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="a2127-135">No código a seguir, `_editItem` valores para `Name` e `IsCompleted` são fornecidos por elementos associados do componente.</span><span class="sxs-lookup"><span data-stu-id="a2127-135">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="a2127-136">O `Id` do item é definido quando o item é selecionado em outra parte da interface do usuário e `EditItem` é chamado.</span><span class="sxs-lookup"><span data-stu-id="a2127-136">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="a2127-137">O método `SaveItem` é disparado selecionando o elemento salvar `<button>`.</span><span class="sxs-lookup"><span data-stu-id="a2127-137">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="a2127-138">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="a2127-138">See the sample app for a complete example.</span></span>

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

<span data-ttu-id="a2127-139">o <xref:System.Net.Http> inclui métodos de extensão adicionais para enviar solicitações HTTP e receber respostas HTTP.</span><span class="sxs-lookup"><span data-stu-id="a2127-139"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="a2127-140">[HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) é usado para enviar uma solicitação HTTP Delete para uma API da Web.</span><span class="sxs-lookup"><span data-stu-id="a2127-140">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="a2127-141">No código a seguir, o elemento delete `<button>` chama o método `DeleteItem`.</span><span class="sxs-lookup"><span data-stu-id="a2127-141">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="a2127-142">O elemento `<input>` associado fornece a `id` do item a ser excluído.</span><span class="sxs-lookup"><span data-stu-id="a2127-142">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="a2127-143">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="a2127-143">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="a2127-144">CORS (compartilhamento de recursos entre origens)</span><span class="sxs-lookup"><span data-stu-id="a2127-144">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="a2127-145">A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que atuou na página da Web.</span><span class="sxs-lookup"><span data-stu-id="a2127-145">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="a2127-146">Essa restrição é chamada de *política de mesma origem*.</span><span class="sxs-lookup"><span data-stu-id="a2127-146">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="a2127-147">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="a2127-147">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="a2127-148">Para fazer solicitações do navegador para um ponto de extremidade com uma origem diferente, o *ponto de extremidade* deve habilitar o [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="a2127-148">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="a2127-149">O aplicativo de exemplo demonstra o uso de CORS no componente de chamada de API da Web (*pages/CallWebAPI. Razor*).</span><span class="sxs-lookup"><span data-stu-id="a2127-149">The sample app demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="a2127-150">Para permitir que outros sites façam solicitações de compartilhamento de recursos entre origens (CORS) para seu aplicativo, consulte <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="a2127-150">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="a2127-151">HttpClient e HttpRequestMessage com opções de solicitação de API de busca</span><span class="sxs-lookup"><span data-stu-id="a2127-151">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="a2127-152">Ao executar em Webassembly em um aplicativo Webassembly Blazor, use [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> para personalizar solicitações.</span><span class="sxs-lookup"><span data-stu-id="a2127-152">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="a2127-153">Por exemplo, você pode especificar o URI de solicitação, o método HTTP e todos os cabeçalhos de solicitação desejados.</span><span class="sxs-lookup"><span data-stu-id="a2127-153">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

<span data-ttu-id="a2127-154">Forneça opções de solicitação para a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de JavaScript subjacente usando a propriedade `WebAssemblyHttpMessageHandler.FetchArgs` na solicitação.</span><span class="sxs-lookup"><span data-stu-id="a2127-154">Supply request options to the underlying JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) using the `WebAssemblyHttpMessageHandler.FetchArgs` property on the request.</span></span> <span data-ttu-id="a2127-155">Conforme mostrado no exemplo a seguir, a propriedade `credentials` é definida como qualquer um dos seguintes valores:</span><span class="sxs-lookup"><span data-stu-id="a2127-155">As shown in the following example, the `credentials` property is set to any of the following values:</span></span>

* <span data-ttu-id="a2127-156">`FetchCredentialsOption.Include` ("include") &ndash; aconselha o navegador a enviar credenciais (como cookies ou cabeçalhos de autenticação HTTP) mesmo para solicitações entre origens.</span><span class="sxs-lookup"><span data-stu-id="a2127-156">`FetchCredentialsOption.Include` ("include") &ndash; Advises the browser to send credentials (such as cookies or HTTP authentication headers) even for cross-origin requests.</span></span> <span data-ttu-id="a2127-157">Permitido somente quando a política CORS está configurada para permitir credenciais.</span><span class="sxs-lookup"><span data-stu-id="a2127-157">Only allowed when the CORS policy is configured to allow credentials.</span></span>
* <span data-ttu-id="a2127-158">`FetchCredentialsOption.Omit` ("omitir") &ndash; aconselha que o navegador nunca envie credenciais (como cookies ou cabeçalhos HTTP auth).</span><span class="sxs-lookup"><span data-stu-id="a2127-158">`FetchCredentialsOption.Omit` ("omit") &ndash; Advises the browser never to send credentials (such as cookies or HTTP auth headers).</span></span>
* <span data-ttu-id="a2127-159">`FetchCredentialsOption.SameOrigin` ("mesma origem") &ndash; aconselha o navegador a enviar credenciais (como cookies ou cabeçalhos de autenticação HTTP) somente se a URL de destino estiver na mesma origem que o aplicativo de chamada.</span><span class="sxs-lookup"><span data-stu-id="a2127-159">`FetchCredentialsOption.SameOrigin` ("same-origin") &ndash; Advises the browser to send credentials (such as cookies or HTTP auth headers) only if the target URL is on the same origin as the calling application.</span></span>

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

<span data-ttu-id="a2127-160">Para obter mais informações sobre as opções de API de busca, consulte [MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="a2127-160">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="a2127-161">Ao enviar credenciais (cookies/cabeçalhos de autorização) em solicitações de CORS, o cabeçalho de `Authorization` deve ser permitido pela política de CORS.</span><span class="sxs-lookup"><span data-stu-id="a2127-161">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="a2127-162">A política a seguir inclui a configuração para o:</span><span class="sxs-lookup"><span data-stu-id="a2127-162">The following policy includes configuration for:</span></span>

* <span data-ttu-id="a2127-163">Origens de solicitação (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="a2127-163">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="a2127-164">Qualquer método (verbo).</span><span class="sxs-lookup"><span data-stu-id="a2127-164">Any method (verb).</span></span>
* <span data-ttu-id="a2127-165">cabeçalhos de `Content-Type` e `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="a2127-165">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="a2127-166">Para permitir um cabeçalho personalizado (por exemplo, `x-custom-header`), liste o cabeçalho ao chamar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="a2127-166">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="a2127-167">As credenciais definidas pelo código JavaScript do lado do cliente (`credentials` propriedade definida como `include`).</span><span class="sxs-lookup"><span data-stu-id="a2127-167">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="a2127-168">Para obter mais informações, consulte <xref:security/cors> e o componente testador de solicitação HTTP do aplicativo de exemplo (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="a2127-168">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a2127-169">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="a2127-169">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="a2127-170">Configuração de ponto de extremidade HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="a2127-170">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="a2127-171">CORS (compartilhamento de recursos entre origens) no W3C</span><span class="sxs-lookup"><span data-stu-id="a2127-171">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
