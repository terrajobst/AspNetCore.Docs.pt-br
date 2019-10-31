---
title: Fazer solicitações HTTP usando IHttpClientFactory no ASP.NET Core
author: stevejgordon
description: Saiba mais sobre como usar a interface IHttpClientFactory para gerenciar instâncias de HttpClient lógicas no ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/27/2019
uid: fundamentals/http-requests
ms.openlocfilehash: a963833acfa12889c8ae3dac443962682e1cb931
ms.sourcegitcommit: 032113208bb55ecfb2faeb6d3e9ea44eea827950
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73190588"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="40b6f-103">Fazer solicitações HTTP usando IHttpClientFactory no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40b6f-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="40b6f-104">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)e [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="40b6f-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="40b6f-105">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="40b6f-106">o `IHttpClientFactory` oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="40b6f-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="40b6f-107">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="40b6f-108">Por exemplo, um cliente chamado *GitHub* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="40b6f-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="40b6f-109">Um cliente padrão pode ser registrado para acesso geral.</span><span class="sxs-lookup"><span data-stu-id="40b6f-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="40b6f-110">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="40b6f-111">Fornece extensões para o middleware baseado em Polly para tirar proveito da delegação de manipuladores no `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="40b6f-112">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes.</span><span class="sxs-lookup"><span data-stu-id="40b6f-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="40b6f-113">O gerenciamento automático evita problemas comuns de DNS (sistema de nomes de domínio) que ocorrem ao gerenciar manualmente `HttpClient` tempos de vida.</span><span class="sxs-lookup"><span data-stu-id="40b6f-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="40b6f-114">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="40b6f-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="40b6f-115">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="40b6f-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="40b6f-116">O código de exemplo nesta versão de tópico usa <xref:System.Text.Json> para desserializar o conteúdo JSON retornado em respostas HTTP.</span><span class="sxs-lookup"><span data-stu-id="40b6f-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="40b6f-117">Para obter exemplos que usam `Json.NET` e `ReadAsAsync<T>`, use o seletor de versão para selecionar uma versão 2. x deste tópico.</span><span class="sxs-lookup"><span data-stu-id="40b6f-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="40b6f-118">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="40b6f-118">Consumption patterns</span></span>

<span data-ttu-id="40b6f-119">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="40b6f-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="40b6f-120">Uso básico</span><span class="sxs-lookup"><span data-stu-id="40b6f-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="40b6f-121">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="40b6f-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="40b6f-122">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="40b6f-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="40b6f-123">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="40b6f-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="40b6f-124">A melhor abordagem depende dos requisitos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="40b6f-125">Uso básico</span><span class="sxs-lookup"><span data-stu-id="40b6f-125">Basic usage</span></span>

<span data-ttu-id="40b6f-126">`IHttpClientFactory` pode ser registrado chamando `AddHttpClient`:</span><span class="sxs-lookup"><span data-stu-id="40b6f-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="40b6f-127">Um `IHttpClientFactory` pode ser solicitado usando [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="40b6f-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="40b6f-128">O código a seguir usa `IHttpClientFactory` para criar uma instância de `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="40b6f-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="40b6f-129">Usar `IHttpClientFactory` como no exemplo anterior é uma boa maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="40b6f-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="40b6f-130">Ele não tem impacto sobre como `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="40b6f-131">Em locais onde `HttpClient` instâncias são criadas em um aplicativo existente, substitua essas ocorrências por chamadas para <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="40b6f-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="40b6f-132">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="40b6f-132">Named clients</span></span>

<span data-ttu-id="40b6f-133">Os clientes nomeados são uma boa opção quando:</span><span class="sxs-lookup"><span data-stu-id="40b6f-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="40b6f-134">O aplicativo requer muitos usos distintos de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="40b6f-135">Muitos `HttpClient`s têm configuração diferente.</span><span class="sxs-lookup"><span data-stu-id="40b6f-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="40b6f-136">A configuração para um `HttpClient` nomeado pode ser especificada durante o registro no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="40b6f-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="40b6f-137">No código anterior, o cliente está configurado com:</span><span class="sxs-lookup"><span data-stu-id="40b6f-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="40b6f-138">O endereço base `https://api.github.com/`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="40b6f-139">Dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="40b6f-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="40b6f-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="40b6f-140">CreateClient</span></span>

<span data-ttu-id="40b6f-141">Cada vez que <xref:System.Net.Http.IHttpClientFactory.CreateClient*> é chamado:</span><span class="sxs-lookup"><span data-stu-id="40b6f-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="40b6f-142">Uma nova instância do `HttpClient` é criada.</span><span class="sxs-lookup"><span data-stu-id="40b6f-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="40b6f-143">A ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="40b6f-143">The configuration action is called.</span></span>

<span data-ttu-id="40b6f-144">Para criar um cliente nomeado, passe seu nome para `CreateClient`:</span><span class="sxs-lookup"><span data-stu-id="40b6f-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="40b6f-145">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="40b6f-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="40b6f-146">O código pode passar apenas o caminho, já que o endereço base configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="40b6f-147">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="40b6f-147">Typed clients</span></span>

<span data-ttu-id="40b6f-148">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="40b6f-148">Typed clients:</span></span>

* <span data-ttu-id="40b6f-149">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="40b6f-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="40b6f-150">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="40b6f-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="40b6f-151">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="40b6f-152">Por exemplo, um único cliente tipado pode ser usado:</span><span class="sxs-lookup"><span data-stu-id="40b6f-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="40b6f-153">Para um ponto de extremidade de back-end único.</span><span class="sxs-lookup"><span data-stu-id="40b6f-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="40b6f-154">Para encapsular toda a lógica lidando com o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="40b6f-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="40b6f-155">Trabalhe com DI e pode ser injetado onde necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="40b6f-156">Um cliente com tipo aceita um parâmetro `HttpClient` em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="40b6f-156">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="40b6f-157">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="40b6f-157">In the preceding code:</span></span>

