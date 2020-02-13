---
title: Fazer solicitações HTTP usando IHttpClientFactory no ASP.NET Core
author: stevejgordon
description: Saiba mais sobre como usar a interface IHttpClientFactory para gerenciar instâncias de HttpClient lógicas no ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
uid: fundamentals/http-requests
ms.openlocfilehash: 93b75525e8a3f10c4e0b655baaff83c0f6e8131b
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171799"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="18561-103">Fazer solicitações HTTP usando IHttpClientFactory no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18561-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18561-104">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)e [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="18561-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="18561-105">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="18561-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="18561-106">o `IHttpClientFactory` oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="18561-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="18561-107">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="18561-108">Por exemplo, um cliente chamado *GitHub* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="18561-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="18561-109">Um cliente padrão pode ser registrado para acesso geral.</span><span class="sxs-lookup"><span data-stu-id="18561-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="18561-110">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="18561-111">Fornece extensões para o middleware baseado em Polly para tirar proveito da delegação de manipuladores no `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="18561-112">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes.</span><span class="sxs-lookup"><span data-stu-id="18561-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="18561-113">O gerenciamento automático evita problemas comuns de DNS (sistema de nomes de domínio) que ocorrem ao gerenciar manualmente `HttpClient` tempos de vida.</span><span class="sxs-lookup"><span data-stu-id="18561-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="18561-114">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="18561-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="18561-115">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="18561-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="18561-116">O código de exemplo nesta versão de tópico usa <xref:System.Text.Json> para desserializar o conteúdo JSON retornado em respostas HTTP.</span><span class="sxs-lookup"><span data-stu-id="18561-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="18561-117">Para obter exemplos que usam `Json.NET` e `ReadAsAsync<T>`, use o seletor de versão para selecionar uma versão 2. x deste tópico.</span><span class="sxs-lookup"><span data-stu-id="18561-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="18561-118">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="18561-118">Consumption patterns</span></span>

<span data-ttu-id="18561-119">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="18561-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="18561-120">Uso básico</span><span class="sxs-lookup"><span data-stu-id="18561-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="18561-121">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="18561-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="18561-122">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="18561-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="18561-123">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="18561-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="18561-124">A melhor abordagem depende dos requisitos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="18561-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="18561-125">Uso básico</span><span class="sxs-lookup"><span data-stu-id="18561-125">Basic usage</span></span>

<span data-ttu-id="18561-126">`IHttpClientFactory` pode ser registrado chamando `AddHttpClient`:</span><span class="sxs-lookup"><span data-stu-id="18561-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="18561-127">Um `IHttpClientFactory` pode ser solicitado usando [injeção de dependência (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="18561-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="18561-128">O código a seguir usa `IHttpClientFactory` para criar uma instância de `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="18561-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="18561-129">Usar `IHttpClientFactory` como no exemplo anterior é uma boa maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="18561-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="18561-130">Ele não tem impacto sobre como `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="18561-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="18561-131">Em locais onde `HttpClient` instâncias são criadas em um aplicativo existente, substitua essas ocorrências por chamadas para <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="18561-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="18561-132">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="18561-132">Named clients</span></span>

<span data-ttu-id="18561-133">Os clientes nomeados são uma boa opção quando:</span><span class="sxs-lookup"><span data-stu-id="18561-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="18561-134">O aplicativo requer muitos usos distintos de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="18561-135">Muitos `HttpClient`s têm configuração diferente.</span><span class="sxs-lookup"><span data-stu-id="18561-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="18561-136">A configuração para um `HttpClient` nomeado pode ser especificada durante o registro no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="18561-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="18561-137">No código anterior, o cliente está configurado com:</span><span class="sxs-lookup"><span data-stu-id="18561-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="18561-138">O endereço base `https://api.github.com/`.</span><span class="sxs-lookup"><span data-stu-id="18561-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="18561-139">Dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="18561-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="18561-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="18561-140">CreateClient</span></span>

<span data-ttu-id="18561-141">Cada vez que <xref:System.Net.Http.IHttpClientFactory.CreateClient*> é chamado:</span><span class="sxs-lookup"><span data-stu-id="18561-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="18561-142">Uma nova instância do `HttpClient` é criada.</span><span class="sxs-lookup"><span data-stu-id="18561-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="18561-143">A ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="18561-143">The configuration action is called.</span></span>

<span data-ttu-id="18561-144">Para criar um cliente nomeado, passe seu nome para `CreateClient`:</span><span class="sxs-lookup"><span data-stu-id="18561-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="18561-145">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="18561-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="18561-146">O código pode passar apenas o caminho, já que o endereço base configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="18561-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="18561-147">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="18561-147">Typed clients</span></span>

<span data-ttu-id="18561-148">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="18561-148">Typed clients:</span></span>

* <span data-ttu-id="18561-149">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="18561-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="18561-150">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="18561-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="18561-151">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="18561-152">Por exemplo, um único cliente tipado pode ser usado:</span><span class="sxs-lookup"><span data-stu-id="18561-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="18561-153">Para um ponto de extremidade de back-end único.</span><span class="sxs-lookup"><span data-stu-id="18561-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="18561-154">Para encapsular toda a lógica lidando com o ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="18561-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="18561-155">Trabalhe com DI e pode ser injetado onde necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="18561-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="18561-156">Um cliente tipado aceita um parâmetro `HttpClient` em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="18561-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="18561-157">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="18561-157">In the preceding code:</span></span>

* <span data-ttu-id="18561-158">A configuração é movida para o cliente digitado.</span><span class="sxs-lookup"><span data-stu-id="18561-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="18561-159">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="18561-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="18561-160">Métodos específicos de API podem ser criados para expor `HttpClient` funcionalidade.</span><span class="sxs-lookup"><span data-stu-id="18561-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="18561-161">Por exemplo, o método `GetAspNetDocsIssues` encapsula o código para recuperar problemas abertos.</span><span class="sxs-lookup"><span data-stu-id="18561-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="18561-162">O código a seguir chama <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> em `Startup.ConfigureServices` para registrar uma classe de cliente tipada:</span><span class="sxs-lookup"><span data-stu-id="18561-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="18561-163">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="18561-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="18561-164">No código anterior, `AddHttpClient` registra `GitHubService` como um serviço transitório.</span><span class="sxs-lookup"><span data-stu-id="18561-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="18561-165">Esse registro usa um método de fábrica para:</span><span class="sxs-lookup"><span data-stu-id="18561-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="18561-166">Crie uma instância de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="18561-167">Crie uma instância do `GitHubService`, passando a instância do `HttpClient` para seu construtor.</span><span class="sxs-lookup"><span data-stu-id="18561-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="18561-168">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="18561-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="18561-169">A configuração de um cliente tipado pode ser especificada durante o registro no `Startup.ConfigureServices`, em vez de no construtor do cliente digitado:</span><span class="sxs-lookup"><span data-stu-id="18561-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="18561-170">O `HttpClient` pode ser encapsulado em um cliente digitado.</span><span class="sxs-lookup"><span data-stu-id="18561-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="18561-171">Em vez de expô-lo como uma propriedade, defina um método que chame a instância de `HttpClient` internamente:</span><span class="sxs-lookup"><span data-stu-id="18561-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="18561-172">No código anterior, a `HttpClient` é armazenada em um campo particular.</span><span class="sxs-lookup"><span data-stu-id="18561-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="18561-173">O acesso ao `HttpClient` é pelo método `GetRepos` público.</span><span class="sxs-lookup"><span data-stu-id="18561-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="18561-174">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="18561-174">Generated clients</span></span>

<span data-ttu-id="18561-175">`IHttpClientFactory` pode ser usado em combinação com bibliotecas de terceiros, como [rEFIt](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="18561-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="18561-176">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="18561-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="18561-177">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="18561-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="18561-178">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="18561-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="18561-179">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="18561-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="18561-180">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="18561-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="18561-181">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="18561-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="18561-182">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="18561-182">Outgoing request middleware</span></span>

<span data-ttu-id="18561-183">`HttpClient` tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="18561-183">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="18561-184">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="18561-184">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="18561-185">Simplifica a definição dos manipuladores a serem aplicados para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="18561-185">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="18561-186">Dá suporte ao registro e encadeamento de vários manipuladores para criar um pipeline de middleware de solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="18561-186">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="18561-187">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="18561-187">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="18561-188">Esse padrão:</span><span class="sxs-lookup"><span data-stu-id="18561-188">This pattern:</span></span>

  * <span data-ttu-id="18561-189">É semelhante ao pipeline de middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18561-189">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="18561-190">Fornece um mecanismo para gerenciar preocupações abrangentes em relação a solicitações HTTP, como:</span><span class="sxs-lookup"><span data-stu-id="18561-190">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="18561-191">cache</span><span class="sxs-lookup"><span data-stu-id="18561-191">caching</span></span>
    * <span data-ttu-id="18561-192">tratamento de erros</span><span class="sxs-lookup"><span data-stu-id="18561-192">error handling</span></span>
    * <span data-ttu-id="18561-193">serialização</span><span class="sxs-lookup"><span data-stu-id="18561-193">serialization</span></span>
    * <span data-ttu-id="18561-194">registro em log</span><span class="sxs-lookup"><span data-stu-id="18561-194">logging</span></span>

<span data-ttu-id="18561-195">Para criar um manipulador de delegação:</span><span class="sxs-lookup"><span data-stu-id="18561-195">To create a delegating handler:</span></span>

* <span data-ttu-id="18561-196">Derive de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="18561-196">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="18561-197">Substitua <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="18561-197">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="18561-198">Execute o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="18561-198">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="18561-199">O código anterior verifica se o cabeçalho de `X-API-KEY` está na solicitação.</span><span class="sxs-lookup"><span data-stu-id="18561-199">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="18561-200">Se `X-API-KEY` estiver ausente, <xref:System.Net.HttpStatusCode.BadRequest> será retornado.</span><span class="sxs-lookup"><span data-stu-id="18561-200">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="18561-201">Mais de um manipulador pode ser adicionado à configuração para um `HttpClient` com <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="18561-201">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="18561-202">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="18561-202">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="18561-203">O `IHttpClientFactory` cria um escopo de injeção de dependência separado para cada manipulador.</span><span class="sxs-lookup"><span data-stu-id="18561-203">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="18561-204">Os manipuladores podem depender dos serviços de qualquer escopo.</span><span class="sxs-lookup"><span data-stu-id="18561-204">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="18561-205">Os serviços dos quais os manipuladores dependem são descartados quando o manipulador é descartado.</span><span class="sxs-lookup"><span data-stu-id="18561-205">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="18561-206">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando o tipo para o manipulador.</span><span class="sxs-lookup"><span data-stu-id="18561-206">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="18561-207">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="18561-207">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="18561-208">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="18561-208">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="18561-209">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="18561-209">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="18561-210">Passe dados para o manipulador usando [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="18561-210">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="18561-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="18561-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="18561-212">Crie um objeto de armazenamento <xref:System.Threading.AsyncLocal`1> personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="18561-212">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="18561-213">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="18561-213">Use Polly-based handlers</span></span>

<span data-ttu-id="18561-214">o `IHttpClientFactory` integra-se com a biblioteca de terceiros [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="18561-214">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="18561-215">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="18561-215">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="18561-216">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="18561-216">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="18561-217">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="18561-217">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="18561-218">As extensões Polly dão suporte à adição de manipuladores baseados em Polly a clientes.</span><span class="sxs-lookup"><span data-stu-id="18561-218">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="18561-219">Polly requer o pacote NuGet [Microsoft. Extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="18561-219">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="18561-220">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="18561-220">Handle transient faults</span></span>

<span data-ttu-id="18561-221">As falhas normalmente ocorrem quando chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="18561-221">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="18561-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> permite que uma política seja definida para lidar com erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="18561-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="18561-223">As políticas configuradas com `AddTransientHttpErrorPolicy` manipulam as seguintes respostas:</span><span class="sxs-lookup"><span data-stu-id="18561-223">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="18561-224">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="18561-224">HTTP 5xx</span></span>
* <span data-ttu-id="18561-225">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="18561-225">HTTP 408</span></span>

<span data-ttu-id="18561-226">`AddTransientHttpErrorPolicy` fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="18561-226">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="18561-227">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="18561-227">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="18561-228">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="18561-228">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="18561-229">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="18561-229">Dynamically select policies</span></span>

<span data-ttu-id="18561-230">Os métodos de extensão são fornecidos para adicionar manipuladores baseados em Polly, por exemplo, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span><span class="sxs-lookup"><span data-stu-id="18561-230">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="18561-231">A sobrecarga de `AddPolicyHandler` a seguir inspeciona a solicitação para decidir qual política aplicar:</span><span class="sxs-lookup"><span data-stu-id="18561-231">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="18561-232">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="18561-232">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="18561-233">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="18561-233">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="18561-234">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="18561-234">Add multiple Polly handlers</span></span>

<span data-ttu-id="18561-235">É comum aninhar políticas Polly:</span><span class="sxs-lookup"><span data-stu-id="18561-235">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="18561-236">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="18561-236">In the preceding example:</span></span>

* <span data-ttu-id="18561-237">Dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="18561-237">Two handlers are added.</span></span>
* <span data-ttu-id="18561-238">O primeiro manipulador usa <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="18561-238">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="18561-239">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="18561-239">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="18561-240">A segunda chamada de `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="18561-240">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="18561-241">Outras solicitações externas serão bloqueadas por 30 segundos se 5 tentativas com falha ocorrerem em sequência.</span><span class="sxs-lookup"><span data-stu-id="18561-241">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="18561-242">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="18561-242">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="18561-243">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="18561-243">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="18561-244">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="18561-244">Add policies from the Polly registry</span></span>

<span data-ttu-id="18561-245">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="18561-245">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="18561-246">No seguinte código:</span><span class="sxs-lookup"><span data-stu-id="18561-246">In the following code:</span></span>

* <span data-ttu-id="18561-247">As políticas "regular" e "longa" são adicionadas.</span><span class="sxs-lookup"><span data-stu-id="18561-247">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="18561-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> adiciona as políticas "regular" e "longa" do registro.</span><span class="sxs-lookup"><span data-stu-id="18561-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="18561-249">Para obter mais informações sobre as integrações `IHttpClientFactory` e Polly, consulte o [wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="18561-249">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="18561-250">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="18561-250">HttpClient and lifetime management</span></span>

<span data-ttu-id="18561-251">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="18561-251">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="18561-252">Um <xref:System.Net.Http.HttpMessageHandler> é criado por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="18561-252">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="18561-253">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="18561-253">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="18561-254">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="18561-254">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="18561-255">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="18561-255">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="18561-256">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="18561-256">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="18561-257">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="18561-257">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="18561-258">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reajam às alterações de DNS (sistema de nomes de domínio).</span><span class="sxs-lookup"><span data-stu-id="18561-258">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="18561-259">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="18561-259">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="18561-260">O valor padrão pode ser substituído em uma base de cliente por nome:</span><span class="sxs-lookup"><span data-stu-id="18561-260">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="18561-261">instâncias de `HttpClient` geralmente podem ser tratadas como objetos .NET que **não** exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="18561-261">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="18561-262">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="18561-262">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="18561-263">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-263">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="18561-264">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="18561-264">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="18561-265">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="18561-265">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="18561-266">Alternativas para IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="18561-266">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="18561-267">O uso de `IHttpClientFactory` em um aplicativo habilitado para DI é evitado:</span><span class="sxs-lookup"><span data-stu-id="18561-267">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="18561-268">Problemas de esgotamento de recursos por meio do pooling `HttpMessageHandler` instâncias.</span><span class="sxs-lookup"><span data-stu-id="18561-268">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="18561-269">Problemas de DNS obsoletos por ciclo `HttpMessageHandler` instâncias em intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="18561-269">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="18561-270">Há maneiras alternativas de resolver os problemas anteriores usando uma instância de <xref:System.Net.Http.SocketsHttpHandler> de vida longa.</span><span class="sxs-lookup"><span data-stu-id="18561-270">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="18561-271">Crie uma instância do `SocketsHttpHandler` quando o aplicativo for iniciado e usado para a vida útil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="18561-271">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="18561-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> para um valor apropriado com base em tempos de atualização de DNS.</span><span class="sxs-lookup"><span data-stu-id="18561-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="18561-273">Crie `HttpClient` instâncias usando `new HttpClient(handler, disposeHandler: false)` conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="18561-273">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="18561-274">As abordagens anteriores resolvem os problemas de gerenciamento de recursos que `IHttpClientFactory` resolve de forma semelhante.</span><span class="sxs-lookup"><span data-stu-id="18561-274">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="18561-275">O `SocketsHttpHandler` compartilha conexões entre instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-275">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="18561-276">Esse compartilhamento impede o esgotamento de soquete.</span><span class="sxs-lookup"><span data-stu-id="18561-276">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="18561-277">O `SocketsHttpHandler` ciclos conexões de acordo com `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="18561-277">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="18561-278">Cookies</span><span class="sxs-lookup"><span data-stu-id="18561-278">Cookies</span></span>

<span data-ttu-id="18561-279">As instâncias de `HttpMessageHandler` em pool resultam em `CookieContainer` objetos que estão sendo compartilhados.</span><span class="sxs-lookup"><span data-stu-id="18561-279">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="18561-280">O compartilhamento de objetos `CookieContainer` imprevistos geralmente resulta em código incorreto.</span><span class="sxs-lookup"><span data-stu-id="18561-280">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="18561-281">Para aplicativos que exigem cookies, considere o:</span><span class="sxs-lookup"><span data-stu-id="18561-281">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="18561-282">Desabilitando a manipulação automática de cookies</span><span class="sxs-lookup"><span data-stu-id="18561-282">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="18561-283">Evitando `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="18561-283">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="18561-284">Chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desabilitar o tratamento automático de cookies:</span><span class="sxs-lookup"><span data-stu-id="18561-284">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="18561-285">Registro em log</span><span class="sxs-lookup"><span data-stu-id="18561-285">Logging</span></span>

<span data-ttu-id="18561-286">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="18561-286">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="18561-287">Habilite o nível de informações apropriado na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="18561-287">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="18561-288">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="18561-288">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="18561-289">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="18561-289">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="18561-290">Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de "System .net. http. HttpClient. **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="18561-290">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="18561-291">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="18561-291">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="18561-292">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="18561-292">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="18561-293">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="18561-293">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="18561-294">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="18561-294">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="18561-295">No exemplo de *MyNamedClient* , essas mensagens são registradas com a categoria de log "System .net. http. HttpClient. **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="18561-295">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="18561-296">Para a solicitação, isso ocorre depois que todos os outros manipuladores forem executados e imediatamente antes de a solicitação ser enviada.</span><span class="sxs-lookup"><span data-stu-id="18561-296">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="18561-297">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="18561-297">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="18561-298">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="18561-298">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="18561-299">Isso pode incluir alterações nos cabeçalhos de solicitação ou no código de status de resposta.</span><span class="sxs-lookup"><span data-stu-id="18561-299">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="18561-300">A inclusão do nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos.</span><span class="sxs-lookup"><span data-stu-id="18561-300">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="18561-301">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="18561-301">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="18561-302">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="18561-302">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="18561-303">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="18561-303">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="18561-304">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="18561-304">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="18561-305">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="18561-305">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="18561-306">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="18561-306">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="18561-307">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="18561-307">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="18561-308">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="18561-308">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="18561-309">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="18561-309">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="18561-310">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="18561-310">In the following example:</span></span>

* <span data-ttu-id="18561-311"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="18561-311"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="18561-312">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-312">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="18561-313">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="18561-313">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="18561-314">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="18561-314">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="18561-315">Middleware de propagação de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="18561-315">Header propagation middleware</span></span>

<span data-ttu-id="18561-316">A propagação de cabeçalho é um middleware ASP.NET Core para propagar cabeçalhos HTTP da solicitação de entrada para as solicitações de cliente HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="18561-316">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="18561-317">Para usar a propagação de cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="18561-317">To use header propagation:</span></span>

* <span data-ttu-id="18561-318">Referencie o pacote [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) .</span><span class="sxs-lookup"><span data-stu-id="18561-318">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="18561-319">Configure o middleware e o `HttpClient` no `Startup`:</span><span class="sxs-lookup"><span data-stu-id="18561-319">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="18561-320">O cliente inclui os cabeçalhos configurados nas solicitações de saída:</span><span class="sxs-lookup"><span data-stu-id="18561-320">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="18561-321">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="18561-321">Additional resources</span></span>

* <span data-ttu-id="18561-322">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) (Usar o HttpClientFactory para implementar solicitações HTTP resilientes)</span><span class="sxs-lookup"><span data-stu-id="18561-322">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)</span></span>
* [<span data-ttu-id="18561-323">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="18561-323">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="18561-324">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="18561-324">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="18561-325">Como serializar e desserializar JSON no .NET</span><span class="sxs-lookup"><span data-stu-id="18561-325">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="18561-326">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="18561-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="18561-327">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="18561-327">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="18561-328">Ele oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="18561-328">It offers the following benefits:</span></span>

* <span data-ttu-id="18561-329">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-329">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="18561-330">Por exemplo, um cliente do *github* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="18561-330">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="18561-331">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="18561-331">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="18561-332">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` e fornece extensões para que o middleware baseado no Polly possa aproveitar esse recurso.</span><span class="sxs-lookup"><span data-stu-id="18561-332">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="18561-333">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-333">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="18561-334">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="18561-334">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="18561-335">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18561-335">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="18561-336">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="18561-336">Consumption patterns</span></span>

<span data-ttu-id="18561-337">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="18561-337">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="18561-338">Uso básico</span><span class="sxs-lookup"><span data-stu-id="18561-338">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="18561-339">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="18561-339">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="18561-340">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="18561-340">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="18561-341">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="18561-341">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="18561-342">Nenhum deles é estritamente superiores ao outro.</span><span class="sxs-lookup"><span data-stu-id="18561-342">None of them are strictly superior to another.</span></span> <span data-ttu-id="18561-343">A melhor abordagem depende das restrições do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="18561-343">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="18561-344">Uso básico</span><span class="sxs-lookup"><span data-stu-id="18561-344">Basic usage</span></span>

<span data-ttu-id="18561-345">O `IHttpClientFactory` pode ser registrado chamando o método de extensão `AddHttpClient` em `IServiceCollection`, dentro do método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="18561-345">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="18561-346">Depois de registrado, o código pode aceitar um `IHttpClientFactory` em qualquer lugar em que os serviços possam ser injetados com [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="18561-346">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="18561-347">O `IHttpClientFactory` pode ser usado para criar uma instância de `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="18561-347">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="18561-348">Usar o `IHttpClientFactory` dessa forma é uma ótima maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="18561-348">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="18561-349">Não há nenhum impacto na maneira em que o `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="18561-349">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="18561-350">Nos locais em que as instâncias de `HttpClient` são criadas no momento, substitua essas ocorrências por uma chamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="18561-350">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="18561-351">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="18561-351">Named clients</span></span>

<span data-ttu-id="18561-352">Quando um aplicativo exige vários usos distintos do `HttpClient`, cada um com uma configuração diferente, uma opção é usar **clientes nomeados**.</span><span class="sxs-lookup"><span data-stu-id="18561-352">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="18561-353">A configuração de um `HttpClient` nomeado pode ser especificada durante o registro em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="18561-353">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="18561-354">No código anterior, `AddHttpClient` é chamado, fornecendo o nome *github*.</span><span class="sxs-lookup"><span data-stu-id="18561-354">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="18561-355">Esse cliente tem algumas configurações padrão aplicadas, ou seja, o endereço básico e dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="18561-355">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="18561-356">Toda vez que o `CreateClient` é chamado, uma nova instância de `HttpClient` é criada e a ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="18561-356">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="18561-357">Para consumir um cliente nomeado, um parâmetro de cadeia de caracteres pode ser passado para `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-357">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="18561-358">Especifique o nome do cliente a ser criado:</span><span class="sxs-lookup"><span data-stu-id="18561-358">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="18561-359">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="18561-359">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="18561-360">Ela pode passar apenas o caminho, pois o endereço básico configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="18561-360">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="18561-361">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="18561-361">Typed clients</span></span>

<span data-ttu-id="18561-362">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="18561-362">Typed clients:</span></span>

* <span data-ttu-id="18561-363">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="18561-363">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="18561-364">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="18561-364">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="18561-365">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-365">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="18561-366">Por exemplo, um único cliente com tipo pode ser usado para um único ponto de extremidade de back-end e encapsular toda a lógica que lida com esse ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="18561-366">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="18561-367">Funcionam com a DI e podem ser injetados no local necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="18561-367">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="18561-368">Um cliente tipado aceita um parâmetro `HttpClient` em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="18561-368">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="18561-369">No código anterior, a configuração é movida para o cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="18561-369">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="18561-370">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="18561-370">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="18561-371">É possível definir métodos específicos da API que expõem a funcionalidade `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-371">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="18561-372">O método `GetAspNetDocsIssues` encapsula o código necessário para consultar e analisar os últimos problemas em aberto de um repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="18561-372">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="18561-373">Para registrar um cliente com tipo, o método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genérico pode ser usado em `Startup.ConfigureServices`, especificando a classe do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="18561-373">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="18561-374">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="18561-374">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="18561-375">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="18561-375">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="18561-376">Se preferir, a configuração de um cliente com tipo poderá ser especificada durante o registro em `Startup.ConfigureServices` e não no construtor do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="18561-376">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="18561-377">É possível encapsular totalmente o `HttpClient` dentro de um cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="18561-377">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="18561-378">Em vez de o expor como uma propriedade, é possível fornecer métodos públicos que chamam a instância de `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="18561-378">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="18561-379">No código anterior, o `HttpClient` é armazenado como um campo privado.</span><span class="sxs-lookup"><span data-stu-id="18561-379">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="18561-380">Todo o acesso para fazer chamadas externas passa pelo método `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="18561-380">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="18561-381">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="18561-381">Generated clients</span></span>

<span data-ttu-id="18561-382">O `IHttpClientFactory` pode ser usado em combinação com outras bibliotecas de terceiros, como a [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="18561-382">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="18561-383">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="18561-383">Refit is a REST library for .NET.</span></span> <span data-ttu-id="18561-384">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="18561-384">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="18561-385">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="18561-385">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="18561-386">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="18561-386">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="18561-387">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="18561-387">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="18561-388">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="18561-388">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="18561-389">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="18561-389">Outgoing request middleware</span></span>

<span data-ttu-id="18561-390">O `HttpClient` já tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="18561-390">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="18561-391">O `IHttpClientFactory` facilita a definição dos manipuladores a serem aplicados a cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="18561-391">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="18561-392">Ele permite o registro e o encadeamento de vários manipuladores para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="18561-392">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="18561-393">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="18561-393">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="18561-394">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18561-394">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="18561-395">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="18561-395">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="18561-396">Para criar um manipulador, defina uma classe derivando-a de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="18561-396">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="18561-397">Substitua o método `SendAsync` para executar o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="18561-397">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="18561-398">O código anterior define um manipulador básico.</span><span class="sxs-lookup"><span data-stu-id="18561-398">The preceding code defines a basic handler.</span></span> <span data-ttu-id="18561-399">Ele verifica se um cabeçalho `X-API-KEY` foi incluído na solicitação.</span><span class="sxs-lookup"><span data-stu-id="18561-399">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="18561-400">Se o cabeçalho estiver ausente, isso poderá evitar a chamada de HTTP e retornar uma resposta adequada.</span><span class="sxs-lookup"><span data-stu-id="18561-400">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="18561-401">Durante o registro, um ou mais manipuladores podem ser adicionados à configuração para um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-401">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="18561-402">Essa tarefa é realizada por meio de métodos de extensão no <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="18561-402">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="18561-403">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="18561-403">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="18561-404">O `IHttpClientFactory` cria um escopo de injeção de dependência separado para cada manipulador.</span><span class="sxs-lookup"><span data-stu-id="18561-404">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="18561-405">Os manipuladores podem depender de serviços de qualquer escopo.</span><span class="sxs-lookup"><span data-stu-id="18561-405">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="18561-406">Os serviços dos quais os manipuladores dependem são descartados quando o manipulador é descartado.</span><span class="sxs-lookup"><span data-stu-id="18561-406">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="18561-407">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando o tipo para o manipulador.</span><span class="sxs-lookup"><span data-stu-id="18561-407">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="18561-408">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="18561-408">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="18561-409">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="18561-409">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="18561-410">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="18561-410">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="18561-411">Passe os dados pelo manipulador usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="18561-411">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="18561-412">Use `IHttpContextAccessor` para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="18561-412">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="18561-413">Crie um objeto de armazenamento `AsyncLocal` personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="18561-413">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="18561-414">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="18561-414">Use Polly-based handlers</span></span>

<span data-ttu-id="18561-415">O `IHttpClientFactory` integra-se a uma biblioteca de terceiros popular chamada [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="18561-415">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="18561-416">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="18561-416">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="18561-417">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="18561-417">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="18561-418">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="18561-418">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="18561-419">As extensões do Polly:</span><span class="sxs-lookup"><span data-stu-id="18561-419">The Polly extensions:</span></span>

* <span data-ttu-id="18561-420">Dão suporte à adição de manipuladores baseados no Polly aos clientes.</span><span class="sxs-lookup"><span data-stu-id="18561-420">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="18561-421">Podem ser usadas depois de instalar o pacote do NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="18561-421">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="18561-422">O pacote não está incluído na estrutura ASP.NET Core compartilhada.</span><span class="sxs-lookup"><span data-stu-id="18561-422">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="18561-423">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="18561-423">Handle transient faults</span></span>

<span data-ttu-id="18561-424">As falhas mais comuns ocorrem quando as chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="18561-424">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="18561-425">Um método de extensão conveniente chamado `AddTransientHttpErrorPolicy` é incluído para permitir que uma política seja definido para tratar erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="18561-425">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="18561-426">As políticas configuradas com esse método de extensão tratam `HttpRequestException`, respostas HTTP 5xx e respostas HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="18561-426">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="18561-427">A extensão `AddTransientHttpErrorPolicy` pode ser usada em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="18561-427">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="18561-428">A extensão fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="18561-428">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="18561-429">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="18561-429">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="18561-430">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="18561-430">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="18561-431">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="18561-431">Dynamically select policies</span></span>

<span data-ttu-id="18561-432">Existem métodos de extensão adicionais que podem ser usados para adicionar manipuladores baseados no Polly.</span><span class="sxs-lookup"><span data-stu-id="18561-432">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="18561-433">Uma dessas extensões é a `AddPolicyHandler`, que tem várias sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="18561-433">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="18561-434">Uma sobrecarga permite que a solicitação seja inspecionada durante a definição da política a ser aplicada:</span><span class="sxs-lookup"><span data-stu-id="18561-434">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="18561-435">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="18561-435">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="18561-436">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="18561-436">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="18561-437">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="18561-437">Add multiple Polly handlers</span></span>

<span data-ttu-id="18561-438">É comum aninhar políticas do Polly para fornecer funcionalidade avançada:</span><span class="sxs-lookup"><span data-stu-id="18561-438">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="18561-439">No exemplo anterior, dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="18561-439">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="18561-440">O primeiro usa a extensão `AddTransientHttpErrorPolicy` para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="18561-440">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="18561-441">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="18561-441">Failed requests are retried up to three times.</span></span> <span data-ttu-id="18561-442">A segunda chamada a `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="18561-442">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="18561-443">As solicitações externas adicionais são bloqueadas por 30 segundos quando ocorrem cinco tentativas com falha consecutivamente.</span><span class="sxs-lookup"><span data-stu-id="18561-443">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="18561-444">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="18561-444">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="18561-445">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="18561-445">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="18561-446">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="18561-446">Add policies from the Polly registry</span></span>

<span data-ttu-id="18561-447">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="18561-447">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="18561-448">É fornecido um método de extensão que permite que um manipulador seja adicionado usando uma política do registro:</span><span class="sxs-lookup"><span data-stu-id="18561-448">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="18561-449">No código anterior, duas políticas são registradas quando `PolicyRegistry` é adicionado ao `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="18561-449">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="18561-450">Para usar uma política do registro, o método `AddPolicyHandlerFromRegistry` é usado, passando o nome da política a ser aplicada.</span><span class="sxs-lookup"><span data-stu-id="18561-450">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="18561-451">Mais informações sobre o `IHttpClientFactory` e as integrações do Polly podem ser encontradas no [Wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="18561-451">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="18561-452">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="18561-452">HttpClient and lifetime management</span></span>

<span data-ttu-id="18561-453">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="18561-453">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="18561-454">Há um <xref:System.Net.Http.HttpMessageHandler> por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="18561-454">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="18561-455">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="18561-455">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="18561-456">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="18561-456">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="18561-457">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="18561-457">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="18561-458">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="18561-458">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="18561-459">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="18561-459">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="18561-460">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reaja a alterações de DNS.</span><span class="sxs-lookup"><span data-stu-id="18561-460">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="18561-461">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="18561-461">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="18561-462">O valor padrão pode ser substituído para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="18561-462">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="18561-463">Para substituí-lo, chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> no `IHttpClientBuilder` que é retornado ao criar o cliente:</span><span class="sxs-lookup"><span data-stu-id="18561-463">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="18561-464">Não é necessário descartar o cliente.</span><span class="sxs-lookup"><span data-stu-id="18561-464">Disposal of the client isn't required.</span></span> <span data-ttu-id="18561-465">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="18561-465">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="18561-466">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-466">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="18561-467">Geralmente, as instâncias de `HttpClient` podem ser tratadas como objetos do .NET e não exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="18561-467">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="18561-468">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="18561-468">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="18561-469">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="18561-469">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="18561-470">Alternativas para IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="18561-470">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="18561-471">O uso de `IHttpClientFactory` em um aplicativo habilitado para DI é evitado:</span><span class="sxs-lookup"><span data-stu-id="18561-471">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="18561-472">Problemas de esgotamento de recursos por meio do pooling `HttpMessageHandler` instâncias.</span><span class="sxs-lookup"><span data-stu-id="18561-472">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="18561-473">Problemas de DNS obsoletos por ciclo `HttpMessageHandler` instâncias em intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="18561-473">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="18561-474">Há maneiras alternativas de resolver os problemas anteriores usando uma instância de <xref:System.Net.Http.SocketsHttpHandler> de vida longa.</span><span class="sxs-lookup"><span data-stu-id="18561-474">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="18561-475">Crie uma instância do `SocketsHttpHandler` quando o aplicativo for iniciado e usado para a vida útil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="18561-475">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="18561-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> para um valor apropriado com base em tempos de atualização de DNS.</span><span class="sxs-lookup"><span data-stu-id="18561-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="18561-477">Crie `HttpClient` instâncias usando `new HttpClient(handler, disposeHandler: false)` conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="18561-477">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="18561-478">As abordagens anteriores resolvem os problemas de gerenciamento de recursos que `IHttpClientFactory` resolve de forma semelhante.</span><span class="sxs-lookup"><span data-stu-id="18561-478">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="18561-479">O `SocketsHttpHandler` compartilha conexões entre instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-479">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="18561-480">Esse compartilhamento impede o esgotamento de soquete.</span><span class="sxs-lookup"><span data-stu-id="18561-480">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="18561-481">O `SocketsHttpHandler` ciclos conexões de acordo com `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="18561-481">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="18561-482">Cookies</span><span class="sxs-lookup"><span data-stu-id="18561-482">Cookies</span></span>

<span data-ttu-id="18561-483">As instâncias de `HttpMessageHandler` em pool resultam em `CookieContainer` objetos que estão sendo compartilhados.</span><span class="sxs-lookup"><span data-stu-id="18561-483">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="18561-484">O compartilhamento de objetos `CookieContainer` imprevistos geralmente resulta em código incorreto.</span><span class="sxs-lookup"><span data-stu-id="18561-484">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="18561-485">Para aplicativos que exigem cookies, considere o:</span><span class="sxs-lookup"><span data-stu-id="18561-485">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="18561-486">Desabilitando a manipulação automática de cookies</span><span class="sxs-lookup"><span data-stu-id="18561-486">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="18561-487">Evitando `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="18561-487">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="18561-488">Chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desabilitar o tratamento automático de cookies:</span><span class="sxs-lookup"><span data-stu-id="18561-488">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="18561-489">Registro em log</span><span class="sxs-lookup"><span data-stu-id="18561-489">Logging</span></span>

<span data-ttu-id="18561-490">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="18561-490">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="18561-491">Habilite o nível apropriado de informações na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="18561-491">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="18561-492">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="18561-492">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="18561-493">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="18561-493">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="18561-494">Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="18561-494">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="18561-495">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="18561-495">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="18561-496">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="18561-496">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="18561-497">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="18561-497">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="18561-498">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="18561-498">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="18561-499">No caso do exemplo de *MyNamedClient*, essas mensagens são registradas em log na categoria de log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="18561-499">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="18561-500">Para a solicitação, isso ocorre depois que todos os outros manipuladores são executados e imediatamente antes que a solicitação seja enviada pela rede.</span><span class="sxs-lookup"><span data-stu-id="18561-500">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="18561-501">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="18561-501">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="18561-502">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="18561-502">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="18561-503">Isso pode incluir, por exemplo, alterações nos cabeçalhos de solicitação ou no código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="18561-503">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="18561-504">Incluir o nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos, quando necessário.</span><span class="sxs-lookup"><span data-stu-id="18561-504">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="18561-505">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="18561-505">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="18561-506">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="18561-506">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="18561-507">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="18561-507">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="18561-508">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="18561-508">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="18561-509">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="18561-509">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="18561-510">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="18561-510">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="18561-511">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="18561-511">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="18561-512">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="18561-512">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="18561-513">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="18561-513">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="18561-514">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="18561-514">In the following example:</span></span>

* <span data-ttu-id="18561-515"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="18561-515"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="18561-516">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-516">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="18561-517">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="18561-517">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="18561-518">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="18561-518">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="18561-519">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="18561-519">Additional resources</span></span>

* <span data-ttu-id="18561-520">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) (Usar o HttpClientFactory para implementar solicitações HTTP resilientes)</span><span class="sxs-lookup"><span data-stu-id="18561-520">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)</span></span>
* [<span data-ttu-id="18561-521">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="18561-521">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="18561-522">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="18561-522">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="18561-523">Por [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) e [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="18561-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="18561-524">É possível registrar e usar um <xref:System.Net.Http.IHttpClientFactory> para configurar e criar instâncias de <xref:System.Net.Http.HttpClient> em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="18561-524">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="18561-525">Ele oferece os seguintes benefícios:</span><span class="sxs-lookup"><span data-stu-id="18561-525">It offers the following benefits:</span></span>

* <span data-ttu-id="18561-526">Fornece um local central para nomear e configurar instâncias lógicas de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-526">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="18561-527">Por exemplo, um cliente do *github* pode ser registrado e configurado para acessar o [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="18561-527">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="18561-528">Um cliente padrão pode ser registrado para outras finalidades.</span><span class="sxs-lookup"><span data-stu-id="18561-528">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="18561-529">Codifica o conceito de middleware de saída por meio da delegação de manipuladores no `HttpClient` e fornece extensões para que o middleware baseado no Polly possa aproveitar esse recurso.</span><span class="sxs-lookup"><span data-stu-id="18561-529">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="18561-530">Gerencia o pooling e o tempo de vida das instâncias de `HttpClientMessageHandler` subjacentes para evitar problemas de DNS comuns que ocorrem no gerenciamento manual de tempos de vida de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-530">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="18561-531">Adiciona uma experiência de registro em log configurável (via `ILogger`) para todas as solicitações enviadas por meio de clientes criados pelo alocador.</span><span class="sxs-lookup"><span data-stu-id="18561-531">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="18561-532">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18561-532">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="18561-533">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="18561-533">Prerequisites</span></span>

<span data-ttu-id="18561-534">Os projetos direcionados ao .NET Framework exigem a instalação do pacote do NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="18561-534">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="18561-535">Os projetos destinados ao .Net Core e a referência ao [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) já incluem o pacote `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="18561-535">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="18561-536">Padrões de consumo</span><span class="sxs-lookup"><span data-stu-id="18561-536">Consumption patterns</span></span>

<span data-ttu-id="18561-537">Há várias maneiras de usar o `IHttpClientFactory` em um aplicativo:</span><span class="sxs-lookup"><span data-stu-id="18561-537">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="18561-538">Uso básico</span><span class="sxs-lookup"><span data-stu-id="18561-538">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="18561-539">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="18561-539">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="18561-540">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="18561-540">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="18561-541">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="18561-541">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="18561-542">Nenhum deles é estritamente superiores ao outro.</span><span class="sxs-lookup"><span data-stu-id="18561-542">None of them are strictly superior to another.</span></span> <span data-ttu-id="18561-543">A melhor abordagem depende das restrições do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="18561-543">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="18561-544">Uso básico</span><span class="sxs-lookup"><span data-stu-id="18561-544">Basic usage</span></span>

<span data-ttu-id="18561-545">O `IHttpClientFactory` pode ser registrado chamando o método de extensão `AddHttpClient` em `IServiceCollection`, dentro do método `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="18561-545">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="18561-546">Depois de registrado, o código pode aceitar um `IHttpClientFactory` em qualquer lugar em que os serviços possam ser injetados com [injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="18561-546">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="18561-547">O `IHttpClientFactory` pode ser usado para criar uma instância de `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="18561-547">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="18561-548">Usar o `IHttpClientFactory` dessa forma é uma ótima maneira de refatorar um aplicativo existente.</span><span class="sxs-lookup"><span data-stu-id="18561-548">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="18561-549">Não há nenhum impacto na maneira em que o `HttpClient` é usado.</span><span class="sxs-lookup"><span data-stu-id="18561-549">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="18561-550">Nos locais em que as instâncias de `HttpClient` são criadas no momento, substitua essas ocorrências por uma chamada a <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="18561-550">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="18561-551">Clientes nomeados</span><span class="sxs-lookup"><span data-stu-id="18561-551">Named clients</span></span>

<span data-ttu-id="18561-552">Quando um aplicativo exige vários usos distintos do `HttpClient`, cada um com uma configuração diferente, uma opção é usar **clientes nomeados**.</span><span class="sxs-lookup"><span data-stu-id="18561-552">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="18561-553">A configuração de um `HttpClient` nomeado pode ser especificada durante o registro em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="18561-553">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="18561-554">No código anterior, `AddHttpClient` é chamado, fornecendo o nome *github*.</span><span class="sxs-lookup"><span data-stu-id="18561-554">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="18561-555">Esse cliente tem algumas configurações padrão aplicadas, ou seja, o endereço básico e dois cabeçalhos necessários para trabalhar com a API do GitHub.</span><span class="sxs-lookup"><span data-stu-id="18561-555">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="18561-556">Toda vez que o `CreateClient` é chamado, uma nova instância de `HttpClient` é criada e a ação de configuração é chamada.</span><span class="sxs-lookup"><span data-stu-id="18561-556">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="18561-557">Para consumir um cliente nomeado, um parâmetro de cadeia de caracteres pode ser passado para `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-557">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="18561-558">Especifique o nome do cliente a ser criado:</span><span class="sxs-lookup"><span data-stu-id="18561-558">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="18561-559">No código anterior, a solicitação não precisa especificar um nome do host.</span><span class="sxs-lookup"><span data-stu-id="18561-559">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="18561-560">Ela pode passar apenas o caminho, pois o endereço básico configurado para o cliente é usado.</span><span class="sxs-lookup"><span data-stu-id="18561-560">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="18561-561">Clientes com tipo</span><span class="sxs-lookup"><span data-stu-id="18561-561">Typed clients</span></span>

<span data-ttu-id="18561-562">Clientes com tipo:</span><span class="sxs-lookup"><span data-stu-id="18561-562">Typed clients:</span></span>

* <span data-ttu-id="18561-563">Fornecem as mesmas funcionalidade que os clientes nomeados sem a necessidade de usar cadeias de caracteres como chaves.</span><span class="sxs-lookup"><span data-stu-id="18561-563">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="18561-564">Fornecem a ajuda do IntelliSense e do compilador durante o consumo de clientes.</span><span class="sxs-lookup"><span data-stu-id="18561-564">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="18561-565">Fornecem um único local para configurar e interagir com um determinado `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-565">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="18561-566">Por exemplo, um único cliente com tipo pode ser usado para um único ponto de extremidade de back-end e encapsular toda a lógica que lida com esse ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="18561-566">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="18561-567">Funcionam com a DI e podem ser injetados no local necessário no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="18561-567">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="18561-568">Um cliente tipado aceita um parâmetro `HttpClient` em seu construtor:</span><span class="sxs-lookup"><span data-stu-id="18561-568">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="18561-569">No código anterior, a configuração é movida para o cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="18561-569">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="18561-570">O objeto `HttpClient` é exposto como uma propriedade pública.</span><span class="sxs-lookup"><span data-stu-id="18561-570">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="18561-571">É possível definir métodos específicos da API que expõem a funcionalidade `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-571">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="18561-572">O método `GetAspNetDocsIssues` encapsula o código necessário para consultar e analisar os últimos problemas em aberto de um repositório GitHub.</span><span class="sxs-lookup"><span data-stu-id="18561-572">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="18561-573">Para registrar um cliente com tipo, o método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> genérico pode ser usado em `Startup.ConfigureServices`, especificando a classe do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="18561-573">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="18561-574">O cliente com tipo é registrado como transitório com a DI.</span><span class="sxs-lookup"><span data-stu-id="18561-574">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="18561-575">O cliente com tipo pode ser injetado e consumido diretamente:</span><span class="sxs-lookup"><span data-stu-id="18561-575">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="18561-576">Se preferir, a configuração de um cliente com tipo poderá ser especificada durante o registro em `Startup.ConfigureServices` e não no construtor do cliente com tipo:</span><span class="sxs-lookup"><span data-stu-id="18561-576">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="18561-577">É possível encapsular totalmente o `HttpClient` dentro de um cliente com tipo.</span><span class="sxs-lookup"><span data-stu-id="18561-577">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="18561-578">Em vez de o expor como uma propriedade, é possível fornecer métodos públicos que chamam a instância de `HttpClient` internamente.</span><span class="sxs-lookup"><span data-stu-id="18561-578">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="18561-579">No código anterior, o `HttpClient` é armazenado como um campo privado.</span><span class="sxs-lookup"><span data-stu-id="18561-579">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="18561-580">Todo o acesso para fazer chamadas externas passa pelo método `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="18561-580">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="18561-581">Clientes gerados</span><span class="sxs-lookup"><span data-stu-id="18561-581">Generated clients</span></span>

<span data-ttu-id="18561-582">O `IHttpClientFactory` pode ser usado em combinação com outras bibliotecas de terceiros, como a [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="18561-582">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="18561-583">Refit é uma biblioteca REST para .NET.</span><span class="sxs-lookup"><span data-stu-id="18561-583">Refit is a REST library for .NET.</span></span> <span data-ttu-id="18561-584">Ela converte APIs REST em interfaces dinâmicas.</span><span class="sxs-lookup"><span data-stu-id="18561-584">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="18561-585">Uma implementação da interface é gerada dinamicamente pelo `RestService` usando `HttpClient` para fazer as chamadas de HTTP externas.</span><span class="sxs-lookup"><span data-stu-id="18561-585">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="18561-586">Uma interface e uma resposta são definidas para representar a API externa e sua resposta:</span><span class="sxs-lookup"><span data-stu-id="18561-586">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="18561-587">Um cliente com tipo pode ser adicionado usando a Refit para gerar a implementação:</span><span class="sxs-lookup"><span data-stu-id="18561-587">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="18561-588">A interface definida pode ser consumida, quando necessário, com a implementação fornecida pela DI e pela Refit:</span><span class="sxs-lookup"><span data-stu-id="18561-588">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="18561-589">Middleware de solicitação de saída</span><span class="sxs-lookup"><span data-stu-id="18561-589">Outgoing request middleware</span></span>

<span data-ttu-id="18561-590">O `HttpClient` já tem o conceito de delegar manipuladores que podem ser vinculados para solicitações HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="18561-590">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="18561-591">O `IHttpClientFactory` facilita a definição dos manipuladores a serem aplicados a cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="18561-591">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="18561-592">Ele permite o registro e o encadeamento de vários manipuladores para criar um pipeline do middleware de solicitação saída.</span><span class="sxs-lookup"><span data-stu-id="18561-592">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="18561-593">Cada um desses manipuladores é capaz de executar o trabalho antes e após a solicitação de saída.</span><span class="sxs-lookup"><span data-stu-id="18561-593">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="18561-594">Esse padrão é semelhante ao pipeline do middleware de entrada no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18561-594">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="18561-595">O padrão fornece um mecanismo para gerenciar interesses paralelos em relação às solicitações HTTP, incluindo o armazenamento em cache, o tratamento de erro, a serialização e o registro em log.</span><span class="sxs-lookup"><span data-stu-id="18561-595">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="18561-596">Para criar um manipulador, defina uma classe derivando-a de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="18561-596">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="18561-597">Substitua o método `SendAsync` para executar o código antes de passar a solicitação para o próximo manipulador no pipeline:</span><span class="sxs-lookup"><span data-stu-id="18561-597">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="18561-598">O código anterior define um manipulador básico.</span><span class="sxs-lookup"><span data-stu-id="18561-598">The preceding code defines a basic handler.</span></span> <span data-ttu-id="18561-599">Ele verifica se um cabeçalho `X-API-KEY` foi incluído na solicitação.</span><span class="sxs-lookup"><span data-stu-id="18561-599">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="18561-600">Se o cabeçalho estiver ausente, isso poderá evitar a chamada de HTTP e retornar uma resposta adequada.</span><span class="sxs-lookup"><span data-stu-id="18561-600">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="18561-601">Durante o registro, um ou mais manipuladores podem ser adicionados à configuração para um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-601">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="18561-602">Essa tarefa é realizada por meio de métodos de extensão no <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="18561-602">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="18561-603">No código anterior, o `ValidateHeaderHandler` é registrado com a DI.</span><span class="sxs-lookup"><span data-stu-id="18561-603">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="18561-604">O manipulador **deve** ser registrado na injeção de dependência como serviço temporário, mas nunca com escopo.</span><span class="sxs-lookup"><span data-stu-id="18561-604">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="18561-605">Se o manipulador estiver registrado como um serviço com escopo e algum serviço do qual ele depende for descartável:</span><span class="sxs-lookup"><span data-stu-id="18561-605">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="18561-606">Os serviços do manipulador poderão ser descartados antes que ele saia do escopo.</span><span class="sxs-lookup"><span data-stu-id="18561-606">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="18561-607">Os serviços de manipulador descartados farão com que o manipulador falhe.</span><span class="sxs-lookup"><span data-stu-id="18561-607">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="18561-608">Depois de registrado, o <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> poderá ser chamado, passando no tipo do manipulador.</span><span class="sxs-lookup"><span data-stu-id="18561-608">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="18561-609">Vários manipuladores podem ser registrados na ordem em que eles devem ser executados.</span><span class="sxs-lookup"><span data-stu-id="18561-609">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="18561-610">Cada manipulador encapsula o próximo manipulador até que o `HttpClientHandler` final execute a solicitação:</span><span class="sxs-lookup"><span data-stu-id="18561-610">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="18561-611">Use uma das seguintes abordagens para compartilhar o estado por solicitação com os manipuladores de mensagens:</span><span class="sxs-lookup"><span data-stu-id="18561-611">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="18561-612">Passe os dados pelo manipulador usando `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="18561-612">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="18561-613">Use `IHttpContextAccessor` para acessar a solicitação atual.</span><span class="sxs-lookup"><span data-stu-id="18561-613">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="18561-614">Crie um objeto de armazenamento `AsyncLocal` personalizado para passar os dados.</span><span class="sxs-lookup"><span data-stu-id="18561-614">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="18561-615">Usar manipuladores baseados no Polly</span><span class="sxs-lookup"><span data-stu-id="18561-615">Use Polly-based handlers</span></span>

<span data-ttu-id="18561-616">O `IHttpClientFactory` integra-se a uma biblioteca de terceiros popular chamada [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="18561-616">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="18561-617">O Polly é uma biblioteca abrangente de tratamento de falha transitória e de resiliência para .NET.</span><span class="sxs-lookup"><span data-stu-id="18561-617">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="18561-618">Ela permite que os desenvolvedores expressem políticas, como Repetição, Disjuntor, Tempo Limite, Isolamento de Bulkhead e Fallback de maneira fluente e thread-safe.</span><span class="sxs-lookup"><span data-stu-id="18561-618">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="18561-619">Os métodos de extensão são fornecidos para habilitar o uso de políticas do Polly com instâncias de `HttpClient` configuradas.</span><span class="sxs-lookup"><span data-stu-id="18561-619">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="18561-620">As extensões do Polly:</span><span class="sxs-lookup"><span data-stu-id="18561-620">The Polly extensions:</span></span>

* <span data-ttu-id="18561-621">Dão suporte à adição de manipuladores baseados no Polly aos clientes.</span><span class="sxs-lookup"><span data-stu-id="18561-621">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="18561-622">Podem ser usadas depois de instalar o pacote do NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="18561-622">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="18561-623">O pacote não está incluído na estrutura ASP.NET Core compartilhada.</span><span class="sxs-lookup"><span data-stu-id="18561-623">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="18561-624">Tratar falhas transitórias</span><span class="sxs-lookup"><span data-stu-id="18561-624">Handle transient faults</span></span>

<span data-ttu-id="18561-625">As falhas mais comuns ocorrem quando as chamadas HTTP externas são transitórias.</span><span class="sxs-lookup"><span data-stu-id="18561-625">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="18561-626">Um método de extensão conveniente chamado `AddTransientHttpErrorPolicy` é incluído para permitir que uma política seja definido para tratar erros transitórios.</span><span class="sxs-lookup"><span data-stu-id="18561-626">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="18561-627">As políticas configuradas com esse método de extensão tratam `HttpRequestException`, respostas HTTP 5xx e respostas HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="18561-627">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="18561-628">A extensão `AddTransientHttpErrorPolicy` pode ser usada em `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="18561-628">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="18561-629">A extensão fornece acesso a um objeto `PolicyBuilder` configurado para tratar erros que representam uma possível falha transitória:</span><span class="sxs-lookup"><span data-stu-id="18561-629">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="18561-630">No código anterior, uma política `WaitAndRetryAsync` é definida.</span><span class="sxs-lookup"><span data-stu-id="18561-630">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="18561-631">As solicitações com falha são repetidas até três vezes com um atraso de 600 ms entre as tentativas.</span><span class="sxs-lookup"><span data-stu-id="18561-631">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="18561-632">Selecionar políticas dinamicamente</span><span class="sxs-lookup"><span data-stu-id="18561-632">Dynamically select policies</span></span>

<span data-ttu-id="18561-633">Existem métodos de extensão adicionais que podem ser usados para adicionar manipuladores baseados no Polly.</span><span class="sxs-lookup"><span data-stu-id="18561-633">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="18561-634">Uma dessas extensões é a `AddPolicyHandler`, que tem várias sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="18561-634">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="18561-635">Uma sobrecarga permite que a solicitação seja inspecionada durante a definição da política a ser aplicada:</span><span class="sxs-lookup"><span data-stu-id="18561-635">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="18561-636">No código anterior, se a solicitação de saída é um HTTP GET, um tempo limite de 10 segundos é aplicado.</span><span class="sxs-lookup"><span data-stu-id="18561-636">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="18561-637">Para qualquer outro método HTTP, é usado um tempo limite de 30 segundos.</span><span class="sxs-lookup"><span data-stu-id="18561-637">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="18561-638">Adicionar vários manipuladores do Polly</span><span class="sxs-lookup"><span data-stu-id="18561-638">Add multiple Polly handlers</span></span>

<span data-ttu-id="18561-639">É comum aninhar políticas do Polly para fornecer funcionalidade avançada:</span><span class="sxs-lookup"><span data-stu-id="18561-639">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="18561-640">No exemplo anterior, dois manipuladores são adicionados.</span><span class="sxs-lookup"><span data-stu-id="18561-640">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="18561-641">O primeiro usa a extensão `AddTransientHttpErrorPolicy` para adicionar uma política de repetição.</span><span class="sxs-lookup"><span data-stu-id="18561-641">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="18561-642">As solicitações com falha são repetidas até três vezes.</span><span class="sxs-lookup"><span data-stu-id="18561-642">Failed requests are retried up to three times.</span></span> <span data-ttu-id="18561-643">A segunda chamada a `AddTransientHttpErrorPolicy` adiciona uma política de disjuntor.</span><span class="sxs-lookup"><span data-stu-id="18561-643">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="18561-644">As solicitações externas adicionais são bloqueadas por 30 segundos quando ocorrem cinco tentativas com falha consecutivamente.</span><span class="sxs-lookup"><span data-stu-id="18561-644">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="18561-645">As políticas de disjuntor são políticas com estado.</span><span class="sxs-lookup"><span data-stu-id="18561-645">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="18561-646">Todas as chamadas por meio desse cliente compartilham o mesmo estado do circuito.</span><span class="sxs-lookup"><span data-stu-id="18561-646">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="18561-647">Adicionar políticas do registro do Polly</span><span class="sxs-lookup"><span data-stu-id="18561-647">Add policies from the Polly registry</span></span>

<span data-ttu-id="18561-648">Uma abordagem para gerenciar as políticas usadas com frequência é defini-las uma única vez e registrá-las em um `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="18561-648">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="18561-649">É fornecido um método de extensão que permite que um manipulador seja adicionado usando uma política do registro:</span><span class="sxs-lookup"><span data-stu-id="18561-649">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="18561-650">No código anterior, duas políticas são registradas quando `PolicyRegistry` é adicionado ao `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="18561-650">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="18561-651">Para usar uma política do registro, o método `AddPolicyHandlerFromRegistry` é usado, passando o nome da política a ser aplicada.</span><span class="sxs-lookup"><span data-stu-id="18561-651">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="18561-652">Mais informações sobre o `IHttpClientFactory` e as integrações do Polly podem ser encontradas no [Wiki do Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="18561-652">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="18561-653">HttpClient e gerenciamento de tempo de vida</span><span class="sxs-lookup"><span data-stu-id="18561-653">HttpClient and lifetime management</span></span>

<span data-ttu-id="18561-654">Uma nova instância de `HttpClient` é chamada, sempre que `CreateClient` é chamado na `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="18561-654">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="18561-655">Há um <xref:System.Net.Http.HttpMessageHandler> por cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="18561-655">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="18561-656">O alocador gerencia a vida útil das instâncias do `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="18561-656">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="18561-657">`IHttpClientFactory` cria pools com as instâncias de `HttpMessageHandler` criadas pelo alocador para reduzir o consumo de recursos.</span><span class="sxs-lookup"><span data-stu-id="18561-657">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="18561-658">Uma instância de `HttpMessageHandler` poderá ser reutilizada no pool, ao criar uma nova instância de `HttpClient`, se o respectivo tempo de vida não tiver expirado.</span><span class="sxs-lookup"><span data-stu-id="18561-658">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="18561-659">O pooling de manipuladores é preferível porque normalmente cada manipulador gerencia as próprias conexões HTTP subjacentes.</span><span class="sxs-lookup"><span data-stu-id="18561-659">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="18561-660">Criar mais manipuladores do que o necessário pode resultar em atrasos de conexão.</span><span class="sxs-lookup"><span data-stu-id="18561-660">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="18561-661">Alguns manipuladores também mantêm as conexões abertas indefinidamente, o que pode impedir que o manipulador reaja a alterações de DNS.</span><span class="sxs-lookup"><span data-stu-id="18561-661">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="18561-662">O tempo de vida padrão do manipulador é de 2 minutos.</span><span class="sxs-lookup"><span data-stu-id="18561-662">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="18561-663">O valor padrão pode ser substituído para cada cliente nomeado.</span><span class="sxs-lookup"><span data-stu-id="18561-663">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="18561-664">Para substituí-lo, chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> no `IHttpClientBuilder` que é retornado ao criar o cliente:</span><span class="sxs-lookup"><span data-stu-id="18561-664">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="18561-665">Não é necessário descartar o cliente.</span><span class="sxs-lookup"><span data-stu-id="18561-665">Disposal of the client isn't required.</span></span> <span data-ttu-id="18561-666">O descarte cancela as solicitações de saída e garante que a determinada instância de `HttpClient` não seja usada depois de chamar <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="18561-666">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="18561-667">`IHttpClientFactory` rastreia e descarta recursos usados pelas instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-667">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="18561-668">Geralmente, as instâncias de `HttpClient` podem ser tratadas como objetos do .NET e não exigem descarte.</span><span class="sxs-lookup"><span data-stu-id="18561-668">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="18561-669">Manter uma única instância de `HttpClient` ativa por uma longa duração é um padrão comum usado, antes do início de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="18561-669">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="18561-670">Esse padrão se torna desnecessário após a migração para `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="18561-670">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="18561-671">Alternativas para IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="18561-671">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="18561-672">O uso de `IHttpClientFactory` em um aplicativo habilitado para DI é evitado:</span><span class="sxs-lookup"><span data-stu-id="18561-672">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="18561-673">Problemas de esgotamento de recursos por meio do pooling `HttpMessageHandler` instâncias.</span><span class="sxs-lookup"><span data-stu-id="18561-673">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="18561-674">Problemas de DNS obsoletos por ciclo `HttpMessageHandler` instâncias em intervalos regulares.</span><span class="sxs-lookup"><span data-stu-id="18561-674">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="18561-675">Há maneiras alternativas de resolver os problemas anteriores usando uma instância de <xref:System.Net.Http.SocketsHttpHandler> de vida longa.</span><span class="sxs-lookup"><span data-stu-id="18561-675">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="18561-676">Crie uma instância do `SocketsHttpHandler` quando o aplicativo for iniciado e usado para a vida útil do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="18561-676">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="18561-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> para um valor apropriado com base em tempos de atualização de DNS.</span><span class="sxs-lookup"><span data-stu-id="18561-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="18561-678">Crie `HttpClient` instâncias usando `new HttpClient(handler, disposeHandler: false)` conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="18561-678">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="18561-679">As abordagens anteriores resolvem os problemas de gerenciamento de recursos que `IHttpClientFactory` resolve de forma semelhante.</span><span class="sxs-lookup"><span data-stu-id="18561-679">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="18561-680">O `SocketsHttpHandler` compartilha conexões entre instâncias de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-680">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="18561-681">Esse compartilhamento impede o esgotamento de soquete.</span><span class="sxs-lookup"><span data-stu-id="18561-681">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="18561-682">O `SocketsHttpHandler` ciclos conexões de acordo com `PooledConnectionLifetime` para evitar problemas de DNS obsoletos.</span><span class="sxs-lookup"><span data-stu-id="18561-682">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="18561-683">Cookies</span><span class="sxs-lookup"><span data-stu-id="18561-683">Cookies</span></span>

<span data-ttu-id="18561-684">As instâncias de `HttpMessageHandler` em pool resultam em `CookieContainer` objetos que estão sendo compartilhados.</span><span class="sxs-lookup"><span data-stu-id="18561-684">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="18561-685">O compartilhamento de objetos `CookieContainer` imprevistos geralmente resulta em código incorreto.</span><span class="sxs-lookup"><span data-stu-id="18561-685">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="18561-686">Para aplicativos que exigem cookies, considere o:</span><span class="sxs-lookup"><span data-stu-id="18561-686">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="18561-687">Desabilitando a manipulação automática de cookies</span><span class="sxs-lookup"><span data-stu-id="18561-687">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="18561-688">Evitando `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="18561-688">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="18561-689">Chame <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> para desabilitar o tratamento automático de cookies:</span><span class="sxs-lookup"><span data-stu-id="18561-689">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="18561-690">Registro em log</span><span class="sxs-lookup"><span data-stu-id="18561-690">Logging</span></span>

<span data-ttu-id="18561-691">Os clientes criado pelo `IHttpClientFactory` registram mensagens de log para todas as solicitações.</span><span class="sxs-lookup"><span data-stu-id="18561-691">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="18561-692">Habilite o nível apropriado de informações na configuração de log para ver as mensagens de log padrão.</span><span class="sxs-lookup"><span data-stu-id="18561-692">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="18561-693">Os registros em log adicionais, como o registro em log dos cabeçalhos de solicitação, estão incluídos somente no nível de rastreamento.</span><span class="sxs-lookup"><span data-stu-id="18561-693">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="18561-694">A categoria de log usada para cada cliente inclui o nome do cliente.</span><span class="sxs-lookup"><span data-stu-id="18561-694">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="18561-695">Um cliente chamado *MyNamedClient*, por exemplo, registra mensagens com uma categoria de `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="18561-695">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="18561-696">Mensagens sufixadas com *LogicalHandler* ocorrem fora do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="18561-696">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="18561-697">Na solicitação, as mensagens são registradas em log antes que qualquer outro manipulador no pipeline a tenha processado.</span><span class="sxs-lookup"><span data-stu-id="18561-697">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="18561-698">Na resposta, as mensagens são registradas depois que qualquer outro manipulador do pipeline tenha recebido a resposta.</span><span class="sxs-lookup"><span data-stu-id="18561-698">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="18561-699">O registro em log também ocorre dentro do pipeline do manipulador de solicitações.</span><span class="sxs-lookup"><span data-stu-id="18561-699">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="18561-700">No caso do exemplo de *MyNamedClient*, essas mensagens são registradas em log na categoria de log `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="18561-700">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="18561-701">Para a solicitação, isso ocorre depois que todos os outros manipuladores são executados e imediatamente antes que a solicitação seja enviada pela rede.</span><span class="sxs-lookup"><span data-stu-id="18561-701">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="18561-702">Na resposta, esse registro em log inclui o estado da resposta antes que ela seja retornada por meio do pipeline do manipulador.</span><span class="sxs-lookup"><span data-stu-id="18561-702">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="18561-703">Habilitar o registro em log dentro e fora do pipeline permite a inspeção das alterações feitas por outros manipuladores do pipeline.</span><span class="sxs-lookup"><span data-stu-id="18561-703">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="18561-704">Isso pode incluir, por exemplo, alterações nos cabeçalhos de solicitação ou no código de status da resposta.</span><span class="sxs-lookup"><span data-stu-id="18561-704">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="18561-705">Incluir o nome do cliente na categoria de log permite a filtragem de log para clientes nomeados específicos, quando necessário.</span><span class="sxs-lookup"><span data-stu-id="18561-705">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="18561-706">Configurar o HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="18561-706">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="18561-707">Talvez seja necessário controlar a configuração do `HttpMessageHandler` interno usado por um cliente.</span><span class="sxs-lookup"><span data-stu-id="18561-707">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="18561-708">Um `IHttpClientBuilder` é retornado ao adicionar clientes nomeados ou com tipo.</span><span class="sxs-lookup"><span data-stu-id="18561-708">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="18561-709">O método de extensão <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pode ser usado para definir um representante.</span><span class="sxs-lookup"><span data-stu-id="18561-709">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="18561-710">O representante que é usado para criar e configurar o `HttpMessageHandler` primário usado pelo cliente:</span><span class="sxs-lookup"><span data-stu-id="18561-710">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="18561-711">Usar IHttpClientFactory em um aplicativo de console</span><span class="sxs-lookup"><span data-stu-id="18561-711">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="18561-712">Em um aplicativo de console, adicione as seguintes referências de pacote ao projeto:</span><span class="sxs-lookup"><span data-stu-id="18561-712">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="18561-713">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="18561-713">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="18561-714">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="18561-714">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="18561-715">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="18561-715">In the following example:</span></span>

* <span data-ttu-id="18561-716"><xref:System.Net.Http.IHttpClientFactory> é registrado no contêiner de serviço do [Host genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="18561-716"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="18561-717">`MyService` cria uma instância de fábrica do cliente do serviço, que é usada para criar um `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="18561-717">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="18561-718">`HttpClient` é usado para recuperar uma página da Web.</span><span class="sxs-lookup"><span data-stu-id="18561-718">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="18561-719">`Main` cria um escopo para executar o método `GetPage` do serviço e gravar os primeiros 500 caracteres do conteúdo da página da Web no console.</span><span class="sxs-lookup"><span data-stu-id="18561-719">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="18561-720">Middleware de propagação de cabeçalho</span><span class="sxs-lookup"><span data-stu-id="18561-720">Header propagation middleware</span></span>

<span data-ttu-id="18561-721">A propagação de cabeçalho é um middleware com suporte da Comunidade para propagar cabeçalhos HTTP da solicitação de entrada para as solicitações de cliente HTTP de saída.</span><span class="sxs-lookup"><span data-stu-id="18561-721">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="18561-722">Para usar a propagação de cabeçalho:</span><span class="sxs-lookup"><span data-stu-id="18561-722">To use header propagation:</span></span>

* <span data-ttu-id="18561-723">Referencie a porta com suporte da Comunidade do pacote [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="18561-723">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="18561-724">ASP.NET Core 3,1 e posterior dá suporte a [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="18561-724">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="18561-725">Configure o middleware e o `HttpClient` no `Startup`:</span><span class="sxs-lookup"><span data-stu-id="18561-725">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="18561-726">O cliente inclui os cabeçalhos configurados nas solicitações de saída:</span><span class="sxs-lookup"><span data-stu-id="18561-726">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="18561-727">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="18561-727">Additional resources</span></span>

* <span data-ttu-id="18561-728">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) (Usar o HttpClientFactory para implementar solicitações HTTP resilientes)</span><span class="sxs-lookup"><span data-stu-id="18561-728">[Use HttpClientFactory to implement resilient HTTP requests](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)</span></span>
* [<span data-ttu-id="18561-729">Implementar repetições de chamadas HTTP com retirada exponencial com o HttpClientFactory e políticas da Polly</span><span class="sxs-lookup"><span data-stu-id="18561-729">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="18561-730">Implementar o padrão de disjuntor</span><span class="sxs-lookup"><span data-stu-id="18561-730">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
