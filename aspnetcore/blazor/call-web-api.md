---
title: Chamar uma API da Web de ASP.NET Core Blazor
author: guardrex
description: Saiba como chamar uma API da Web de um aplicativo Blazor usando auxiliares JSON, incluindo a criação de solicitações de compartilhamento de recursos entre origens (CORS).
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/call-web-api
ms.openlocfilehash: f1929b48275a36552f061a64823267df0f3acabc
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943908"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="b89cf-103">Chamar uma API da Web de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b89cf-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="b89cf-104">Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)e [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="b89cf-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="b89cf-105">[Blazor aplicativos Webassembly](xref:blazor/hosting-models#blazor-webassembly) chamam APIs Web usando um serviço de `HttpClient` pré-configurado.</span><span class="sxs-lookup"><span data-stu-id="b89cf-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="b89cf-106">Redação de solicitações, que podem incluir opções de [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de JavaScript, usando Blazor auxiliares JSON ou com <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="b89cf-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span>

<span data-ttu-id="b89cf-107">os aplicativos [doBlazor Server](xref:blazor/hosting-models#blazor-server) chamam APIs da web usando <xref:System.Net.Http.HttpClient> instâncias normalmente criadas usando <xref:System.Net.Http.IHttpClientFactory>.</span><span class="sxs-lookup"><span data-stu-id="b89cf-107">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="b89cf-108">Para obter mais informações, consulte <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="b89cf-108">For more information, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="b89cf-109">[Exiba ou baixe o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample)) &ndash; selecione o aplicativo *BlazorWebAssemblySample* .</span><span class="sxs-lookup"><span data-stu-id="b89cf-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="b89cf-110">Consulte os seguintes componentes no aplicativo de exemplo *BlazorWebAssemblySample* :</span><span class="sxs-lookup"><span data-stu-id="b89cf-110">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="b89cf-111">Chamar a API Web (*pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="b89cf-111">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="b89cf-112">Testador de solicitação HTTP (*componentes/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="b89cf-112">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="b89cf-113">Pacotes</span><span class="sxs-lookup"><span data-stu-id="b89cf-113">Packages</span></span>

<span data-ttu-id="b89cf-114">Referencie o [Microsoft. AspNetCore.Blazorexperimental. HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) pacote NuGet no arquivo de projeto.</span><span class="sxs-lookup"><span data-stu-id="b89cf-114">Reference the *experimental* [Microsoft.AspNetCore.Blazor.HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet package in the project file.</span></span> <span data-ttu-id="b89cf-115">o `Microsoft.AspNetCore.Blazor.HttpClient` é baseado em `HttpClient` e [System. Text. JSON](https://www.nuget.org/packages/System.Text.Json/).</span><span class="sxs-lookup"><span data-stu-id="b89cf-115">`Microsoft.AspNetCore.Blazor.HttpClient` is based on `HttpClient` and [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/).</span></span>

<span data-ttu-id="b89cf-116">Para usar uma API estável, use o pacote [Microsoft. AspNet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) , que usa [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/)/[JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="b89cf-116">To use a stable API, use the [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) package, which uses [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span> <span data-ttu-id="b89cf-117">Usar a API estável no `Microsoft.AspNet.WebApi.Client` não fornece os auxiliares JSON descritos neste tópico, que são exclusivos para o pacote de `Microsoft.AspNetCore.Blazor.HttpClient` experimental.</span><span class="sxs-lookup"><span data-stu-id="b89cf-117">Using the stable API in `Microsoft.AspNet.WebApi.Client` doesn't provide the JSON helpers described in this topic, which are unique to the experimental `Microsoft.AspNetCore.Blazor.HttpClient` package.</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="b89cf-118">Auxiliares HttpClient e JSON</span><span class="sxs-lookup"><span data-stu-id="b89cf-118">HttpClient and JSON helpers</span></span>

<span data-ttu-id="b89cf-119">Em um aplicativo Webassembly Blazor, o [HttpClient](xref:fundamentals/http-requests) está disponível como um serviço pré-configurado para fazer solicitações de volta para o servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="b89cf-119">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="b89cf-120">Um aplicativo do Blazor Server não inclui um serviço de `HttpClient` por padrão.</span><span class="sxs-lookup"><span data-stu-id="b89cf-120">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="b89cf-121">Forneça um `HttpClient` ao aplicativo usando a [infraestrutura de fábrica do HttpClient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="b89cf-121">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="b89cf-122">os auxiliares `HttpClient` e JSON também são usados para chamar pontos de extremidade de API Web de terceiros.</span><span class="sxs-lookup"><span data-stu-id="b89cf-122">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="b89cf-123">`HttpClient` é implementado usando a [API de busca](https://developer.mozilla.org/docs/Web/API/Fetch_API) de navegador e está sujeito a suas limitações, incluindo a imposição da mesma política de origem.</span><span class="sxs-lookup"><span data-stu-id="b89cf-123">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="b89cf-124">O endereço base do cliente é definido como o endereço do servidor de origem.</span><span class="sxs-lookup"><span data-stu-id="b89cf-124">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="b89cf-125">Injetar uma instância de `HttpClient` usando a diretiva `@inject`:</span><span class="sxs-lookup"><span data-stu-id="b89cf-125">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="b89cf-126">Nos exemplos a seguir, uma API Web todo processa as operações CRUD (criar, ler, atualizar e excluir).</span><span class="sxs-lookup"><span data-stu-id="b89cf-126">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="b89cf-127">Os exemplos são baseados em uma classe `TodoItem` que armazena:</span><span class="sxs-lookup"><span data-stu-id="b89cf-127">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="b89cf-128">ID (`Id`, `long`) &ndash; ID exclusiva do item.</span><span class="sxs-lookup"><span data-stu-id="b89cf-128">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="b89cf-129">Nome (`Name`, `string`) &ndash; nome do item.</span><span class="sxs-lookup"><span data-stu-id="b89cf-129">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="b89cf-130">Status (`IsComplete`, `bool`) &ndash; indica se o item de tarefas pendentes foi concluído.</span><span class="sxs-lookup"><span data-stu-id="b89cf-130">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="b89cf-131">Os métodos auxiliares JSON enviam solicitações para um URI (uma API Web nos exemplos a seguir) e processam a resposta:</span><span class="sxs-lookup"><span data-stu-id="b89cf-131">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="b89cf-132">`GetJsonAsync` &ndash; envia uma solicitação HTTP GET e analisa o corpo da resposta JSON para criar um objeto.</span><span class="sxs-lookup"><span data-stu-id="b89cf-132">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="b89cf-133">No código a seguir, os `_todoItems` são exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="b89cf-133">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="b89cf-134">O método `GetTodoItems` é disparado quando o processamento do componente é concluído ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="b89cf-134">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="b89cf-135">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="b89cf-135">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="b89cf-136">`PostJsonAsync` &ndash; envia uma solicitação HTTP POST, incluindo conteúdo codificado em JSON, e analisa o corpo da resposta JSON para criar um objeto.</span><span class="sxs-lookup"><span data-stu-id="b89cf-136">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="b89cf-137">No código a seguir, `_newItemName` é fornecido por um elemento associado do componente.</span><span class="sxs-lookup"><span data-stu-id="b89cf-137">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="b89cf-138">O método `AddItem` é disparado selecionando um elemento `<button>`.</span><span class="sxs-lookup"><span data-stu-id="b89cf-138">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="b89cf-139">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="b89cf-139">See the sample app for a complete example.</span></span>

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

* <span data-ttu-id="b89cf-140">`PutJsonAsync` &ndash; envia uma solicitação HTTP PUT, incluindo conteúdo codificado em JSON.</span><span class="sxs-lookup"><span data-stu-id="b89cf-140">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="b89cf-141">No código a seguir, `_editItem` valores para `Name` e `IsCompleted` são fornecidos por elementos associados do componente.</span><span class="sxs-lookup"><span data-stu-id="b89cf-141">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="b89cf-142">O `Id` do item é definido quando o item é selecionado em outra parte da interface do usuário e `EditItem` é chamado.</span><span class="sxs-lookup"><span data-stu-id="b89cf-142">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="b89cf-143">O método `SaveItem` é disparado selecionando o elemento salvar `<button>`.</span><span class="sxs-lookup"><span data-stu-id="b89cf-143">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="b89cf-144">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="b89cf-144">See the sample app for a complete example.</span></span>

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

<span data-ttu-id="b89cf-145">o <xref:System.Net.Http> inclui métodos de extensão adicionais para enviar solicitações HTTP e receber respostas HTTP.</span><span class="sxs-lookup"><span data-stu-id="b89cf-145"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="b89cf-146">[HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) é usado para enviar uma solicitação HTTP Delete para uma API da Web.</span><span class="sxs-lookup"><span data-stu-id="b89cf-146">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="b89cf-147">No código a seguir, o elemento delete `<button>` chama o método `DeleteItem`.</span><span class="sxs-lookup"><span data-stu-id="b89cf-147">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="b89cf-148">O elemento `<input>` associado fornece a `id` do item a ser excluído.</span><span class="sxs-lookup"><span data-stu-id="b89cf-148">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="b89cf-149">Consulte o aplicativo de exemplo para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="b89cf-149">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="b89cf-150">CORS (compartilhamento de recursos entre origens)</span><span class="sxs-lookup"><span data-stu-id="b89cf-150">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="b89cf-151">A segurança do navegador impede que uma página da Web faça solicitações para um domínio diferente daquele que atuou na página da Web.</span><span class="sxs-lookup"><span data-stu-id="b89cf-151">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="b89cf-152">Essa restrição é chamada de *política de mesma origem*.</span><span class="sxs-lookup"><span data-stu-id="b89cf-152">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="b89cf-153">A política de mesma origem impede que um site mal-intencionado leia dados confidenciais de outro site.</span><span class="sxs-lookup"><span data-stu-id="b89cf-153">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="b89cf-154">Para fazer solicitações do navegador para um ponto de extremidade com uma origem diferente, o *ponto de extremidade* deve habilitar o [CORS (compartilhamento de recursos entre origens)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="b89cf-154">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="b89cf-155">O [aplicativo de exemploBlazor Webassembly (BlazorWebAssemblySample)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstra o uso do CORS no componente chamar API da Web (*pages/CallWebAPI. Razor*).</span><span class="sxs-lookup"><span data-stu-id="b89cf-155">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="b89cf-156">Para permitir que outros sites façam solicitações de compartilhamento de recursos entre origens (CORS) para seu aplicativo, consulte <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="b89cf-156">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="b89cf-157">HttpClient e HttpRequestMessage com opções de solicitação de API de busca</span><span class="sxs-lookup"><span data-stu-id="b89cf-157">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="b89cf-158">Ao executar em Webassembly em um aplicativo Webassembly Blazor, use [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> para personalizar solicitações.</span><span class="sxs-lookup"><span data-stu-id="b89cf-158">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="b89cf-159">Por exemplo, você pode especificar o URI de solicitação, o método HTTP e todos os cabeçalhos de solicitação desejados.</span><span class="sxs-lookup"><span data-stu-id="b89cf-159">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

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

<span data-ttu-id="b89cf-160">Para obter mais informações sobre as opções de API de busca, consulte [MDN Web docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="b89cf-160">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="b89cf-161">Ao enviar credenciais (cookies/cabeçalhos de autorização) em solicitações de CORS, o cabeçalho de `Authorization` deve ser permitido pela política de CORS.</span><span class="sxs-lookup"><span data-stu-id="b89cf-161">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="b89cf-162">A política a seguir inclui a configuração para o:</span><span class="sxs-lookup"><span data-stu-id="b89cf-162">The following policy includes configuration for:</span></span>

* <span data-ttu-id="b89cf-163">Origens de solicitação (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="b89cf-163">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="b89cf-164">Qualquer método (verbo).</span><span class="sxs-lookup"><span data-stu-id="b89cf-164">Any method (verb).</span></span>
* <span data-ttu-id="b89cf-165">cabeçalhos de `Content-Type` e `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="b89cf-165">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="b89cf-166">Para permitir um cabeçalho personalizado (por exemplo, `x-custom-header`), liste o cabeçalho ao chamar <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="b89cf-166">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="b89cf-167">As credenciais definidas pelo código JavaScript do lado do cliente (`credentials` propriedade definida como `include`).</span><span class="sxs-lookup"><span data-stu-id="b89cf-167">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="b89cf-168">Para obter mais informações, consulte <xref:security/cors> e o componente testador de solicitação HTTP do aplicativo de exemplo (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="b89cf-168">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b89cf-169">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="b89cf-169">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="b89cf-170">Configuração de ponto de extremidade HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="b89cf-170">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="b89cf-171">CORS (compartilhamento de recursos entre origens) no W3C</span><span class="sxs-lookup"><span data-stu-id="b89cf-171">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