* <span data-ttu-id="40b6f-158">A configuração é movida para o cliente digitado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="40b6f-159">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="40b6f-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="40b6f-160">Métodos específicos de API podem ser criados para expor `HttpClient` funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="40b6f-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="40b6f-161">Por exemplo, o método `GetAspNetDocsIssues` encapsula o código para recuperar problemas abertos.</span><span class="sxs-lookup"><span data-stu-id="40b6f-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="40b6f-162">O código a seguir chama <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> em `Startup.ConfigureServices` para registrar uma classe de cliente tipada:</span><span class="sxs-lookup"><span data-stu-id="40b6f-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="40b6f-163">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="40b6f-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="40b6f-164">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="40b6f-164">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="40b6f-165">A configuração de um cliente tipado pode ser especificada durante o registro no `Startup.ConfigureServices`, em vez de no construtor do cliente digitado:</span><span class="sxs-lookup"><span data-stu-id="40b6f-165">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="40b6f-166">O `HttpClient` pode ser encapsulado em um cliente digitado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-166">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="40b6f-167">Em vez de expô-lo como uma propriedade, defina um método que chame a instância de `HttpClient` internamente:</span><span class="sxs-lookup"><span data-stu-id="40b6f-167">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="40b6f-168">No código anterior, a `HttpClient` é armazenada em um campo particular.</span><span class="sxs-lookup"><span data-stu-id="40b6f-168">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="40b6f-169">O acesso ao `HttpClient` é pelo método `GetRepos` público.</span><span class="sxs-lookup"><span data-stu-id="40b6f-169">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="40b6f-170">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="40b6f-170">Generated clients</span></span>

<span data-ttu-id="40b6f-171">`IHttpClientFactory` pode ser usado em combinação com bibliotecas de terceiros, como [rEFIt](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="40b6f-171">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="40b6f-172">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="40b6f-172">Refit is a REST library for .NET.</span></span> <span data-ttu-id="40b6f-173">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="40b6f-173">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="40b6f-174">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="40b6f-174">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="40b6f-175">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="40b6f-175">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="40b6f-176">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="40b6f-176">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="40b6f-177">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="40b6f-177">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="40b6f-178">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="40b6f-178">Outgoing request middleware</span></span>

<span data-ttu-id="40b6f-179">`HttpClient` tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="40b6f-179">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="40b6f-180">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="40b6f-180">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="40b6f-181">Simplifica a definição dos manipuladores a serem aplicados para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-181">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="40b6f-182">Dá suporte ao registro e encadeamento de vários manipuladores para criar um pipeline de middleware de solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="40b6f-182">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="40b6f-183">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="40b6f-183">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="40b6f-184">Esse padrão:</span><span class="sxs-lookup"><span data-stu-id="40b6f-184">This pattern:</span></span>

  * <span data-ttu-id="40b6f-185">É semelhante ao pipeline de middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="40b6f-185">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="40b6f-186">Fornece um mecanismo para gerenciar preocupações abrangentes em relação a solicitações HTTP, como:</span><span class="sxs-lookup"><span data-stu-id="40b6f-186">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="40b6f-187">cache</span><span class="sxs-lookup"><span data-stu-id="40b6f-187">caching</span></span>
    * <span data-ttu-id="40b6f-188">tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="40b6f-188">error handling</span></span>
    * <span data-ttu-id="40b6f-189">serialização</span><span class="sxs-lookup"><span data-stu-id="40b6f-189">serialization</span></span>
    * <span data-ttu-id="40b6f-190">log</span><span class="sxs-lookup"><span data-stu-id="40b6f-190">logging</span></span>

<span data-ttu-id="40b6f-191">Para criar um manipulador de delegação:</span><span class="sxs-lookup"><span data-stu-id="40b6f-191">To create a delegating handler:</span></span>

* <span data-ttu-id="40b6f-192">Derive de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="40b6f-192">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="40b6f-193">Substitua <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="40b6f-193">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="40b6f-194">Execute o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="40b6f-194">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="40b6f-195">O código anterior verifica se o cabeçalho de `X-API-KEY` está na solicitação.</span><span class="sxs-lookup"><span data-stu-id="40b6f-195">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="40b6f-196">Se `X-API-KEY` estiver ausente, <xref:System.Net.HttpStatusCode.BadRequest> será retornado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-196">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="40b6f-197">Mais de um manipulador pode ser adicionado à configuração para um `HttpClient` com <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="40b6f-197">More than one handler can be added to the configuration for a `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="40b6f-198">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="40b6f-198">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="40b6f-199">O `IHttpClientFactory` cria um escopo de injeção de dependência separado para cada manipulador.</span><span class="sxs-lookup"><span data-stu-id="40b6f-199">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="40b6f-200">Os manipuladores podem depender dos serviços de qualquer escopo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-200">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="40b6f-201">Os serviços dos quais os manipuladores dependem são descartados quando o manipulador é descartado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-201">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="40b6f-202">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando o tipo para o manipulador.</span><span class="sxs-lookup"><span data-stu-id="40b6f-202">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="40b6f-203">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="40b6f-203">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="40b6f-204">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="40b6f-204">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="40b6f-205">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="40b6f-205">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="40b6f-206">Passe dados para o manipulador usando [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="40b6f-206">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="40b6f-207">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="40b6f-207">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="40b6f-208">Crie um objeto de armazenamento <xref:System.Threading.AsyncLocal`1> personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="40b6f-208">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="40b6f-209">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="40b6f-209">Use Polly-based handlers</span></span>

<span data-ttu-id="40b6f-210">o `IHttpClientFactory` integra-se com a biblioteca de terceiros [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="40b6f-210">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="40b6f-211">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="40b6f-211">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="40b6f-212">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="40b6f-212">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="40b6f-213">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="40b6f-213">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="40b6f-214">As extensões Polly dão suporte à adição de manipuladores baseados em Polly a clientes.</span><span class="sxs-lookup"><span data-stu-id="40b6f-214">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="40b6f-215">Polly requer o pacote NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="40b6f-215">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="40b6f-216">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="40b6f-216">Handle transient faults</span></span>

<span data-ttu-id="40b6f-217">As falhas normalmente ocorrem quando chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="40b6f-217">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="40b6f-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> permite que uma política seja definida para lidar com erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="40b6f-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="40b6f-219">As políticas configuradas com `AddTransientHttpErrorPolicy` manipulam as seguintes respostas:</span><span class="sxs-lookup"><span data-stu-id="40b6f-219">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="40b6f-220">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="40b6f-220">HTTP 5xx</span></span>
* <span data-ttu-id="40b6f-221">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="40b6f-221">HTTP 408</span></span>

<span data-ttu-id="40b6f-222">`AddTransientHttpErrorPolicy` fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="40b6f-222">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="40b6f-223">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="40b6f-223">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="40b6f-224">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="40b6f-224">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="40b6f-225">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="40b6f-225">Dynamically select policies</span></span>

<span data-ttu-id="40b6f-226">Os métodos de extensão são fornecidos para adicionar manipuladores baseados em Polly, por exemplo, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span><span class="sxs-lookup"><span data-stu-id="40b6f-226">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="40b6f-227">A sobrecarga de `AddPolicyHandler` a seguir inspeciona a solicitação para decidir qual política aplicar:</span><span class="sxs-lookup"><span data-stu-id="40b6f-227">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="40b6f-228">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-228">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="40b6f-229">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="40b6f-229">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="40b6f-230">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="40b6f-230">Add multiple Polly handlers</span></span>

<span data-ttu-id="40b6f-231">É comum aninhar políticas Polly:</span><span class="sxs-lookup"><span data-stu-id="40b6f-231">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="40b6f-232">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="40b6f-232">In the preceding example:</span></span>

* <span data-ttu-id="40b6f-233">Dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="40b6f-233">Two handlers are added.</span></span>
* <span data-ttu-id="40b6f-234">O primeiro manipulador usa <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="40b6f-234">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="40b6f-235">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="40b6f-235">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="40b6f-236">A segunda chamada de `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="40b6f-236">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="40b6f-237">Outras solicitações externas serão bloqueadas por 30 segundos se 5 tentativas com falha ocorrerem em sequência.</span><span class="sxs-lookup"><span data-stu-id="40b6f-237">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="40b6f-238">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-238">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="40b6f-239">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="40b6f-239">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="40b6f-240">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="40b6f-240">Add policies from the Polly registry</span></span>

<span data-ttu-id="40b6f-241">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-241">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="40b6f-242">No seguinte código:</span><span class="sxs-lookup"><span data-stu-id="40b6f-242">In the following code:</span></span>

* <span data-ttu-id="40b6f-243">As políticas "regular" e "longa" são adicionadas.</span><span class="sxs-lookup"><span data-stu-id="40b6f-243">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="40b6f-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adiciona as políticas "regular" e "longa" do registro.</span><span class="sxs-lookup"><span data-stu-id="40b6f-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="40b6f-245">Para obter mais informações sobre as integrações `IHttpClientFactory` e Polly, consulte o [wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="40b6f-245">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="40b6f-246">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="40b6f-246">HttpClient and lifetime management</span></span>

<span data-ttu-id="40b6f-247">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-247">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="40b6f-248">Um <xref:System.Net.Http.HttpMessageHandler> é criado por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-248">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="40b6f-249">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-249">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="40b6f-250">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="40b6f-250">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="40b6f-251">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-251">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="40b6f-252">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="40b6f-252">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="40b6f-253">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="40b6f-253">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="40b6f-254">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reajam às alterações de DNS (sistema de nomes de domínio).</span><span class="sxs-lookup"><span data-stu-id="40b6f-254">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="40b6f-255">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="40b6f-255">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="40b6f-256">O valor padrão pode ser substituído em uma base de cliente por nome:</span><span class="sxs-lookup"><span data-stu-id="40b6f-256">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="40b6f-257">instâncias de `HttpClient` geralmente podem ser tratadas como objetos .NET que **não** exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="40b6f-257">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="40b6f-258">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="40b6f-258">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="40b6f-259">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-259">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="40b6f-260">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-260">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="40b6f-261">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-261">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="40b6f-262">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="40b6f-262">Logging</span></span>

<span data-ttu-id="40b6f-263">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="40b6f-263">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="40b6f-264">Habilite o nível de informações apropriado na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="40b6f-264">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="40b6f-265">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="40b6f-265">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="40b6f-266">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="40b6f-266">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="40b6f-267">Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de "System .net. http. HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="40b6f-267">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="40b6f-268">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="40b6f-268">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="40b6f-269">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-269">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="40b6f-270">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="40b6f-270">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="40b6f-271">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="40b6f-271">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="40b6f-272">No exemplo de *MyNamedClient* , essas mensagens são registradas com a categoria de log "System .net. http. HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="40b6f-272">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="40b6f-273">Para a solicitação, isso ocorre depois que todos os outros manipuladores forem executados e imediatamente antes de a solicitação ser enviada.</span><span class="sxs-lookup"><span data-stu-id="40b6f-273">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="40b6f-274">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="40b6f-274">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="40b6f-275">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="40b6f-275">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="40b6f-276">Isso pode incluir alterações nos cabeçalhos de solicitação ou no código de status de resposta.</span><span class="sxs-lookup"><span data-stu-id="40b6f-276">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="40b6f-277">A inclusão do nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos.</span><span class="sxs-lookup"><span data-stu-id="40b6f-277">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="40b6f-278">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="40b6f-278">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="40b6f-279">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="40b6f-279">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="40b6f-280">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-280">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="40b6f-281">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="40b6f-281">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="40b6f-282">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="40b6f-282">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="40b6f-283">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="40b6f-283">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="40b6f-284">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="40b6f-284">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="40b6f-285">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="40b6f-285">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="40b6f-286">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="40b6f-286">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="40b6f-287">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="40b6f-287">In the following example:</span></span>

* <span data-ttu-id="40b6f-288"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="40b6f-288"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="40b6f-289">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-289">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="40b6f-290">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="40b6f-290">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="40b6f-291">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="40b6f-291">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="40b6f-292">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="40b6f-292">Additional resources</span></span>

* <span data-ttu-id="40b6f-293">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) (Usar o HttpClientFactory para implementar solicitações HTTP resilientes)</span><span class="sxs-lookup"><span data-stu-id="40b6f-293">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)</span></span>
* [<span data-ttu-id="40b6f-294">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="40b6f-294">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="40b6f-295">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="40b6f-295">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="40b6f-296">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="40b6f-296">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="40b6f-297">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-297">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="40b6f-298">Ele oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="40b6f-298">It offers the following benefits:</span></span>

* <span data-ttu-id="40b6f-299">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-299">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="40b6f-300">Por exemplo, um cliente do *github* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="40b6f-300">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="40b6f-301">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="40b6f-301">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="40b6f-302">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` e fornece extensões para que o middleware baseado no Polly possa aproveitar esse recurso.</span><span class="sxs-lookup"><span data-stu-id="40b6f-302">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="40b6f-303">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-303">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="40b6f-304">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="40b6f-304">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="40b6f-305">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="40b6f-305">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="40b6f-306">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="40b6f-306">Consumption patterns</span></span>

<span data-ttu-id="40b6f-307">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="40b6f-307">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="40b6f-308">Uso básico</span><span class="sxs-lookup"><span data-stu-id="40b6f-308">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="40b6f-309">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="40b6f-309">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="40b6f-310">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="40b6f-310">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="40b6f-311">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="40b6f-311">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="40b6f-312">Nenhum deles é estritamente superiores ao outro.</span><span class="sxs-lookup"><span data-stu-id="40b6f-312">None of them are strictly superior to another.</span></span> <span data-ttu-id="40b6f-313">A melhor abordagem depende das restrições do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-313">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="40b6f-314">Uso básico</span><span class="sxs-lookup"><span data-stu-id="40b6f-314">Basic usage</span></span>

<span data-ttu-id="40b6f-315">O `IHttpClientFactory` pode ser registrado chamando o método de extensão `AddHttpClient` em `IServiceCollection`, dentro do método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-315">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="40b6f-316">Depois de registrado, o código pode aceitar um `IHttpClientFactory` em qualquer lugar em que os serviços possam ser injetados com [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="40b6f-316">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="40b6f-317">O `IHttpClientFactory` pode ser usado para criar uma instância de `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="40b6f-317">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="40b6f-318">Usar o `IHttpClientFactory` dessa forma é uma ótima maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="40b6f-318">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="40b6f-319">Não há nenhum impacto na maneira em que o `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-319">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="40b6f-320">Nos locais em que as instâncias de `HttpClient` são criadas no momento, substitua essas ocorrências por uma chamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="40b6f-320">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="40b6f-321">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="40b6f-321">Named clients</span></span>

<span data-ttu-id="40b6f-322">Quando um aplicativo exige vários usos distintos do `HttpClient`, cada um com uma configuração diferente, uma opção é usar **clientes nomeados**.</span><span class="sxs-lookup"><span data-stu-id="40b6f-322">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="40b6f-323">A configuração de um `HttpClient` nomeado pode ser especificada durante o registro em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-323">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="40b6f-324">No código anterior, `AddHttpClient` é chamado, fornecendo o nome *github*.</span><span class="sxs-lookup"><span data-stu-id="40b6f-324">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="40b6f-325">Esse cliente tem algumas configurações padrão aplicadas, ou seja, o endereço básico e dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="40b6f-325">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="40b6f-326">Toda vez que o `CreateClient` é chamado, uma nova instância de `HttpClient` é criada e a ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="40b6f-326">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="40b6f-327">Para consumir um cliente nomeado, um parâmetro de cadeia de caracteres pode ser passado para `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-327">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="40b6f-328">Especifique o nome do cliente a ser criado:</span><span class="sxs-lookup"><span data-stu-id="40b6f-328">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="40b6f-329">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="40b6f-329">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="40b6f-330">Ela pode passar apenas o caminho, pois o endereço básico configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-330">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="40b6f-331">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="40b6f-331">Typed clients</span></span>

<span data-ttu-id="40b6f-332">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="40b6f-332">Typed clients:</span></span>

* <span data-ttu-id="40b6f-333">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="40b6f-333">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="40b6f-334">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="40b6f-334">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="40b6f-335">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-335">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="40b6f-336">Por exemplo, um único cliente com tipo pode ser usado para um único ponto de extremidade de back-end e encapsular toda a lógica que lida com esse ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="40b6f-336">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="40b6f-337">Funcionam com a DI e podem ser injetados no local necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-337">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="40b6f-338">Um cliente com tipo aceita um parâmetro `HttpClient` em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="40b6f-338">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="40b6f-339">No código anterior, a configuração é movida para o cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-339">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="40b6f-340">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="40b6f-340">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="40b6f-341">É possível definir métodos específicos da API que expõem a funcionalidade `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-341">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="40b6f-342">O método `GetAspNetDocsIssues` encapsula o código necessário para consultar e analisar os últimos problemas em aberto de um repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="40b6f-342">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="40b6f-343">Para registrar um cliente com tipo, o método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genérico pode ser usado em `Startup.ConfigureServices`, especificando a classe do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="40b6f-343">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="40b6f-344">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="40b6f-344">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="40b6f-345">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="40b6f-345">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="40b6f-346">Se preferir, a configuração de um cliente com tipo poderá ser especificada durante o registro em `Startup.ConfigureServices` e não no construtor do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="40b6f-346">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="40b6f-347">É possível encapsular totalmente o `HttpClient` dentro de um cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-347">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="40b6f-348">Em vez de o expor como uma propriedade, é possível fornecer métodos públicos que chamam a instância de `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="40b6f-348">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="40b6f-349">No código anterior, o `HttpClient` é armazenado como um campo privado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-349">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="40b6f-350">Todo o acesso para fazer chamadas externas passa pelo método `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-350">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="40b6f-351">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="40b6f-351">Generated clients</span></span>

<span data-ttu-id="40b6f-352">O `IHttpClientFactory` pode ser usado em combinação com outras bibliotecas de terceiros, como a [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="40b6f-352">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="40b6f-353">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="40b6f-353">Refit is a REST library for .NET.</span></span> <span data-ttu-id="40b6f-354">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="40b6f-354">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="40b6f-355">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="40b6f-355">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="40b6f-356">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="40b6f-356">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="40b6f-357">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="40b6f-357">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="40b6f-358">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="40b6f-358">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="40b6f-359">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="40b6f-359">Outgoing request middleware</span></span>

<span data-ttu-id="40b6f-360">O `HttpClient` já tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="40b6f-360">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="40b6f-361">O `IHttpClientFactory` facilita a definição dos manipuladores a serem aplicados a cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-361">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="40b6f-362">Ele permite o registro e o encadeamento de vários manipuladores para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="40b6f-362">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="40b6f-363">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="40b6f-363">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="40b6f-364">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="40b6f-364">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="40b6f-365">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="40b6f-365">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="40b6f-366">Para criar um manipulador, defina uma classe derivando-a de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="40b6f-366">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="40b6f-367">Substitua o método `SendAsync` para executar o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="40b6f-367">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="40b6f-368">O código anterior define um manipulador básico.</span><span class="sxs-lookup"><span data-stu-id="40b6f-368">The preceding code defines a basic handler.</span></span> <span data-ttu-id="40b6f-369">Ele verifica se um cabeçalho `X-API-KEY` foi incluído na solicitação.</span><span class="sxs-lookup"><span data-stu-id="40b6f-369">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="40b6f-370">Se o cabeçalho estiver ausente, isso poderá evitar a chamada de HTTP e retornar uma resposta adequada.</span><span class="sxs-lookup"><span data-stu-id="40b6f-370">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="40b6f-371">Durante o registro, um ou mais manipuladores podem ser adicionados à configuração de um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-371">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="40b6f-372">Essa tarefa é realizada por meio de métodos de extensão no <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="40b6f-372">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="40b6f-373">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="40b6f-373">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="40b6f-374">O `IHttpClientFactory` cria um escopo de injeção de dependência separado para cada manipulador.</span><span class="sxs-lookup"><span data-stu-id="40b6f-374">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="40b6f-375">Os manipuladores podem depender de serviços de qualquer escopo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-375">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="40b6f-376">Os serviços dos quais os manipuladores dependem são descartados quando o manipulador é descartado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-376">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="40b6f-377">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando o tipo para o manipulador.</span><span class="sxs-lookup"><span data-stu-id="40b6f-377">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="40b6f-378">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="40b6f-378">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="40b6f-379">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="40b6f-379">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="40b6f-380">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="40b6f-380">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="40b6f-381">Passe os dados pelo manipulador usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-381">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="40b6f-382">Use `IHttpContextAccessor` para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="40b6f-382">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="40b6f-383">Crie um objeto de armazenamento `AsyncLocal` personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="40b6f-383">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="40b6f-384">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="40b6f-384">Use Polly-based handlers</span></span>

<span data-ttu-id="40b6f-385">O `IHttpClientFactory` integra-se a uma biblioteca de terceiros popular chamada [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="40b6f-385">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="40b6f-386">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="40b6f-386">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="40b6f-387">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="40b6f-387">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="40b6f-388">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="40b6f-388">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="40b6f-389">As extensões do Polly:</span><span class="sxs-lookup"><span data-stu-id="40b6f-389">The Polly extensions:</span></span>

* <span data-ttu-id="40b6f-390">Dão suporte à adição de manipuladores baseados no Polly aos clientes.</span><span class="sxs-lookup"><span data-stu-id="40b6f-390">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="40b6f-391">Podem ser usadas depois de instalar o pacote do NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="40b6f-391">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="40b6f-392">O pacote não está incluído na estrutura ASP.NET Core compartilhada.</span><span class="sxs-lookup"><span data-stu-id="40b6f-392">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="40b6f-393">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="40b6f-393">Handle transient faults</span></span>

<span data-ttu-id="40b6f-394">As falhas mais comuns ocorrem quando as chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="40b6f-394">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="40b6f-395">Um método de extensão conveniente chamado `AddTransientHttpErrorPolicy` é incluído para permitir que uma política seja definido para tratar erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="40b6f-395">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="40b6f-396">As políticas configuradas com esse método de extensão tratam `HttpRequestException`, respostas HTTP 5xx e respostas HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="40b6f-396">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="40b6f-397">A extensão `AddTransientHttpErrorPolicy` pode ser usada em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-397">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="40b6f-398">A extensão fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="40b6f-398">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="40b6f-399">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="40b6f-399">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="40b6f-400">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="40b6f-400">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="40b6f-401">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="40b6f-401">Dynamically select policies</span></span>

<span data-ttu-id="40b6f-402">Existem métodos de extensão adicionais que podem ser usados para adicionar manipuladores baseados no Polly.</span><span class="sxs-lookup"><span data-stu-id="40b6f-402">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="40b6f-403">Uma dessas extensões é a `AddPolicyHandler`, que tem várias sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="40b6f-403">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="40b6f-404">Uma sobrecarga permite que a solicitação seja inspecionada durante a definição da política a ser aplicada:</span><span class="sxs-lookup"><span data-stu-id="40b6f-404">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="40b6f-405">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-405">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="40b6f-406">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="40b6f-406">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="40b6f-407">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="40b6f-407">Add multiple Polly handlers</span></span>

<span data-ttu-id="40b6f-408">É comum aninhar políticas do Polly para fornecer funcionalidade avançada:</span><span class="sxs-lookup"><span data-stu-id="40b6f-408">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="40b6f-409">No exemplo anterior, dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="40b6f-409">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="40b6f-410">O primeiro usa a extensão `AddTransientHttpErrorPolicy` para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="40b6f-410">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="40b6f-411">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="40b6f-411">Failed requests are retried up to three times.</span></span> <span data-ttu-id="40b6f-412">A segunda chamada a `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="40b6f-412">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="40b6f-413">As solicitações externas adicionais são bloqueadas por 30 segundos quando ocorrem cinco tentativas com falha consecutivamente.</span><span class="sxs-lookup"><span data-stu-id="40b6f-413">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="40b6f-414">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-414">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="40b6f-415">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="40b6f-415">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="40b6f-416">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="40b6f-416">Add policies from the Polly registry</span></span>

<span data-ttu-id="40b6f-417">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-417">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="40b6f-418">É fornecido um método de extensão que permite que um manipulador seja adicionado usando uma política do registro:</span><span class="sxs-lookup"><span data-stu-id="40b6f-418">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="40b6f-419">No código anterior, duas políticas são registradas quando `PolicyRegistry` é adicionado ao `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-419">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="40b6f-420">Para usar uma política do registro, o método `AddPolicyHandlerFromRegistry` é usado, passando o nome da política a ser aplicada.</span><span class="sxs-lookup"><span data-stu-id="40b6f-420">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="40b6f-421">Mais informações sobre o `IHttpClientFactory` e as integrações do Polly podem ser encontradas no [Wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="40b6f-421">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="40b6f-422">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="40b6f-422">HttpClient and lifetime management</span></span>

<span data-ttu-id="40b6f-423">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-423">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="40b6f-424">Há um <xref:System.Net.Http.HttpMessageHandler> por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-424">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="40b6f-425">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-425">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="40b6f-426">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="40b6f-426">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="40b6f-427">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-427">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="40b6f-428">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="40b6f-428">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="40b6f-429">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="40b6f-429">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="40b6f-430">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reaja a alterações de DNS.</span><span class="sxs-lookup"><span data-stu-id="40b6f-430">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="40b6f-431">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="40b6f-431">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="40b6f-432">O valor padrão pode ser substituído para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-432">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="40b6f-433">Para substituí-lo, chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> no `IHttpClientBuilder` que é retornado ao criar o cliente:</span><span class="sxs-lookup"><span data-stu-id="40b6f-433">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="40b6f-434">Não é necessário descartar o cliente.</span><span class="sxs-lookup"><span data-stu-id="40b6f-434">Disposal of the client isn't required.</span></span> <span data-ttu-id="40b6f-435">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="40b6f-435">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="40b6f-436">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-436">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="40b6f-437">Geralmente, as instâncias de `HttpClient` podem ser tratadas como objetos do .NET e não exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="40b6f-437">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="40b6f-438">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-438">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="40b6f-439">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-439">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="40b6f-440">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="40b6f-440">Logging</span></span>

<span data-ttu-id="40b6f-441">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="40b6f-441">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="40b6f-442">Habilite o nível apropriado de informações na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="40b6f-442">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="40b6f-443">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="40b6f-443">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="40b6f-444">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="40b6f-444">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="40b6f-445">Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-445">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="40b6f-446">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="40b6f-446">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="40b6f-447">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-447">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="40b6f-448">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="40b6f-448">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="40b6f-449">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="40b6f-449">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="40b6f-450">No caso do exemplo de *MyNamedClient*, essas mensagens são registradas em log na categoria de log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-450">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="40b6f-451">Para a solicitação, isso ocorre depois que todos os outros manipuladores são executados e imediatamente antes que a solicitação seja enviada pela rede.</span><span class="sxs-lookup"><span data-stu-id="40b6f-451">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="40b6f-452">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="40b6f-452">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="40b6f-453">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="40b6f-453">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="40b6f-454">Isso pode incluir, por exemplo, alterações nos cabeçalhos de solicitação ou no código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="40b6f-454">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="40b6f-455">Incluir o nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos, quando necessário.</span><span class="sxs-lookup"><span data-stu-id="40b6f-455">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="40b6f-456">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="40b6f-456">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="40b6f-457">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="40b6f-457">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="40b6f-458">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-458">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="40b6f-459">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="40b6f-459">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="40b6f-460">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="40b6f-460">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="40b6f-461">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="40b6f-461">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="40b6f-462">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="40b6f-462">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="40b6f-463">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="40b6f-463">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="40b6f-464">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="40b6f-464">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="40b6f-465">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="40b6f-465">In the following example:</span></span>

* <span data-ttu-id="40b6f-466"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="40b6f-466"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="40b6f-467">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-467">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="40b6f-468">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="40b6f-468">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="40b6f-469">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="40b6f-469">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="40b6f-470">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="40b6f-470">Additional resources</span></span>

* <span data-ttu-id="40b6f-471">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) (Usar o HttpClientFactory para implementar solicitações HTTP resilientes)</span><span class="sxs-lookup"><span data-stu-id="40b6f-471">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)</span></span>
* [<span data-ttu-id="40b6f-472">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="40b6f-472">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="40b6f-473">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="40b6f-473">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="40b6f-474">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="40b6f-474">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="40b6f-475">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-475">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="40b6f-476">Ele oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="40b6f-476">It offers the following benefits:</span></span>

* <span data-ttu-id="40b6f-477">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-477">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="40b6f-478">Por exemplo, um cliente do *github* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="40b6f-478">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="40b6f-479">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="40b6f-479">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="40b6f-480">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` e fornece extensões para que o middleware baseado no Polly possa aproveitar esse recurso.</span><span class="sxs-lookup"><span data-stu-id="40b6f-480">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="40b6f-481">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-481">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="40b6f-482">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="40b6f-482">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="40b6f-483">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="40b6f-483">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="40b6f-484">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="40b6f-484">Prerequisites</span></span>

<span data-ttu-id="40b6f-485">Os projetos direcionados ao .NET Framework exigem a instalação do pacote do NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="40b6f-485">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="40b6f-486">Os projetos destinados ao .Net Core e a referência ao [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) já incluem o pacote `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-486">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="40b6f-487">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="40b6f-487">Consumption patterns</span></span>

<span data-ttu-id="40b6f-488">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="40b6f-488">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="40b6f-489">Uso básico</span><span class="sxs-lookup"><span data-stu-id="40b6f-489">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="40b6f-490">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="40b6f-490">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="40b6f-491">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="40b6f-491">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="40b6f-492">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="40b6f-492">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="40b6f-493">Nenhum deles é estritamente superiores ao outro.</span><span class="sxs-lookup"><span data-stu-id="40b6f-493">None of them are strictly superior to another.</span></span> <span data-ttu-id="40b6f-494">A melhor abordagem depende das restrições do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-494">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="40b6f-495">Uso básico</span><span class="sxs-lookup"><span data-stu-id="40b6f-495">Basic usage</span></span>

<span data-ttu-id="40b6f-496">O `IHttpClientFactory` pode ser registrado chamando o método de extensão `AddHttpClient` em `IServiceCollection`, dentro do método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-496">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="40b6f-497">Depois de registrado, o código pode aceitar um `IHttpClientFactory` em qualquer lugar em que os serviços possam ser injetados com [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="40b6f-497">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="40b6f-498">O `IHttpClientFactory` pode ser usado para criar uma instância de `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="40b6f-498">The `IHttpClientFactory` can be used to create a `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="40b6f-499">Usar o `IHttpClientFactory` dessa forma é uma ótima maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="40b6f-499">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="40b6f-500">Não há nenhum impacto na maneira em que o `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-500">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="40b6f-501">Nos locais em que as instâncias de `HttpClient` são criadas no momento, substitua essas ocorrências por uma chamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="40b6f-501">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="40b6f-502">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="40b6f-502">Named clients</span></span>

<span data-ttu-id="40b6f-503">Quando um aplicativo exige vários usos distintos do `HttpClient`, cada um com uma configuração diferente, uma opção é usar **clientes nomeados**.</span><span class="sxs-lookup"><span data-stu-id="40b6f-503">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="40b6f-504">A configuração de um `HttpClient` nomeado pode ser especificada durante o registro em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-504">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="40b6f-505">No código anterior, `AddHttpClient` é chamado, fornecendo o nome *github*.</span><span class="sxs-lookup"><span data-stu-id="40b6f-505">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="40b6f-506">Esse cliente tem algumas configurações padrão aplicadas, ou seja, o endereço básico e dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="40b6f-506">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="40b6f-507">Toda vez que o `CreateClient` é chamado, uma nova instância de `HttpClient` é criada e a ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="40b6f-507">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="40b6f-508">Para consumir um cliente nomeado, um parâmetro de cadeia de caracteres pode ser passado para `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-508">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="40b6f-509">Especifique o nome do cliente a ser criado:</span><span class="sxs-lookup"><span data-stu-id="40b6f-509">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="40b6f-510">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="40b6f-510">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="40b6f-511">Ela pode passar apenas o caminho, pois o endereço básico configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-511">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="40b6f-512">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="40b6f-512">Typed clients</span></span>

<span data-ttu-id="40b6f-513">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="40b6f-513">Typed clients:</span></span>

* <span data-ttu-id="40b6f-514">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="40b6f-514">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="40b6f-515">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="40b6f-515">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="40b6f-516">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-516">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="40b6f-517">Por exemplo, um único cliente com tipo pode ser usado para um único ponto de extremidade de back-end e encapsular toda a lógica que lida com esse ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="40b6f-517">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="40b6f-518">Funcionam com a DI e podem ser injetados no local necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-518">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="40b6f-519">Um cliente com tipo aceita um parâmetro `HttpClient` em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="40b6f-519">A typed client accepts a `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="40b6f-520">No código anterior, a configuração é movida para o cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-520">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="40b6f-521">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="40b6f-521">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="40b6f-522">É possível definir métodos específicos da API que expõem a funcionalidade `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-522">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="40b6f-523">O método `GetAspNetDocsIssues` encapsula o código necessário para consultar e analisar os últimos problemas em aberto de um repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="40b6f-523">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="40b6f-524">Para registrar um cliente com tipo, o método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genérico pode ser usado em `Startup.ConfigureServices`, especificando a classe do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="40b6f-524">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="40b6f-525">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="40b6f-525">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="40b6f-526">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="40b6f-526">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="40b6f-527">Se preferir, a configuração de um cliente com tipo poderá ser especificada durante o registro em `Startup.ConfigureServices` e não no construtor do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="40b6f-527">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="40b6f-528">É possível encapsular totalmente o `HttpClient` dentro de um cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-528">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="40b6f-529">Em vez de o expor como uma propriedade, é possível fornecer métodos públicos que chamam a instância de `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="40b6f-529">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="40b6f-530">No código anterior, o `HttpClient` é armazenado como um campo privado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-530">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="40b6f-531">Todo o acesso para fazer chamadas externas passa pelo método `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-531">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="40b6f-532">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="40b6f-532">Generated clients</span></span>

<span data-ttu-id="40b6f-533">O `IHttpClientFactory` pode ser usado em combinação com outras bibliotecas de terceiros, como a [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="40b6f-533">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="40b6f-534">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="40b6f-534">Refit is a REST library for .NET.</span></span> <span data-ttu-id="40b6f-535">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="40b6f-535">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="40b6f-536">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="40b6f-536">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="40b6f-537">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="40b6f-537">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="40b6f-538">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="40b6f-538">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="40b6f-539">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="40b6f-539">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="40b6f-540">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="40b6f-540">Outgoing request middleware</span></span>

<span data-ttu-id="40b6f-541">O `HttpClient` já tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="40b6f-541">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="40b6f-542">O `IHttpClientFactory` facilita a definição dos manipuladores a serem aplicados a cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-542">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="40b6f-543">Ele permite o registro e o encadeamento de vários manipuladores para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="40b6f-543">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="40b6f-544">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="40b6f-544">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="40b6f-545">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="40b6f-545">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="40b6f-546">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="40b6f-546">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="40b6f-547">Para criar um manipulador, defina uma classe derivando-a de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="40b6f-547">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="40b6f-548">Substitua o método `SendAsync` para executar o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="40b6f-548">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="40b6f-549">O código anterior define um manipulador básico.</span><span class="sxs-lookup"><span data-stu-id="40b6f-549">The preceding code defines a basic handler.</span></span> <span data-ttu-id="40b6f-550">Ele verifica se um cabeçalho `X-API-KEY` foi incluído na solicitação.</span><span class="sxs-lookup"><span data-stu-id="40b6f-550">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="40b6f-551">Se o cabeçalho estiver ausente, isso poderá evitar a chamada de HTTP e retornar uma resposta adequada.</span><span class="sxs-lookup"><span data-stu-id="40b6f-551">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="40b6f-552">Durante o registro, um ou mais manipuladores podem ser adicionados à configuração de um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-552">During registration, one or more handlers can be added to the configuration for a `HttpClient`.</span></span> <span data-ttu-id="40b6f-553">Essa tarefa é realizada por meio de métodos de extensão no <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="40b6f-553">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="40b6f-554">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="40b6f-554">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="40b6f-555">O manipulador **deve** ser registrado na injeção de dependência como serviço temporário, mas nunca com escopo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-555">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="40b6f-556">Se o manipulador estiver registrado como um serviço com escopo e algum serviço do qual ele depende for descartável:</span><span class="sxs-lookup"><span data-stu-id="40b6f-556">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="40b6f-557">Os serviços do manipulador poderão ser descartados antes que ele saia do escopo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-557">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="40b6f-558">Os serviços de manipulador descartados farão com que o manipulador falhe.</span><span class="sxs-lookup"><span data-stu-id="40b6f-558">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="40b6f-559">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando no tipo do manipulador.</span><span class="sxs-lookup"><span data-stu-id="40b6f-559">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="40b6f-560">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="40b6f-560">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="40b6f-561">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="40b6f-561">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="40b6f-562">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="40b6f-562">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="40b6f-563">Passe os dados pelo manipulador usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-563">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="40b6f-564">Use `IHttpContextAccessor` para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="40b6f-564">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="40b6f-565">Crie um objeto de armazenamento `AsyncLocal` personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="40b6f-565">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="40b6f-566">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="40b6f-566">Use Polly-based handlers</span></span>

<span data-ttu-id="40b6f-567">O `IHttpClientFactory` integra-se a uma biblioteca de terceiros popular chamada [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="40b6f-567">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="40b6f-568">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="40b6f-568">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="40b6f-569">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="40b6f-569">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="40b6f-570">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="40b6f-570">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="40b6f-571">As extensões do Polly:</span><span class="sxs-lookup"><span data-stu-id="40b6f-571">The Polly extensions:</span></span>

* <span data-ttu-id="40b6f-572">Dão suporte à adição de manipuladores baseados no Polly aos clientes.</span><span class="sxs-lookup"><span data-stu-id="40b6f-572">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="40b6f-573">Podem ser usadas depois de instalar o pacote do NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="40b6f-573">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="40b6f-574">O pacote não está incluído na estrutura ASP.NET Core compartilhada.</span><span class="sxs-lookup"><span data-stu-id="40b6f-574">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="40b6f-575">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="40b6f-575">Handle transient faults</span></span>

<span data-ttu-id="40b6f-576">As falhas mais comuns ocorrem quando as chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="40b6f-576">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="40b6f-577">Um método de extensão conveniente chamado `AddTransientHttpErrorPolicy` é incluído para permitir que uma política seja definido para tratar erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="40b6f-577">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="40b6f-578">As políticas configuradas com esse método de extensão tratam `HttpRequestException`, respostas HTTP 5xx e respostas HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="40b6f-578">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="40b6f-579">A extensão `AddTransientHttpErrorPolicy` pode ser usada em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-579">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="40b6f-580">A extensão fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="40b6f-580">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="40b6f-581">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="40b6f-581">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="40b6f-582">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="40b6f-582">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="40b6f-583">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="40b6f-583">Dynamically select policies</span></span>

<span data-ttu-id="40b6f-584">Existem métodos de extensão adicionais que podem ser usados para adicionar manipuladores baseados no Polly.</span><span class="sxs-lookup"><span data-stu-id="40b6f-584">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="40b6f-585">Uma dessas extensões é a `AddPolicyHandler`, que tem várias sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="40b6f-585">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="40b6f-586">Uma sobrecarga permite que a solicitação seja inspecionada durante a definição da política a ser aplicada:</span><span class="sxs-lookup"><span data-stu-id="40b6f-586">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="40b6f-587">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-587">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="40b6f-588">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="40b6f-588">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="40b6f-589">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="40b6f-589">Add multiple Polly handlers</span></span>

<span data-ttu-id="40b6f-590">É comum aninhar políticas do Polly para fornecer funcionalidade avançada:</span><span class="sxs-lookup"><span data-stu-id="40b6f-590">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="40b6f-591">No exemplo anterior, dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="40b6f-591">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="40b6f-592">O primeiro usa a extensão `AddTransientHttpErrorPolicy` para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="40b6f-592">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="40b6f-593">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="40b6f-593">Failed requests are retried up to three times.</span></span> <span data-ttu-id="40b6f-594">A segunda chamada a `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="40b6f-594">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="40b6f-595">As solicitações externas adicionais são bloqueadas por 30 segundos quando ocorrem cinco tentativas com falha consecutivamente.</span><span class="sxs-lookup"><span data-stu-id="40b6f-595">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="40b6f-596">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-596">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="40b6f-597">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="40b6f-597">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="40b6f-598">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="40b6f-598">Add policies from the Polly registry</span></span>

<span data-ttu-id="40b6f-599">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-599">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="40b6f-600">É fornecido um método de extensão que permite que um manipulador seja adicionado usando uma política do registro:</span><span class="sxs-lookup"><span data-stu-id="40b6f-600">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="40b6f-601">No código anterior, duas políticas são registradas quando `PolicyRegistry` é adicionado ao `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-601">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="40b6f-602">Para usar uma política do registro, o método `AddPolicyHandlerFromRegistry` é usado, passando o nome da política a ser aplicada.</span><span class="sxs-lookup"><span data-stu-id="40b6f-602">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="40b6f-603">Mais informações sobre o `IHttpClientFactory` e as integrações do Polly podem ser encontradas no [Wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="40b6f-603">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="40b6f-604">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="40b6f-604">HttpClient and lifetime management</span></span>

<span data-ttu-id="40b6f-605">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-605">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="40b6f-606">Há um <xref:System.Net.Http.HttpMessageHandler> por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-606">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="40b6f-607">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-607">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="40b6f-608">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="40b6f-608">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="40b6f-609">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-609">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="40b6f-610">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="40b6f-610">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="40b6f-611">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="40b6f-611">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="40b6f-612">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reaja a alterações de DNS.</span><span class="sxs-lookup"><span data-stu-id="40b6f-612">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="40b6f-613">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="40b6f-613">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="40b6f-614">O valor padrão pode ser substituído para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-614">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="40b6f-615">Para substituí-lo, chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> no `IHttpClientBuilder` que é retornado ao criar o cliente:</span><span class="sxs-lookup"><span data-stu-id="40b6f-615">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="40b6f-616">Não é necessário descartar o cliente.</span><span class="sxs-lookup"><span data-stu-id="40b6f-616">Disposal of the client isn't required.</span></span> <span data-ttu-id="40b6f-617">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="40b6f-617">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="40b6f-618">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-618">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="40b6f-619">Geralmente, as instâncias de `HttpClient` podem ser tratadas como objetos do .NET e não exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="40b6f-619">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="40b6f-620">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-620">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="40b6f-621">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-621">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

## <a name="logging"></a><span data-ttu-id="40b6f-622">Registrando em log</span><span class="sxs-lookup"><span data-stu-id="40b6f-622">Logging</span></span>

<span data-ttu-id="40b6f-623">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="40b6f-623">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="40b6f-624">Habilite o nível apropriado de informações na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="40b6f-624">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="40b6f-625">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="40b6f-625">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="40b6f-626">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="40b6f-626">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="40b6f-627">Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-627">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="40b6f-628">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="40b6f-628">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="40b6f-629">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="40b6f-629">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="40b6f-630">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="40b6f-630">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="40b6f-631">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="40b6f-631">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="40b6f-632">No caso do exemplo de *MyNamedClient*, essas mensagens são registradas em log na categoria de log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-632">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="40b6f-633">Para a solicitação, isso ocorre depois que todos os outros manipuladores são executados e imediatamente antes que a solicitação seja enviada pela rede.</span><span class="sxs-lookup"><span data-stu-id="40b6f-633">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="40b6f-634">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="40b6f-634">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="40b6f-635">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="40b6f-635">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="40b6f-636">Isso pode incluir, por exemplo, alterações nos cabeçalhos de solicitação ou no código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="40b6f-636">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="40b6f-637">Incluir o nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos, quando necessário.</span><span class="sxs-lookup"><span data-stu-id="40b6f-637">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="40b6f-638">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="40b6f-638">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="40b6f-639">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="40b6f-639">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="40b6f-640">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="40b6f-640">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="40b6f-641">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="40b6f-641">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="40b6f-642">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="40b6f-642">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="40b6f-643">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="40b6f-643">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="40b6f-644">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="40b6f-644">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="40b6f-645">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="40b6f-645">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="40b6f-646">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="40b6f-646">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="40b6f-647">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="40b6f-647">In the following example:</span></span>

* <span data-ttu-id="40b6f-648"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="40b6f-648"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="40b6f-649">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="40b6f-649">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="40b6f-650">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="40b6f-650">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="40b6f-651">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="40b6f-651">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="40b6f-652">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="40b6f-652">Additional resources</span></span>

* <span data-ttu-id="40b6f-653">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) (Usar o HttpClientFactory para implementar solicitações HTTP resilientes)</span><span class="sxs-lookup"><span data-stu-id="40b6f-653">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)</span></span>
* [<span data-ttu-id="40b6f-654">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="40b6f-654">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="40b6f-655">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="40b6f-655">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
